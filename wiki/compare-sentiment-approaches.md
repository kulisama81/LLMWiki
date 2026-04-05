---
title: "Comparison: Sentiment Analysis Approaches"
type: comparison
sources: [anichat-visual-novel-system.md, review-scraping-architecture-favcollege.md, political-trending-score-pollitique.md]
created: 2026-04-04
updated: 2026-04-04
---

# Comparing Sentiment Analysis Approaches

Three projects use keyword-based sentiment analysis for different purposes. Same family of technique, different tradeoffs.

## Side-by-Side

| Dimension | AniChat (Emotion) | FavCollege (Reviews) | Pollitique (Trending) |
|---|---|---|---|
| **Input** | Chat messages (1-2 sentences) | Social media posts (100+ chars) | Follower counts over time |
| **Output** | 1 of 6 emotion labels | -1 to +1 score → 1-5 stars | 0-100 normalized score |
| **Method** | Keyword + emoji + punctuation | `sentiment` npm library | Growth rate normalization |
| **Categories** | happy, sad, angry, surprised, romantic, neutral | academics, campus life, career, value, facilities, diversity, location | Single weighted score |
| **Accuracy** | 94% (17/18 test cases) | Not measured | N/A (formula-based) |
| **Speed** | ~2ms | Batch (offline) | Batch (6-hour cron) |
| **Failure mode** | "I'm good!" detected as angry | Sarcasm misclassified | Stale data if API fails |

## What Each Gets Right

**AniChat** optimizes for **speed and determinism**. At 2ms per message, it runs on every keystroke. The keyword approach means the same input always produces the same output — critical when driving visual rendering (you can't have the camera angle flickering).

**FavCollege** optimizes for **aggregate accuracy**. Individual reviews may be misclassified, but across 20+ reviews per college the average sentiment converges to something meaningful. The denormalized cache pattern trades real-time accuracy for page load speed.

**Pollitique** optimizes for **relative ranking**. The absolute score doesn't matter — what matters is whether Politician A is trending higher than Politician B. Normalization to 0-100 with max-value scaling ensures the ranking stays stable even as raw numbers grow.

## What Each Gets Wrong

**AniChat**: Exclamation marks as anger signal. The "I'm good!" bug happened because `!` boosted anger scores for positive messages. Fix: explicit allow-list patterns for positive keywords with exclamation marks.

**FavCollege**: No sarcasm detection. "Best school ever, if you enjoy crying in the library" reads as positive due to "best" and "enjoy" keywords. Would need NLP or LLM-based analysis to catch this — a deliberate scope exclusion.

**Pollitique**: Twitter API is write-only on free tier, so follower growth can't actually be measured without $100/month. The design adapts gracefully (adjusted weights, fallback to YouTube-only) but the original 40% Twitter weight was based on an assumption that broke.

## The Pattern

All three share a deliberate choice: **keyword-based over ML-based**. The reasons:
- Zero external dependencies (no API calls to OpenAI/Google for classification)
- Deterministic (same input → same output)
- Debuggable (you can read the keyword list)
- Fast (microseconds, not milliseconds)
- Free (no per-call costs)

The limitation is always the same: sarcasm, irony, and context-dependent meaning. None of the three projects considered this worth solving given their use cases.

## When to Use Which

- **Real-time, per-message** (driving UI): AniChat approach — discrete labels, keyword matching, variety enforcement
- **Batch, aggregate** (informing users): FavCollege approach — continuous score, aggregate to smooth errors, cache for performance
- **Ranking, relative** (comparing entities): Pollitique approach — normalize to common scale, weight by importance, handle missing data gracefully

See also: [[sentiment-analysis]], [[anichat-visual-novel-system]], [[source-review-scraping]], [[source-political-trending-score]]
