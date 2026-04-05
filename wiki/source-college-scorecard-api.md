---
title: "Source Summary: College Scorecard API Integration"
type: source-summary
sources: [college-scorecard-api-favcollege.md]
created: 2026-04-04
updated: 2026-04-04
---

# College Scorecard API Integration

Summary of integrating the US Department of Education's College Scorecard API into the FavCollege platform.

## Key Takeaway

The College Scorecard API is a remarkably comprehensive free government API — 40+ data fields per college across ~7,000 institutions, with 1,000 requests/hour. It covers tuition, acceptance rates, SAT/ACT, enrollment, graduation rates, and post-graduation earnings. The gap is in qualitative data: no photos, no student reviews, no program details.

## What the API Provides

Quantitative data: tuition (in-state/out-of-state), acceptance rate, SAT/ACT scores, enrollment, graduation rate (4-year 150% time), median earnings 10 years post-entry, student demographics, Carnegie classification (R1/R2 research vs. master's colleges), school ownership (public/private), locale (urban/suburban/rural).

## What It Doesn't Provide

Photos (use Unsplash placeholders), student reviews (need scraping system), real-time data (updated annually), detailed program listings (separate Field of Study endpoint).

## Data Pipeline

Fetch -> Map (auto-generate tags from Carnegie classification, map states to regions, determine size/environment) -> Import to Supabase (batch with rate limiting). Toggle `USE_MOCK_DATA = false` to switch.

## Carnegie Classifications

R1 (Very High Research), R2 (High Research), D/PU (Doctoral/Professional), Master's Colleges. These codes determine institutional tags and filtering.

See also: [[source-review-scraping]], [[sentiment-analysis]]
