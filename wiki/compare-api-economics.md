---
title: "Comparison: API Economics Across Projects"
type: comparison
sources: [political-trending-score-pollitique.md, college-scorecard-api-favcollege.md, review-scraping-architecture-favcollege.md, weather-provider-architecture-snowcheck.md, ai-image-consistency-gemini.md]
created: 2026-04-04
updated: 2026-04-04
---

# Comparing API Economics Across Projects

Every project depends on external APIs. The cost, rate limits, and access constraints shaped architecture decisions as much as technical requirements.

## API Landscape (2026)

| API | Project | Cost | Rate Limit | Access Level | Surprise |
|---|---|---|---|---|---|
| **Twitter v2 Free** | Pollitique | $0 | N/A | Write-only | Can't read any data |
| **Twitter v2 Basic** | Pollitique | $100/mo | Standard | Read + write | Required for any useful data |
| **YouTube Data v3** | Pollitique | $0 | 10K units/day | Full read | Generous free tier |
| **TikTok** | Pollitique | N/A | N/A | No public API | Not available at all |
| **College Scorecard** | FavCollege | $0 | 1K req/hour | Full read | Remarkably comprehensive |
| **Reddit** | FavCollege | $0 | 60 req/min | Full read | Requires app approval (1-3 days) |
| **Open-Meteo** | SnowCheck | $0 | Generous | Full read | No API key needed |
| **Gemini 2.5 Flash** | AniChat | Pay-per-use | Standard | Full | Fast, good for character gen |

## Key Insight: Free Tier Traps

**Twitter's free tier is write-only in 2026.** This is the biggest API economics surprise across all projects. Pollitique was designed around Twitter follower tracking, but the free tier can't read followers, mentions, or retweets. The $100/month Basic tier is required for any read access. This forced a complete redesign of the trending score weights.

**Contrast with government APIs.** The College Scorecard API provides 40+ fields per college across 7,000 institutions, completely free, 1,000 requests/hour. No signup friction — API key arrives via email in minutes. Government APIs are the best-kept secret in web development.

## Architecture Responses to API Constraints

### Pollitique: Graceful Degradation
When Twitter went write-only: adjusted weights (40% YouTube, 40% follower growth from whatever's available, 20% TikTok manual entry). Four-tier fallback chain. Color-coded freshness indicators. The architecture survived a major data source disappearing.

### SnowCheck: Provider Pattern
Abstract data sources behind a normalized interface so providers can be swapped without changing consumers. Currently only Open-Meteo (free, no key), but Weather Unlocked or Snow-Forecast.com can be added when needed. See [[provider-pattern]].

### FavCollege: Denormalized Cache
Reviews scraped in batch (2-second delays between requests), aggregated offline, cached on parent table. Avoids real-time API dependency on page load. The sync script (`npm run update-counts`) is the bridge between batch scraping and fast reads.

### AniChat: Pre-Generation + LRU Cache
Generate all character variants upfront (12 emotion avatars), cache with LRU (50 entries). Reduces per-session Gemini API calls to near zero after warmup. Three-tier fallback: cached image → pre-generated PNG → SVG.

## Cost of Running All Projects

| Project | Monthly API Cost | Could Be |
|---|---|---|
| Pollitique (current) | $0 (YouTube only) | $100 with Twitter Basic |
| FavCollege | $0 | $0 (all free APIs) |
| SnowCheck | $0 | $0 (Open-Meteo is free) |
| AniChat | ~$1-5 (Gemini usage) | $0 with full pre-generation |
| **Total** | **~$1-5/month** | **$100-105 with Twitter** |

## Lessons

1. **Check API pricing before designing** — Twitter's write-only free tier was discovered during implementation, not planning
2. **Government APIs are underrated** — College Scorecard provides more data for free than most commercial APIs
3. **Design for API disappearance** — Pollitique's fallback chain saved the project when Twitter access was lost
4. **Cache aggressively** — every project caches (6-hour Supabase, LRU, denormalized columns, pre-generated images)
5. **Batch when possible** — real-time API calls on page load are fragile; offline scraping + cache is more resilient

See also: [[provider-pattern]], [[source-political-trending-score]], [[source-college-scorecard-api]], [[source-weather-provider-architecture]]
