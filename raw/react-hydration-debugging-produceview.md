# React 19 + Next.js Hydration Debugging — Produceview Demo

Source: debugging session on kulisama81/produceview (Produce-View org), May 2026, building the `/demo/distributor` FSMA 204 distributor MVP demo.

## The Symptom

User reported: "the theme switching buttons don't work — they render but clicking them does nothing." Reproduced on the live demo. Page rendered correctly, navigation worked, but ALL interactive elements were inert. Five distinct fix attempts before identifying the actual root cause. The page was tested through SSH/LAN tunneling to a local dev server.

## Root Cause Layers (in order of discovery)

The bug had MULTIPLE compounding layers. Fixing one didn't reveal the next until the symptoms changed.

### Layer 1 — Non-deterministic useState initializers

The first apparent issue was that the React state provider seeded its initial state with `new Date()` data:

```tsx
// BROKEN
const initialSnapshot = useMemo(() => buildInitial(new Date()), []);
const [state, setState] = useState<Persisted>(initialSnapshot);
```

`new Date()` runs at SSR (server time, UTC) and again at client first render (local TZ). The resulting ISO timestamps differ, which means SSR HTML and client virtual DOM produce different rendered output. React 19 detects the hydration mismatch.

**Fix:** initialize with a deterministic empty state, populate in `useEffect`:

```tsx
const EMPTY_STATE: Persisted = { customers: [], shipments: [], hydratedAt: "" };

const [state, setState] = useState<Persisted>(EMPTY_STATE);
const [ready, setReady] = useState(false);

useEffect(() => {
  const saved = loadFromStorage();
  setState(saved ?? buildInitial(new Date()));
  setReady(true);
}, []);
```

Components consuming the state check `ready` before rendering data-dependent UI.

### Layer 2 — `useState(todayIso())` in form pages

Same bug, different form: date inputs initialized with `useState(todayIso())`. The function reads `new Date()` at render time. SSR and client produced different ISO date strings, especially across timezone boundaries.

```tsx
// BROKEN
const [date, setDate] = useState(todayIso());

// FIXED
const [date, setDate] = useState("");
useEffect(() => { setDate(todayIso()); }, []);
```

Same pattern: empty default, populate after mount.

### Layer 3 — Missing `suppressHydrationWarning` on `<html>`

The actual root cause that kept buttons inert. The app uses an inline init script in `<head>` to set a theme before React hydrates:

```js
// inline script that runs before React
document.documentElement.dataset.theme = "ios";
```

When React 19 hydrates, it compares its tree (`<html lang="en" className="...">`) to the live DOM (`<html lang="en" className="..." data-theme="ios">`). The `data-theme` attribute is in the DOM but not in React's tree. React 19 treats this attribute mismatch as a hydration failure and **bails out of hydrating the entire tree** — no event handlers are attached anywhere, even on unrelated client components like the theme switcher itself.

**Fix:** tell React to ignore the discrepancy on that element:

```tsx
<html lang="en" className="..." suppressHydrationWarning>
```

This is the same fix the `next-themes` library documents. The flag suppresses the warning AND prevents the hydration bailout.

### Layer 4 — Belt-and-suspenders: native event listeners

Even after fixing layers 1-3, the issue persisted on the user's setup. As defensive insurance, the theme switcher was rewritten to use `useRef` + native `addEventListener` instead of React's synthetic `onClick`:

```tsx
const groupRef = useRef<HTMLDivElement | null>(null);

useEffect(() => {
  const buttons = Array.from(groupRef.current?.querySelectorAll<HTMLButtonElement>("button[data-theme-id]") ?? []);
  const handler = (e: Event) => {
    const id = (e.currentTarget as HTMLButtonElement).dataset.themeId;
    // ...apply theme
  };
  buttons.forEach(b => b.addEventListener("click", handler));
  return () => buttons.forEach(b => b.removeEventListener("click", handler));
}, []);
```

This pattern works as long as `useEffect` fires (which it does after first paint regardless of synthetic-event delegation status). The button reads its target theme from a `data-theme-id` attribute on the DOM node, not from a JS closure.

