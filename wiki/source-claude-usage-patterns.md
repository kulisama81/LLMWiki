---
title: "Source Summary: Claude Usage Patterns Across Repos"
type: source-summary
sources: [claude-usage-patterns-across-repos.md]
created: 2026-04-04
updated: 2026-04-04
---

# Claude Usage Patterns Across kulisama81 Repos

Summary of how Claude has been used across 10+ repositories from Dec 2025 to Apr 2026.

## Key Takeaway

Claude integration evolved through three distinct phases — from simple co-authoring to ticket-driven development to fully automated multi-agent pipelines — over roughly 4 months. The trajectory shows increasing delegation and trust, with quality maintained through automated gates rather than manual review.

## Three Phases of Evolution

### Phase 1: Direct Collaboration (Dec 2025 — Jan 2026)
Simple CLAUDE.md with "DO NOT" rules, direct co-authored commits, primarily [[sonnet-4-5]]. SnowCheck was the first project (90% Claude commits). Key innovation: "update CLAUDE.md whenever Claude makes a mistake."

### Phase 2: Ticket-Driven Development (Jan — Feb 2026)
Added `tk` ticket tracker, GitHub Actions for mobile ticket creation, more detailed CLAUDE.md files with 16+ gotchas rules. Projects like [[favcollege]] and [[anichat]] built rapidly.

### Phase 3: Multi-Agent Pipelines (Mar — Apr 2026)
Full orchestration with 6 specialized agents in [[apibstudyguide]]. Automated quality gates via hooks. Analytics-driven prioritization. Dropped Co-Authored-By in favor of structured ticket-referenced commits. [[tkt]] replaced `tk` with MCP server integration.

## Statistics

- **~83+ explicit Claude co-authored commits** across all repos
- **10 repos** with some Claude involvement
- **8 repos** with CLAUDE.md files
- **Highest density**: SnowCheck (90%), anichat (100%), Pollitique (80%)

## Notable Patterns

- **Model selection became intentional**: Opus for planning/orchestration, Sonnet for execution — mirrors PM/engineer split
- **Single-day builds**: anichat and Mimigame were built in one day each
- **Self-enforcing quality**: PreToolUse hooks block ticket closure without clean UX review
- **Living documentation**: "DO NOT" lists grew from experience, encoding mistakes as institutional knowledge

See also: [[claude-integration-timeline]], [[multi-agent-architecture]]
