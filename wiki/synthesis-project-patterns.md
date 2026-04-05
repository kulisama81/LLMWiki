---
title: "Synthesis: Patterns Across All Projects"
type: synthesis
sources: [claude-usage-patterns-across-repos.md, manga-cinematography-best-practices.md, ai-image-consistency-gemini.md, anichat-visual-novel-system.md, japan-summer-2026-itinerary.md, trip-data-model-schema.md, rotoscoping-pixel-art-mimigame.md, political-trending-score-pollitique.md, college-scorecard-api-favcollege.md, review-scraping-architecture-favcollege.md, weather-provider-architecture-snowcheck.md, ib-competitive-landscape-apibstudyguide.md]
created: 2026-04-04
updated: 2026-04-04
---

# Patterns Across All Projects

Synthesized from 12 sources spanning 8 repositories, Dec 2025 — Apr 2026. These are the recurring decisions and approaches that appear across projects regardless of domain.

## 1. Keyword-Based Over ML-Based

Three projects independently chose keyword/rule-based approaches over ML models for classification tasks:
- AniChat: emotion detection (94% accuracy with keywords)
- FavCollege: review sentiment and topic categorization
- Pollitique: trending score normalization

**Why it keeps winning**: zero external dependencies, deterministic output, debuggable by reading a list, microsecond latency, no per-call costs. The sarcasm/irony gap is accepted as out of scope in every case.

See [[compare-sentiment-approaches]] for detailed comparison.

## 2. Cache Everything, Everywhere

Every project with external API dependencies caches aggressively:

| Project | Cache Strategy | TTL |
|---|---|---|
| Pollitique | Supabase politician_cache table | 6 hours |
| FavCollege | Denormalized aggregates on colleges table | Until sync script runs |
| SnowCheck | Next.js ISR | 30 min (originally), moving to 6h |
| AniChat | LRU Map (50 entries, ~90MB) | Session lifetime |

The pattern: **never call an API on page load if you can serve from cache**. Accept eventual consistency. Provide freshness indicators (timestamps, color-coded badges) so users know what they're looking at.

## 3. Provider/Strategy Pattern for External Data

Two projects use the strategy pattern to abstract data sources: [[provider-pattern]]
- SnowCheck: `BaseWeatherProvider` with normalized `SnowForecast` output
- FavCollege: `USE_MOCK_DATA` toggle between mock and Supabase

The pattern proves its value when APIs change pricing or disappear (Twitter going write-only). Swap the provider, don't rewrite the consumer.

## 4. Pre-Generation Over Real-Time Generation

When possible, generate assets ahead of time rather than on demand:
- AniChat: 12 emotion avatars pre-generated, cached for instant display
- Mimigame: sprite sheet pre-built from reference frames
- FavCollege: review aggregates pre-calculated by sync script

**Why**: eliminates latency variance, works offline, reduces API costs, provides guaranteed consistency (same image every time vs. regeneration drift).

## 5. "DO NOT" Lists as Living Documentation

CLAUDE.md files across repos accumulate "DO NOT" rules from real mistakes:
- SnowCheck: 10 rules (don't bypass provider pattern, don't add error handling for impossible scenarios)
- FavCollege: 16 rules (the most extensive)
- Mimigame: 10 rules (don't use image smoothing, don't exceed 12 colors)
- AniChat: detailed emotion/camera constraints

**The insight**: "Update this file whenever you see Claude make a mistake" turns errors into institutional knowledge. The rules grow from experience, not upfront design. By Phase 3 ([[apibstudyguide]]), this evolved into automated quality gates (hooks) that enforce rules without human vigilance.

## 6. Single-Day Builds Are Viable

Two projects were built completely in one day:
- AniChat: 100% Claude, 3 commits, complete visual novel system with emotion detection
- Mimigame: 67% Claude, complete pixel art pipeline from video footage to playable demo

**What makes this possible**: Claude handles all boilerplate and integration; humans provide domain knowledge and creative direction. The constraint is design decisions, not implementation time.

## 7. Normalize Heterogeneous Data

Every project that consumes external data normalizes it to an internal model:
- SnowCheck: different weather APIs → standard `SnowForecast` type with unit conversions
- FavCollege: College Scorecard API fields → application `College` type with auto-generated tags
- Pollitique: Twitter/YouTube/TikTok metrics → 0-100 normalized trending score
- Japan-Sum-2026: heterogeneous travel entries → standardized `stay`/`trip`/`transport` schema

The normalization layer is where domain knowledge lives: Carnegie classification codes, unit conversions, sentiment-to-rating mappings, coordinate validation rules.

## 8. Three-Tier Fallback Chains

Multiple projects use tiered fallback for resilience:
- AniChat: pre-generated avatar → talking/idle PNG → SVG fallback
- Pollitique: live API → recent snapshot → older snapshot → exclude
- SnowCheck: primary provider → fallback provider (by priority)

The pattern: define the ideal path, then degrade gracefully through increasingly stale/generic alternatives. Never let the user see an error if you can show something useful.

## What Didn't Work

- **Twitter API assumptions**: Pollitique designed around Twitter data, then discovered the free tier is write-only. Lesson: verify API access before designing around it.
- **LibreSprite scripting**: V8 embedding broken in compiled build. Lesson: test tool capabilities before committing to a workflow.
- **Exclamation marks as anger signal**: AniChat's "I'm good!" bug. Lesson: test sentiment systems with positive messages that use emphatic punctuation.

See also: [[compare-api-economics]], [[compare-sentiment-approaches]], [[source-claude-usage-patterns]]
