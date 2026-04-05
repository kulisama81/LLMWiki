# Student Review Scraping Architecture

Combined from kulisama81/favcollege — REVIEWS_GUIDE.md + REDDIT_REVIEWS_GUIDE.md
Date: 2026-01-24

## Overview

Architecture for scraping real student reviews from Twitter and Reddit, with sentiment analysis, topic categorization, and denormalized caching for the FavCollege college comparison platform.

## Supported Sources
- Twitter/X (active)
- Reddit (pending API approval)
- Future: Niche.com, College Confidential, RateMyProfessors

## Review Detection Criteria
- Minimum 100 characters
- First-person perspective (I, my, me, I'm, I've)
- Review-like keywords: "experience at", "life at", "student at", "love about"
- No retweets, simple replies, promotional content, or non-English content

## Sentiment Analysis Pipeline

### Keyword-Based Analysis
Uses `sentiment` npm library. Scores normalized to -1 (very negative) to +1 (very positive).

### Conversion to Rating
rating = round((sentiment + 1) * 2 + 1) — maps -1..+1 to 1..5 stars

### Thresholds
- Positive (score > 0.2): 4-5 stars
- Neutral (-0.2 to +0.2): 3 stars
- Negative (score < -0.2): 1-2 stars

## Topic Categorization

Keyword-based categorization into 7 topics:
- Academics: professor, class, exam, grade
- Campus Life: dorm, food, party, social, friends
- Career: job, internship, career, placement
- Value: tuition, cost, scholarship, financial
- Facilities: gym, library, lab, building
- Diversity: diverse, inclusive, culture, international
- Location: location, city, weather, campus

## Twitter Integration

### Search Strategy
Query format: `"term" (experience OR life OR student OR love OR hate) -is:retweet -is:reply lang:en`
Max 30 tweets per search term, up to 15 reviews per college.

### Engagement Scoring
Tweet metrics (likes, retweets, replies) mapped to helpful_votes.
Tweets with 100+ engagement marked as is_top_comment.

### Rate Limiting
2 second delay between searches. 429 error: wait 60 seconds. Don't scrape more than 50 colleges at once.

## Reddit Integration

### Subreddit Mapping
25+ colleges mapped to their subreddits (UC system, Cal State, Arizona universities, etc.).

### Verification
Checks user karma, account age. Prioritizes verified student flairs. Higher upvote count = higher helpful_votes.

## Denormalized Cache Pattern

### The Problem
Reviews stored in student_feedback table, but college pages read from cached fields on colleges table for performance (avoiding COUNT/AVG aggregate queries on every page load).

### Cached Fields
- colleges.feedback_summary.totalReviews
- colleges.feedback_summary.averageRating
- colleges.feedback_summary.sentimentScore

### Sync Script
`npm run update-counts` — queries actual reviews, calculates aggregates, updates cached values. Must run after every scrape.

## Database Schema

student_feedback table:
- id, college_id, student_id (e.g. "twitter_123456")
- date, graduation_year, major
- verified (boolean, social media reviews are unverified)
- rating (1-5), comment (text)
- sentiment (jsonb: {score, label})
- categories (jsonb array)
- helpful_votes, is_top_comment
- detailed_ratings (jsonb)

## Adding New Sources (Template)

Standard pipeline:
1. Initialize API client
2. Detect review-like content (length, first-person, keywords)
3. Analyze sentiment
4. Convert sentiment to 1-5 rating
5. Categorize by topic
6. Convert to database format with nanoid
7. Batch insert into Supabase
