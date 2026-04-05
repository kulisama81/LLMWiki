---
title: "Source Summary: Multi-Platform Political Trending Score"
type: source-summary
sources: [political-trending-score-pollitique.md]
created: 2026-04-04
updated: 2026-04-04
---

# Multi-Platform Political Trending Score

Summary of the Pollitique trending system design — a weighted multi-platform score for tracking 21 French presidential candidates.

## Key Takeaway

Building a social media trending system in 2026 is constrained more by API economics than by engineering. Twitter's free tier is write-only (can't read follower data), YouTube gives 10K free units/day, and TikTok has no public API. The design adapts to these realities with a weighted formula and graceful degradation.

## Trending Score Formula

`score = (followerGrowth * 0.40) + (youtubeViews * 0.40) + (tiktokViews * 0.20)`

Each metric normalized to 0-100. Growth mapped from -5%..+5% range.

## API Economics Discovery

| Platform | Cost | Access Level |
|---|---|---|
| Twitter v2 Free | $0 | Write-only (useless for reading) |
| Twitter v2 Basic | $100/month | Read access |
| YouTube Data API v3 | $0 | 10,000 units/day (plenty) |
| TikTok | N/A | No public API in 2026 |

This forced a design decision: launch without Twitter read data, adjust weights, add manual TikTok entry.

## Caching Architecture

6-hour refresh cycle via Supabase PostgreSQL + Vercel cron. Three tables: politicians (baseline), politician_snapshots (4x daily history), politician_cache (fast reads). Page loads <500ms from cache. Total cost: $0/month on free tiers.

## Error Handling

Four-tier fallback: live API -> recent snapshot (<24h) -> older snapshot (with warning) -> exclude from ranking. User sees color-coded freshness indicators.

See also: [[source-claude-usage-patterns]], [[sentiment-analysis]]
