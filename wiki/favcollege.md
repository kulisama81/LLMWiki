---
title: FavCollege
type: entity
sources: [college-scorecard-api-favcollege.md, review-scraping-architecture-favcollege.md]
created: 2026-04-04
updated: 2026-04-04
---

# FavCollege

College comparison platform (kulisama81/favcollege) built with Next.js 15 and Supabase. 74% Claude commits across Jan-Feb 2026. First project with 16 "DO NOT" rules in CLAUDE.md and first use of Opus 4.6 alongside Sonnet 4.5.

## Architecture

Two data pipelines feed the platform:

1. **College Scorecard API** — US Dept of Education's free API providing 40+ fields per college across ~7,000 institutions. Imported via a mapper that auto-generates tags from Carnegie classifications and normalizes data to application types. See [[source-college-scorecard-api]].

2. **Student Review Scraping** — Twitter and Reddit scraping with [[sentiment-analysis]] pipeline (keyword-based, -1 to +1 normalized to 1-5 stars), topic categorization (7 categories), and a denormalized cache pattern for fast page loads. See [[source-review-scraping]].

Both use the [[provider-pattern]] — a `USE_MOCK_DATA` toggle switches between mock data and Supabase-backed real data.

## Notable Patterns

- GitHub Actions integration (`create-ticket.yml`) for mobile ticket creation from Issues
- 16 "DO NOT" rules in CLAUDE.md — the most extensive at the time
- Denormalized caching: aggregate review stats cached on colleges table, synced by separate script

See also: [[source-college-scorecard-api]], [[source-review-scraping]], [[source-claude-usage-patterns]]
