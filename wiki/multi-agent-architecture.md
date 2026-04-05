---
title: Multi-Agent Architecture
type: concept
sources: [claude-usage-patterns-across-repos.md]
created: 2026-04-04
updated: 2026-04-04
---

# Multi-Agent Architecture

The most advanced Claude integration pattern, deployed in [[apibstudyguide]].

## The 6-Agent Pipeline

| Agent | Role | Model | Behavior |
|---|---|---|---|
| **planner** | TPM/PM — picks tickets, orchestrates | Opus | Never writes code. Analytics-driven scoring (Tier 1-4). |
| **autoresearch** | Competitive analysis | Sonnet | WebFetch/WebSearch. Creates tickets for gaps. Budget-limited. |
| **creator** | Subject expert | Sonnet | Creates/updates MDX content. Requests `/simplify` after changes. |
| **validator** | Static analysis | Sonnet | Runs `content-validate.mjs` and `smoke-test.mjs`. |
| **reviewer** | Factual accuracy | Sonnet | Read-only. Creates tickets for errors found. |
| **ux-reviewer** | Visual QA | Sonnet | Headless browser checks via `ux-review.mjs`. |

## Automated Pipeline (via SubagentStop hooks)

```
creator → /simplify → validator → reviewer → ux-reviewer
```

Each agent's completion triggers the next. No human intervention required between steps.

## Quality Gate (PreToolUse hook)

Tickets cannot be closed unless a UX review report exists that is:
- Less than 30 minutes old
- Shows 0 issues

This prevents regressions without requiring human vigilance.

## Model Assignment Pattern

**Opus for planning, Sonnet for execution** — mirrors a PM/engineer split. The planner needs judgment and prioritization; execution agents need speed and accuracy.

## Supporting Infrastructure

- **`tkt` MCP server**: Native ticket management within Claude sessions
- **`sync-feedback.mjs`**: SessionStart hook pulls student feedback from GitHub Issues
- **Session logging**: planner and autoresearch maintain `.claude/*-log.json`

See also: [[source-claude-usage-patterns]], [[claude-integration-timeline]]
