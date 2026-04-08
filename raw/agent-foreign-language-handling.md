# Agent Foreign Language Handling — Lessons from AP French

Source: apibstudyguide project, ticket t-04c9 and t-d96f (April 2026)

## The Bug

The creator agent generated an entire AP French Language & Culture study guide (783 lines, 10 files) with ZERO French accents. Every French word was written without diacritical marks: "francais" instead of "français", "reponse" instead of "réponse", "esthetique" instead of "esthétique".

This is an AP French exam prep guide — correct accents are fundamental. Students learning from these guides would memorize incorrect spellings. Missing accents also change meaning (e.g., "ou" = or, "où" = where; "a" = has, "à" = to/at).

## Root Cause

The creator agent has no awareness of the target language. It treats all content as English text regardless of the subject. There was no validation check for language-specific orthography.

## The Fix (t-d96f)

### 1. Creator Agent — Research Before Writing

Before writing content for a foreign language subject, the creator must RESEARCH the target language's orthographic conventions. Not assume — verify.

Steps:
a. Identify the target language from the subject slug and CLAUDE.md
b. Research: does this language use diacritics/accents? Special punctuation? Non-Latin script? RTL?
c. Document the findings
d. Apply conventions throughout all content

Critical insight: NOT all foreign languages need special handling. English, Malay, Indonesian have no special orthographic requirements. French, Spanish, German, Vietnamese, Turkish have extensive diacritical systems. Chinese/Japanese need proper character sets. Arabic/Hebrew need RTL. The creator can't know without checking.

### 2. Validator — Language-Specific Checks

Language rules defined per subject in workflow-config.json:
```json
{
  "ap-french": {
    "language": "French",
    "requiredCharacterClasses": ["éèêë", "àâ", "ùû", "ïî", "ô", "ç"],
    "commonErrors": {"francais": "français", "reponse": "réponse", "etudier": "étudier"}
  }
}
```

Only subjects with explicit language rules get checked — no false positives for English-language subjects. The config is populated by the creator during its research step, so new languages are automatically covered.

### 3. Generic Safety Check

For any subject tagged as a foreign language that has defined character classes: if the content files contain zero characters from those classes, flag as critical error. Zero French accents in a French guide = guaranteed bug.

## Key Principle

LLM agents default to English orthographic patterns. When generating content in other languages, they must be explicitly instructed about that language's writing system. This is not something to assume — it requires a research step because languages vary enormously in their orthographic complexity.

## Broader Pattern

This is an instance of a broader problem: agents applying defaults from one domain to another without adaptation. Same pattern appears in:
- Content structure (IB conventions applied to AP content)
- Navigation (homepage hardcoded for original subjects, not updated for new ones)
- Commit message format (tkt close syntax not matching tkt's expected format)

The fix pattern is always the same: research → configure → validate → test for regression.
