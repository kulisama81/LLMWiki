---
title: React 19 Hydration Pitfalls
type: concept
sources: [react-hydration-debugging-produceview.md]
created: 2026-05-16
updated: 2026-05-16
---

# React 19 Hydration Pitfalls

A taxonomy of patterns that cause silent hydration failures in React 19 + Next.js App Router. When hydration aborts, rendered elements remain on screen looking correct but receive no event handlers — so the page LOOKS fine but is dead to clicks. Diagnosing this without the browser console is nearly impossible because server logs never see client-side hydration failures.

## The Cardinal Symptom

> "The button renders, but clicking it does nothing."

That sentence is the hydration-failure tell. CSS click-blocking has visible symptoms (overlay, wrong cursor, pointer-events). Hydration failure has none — the DOM is correct, only the event delegation is missing.

## Anti-Patterns

### 1. Non-deterministic `useState` initializers

Any `useState` initializer that reads `Date`, `Math.random()`, `performance.now()`, browser APIs, or locale-sensitive output produces different values on SSR (server time / UTC) and on client first render (local time / local TZ). React detects the mismatch and aborts.

```tsx
// BROKEN
const [date, setDate] = useState(new Date().toISOString().slice(0, 10));
const [seed, setSeed] = useState(() => Math.random());
const [stats, setStats] = useState(() => computeFromCurrentTime());

// FIXED — deterministic empty, populate after mount
const [date, setDate] = useState("");
useEffect(() => { setDate(todayIso()); }, []);
```

Same applies to `useMemo` if it's used to seed render output (rare but possible).

### 2. Inline DOM mutation before React hydrates without `suppressHydrationWarning`

A common pattern: an inline `<script>` in `<head>` that reads `localStorage` and applies a theme attribute to `<html>` BEFORE React hydrates, to avoid theme flash:

```html
<script>
  document.documentElement.dataset.theme = localStorage.getItem('theme') ?? 'light';
</script>
```

Without `suppressHydrationWarning` on the `<html>` element, React 19 sees the attribute in the DOM but not in its tree, treats this as a hydration mismatch, and **bails out of hydrating the entire tree**. Every client component below loses its event handlers.

```tsx
// FIXED
<html lang="en" suppressHydrationWarning>
```

This is the documented `next-themes` pattern. The flag suppresses both the warning AND the bailout for that element only.

### 3. Browser-only globals in render path

`window`, `document`, `navigator`, `localStorage` — these don't exist on SSR. Reading them at render time throws on the server; reading them at client render time works but means SSR and client diverge.

```tsx
// BROKEN
const [theme, setTheme] = useState(localStorage.getItem('theme') ?? 'light');

// FIXED
const [theme, setTheme] = useState('light');
useEffect(() => { setTheme(localStorage.getItem('theme') ?? 'light'); }, []);
```

### 4. The "renders but doesn't respond" propagation rule

When one client component fails to hydrate, React 19 may abort hydration on a much larger subtree than expected. In the produceview session, a hydration mismatch deep inside a state provider made the entire layout's `ThemeSwitcher` (a sibling) non-interactive, because the abort propagated to the common parent. Fix the deepest hydration issue first; surface-level workarounds won't help.

## Defensive Pattern — Native Listeners for Critical UX

For elements that MUST work even if hydration has gone sideways (theme switcher, login button, error recovery), attach listeners with `useRef` + `addEventListener` in `useEffect` instead of relying on React's synthetic `onClick`:

```tsx
const groupRef = useRef<HTMLDivElement | null>(null);

useEffect(() => {
  const buttons = Array.from(
    groupRef.current?.querySelectorAll<HTMLButtonElement>("button[data-theme-id]") ?? []
  );
  const handler = (e: Event) => {
    const id = (e.currentTarget as HTMLButtonElement).dataset.themeId;
    if (id) applyTheme(id);
  };
  buttons.forEach(b => b.addEventListener("click", handler));
  return () => buttons.forEach(b => b.removeEventListener("click", handler));
}, []);
```

Native listeners only require `useEffect` to fire — which it does after first paint regardless of synthetic-event-delegation status. The DOM `data-*` attribute on the button replaces the JS closure for parameter passing.

Use sparingly. Adds slight code overhead; only worth it for paths that must survive even partial hydration failure.

## Debugging Checklist

When you see "renders but doesn't respond":

- [ ] Open browser DevTools console. Hydration warnings appear there exclusively — server logs are silent on this.
- [ ] Audit every `useState` and `useMemo` initializer for `Date`, `Math.random`, `Date.now`, browser globals, locale formatting.
- [ ] If there's a theme/init script that mutates `document.documentElement`, ensure `<html suppressHydrationWarning>`.
- [ ] Run `npm run build && npm run start` to eliminate dev-mode variables. See [[nextjs-dev-vs-prod-debugging]].
- [ ] If all clean, suspect browser extensions, service workers from prior sessions, or React-version bugs.

## Related

- [[source-react-hydration-debugging]] — the source session this concept was extracted from.
- [[nextjs-dev-vs-prod-debugging]] — when to suspect dev mode and how to verify quickly.
