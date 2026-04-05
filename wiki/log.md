---
title: Wiki Log
---

# Log

Chronological record of wiki operations.

## [2026-04-04] init | Wiki scaffolded
Initial project setup. Created directory structure, CLAUDE.md schema, index, and log.

## [2026-04-04] ingest | Claude Usage Patterns Across Repos
Scanned all kulisama81 GitHub repos for Claude-related patterns. Created raw source document with commit statistics, CLAUDE.md evolution, model usage progression, and 3-phase integration timeline. Generated 3 wiki pages: source summary, timeline entity, and multi-agent architecture concept.

## [2026-04-04] ingest | Manga Cinematography Best Practices
Ingested from kulisama81/anichat. Camera framing rules for visual novels based on manga/anime conventions — eyes vs. mouth close-ups, emotion-to-angle mapping. Created source summary page.

## [2026-04-04] ingest | AI Image Consistency with Gemini
Ingested from kulisama81/anichat. Techniques for consistent character generation with Nano Banana (Gemini 2.5 Flash) — detailed descriptions, explicit constraints, separation of static/variable. Created source summary page.

## [2026-04-04] ingest | AniChat Visual Novel System
Ingested from kulisama81/anichat. Combined emotion detection (94% accuracy, 6 emotions) + visual novel camera director (4 angles) + scene composer (multi-layer z-indexed). Created entity page linking all three anichat sources.

## [2026-04-04] ingest | Japan Summer 2026 Itinerary
Ingested from kulisama81/Japan-Sum-2026. 14-day family trip with JR Pass economics, Sunrise Express overnight train, Tawaraya Ryokan, July heat management, Nagoya food culture. Created source summary page.

## [2026-04-04] ingest | Trip Data Model Schema
Ingested from kulisama81/Japan-Sum-2026. Formal YAML-frontmatter schema for travel itineraries — stay/trip/transport entry types, tripId linking, itinerary YAML blocks. Created concept page.

## [2026-04-04] ingest | Rotoscoping for Pixel Art
Ingested from kulisama81/Mimigame. Jordan Mechner's Prince of Persia technique applied to a Great Pyrenees dog — 171 video frames to 9-frame gallop cycle, 80x80px sprites. Created source summary + rotoscoping concept page.

## [2026-04-04] ingest | Multi-Platform Political Trending Score
Ingested from kulisama81/Pollitique. Weighted trending formula (follower growth 40%, YouTube 40%, TikTok 20%), social media API constraints (Twitter free tier is write-only in 2026), 6-hour Supabase caching. Created source summary page.

## [2026-04-04] ingest | College Scorecard API Integration
Ingested from kulisama81/favcollege. US Dept of Education API with 40+ fields per college across 7,000 institutions. Carnegie classifications, data normalization pipeline. Created source summary page.

## [2026-04-04] ingest | Student Review Scraping Architecture
Ingested from kulisama81/favcollege. Twitter/Reddit scraping with sentiment analysis pipeline, topic categorization, denormalized cache pattern. Created source summary page.

## [2026-04-04] ingest | Weather Provider Architecture
Ingested from kulisama81/SnowCheck. Strategy/provider pattern for weather APIs with BaseWeatherProvider helpers. OpenSnow-inspired UX analysis with filter chips and progressive disclosure. Created source summary + provider-pattern concept page.

## [2026-04-04] ingest | IB Competitive Landscape & Distribution
Ingested from kulisama81/apibstudyguide. Competitive analysis of 10 IB study resource sites, Reddit r/IBO posting strategy (timing, flair, engagement rules), resource list pitches. Created source summary page.

## [2026-04-04] ingest | Cross-cutting concepts
Created 2 cross-cutting concept pages: sentiment-analysis (linking anichat + favcollege + Pollitique patterns) and provider-pattern (linking SnowCheck + favcollege abstractions).

## [2026-04-04] lint | First wiki-wide lint pass
Found 8 broken wikilinks and 1 orphan page. Fixed all: created 3 entity pages (favcollege, apibstudyguide, tkt), relinked 5 broken references to existing pages (anichat -> anichat-visual-novel-system, manga-cinematography -> source-manga-cinematography, ai-image-consistency -> source-ai-image-consistency, emotion-detection -> sentiment-analysis), converted 1 to plain text (sonnet-4-5). Added inbound link to orphan page (rotoscoping).

## [2026-04-04] create | Comparison: Sentiment Analysis Approaches
Side-by-side comparison of keyword-based sentiment across anichat (real-time emotion detection), favcollege (batch review scoring), and Pollitique (trending normalization). Covers when to use which approach, what each gets right/wrong, and why keyword-over-ML keeps winning.

## [2026-04-04] create | Comparison: API Economics Across Projects
Cross-project comparison of API costs and constraints. Key finding: Twitter free tier is write-only in 2026, government APIs (College Scorecard) are underrated, and every project caches aggressively as a response to API fragility.

## [2026-04-04] create | Synthesis: Patterns Across All Projects
Distilled 8 recurring patterns from 12 sources: keyword-over-ML, cache everything, provider pattern, pre-generation over real-time, DO NOT lists as living docs, single-day builds, data normalization layers, and three-tier fallback chains. Also captures what didn't work.

## [2026-04-04] update | README
Replaced bare `# LLMWiki` with project description, structure overview, current contents summary, getting started guide, and browse links.

## [2026-04-05] ingest | GA4 + Google Search Console Agent Setup

Source: hands-on setup session in apibstudyguide. Covers GCP service account creation, GA4 Data API + Search Console API integration, domain vs URL prefix property gotcha, cron scheduling, and agent configuration. Filed as `source-ga4-gsc-agent-setup.md`.
