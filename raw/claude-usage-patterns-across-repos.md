# Claude Usage Patterns Across kulisama81 Repos

Extracted 2026-04-04 from GitHub repos owned by kulisama81.

## Timeline & Commit Statistics

| Repo | Date Range | Claude Commits | Total Commits | % | Models Used |
|---|---|---|---|---|---|
| SnowCheck | 2025-12-14 — 2026-01-25 | 52 | 58 | 90% | Sonnet 4.5, Opus 4.5 |
| Mimigame | 2026-01-19 | 2 | 3 | 67% | Sonnet 4.5 |
| Pollitique | 2026-01-22 — 2026-01-25 | 4 | 5 | 80% | Sonnet 4.5 |
| favcollege | 2026-01-19 — 2026-02-09 | 14 | 19 | 74% | Sonnet 4.5, Opus 4.6 |
| anichat | 2026-02-03 | 3 | 3 | 100% | Sonnet 4.5 |
| Japan-Sum-2026 | 2026-03-19 — 2026-03-20 | 2 | 12 | 17% | Sonnet 4.6 |
| apibstudyguide | 2026-03-23 — 2026-04-05 | 141* | 141 | 100%* | Sonnet 4.6, Opus 4.6 |
| LLMWiki | 2026-04-04 | 1 | 3 | 33% | Opus 4.6 (1M) |

*apibstudyguide dropped explicit Co-Authored-By trailers in favor of structured commit messages with ticket references. All commits are Claude-driven via multi-agent pipeline.

**Total across repos: ~83+ explicit Claude co-authored commits, Dec 2025 — Apr 2026.**

## Evolution of Claude Integration (3 phases)

### Phase 1: Direct Collaboration (Dec 2025 — Jan 2026)
**Repos**: SnowCheck, Mimigame, Pollitique

- Simple CLAUDE.md with project conventions and "DO NOT" rules
- Direct Claude co-authored commits with `Co-Authored-By` trailers
- Model: primarily Sonnet 4.5, with Opus 4.5 for design-heavy work (SnowCheck)
- Task management via early `tk` CLI tool
- Branch naming: `claude/feature-name-sessionId`
- CLAUDE.md philosophy: "Update this file whenever you see Claude make a mistake"

### Phase 2: Ticket-Driven Development (Jan — Feb 2026)
**Repos**: favcollege, anichat

- Added `tk` ticket system for structured task tracking
- More detailed CLAUDE.md files with extensive gotchas lists
- GitHub Actions integration (`create-ticket.yml`) for mobile ticket creation from Issues
- More detailed commit messages (multi-paragraph with file lists)
- First use of Opus 4.6 model alongside Sonnet 4.5

### Phase 3: Multi-Agent Pipelines (Mar — Apr 2026)
**Repos**: apibstudyguide, LLMWiki

- Full multi-agent orchestration with 6 specialized agents
- Ticket system evolved to `tkt` (Go-based) with MCP server integration
- Dropped Co-Authored-By convention — moved to ticket-referenced structured commits
- Model-appropriate assignment: Opus for planner/orchestrator, Sonnet for execution agents
- Automated quality gates via hooks (SubagentStop, PreToolUse)
- Self-enforcing pipeline: creator → /simplify → validator → reviewer → ux-reviewer
- Autonomous research agent with budget limits and session logging
- Analytics-driven ticket prioritization (Tier 1-4 scoring)

## CLAUDE.md Template Evolution

All CLAUDE.md files follow a maturing structure:

**Common sections across repos:**
- Project Overview
- Task/Ticket Management
- Dev Setup & Commands
- Git Workflow
- Architecture Overview
- Code Conventions
- Common Gotchas / "DO NOT" rules
- Testing
- Recent Changes
- Resources

**Notable conventions:**
- "Update this file whenever you see Claude make a mistake" — present in early repos
- "DO NOT" rules: SnowCheck had 10, favcollege had 16 — grew from experience
- Later repos (apibstudyguide) added agent-specific instructions and workflow documentation

## Per-Repo Details

### SnowCheck (snowwow) — First Claude Project
- **Stack**: Next.js 14, Tailwind, Recharts
- **Focus**: Snow forecast app with provider-based architecture
- **CLAUDE.md**: Comprehensive with debugging tips, 10 "DO NOT" rules
- **Pattern**: Exponential backoff retry on push failures
- **Claude's role**: 90% of all commits — primary developer

