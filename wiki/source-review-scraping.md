---
title: "Source Summary: Student Review Scraping Architecture"
type: source-summary
sources: [review-scraping-architecture-favcollege.md]
created: 2026-04-04
updated: 2026-04-04
---

# Student Review Scraping Architecture

Summary of the favcollege system for scraping, analyzing, and caching student reviews from Twitter and Reddit.

## Key Takeaway

The architecture solves three problems: sourcing authentic student reviews at scale (scraping), understanding them (sentiment analysis + topic categorization), and serving them fast (denormalized caching). The denormalized cache pattern — storing aggregates on the parent table and syncing via a separate script — is a practical tradeoff between real-time accuracy and page load speed.

## Sentiment Analysis Pipeline

Keyword-based using the `sentiment` npm library. Score normalized to -1..+1, then mapped to 1-5 stars: `rating = round((sentiment + 1) * 2 + 1)`.

Thresholds: >0.2 positive (4-5 stars), -0.2..+0.2 neutral (3 stars), <-0.2 negative (1-2 stars).

## Topic Categorization

Seven categories detected via keyword matching: academics, campus life, career, value, facilities, diversity, location. Each review can have multiple categories.

## Denormalized Cache Pattern

Reviews live in `student_feedback` table. College pages read from cached fields on `colleges` table (totalReviews, averageRating, sentimentScore). A sync script (`npm run update-counts`) recalculates aggregates and updates cached values. Must run after every scrape.

This avoids expensive COUNT/AVG aggregate queries on every page load while accepting eventual consistency.

## Review Quality Filters

- Minimum 100 characters
- Must use first-person pronouns (I, my, me)
- No retweets, simple replies, promotional content
- Twitter: engagement metrics map to helpful_votes; 100+ engagement = is_top_comment
- Reddit: karma and account age checks; verified student flairs prioritized

See also: [[source-college-scorecard-api]], [[sentiment-analysis]]