### Layer 5 — The actual fix: dev mode

After all four code-level fixes were in place, buttons STILL didn't work. The user provided console output showing only WebSocket failures:

```
WebSocket connection to 'ws://192.168.7.32:3000/_next/webpack-hmr?id=...' failed
```

That's Next.js's dev-mode Hot Module Replacement (HMR) WebSocket trying to reach back to the dev server. From another device on the same LAN, the WS connection was being blocked or filtered (HTTP worked fine, but WS didn't). This appeared to interfere with React's ability to attach event handlers — possibly via Next.js's dev runtime retrying the WS in a way that disrupted hydration.

**Final fix:** run a production build locally:

```
npm run build
npm run start -- -H 0.0.0.0 -p 3000
```

Same URL (`192.168.7.32:3000`), no HMR, no WebSocket, no dev runtime. Buttons worked immediately.

## Generalizable Lessons

### Hydration debugging anti-patterns

These patterns ALL cause silent hydration failures in React 19 + Next.js App Router:

1. **Time-based useState initializers:** `useState(new Date()...)`, `useState(Date.now())`, `useState(() => formatDate(...))`. Anything that reads the wall clock at render time will differ between SSR and client.
2. **Math.random() in render path:** generates different values per environment.
3. **Browser-only APIs in initial state:** `window`, `localStorage`, `navigator`, etc. — these throw or undefined on SSR.
4. **DOM-mutating scripts before React hydrates without `suppressHydrationWarning`** on the affected element.

### The "renders but doesn't respond" pattern

When buttons or interactive elements RENDER correctly but don't respond to clicks, the cause is almost always hydration failure (or a child error that propagates up the tree in React 19). The visual SSR output is fine; React just never attaches its event handlers because hydration aborted. Easy to confuse with a click-blocking CSS issue, but CSS issues usually have visual symptoms; this one doesn't.

### The dev-mode reproduction trap

Next.js's dev mode adds substantial runtime infrastructure: HMR client, Fast Refresh, route prefetching, error overlays. Some of these depend on the dev server being reachable from the client browser. **When testing from another device on the same LAN (or via a tunnel), the dev runtime can fail in subtle ways that don't appear locally.**

The reproduction trick: when something works on `localhost` for the developer but fails for a remote tester, BUILD a production bundle and serve it (`npm run build && npm run start`). This eliminates the dev runtime as a variable. If the issue disappears in prod, it was dev-mode-specific. If it persists, it's real application code.

### Defensive event handling

For globally-important interactive elements (theme switcher, login form, error recovery buttons), prefer `useRef` + native `addEventListener` in a `useEffect` over `onClick` props. The synthetic event system requires hydration to have completed; native listeners only require `useEffect` to fire. Trade off slightly more code for hydration-independence on critical UX paths.

## Diagnostic Sequence

When investigating "renders but doesn't respond" in React 19 + Next.js:

1. **Open browser DevTools console.** Look for hydration warnings ("Hydration failed", "Text content does not match", "Expected server HTML to contain..."). Console output is the only ground truth; server logs don't show client-side hydration failures.
2. **Audit `useState` initializers** for anything non-deterministic — `new Date()`, `Math.random()`, `Date.now()`, locale-dependent formatting, `window.*`.
3. **Check the `<html>` element** for any pre-React DOM mutation (theme scripts, `__NEXT_DATA__` overlays, analytics shims). If found, ensure `suppressHydrationWarning` is on the affected element.
4. **Run a production build** (`npm run build && npm run start`) to eliminate dev-mode variables.
5. **If still broken**, suspect browser extensions, service workers from prior sessions, or React-version-specific bugs.

## Commit Trail

The relevant commits in produceview (in order applied):
- `844c88f` — Initial app + hydration fix attempt (DistributorProvider → empty initial state)
- `4b9a35c` — Added `suppressHydrationWarning` + fixed `todayIso()` initializers
- `9af9f4d` — Bypass React synthetic events with native `addEventListener` for theme buttons
- (no commit needed for layer 5) — Switched dev server to `next start` to verify

Layer 5's solution was operational, not code: prod-mode-serve instead of dev-mode-serve.
