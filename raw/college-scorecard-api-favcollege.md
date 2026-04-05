# College Scorecard API Integration

Combined from kulisama81/favcollege — PLAN_COLLEGE_SCORECARD.md + SCORECARD_INTEGRATION.md
Date: 2026-01-21

## Overview

Integration of real US college data from the US Department of Education's College Scorecard API into the FavCollege platform. The API provides comprehensive data on ~7,000 US colleges and universities, completely free with 1,000 requests/hour limit.

## What the API Provides (40+ fields per college)

### Real Data
- School name, city, state, region
- Tuition costs (in-state and out-of-state)
- Acceptance rate
- SAT/ACT scores (when available)
- Enrollment numbers
- Graduation/completion rate (4-year, 150% time)
- Post-graduation earnings (median 10 years after entry)
- Student demographics
- School type (public/private/for-profit)
- Campus size (small/medium/large)
- Location type (urban/suburban/rural)
- Carnegie classification (R1/R2 research vs. master's colleges)

### Not Available from API
- Actual college photos (placeholder images used)
- Student reviews (need separate collection system)
- Real-time data (updated annually)
- Detailed program listings (basic categories only)

## Key API Field Mappings

| College Scorecard Field | Application Field |
|---|---|
| school.name | name |
| latest.cost.tuition.in_state | metrics.tuition.inState |
| latest.cost.tuition.out_of_state | metrics.tuition.outOfState |
| latest.admissions.admission_rate.overall | metrics.acceptanceRate (as %) |
| latest.admissions.sat_scores.average.overall | metrics.averageSAT |
| latest.student.size | metrics.enrollment.total |
| latest.completion.completion_rate_4yr_150nt | metrics.graduationRate (as %) |
| latest.earnings.10_yrs_after_entry.median | metrics.averageStartingSalary |
| school.ownership | Tags (1=Public, 2=Private, 3=For-Profit) |
| school.locale | environment (urban/suburban/rural) |
| school.carnegie_basic | Tags (R1/R2 research, master's, etc.) |

## Carnegie Classification Codes
- 15: R1 - Doctoral Universities (Very High Research)
- 16: R2 - Doctoral Universities (High Research)
- 17: D/PU - Doctoral/Professional Universities
- 18-23: Master's Colleges & Universities

## Import Modes
- Top 100 colleges nationwide (recommended for first import)
- Diverse geographic set (10 colleges x 10 states)
- Specific state (up to 50 colleges per state)

## Data Pipeline
1. Fetch from College Scorecard API (collegeScorecard.ts)
2. Map API fields to application model (scorecardMapper.ts) — auto-generates tags, maps states to regions, determines size/environment
3. Import into Supabase database (import-scorecard-colleges.ts) — handles batching, rate limiting, error recovery
4. Toggle USE_MOCK_DATA = false to switch from mock to real data

## Generated/Estimated Data
- Hero images: placeholder from Unsplash (to be replaced)
- Tags: auto-generated from characteristics (e.g. "Highly Selective", "Research University")
- Description: basic description from data points
- Interests: basic program categories (limited without Field of Study API)

## Resources
- API Documentation: collegescorecard.ed.gov/data/api-documentation
- Data Dictionary: collegescorecard.ed.gov/assets/CollegeScorecardDataDictionary.xlsx
- Get API Key: api.data.gov/signup/ (free, arrives via email within minutes)
