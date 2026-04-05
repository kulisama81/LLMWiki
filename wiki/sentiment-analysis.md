---
title: Sentiment Analysis
type: concept
sources: [review-scraping-architecture-favcollege.md, anichat-visual-novel-system.md, political-trending-score-pollitique.md]
created: 2026-04-04
updated: 2026-04-04
---

# Sentiment Analysis

Lightweight, keyword-based sentiment analysis appears across three projects with different applications but similar techniques.

## Three Implementations

### AniChat — Emotion Detection
Detects 6 emotions (happy, sad, angry, surprised, romantic, neutral) from chat messages. Keyword + emoji + punctuation pattern analysis. 94% accuracy. Used to drive camera angle selection in the [[anichat-visual-novel-system]].

Notable bug: "I'm good!" detected as angry because exclamation marks boosted anger scores. Fix: explicit "avoid mouth" patterns for positive keywords.

### FavCollege — Review Sentiment
Uses `sentiment` npm library to score student reviews from -1 to +1, then maps to 1-5 star ratings: `rating = round((sentiment + 1) * 2 + 1)`. Categorizes reviews into 7 topics via keyword matching. See [[source-review-scraping]].

### Pollitique — Trending Score Component
Follower growth rate as a normalized component of a weighted trending score. Not traditional sentiment analysis but the same normalization pattern (map a range to 0-100 scale). See [[source-political-trending-score]].

## Common Patterns

All three use keyword-based approaches rather than ML models:
- Keyword lists per category/emotion
- Emoji and punctuation as signal boosters
- Normalization to a standard scale
- Explicit thresholds for classification

This is a deliberate tradeoff: fast, predictable, debuggable, zero dependencies on external ML services. The limitation is sarcasm and compound emotions.

See also: [[source-review-scraping]], [[source-political-trending-score]], [[anichat-visual-novel-system]]
