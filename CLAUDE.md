# LLM Wiki

A persistent, LLM-maintained knowledge base following Andrej Karpathy's LLM Wiki pattern.
Reference: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## Architecture

Three layers:

- **`raw/`** — Immutable source documents (articles, papers, notes, images). The LLM reads from these but never modifies them. Subdirectory `raw/assets/` holds images and attachments.
- **`wiki/`** — LLM-generated and maintained markdown pages. Summaries, entity pages, concept pages, comparisons, synthesis. The LLM owns this layer entirely — creates, updates, and cross-references pages. The user reads; the LLM writes.
- **`CLAUDE.md`** (this file) — Schema and conventions that govern how the LLM operates on the wiki.

## Conventions

### Page format
- All wiki pages are markdown files in `wiki/`.
- Use `[[page-name]]` wikilink syntax for cross-references between pages.
- Each page should have YAML frontmatter:
  ```yaml
  ---
  title: Page Title
  type: source-summary | entity | concept | comparison | synthesis | query-result
  sources: [list of source filenames this page draws from]
  created: YYYY-MM-DD
  updated: YYYY-MM-DD
  ---
  ```
- Use descriptive, kebab-case filenames (e.g. `wiki/machine-learning-overview.md`).

### Naming
- Source summary pages: `wiki/source-{short-name}.md`
- Entity pages: `wiki/{entity-name}.md`
- Concept pages: `wiki/{concept-name}.md`
- Comparison pages: `wiki/compare-{topic}.md`

## Operations

### Ingest
When a new source is added to `raw/`:
1. Read the source document fully.
2. Discuss key takeaways with the user.
3. Create a source summary page in `wiki/`.
4. Update `wiki/index.md` — add the new page with a one-line summary.
5. Create or update relevant entity and concept pages across the wiki.
6. Update cross-references (`[[wikilinks]]`) in affected pages.
7. Append an entry to `wiki/log.md`.

### Query
When the user asks a question:
1. Read `wiki/index.md` to find relevant pages.
2. Read relevant wiki pages.
3. Synthesize an answer with citations to wiki pages and sources.
4. If the answer is substantial or reusable, offer to file it as a new wiki page.

### Lint
Periodic health checks — run when asked or after significant ingests:
- Contradictions between pages
- Stale claims superseded by newer sources
- Orphan pages with no inbound links
- Important concepts mentioned but lacking their own page
- Missing cross-references
- Data gaps that could be filled

## Special files

- **`wiki/index.md`** — Content catalog. Every wiki page listed with a link and one-line summary, organized by type. Updated on every ingest. The LLM reads this first when answering queries.
- **`wiki/log.md`** — Append-only chronological record. Each entry: `## [YYYY-MM-DD] operation | Subject`. Records ingests, queries filed as pages, and lint passes.
