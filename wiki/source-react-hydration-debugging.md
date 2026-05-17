---
title: "Source Summary: React 19 + Next.js Hydration Debugging"
type: source-summary
sources: [react-hydration-debugging-produceview.md]
created: 2026-05-16
updated: 2026-05-16
---

# React 19 + Next.js Hydration Debugging — Produceview Session

Multi-layer debugging session on `kulisama81/produceview` (Produce-View org) while building the `/demo/distributor` MVP. Symptom: interactive elements rendered but were inert. Root cause turned out to be a stack of four independent issues plus a non-code-related fifth.

## Key Takeaway

In React 19 + Next.js App Router, "renders but doesn't respond" is almost always hydration failure. React aborts hydration on the affected subtree (or the whole tree if the failure is at `<html>`) and never attaches event handlers. The SSR output stays on screen, looking correct but dead. CSS click-blocking has visual symptoms; this doesn't.

## The Four Code-Level Anti-Patterns

See [[react-hydration-pitfalls]] for the full taxonomy. Briefly:

1. **Non-deterministic `useState` initializers** — `useState(new Date()...)`, `Math.random()`, anything reading the wall clock. SSR and client produce different values → mismatch → hydration abort.
2. **`useState(todayIso())` in date inputs** — same pattern, applied to form defaults.
3. **Inline DOM-mutation scripts without `suppressHydrationWarning`** — the load-bearing fix. Theme scripts that set `document.documentElement.dataset.theme` before React hydrates require `suppressHydrationWarning` on the `<html>` element, or React 19 bails out of hydrating the entire tree.
4. **Defensive native listeners** — for globally-important elements like theme switchers, prefer `useRef` + `addEventListener` in `useEffect` over React's synthetic `onClick`. Hydration-independent.

## The Fifth Layer — Dev Mode

After fixing all four code issues, the bug persisted for a remote tester accessing the dev server from another machine on the LAN. Console showed:

```
WebSocket connection to 'ws://.../_next/webpack-hmr' failed
```

Next.js dev mode requires HMR WebSocket to function. When that WS can't connect across the LAN (blocked/filtered), the dev runtime appears to interfere with React's hydration. **Fix:** switch to a production build (`npm run build && npm run start`) — eliminates the dev runtime entirely. Buttons worked immediately on the same URL.

## Diagnostic Sequence

1. Open browser DevTools console; look for hydration warnings. Server logs don't show them.
2. Audit `useState` initializers for non-determinism (`Date`, `Math.random`, locale-sensitive formatting).
3. Check `<html>` for pre-React DOM mutation; if found, ensure `suppressHydrationWarning`.
4. Run a production build to eliminate dev-mode variables.
5. If still broken, suspect browser extensions / service workers / React-version bugs.

## Cross-References

- [[react-hydration-pitfalls]] — the four code-level anti-patterns + the dev-mode trap, formatted as a checklist.
- [[nextjs-dev-vs-prod-debugging]] — when to suspect dev mode and how to verify with a prod build.

## Commit Trail

In `kulisama81/produceview`:
- `844c88f` — App + first hydration fix (empty initial state in provider)
- `4b9a35c` — `suppressHydrationWarning` + date-input fixes
- `9af9f4d` — Native event listeners for theme switcher
- Layer 5 had no code commit; the fix was switching `next dev` → `next start`.
