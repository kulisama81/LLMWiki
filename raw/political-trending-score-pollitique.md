# Multi-Platform Political Trending Score System

Extracted from kulisama81/Pollitique — IMPLEMENTATION_PLAN.md
Date: 2026-01-22

## Overview

Design for transforming Pollitique from a simple follower tracker into a multi-platform trending system with 6-hour caching, tracking 21 French presidential candidates across Twitter, YouTube, and TikTok.

## Current Problems Solved
- Every page refresh triggered 21 Twitter API calls (10-20 seconds)
- No persistence — data lost on server restart
- Only tracking Twitter follower counts
- 30-minute ISR cache too frequent

## API Constraints Discovered

### Twitter API v2 Free Tier (2026)
- Free tier is WRITE-ONLY — cannot read mentions, retweets, or follower data
- Basic tier required: $100/month for read access
- Decision: start without Twitter mentions/retweets, use adjusted trending score

### YouTube Data API v3
- Free tier: 10,000 units/day
- Sufficient for 4 refreshes/day (21 politicians x ~100 units = ~2,100 per refresh)
- No payment required

### TikTok API
- Not publicly accessible in 2026
- Decision: manual data entry system

## Trending Score Formula

Weights (sum to 1.0):
- Follower growth: 40%
- YouTube views (3-day): 40%
- TikTok views (3-day): 20%

### Normalization
Each metric normalized to 0-100 scale:
- Growth: map -5% to +5% range to 0-100 (so -5% = 0, 0% = 50, +5% = 100)
- YouTube: (views / max_views) * 100
- TikTok: (views / max_views) * 100

Weighted score = (normGrowth * 0.40) + (normYouTube * 0.40) + (normTikTok * 0.20)

## Architecture

### 6-Hour Caching with Supabase
- Database: Supabase PostgreSQL (free tier: 500 MB, 2 GB bandwidth)
- Tables: politicians (baseline), politician_snapshots (4x daily), politician_cache (fast reads)
- Page loads in <500ms from cache
- Cron job refreshes every 6 hours (0 */6 * * *)

### Data Flow
1. User visits -> read politician_cache table -> return cached data (<500ms)
2. Cron runs every 6h -> fetch Twitter/YouTube/TikTok -> calculate growth rates -> calculate trending scores -> update snapshots + cache

### Error Handling Strategy
- Primary: call external API
- Fallback 1: most recent snapshot (<24 hours)
- Fallback 2: older snapshot with warning
- Fallback 3: exclude from trending calculations

User-facing freshness indicators:
- Green: all data current (<12 hours)
- Yellow: some data stale (12-24 hours)
- Red: data outdated (>24 hours)
- Gray: missing data source

## Political Data

21 French presidential candidates tracked with party affiliations (LREM, RN, LFI, LR, PS, EELV), positions, Twitter handles, and popularity metrics.

## Budget
- Minimum: $0/month (Supabase free, YouTube free, Vercel free)
- Full Twitter integration: $100-120/month (Twitter Basic API + optional Vercel Pro)

## Tech Stack
- Next.js 14 (App Router), TypeScript, Tailwind CSS
- Supabase PostgreSQL for persistence
- Vercel cron jobs for scheduled refresh
- YouTube Data API v3 for video views
