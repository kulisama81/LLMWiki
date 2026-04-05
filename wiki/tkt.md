---
title: tkt (Ticket Management)
type: entity
sources: [claude-usage-patterns-across-repos.md]
created: 2026-04-04
updated: 2026-04-04
---

# tkt

Go-based ticket management tool with MCP server integration, replacing the earlier `tk` CLI. Central storage at `~/.tickets/`, backed up to kulisama81/tickets repo.

## Evolution

| Version | Period | Features |
|---|---|---|
| `tk` (early) | Dec 2025 — Feb 2026 | CLI ticket system, used in [[favcollege]] and SnowCheck |
| `tkt` (current) | Mar 2026+ | Go-based, MCP server for native Claude session integration |

## Role in Multi-Agent Architecture

In [[apibstudyguide]], tkt is the central coordination mechanism:
- Planner agent creates and prioritizes tickets (Tier 1-4 scoring)
- Autoresearch agent creates tickets for gaps found
- Reviewer agent creates tickets for errors found
- PreToolUse hook blocks ticket closure without clean UX review

The shift from CLI to MCP server allowed agents to manage tickets natively within Claude sessions rather than shelling out to a CLI.

See also: [[multi-agent-architecture]], [[apibstudyguide]], [[source-claude-usage-patterns]]
