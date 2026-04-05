# LLM Wiki

> "The dream is that you have a personal wiki that a LLM maintains for you... the wiki is a persistent, compounding artifact."
> — [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

Humans abandon wikis because the maintenance burden grows faster than the value. Cross-references decay, pages go stale, new knowledge never gets filed. The whole thing collapses under its own weight.

LLMs don't have this problem. They don't forget cross-references, never get bored updating pages, and can touch dozens of files in a single pass. So flip the labor split: **humans curate sources and direct analysis; the LLM handles all the bookkeeping**.

This repo is that experiment — a persistent knowledge base where Claude ingests raw sources, generates summaries, builds entity and concept pages, maintains cross-references, and runs its own lint passes. Every new source enriches what's already there. The wiki compounds.

## What's In Here

I've been building with Claude across 8+ repos since December 2025 — a snow forecast app, a visual novel with manga cinematography and AI-generated characters, a college comparison platform, a French political tracker, a pixel art game using 1989 rotoscoping techniques, an IB study guide with a 6-agent pipeline, and a Japan travel planner. Each project produced domain knowledge worth keeping: architecture decisions, API gotchas, design patterns, things that worked, things that didn't.

Instead of letting all of that rot in scattered CLAUDE.md files and commit messages, I pointed Claude at everything and said: build me a wiki.

**12 raw sources** became **25+ wiki pages** — source summaries, entity pages, concept pages, comparisons, and a synthesis that distills 8 recurring patterns across every project. The wiki found connections I hadn't noticed: the same keyword-based sentiment analysis appearing in three unrelated projects, the same provider/strategy pattern in two, the same caching-everything response to API fragility in all of them.

## How It Works

Three layers:

```
raw/          Immutable source documents — the LLM reads, never modifies
wiki/         LLM-generated markdown — summaries, entities, concepts, comparisons, synthesis
CLAUDE.md     Schema and conventions governing how the LLM operates
```

Three operations:

| Operation | What happens |
|---|---|
| **Ingest** | Drop a doc in `raw/`, Claude reads it, creates source summary + entity/concept pages, updates the index, adds cross-references, logs everything |
| **Query** | Ask a question, Claude searches the wiki, synthesizes an answer with citations, optionally files it as a new page |
| **Lint** | Claude checks for broken wikilinks, stale claims, orphan pages, missing concepts, and data gaps |

From any Claude session in any project, I can say `add2wiki: <topic>` and Claude will fetch the relevant content, write it to `raw/`, run the full ingest pipeline, commit, and push. The wiki grows from wherever I'm working.

## Browse It

The wiki is published as a browsable site with graph view, full-text search, backlinks, and popover previews:

**[kulisama81.github.io/LLMWiki](https://kulisama81.github.io/LLMWiki/)**

Or browse the source directly:
- [`wiki/index.md`](wiki/index.md) — Content catalog (start here)
- [`wiki/log.md`](wiki/log.md) — Chronological record of all operations

## What the Wiki Found

Some patterns that emerged only after Claude synthesized across all 12 sources:

- **Keyword-based over ML-based** — Three projects independently chose keyword sentiment analysis over ML models. Same tradeoff every time: deterministic, debuggable, zero dependencies, microsecond latency. Sarcasm gap accepted as out of scope.
- **Cache everything** — Every project with API dependencies caches aggressively (6-hour Supabase, LRU maps, denormalized columns, pre-generated images). The response to API fragility is always the same.
- **Twitter's free tier is write-only in 2026** — This broke Pollitique's original design. The wiki captured the architectural response: adjusted weights, four-tier fallback, graceful degradation.
- **Government APIs are underrated** — The College Scorecard API provides 40+ fields per college across 7,000 institutions, completely free. Meanwhile Twitter charges $100/month to read follower counts.
- **"DO NOT" lists as institutional memory** — CLAUDE.md files accumulate rules from real mistakes. They grew from 10 to 16+ rules across projects. By the most advanced project, they'd evolved into automated hooks that enforce rules without human vigilance.

## Try It Yourself

Fork this repo, delete everything in `raw/` and `wiki/` (keep `CLAUDE.md` and the structure), and start feeding it your own sources. The schema in `CLAUDE.md` tells Claude exactly how to operate.

Or just start a conversation in this directory:

```
> ingest raw/some-document.md
```

Claude takes it from there.

## Stack

- [Claude Code](https://claude.ai/claude-code) — the LLM doing all the wiki maintenance
- [Quartz v4](https://quartz.jzhao.xyz/) — static site generator for the browsable wiki
- GitHub Pages — hosting, auto-deploys on push to main
- Karpathy's [LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — the architecture this implements
