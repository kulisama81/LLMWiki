---
title: Next.js Dev vs Prod — Bug Reproduction
type: concept
sources: [react-hydration-debugging-produceview.md]
created: 2026-05-16
updated: 2026-05-16
---

# Next.js Dev vs Prod — Bug Reproduction

Next.js dev mode (`next dev`) adds substantial runtime infrastructure that production builds don't have. This infrastructure occasionally fails in environment-specific ways that LOOK like application bugs but disappear in production. Knowing when to suspect dev mode saves hours.

## What Dev Mode Adds

- **HMR WebSocket** — keeps a persistent connection to the dev server to push code updates. If the WS can't reach the server, the client retries in a loop.
- **Fast Refresh** — re-mounts components on edit, can leave stale state if a render throws during a refresh cycle.
- **Route prefetching** — speculative fetching for client navigation.
- **Error overlay + source maps** — heavier client bundle.
- **Per-request compilation** — pages compile on demand the first time they're hit.

In production (`next build && next start`), all of this is gone. Static and serverless functions execute the pre-built output. No WS, no HMR, no overlay.

## The Reproduction Trap

A specific class of bug only manifests when:
- The dev server runs on machine A
- The browser runs on machine B
- A and B are on the same LAN (or A is tunneled to B via SSH/ngrok)

The HMR WebSocket tries to reach back from B to A. If the network blocks WS (firewalls, VPN, browser extensions), the client logs an error and retries. **In some setups this disrupts React's ability to attach event handlers, possibly via the dev runtime retrying in a way that races with hydration.** The symptom: page loads fine, interactive elements look correct, but they don't respond to clicks.

The smoking-gun console output:
```
WebSocket connection to 'ws://192.168.X.X:3000/_next/webpack-hmr?id=...' failed
```

Repeated many times. If you see this AND interactive elements aren't responding, switch to a prod build before debugging further.

## The 30-Second Test

If something works on `localhost` for the developer but fails for a remote tester, BEFORE assuming it's an application bug:

```bash
# Stop dev server, then:
npm run build
npm run start -- -H 0.0.0.0 -p 3000
```

Same port, same URL, no dev runtime. If the issue disappears, it was dev-mode-specific. If it persists, it's real application code and worth deep debugging. The build typically takes 30-60 seconds.

## When to Use This

- A bug reproduces ONLY for testers accessing the dev server from another device.
- Console shows HMR WebSocket failures.
- Interactive elements render but don't respond (see also [[react-hydration-pitfalls]]).
- HMR/Fast Refresh has left the page in a stale state (less common — usually a hard refresh fixes this).

## When NOT to Use This

- Local-only bugs (use dev mode for the iteration speed).
- Bugs that already reproduce in production deploy.
- TypeScript or compile errors — those need dev mode's faster feedback loop.

## Related

- [[react-hydration-pitfalls]] — when the "renders but doesn't respond" symptom is genuinely a hydration issue rather than dev-mode-specific.
- [[source-react-hydration-debugging]] — the case study where this came up.
