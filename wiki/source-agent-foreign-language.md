---
title: Agent Foreign Language Handling
type: source-summary
sources: [agent-foreign-language-handling.md]
created: 2026-04-07
updated: 2026-04-07
---

# Agent Foreign Language Handling

Lessons from AP French guide creation in [[apibstudyguide]] (April 2026).

## Key Finding

LLM agents default to English orthographic patterns. The creator agent generated 783 lines of AP French content with zero French accents — "francais" instead of "français" throughout. Students would memorize incorrect spellings from a language exam prep guide.

## Root Cause

No target-language awareness in the [[multi-agent-architecture]] pipeline. The creator, validator, and reviewer all treated content as English regardless of subject.

## Fix Pattern: Research → Configure → Validate → Regress-Test

1. **Creator researches** the target language's orthography before writing (accents, scripts, directionality, punctuation)
2. **Creator documents** language rules in workflow-config.json during the research step
3. **Validator checks** content against language-specific rules (missing accents, common misspellings)
4. **Regression test** prevents recurrence (zero-accent check for configured languages)

## Critical Insight

Not all foreign languages need special handling — English, Malay, Indonesian have no diacritics. The creator must research, not assume. Language-specific validation only runs for subjects with explicitly configured rules.

## Broader Pattern

This is an instance of **agent domain blindness** — applying defaults from one context to another without adaptation. Same pattern caused:
- Orphaned guides (homepage not updated for new subjects)
- Broken commit format (tkt close syntax wrong)
- "Coming soon" placeholders shipped to production

The fix is always: research → configure → validate → test.

See also: [[multi-agent-architecture]], [[apibstudyguide]], [[synthesis-project-patterns]]
