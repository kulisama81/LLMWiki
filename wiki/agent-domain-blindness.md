---
title: Agent Domain Blindness
type: concept
sources: [agent-foreign-language-handling.md]
created: 2026-04-07
updated: 2026-04-07
---

# Agent Domain Blindness

A failure mode in [[multi-agent-architecture]] pipelines where agents apply defaults from one domain to another without adapting to the new context.

## Definition

When an LLM agent is trained or prompted in one context (e.g., English content, IB Biology) and then asked to work in a different context (e.g., French language, AP program), it carries over assumptions from the original domain. These assumptions are invisible — the agent doesn't know what it doesn't know.

## Observed Instances (apibstudyguide)

| Instance | What happened | What was blind |
|---|---|---|
| **French accents** | Creator wrote AP French guide with zero accents | Language orthography |
| **Orphaned guides** | Math AI SL created but not wired to homepage | Navigation entry points |
| **AP nav hidden** | AP French live but nav bar still IB-only | Program-level navigation |
| **Commit format** | `Closes t-XXXX` instead of `Closes: [t-XXXX]` | Tool-specific syntax |
| **Placeholders shipped** | "Coming soon" sections sent to production | Content completeness |
| **Homepage hardcoded** | 4 subjects hardcoded, new subjects invisible | Dynamic vs static rendering |

## Fix Pattern

Always the same four steps:

1. **Research** — agent must actively investigate the new domain before acting
2. **Configure** — document domain-specific rules in config (workflow-config.json, CLAUDE.md)
3. **Validate** — automated checks catch violations (content-validate.mjs, ux-review.mjs)
4. **Regress-test** — add to the test suite so it can't recur

## Design Principle

**Never trust an agent to know what it doesn't know.** The pipeline must include:
- A research step before creation (creator reads CLAUDE.md, researches target language)
- A validation step after creation (validator checks against known rules)
- A rendered verification step (UX reviewer checks the actual output)

Each layer catches different classes of blindness. No single layer is sufficient.

## Relation to Multi-Agent Pipeline

The 6-agent pipeline in [[apibstudyguide]] was designed to catch errors through layered review (creator → validator → reviewer → ux-reviewer). Agent domain blindness is the class of errors that slip through ALL layers because none of them have the domain knowledge to detect the issue. The fix is adding domain-specific rules that each layer can check against.

See also: [[multi-agent-architecture]], [[source-agent-foreign-language]], [[synthesis-project-patterns]]
