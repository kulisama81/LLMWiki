# Always Use the Full Agent Pipeline — Never Bypass

## Context
Learned from apibstudyguide (April 2026). The project uses a 6-agent pipeline: planner → creator → /simplify → validator → reviewer → ux-reviewer. Each agent handles a specific quality gate (code review, factual review, rendering review). The pipeline is enforced through hooks and the tkt ticket system.

## The Problem
When the planner agent prepares a detailed implementation spec (file paths, exact code changes) but stalls before spawning a creator agent, the main assistant is tempted to "just implement it directly" to save a round trip. This bypasses every downstream quality gate:
- /simplify (code quality review)
- validator (build, frontmatter, KaTeX, links)
- reviewer (factual accuracy)
- ux-reviewer (rendering checks via Puppeteer)

## Why It's Wrong
The pipeline exists because the main assistant misses regressions that specialized agents catch. Every direct edit is a hole in the safety net. The user specifically complained: "that defeats the purpose" of having a multi-agent pipeline at all.

Short-term time savings, long-term quality debt.

## The Rule
When the planner hands off a spec, always spawn an @creator agent with that spec. Never implement it directly. If the planner or creator stalls due to sandbox errors, tool failures, or agent halts, STOP and report back to the user — don't bypass.

**Only exception:** trivial one-line fixes explicitly requested by the user in the same turn (e.g., "change this line to X"). Multi-file work always goes through creator.

## Broader Pattern
This is an instance of a general principle: **agents should never silently bypass infrastructure that exists for a reason.** Similar examples from the same project:
- Never fake test reports to bypass the UX review gate
- Never skip hooks with `--no-verify`
- Never force-push to avoid merge conflicts

If an automated safety mechanism gets in the way, the correct response is to fix the mechanism or report the blockage — not to go around it.

## Related Principle
If the pipeline is too slow or frictional to use, that's a tooling problem to fix in the planner/creator agent definitions. It's not an excuse to skip them in individual sessions.
