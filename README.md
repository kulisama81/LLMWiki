# LLM Wiki

A persistent, LLM-maintained knowledge base following [Andrej Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

The wiki is a compounding artifact: each new source enriches existing pages, creates new cross-references, and deepens the knowledge graph. The LLM handles all summarization, cross-referencing, and consistency maintenance. The human curates sources and directs analysis.

## Structure

```
raw/          Immutable source documents (the LLM reads, never modifies)
wiki/         LLM-generated markdown pages (summaries, entities, concepts, comparisons, synthesis)
CLAUDE.md     Schema and conventions governing how the LLM operates
```

## Current Contents

**12 raw sources** from 8 repositories covering: Claude integration patterns, manga cinematography, AI image generation, Japan travel, pixel art rotoscoping, political trending algorithms, US college data APIs, student review scraping, weather provider architecture, and IB study resource strategy.

**25 wiki pages**: 10 source summaries, 5 entity pages, 5 concept pages, 2 comparison pages, 1 synthesis page.

## Getting Started

Start a conversation with Claude in this directory. Key operations:

- **Ingest**: Drop a document into `raw/` and ask Claude to ingest it
- **Query**: Ask a question — Claude searches `wiki/index.md`, reads relevant pages, synthesizes an answer
- **Lint**: Ask Claude to run a lint pass — checks for broken links, stale claims, orphan pages, missing concepts

## Browse

- [`wiki/index.md`](wiki/index.md) — Content catalog (start here)
- [`wiki/log.md`](wiki/log.md) — Chronological record of all operations
- [Live site](https://kulisama81.github.io/LLMWiki/) — Browsable wiki with graph view (powered by Quartz)