### favcollege — College Finder
- **Stack**: Next.js 15, Supabase, College Scorecard API
- **Focus**: College comparison with review scraping (Twitter/Reddit)
- **CLAUDE.md**: 16 "DO NOT" rules, review scraping workflow
- **Innovation**: GitHub Actions for mobile ticket creation from Issues
- **Claude's role**: 74% of commits

### anichat — Anime Chat App
- **Stack**: JavaScript, Gemini API
- **Focus**: Visual novel chat with emotion detection and camera systems
- **CLAUDE.md**: Extremely detailed — 6 emotions, 6 camera angles, manga cinematography
- **Notable**: Documented critical bug fix ("i'm good!" detected as angry)
- **Claude's role**: 100% of commits (3 total, single day build)

### Pollitique — French Politics App
- **Stack**: Next.js 14, Tailwind, TypeScript
- **Focus**: French political polls and analysis
- **CLAUDE.md**: Standard conventions with i18n for French content
- **Claude's role**: 80% of commits

### Mimigame — Pixel Art Game
- **Stack**: Python
- **Focus**: Prince of Persia-style game featuring a dog named Mimi
- **CLAUDE.md**: Sprite specs (80x80px, 9-frame run cycle), rotoscoping workflow, LibreSprite
- **Claude's role**: 67% of commits (single day)

### apibstudyguide — AP/IB Study Guides (Most Advanced)
- **Stack**: Astro 6, MDX, KaTeX, Mermaid
- **Focus**: Study guide site with automated content pipeline
- **6 Agents**:
  1. `autoresearch.md` — Explores competitors via WebFetch/WebSearch, creates tickets (model: sonnet)
  2. `creator.md` — Subject expert, creates MDX content (model: sonnet)
  3. `planner.md` — TPM/PM, picks tickets by analytics-driven scoring, orchestrates others (model: opus)
  4. `reviewer.md` — Factual accuracy checker, read-only (model: sonnet)
  5. `ux-reviewer.md` — Visual QA via headless browser (model: sonnet)
  6. `validator.md` — Static analysis, build checks (model: sonnet)
- **Hooks**:
  - SessionStart: `sync-feedback.mjs` pulls student feedback from GitHub Issues
  - SubagentStop: Automated pipeline chaining (creator → simplify → validator → reviewer → ux-reviewer)
  - PreToolUse: Blocks ticket closure without clean UX review (<30 min old, 0 issues)
- **MCP**: `tkt` server for native ticket management
- **Innovation**: Self-enforcing quality system, analytics-driven prioritization

### Japan-Sum-2026 — Travel Planning
- **No CLAUDE.md**
- Minimal Claude involvement (2 of 12 commits), model: Sonnet 4.6

## Model Usage Progression

| Period | Primary Model | Secondary Model | Use Case |
|---|---|---|---|
| Dec 2025 — Feb 2026 | Sonnet 4.5 | Opus 4.5 | Opus for design-heavy work |
| Mar 2026 | Sonnet 4.6 | Opus 4.6 | Opus for planner/orchestrator |
| Apr 2026 | Opus 4.6 (1M) | Sonnet 4.6 | Opus for complex synthesis |

**Key insight**: Model selection became intentional — Opus for planning/orchestration, Sonnet for execution. This mirrors a PM/engineer split.

## Key Learnings

1. **CLAUDE.md as living documentation**: The "update when Claude makes a mistake" approach turns errors into institutional knowledge. "DO NOT" lists grew from 10 to 16+ rules across projects.

2. **Ticket-driven workflow is essential at scale**: Moving from ad-hoc commits to ticket-referenced structured development made Claude's output more predictable and reviewable.

3. **Multi-agent specialization works**: Splitting roles (planner, creator, reviewer, validator, ux-reviewer) mirrors real engineering teams and prevents any single agent from taking shortcuts.

4. **Automated quality gates prevent regressions**: The PreToolUse hook blocking ticket closure without clean UX review is a pattern that enforces standards without human vigilance.

5. **Attribution evolves**: Early projects used Co-Authored-By for transparency. The most mature project dropped it — suggesting the user moved from "Claude helped me" to "Claude is my development pipeline."

6. **Single-day builds are viable**: Both anichat (100% Claude, 3 commits) and Mimigame (67% Claude, 2 commits) were built in single days, suggesting Claude enables rapid prototyping of complete applications.

## Global Configuration

- **`~/.claude/settings.json`**: `effortLevel: "high"`, `autoDreamEnabled: true`
- **`~/.claude/CLAUDE.md`**: Global tkt instructions — run `tkt workflow` before first tkt command, use friendly ticket names
- **Ticket storage**: Central at `~/.tickets/`, backed up to `kulisama81/tickets` repo
