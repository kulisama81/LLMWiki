---
title: Google Analytics (GA4) + Search Console Setup for Claude Agents
type: source-summary
sources: [apibstudyguide.md]
created: 2026-04-05
updated: 2026-04-05
---

# Google Analytics (GA4) + Search Console Setup for Claude Agents

How to set up automated GA4 and Google Search Console data pipelines so Claude agents can read real traffic and indexing data. Learned from [[apibstudyguide]] (April 2026).

## Why This Matters

Without analytics data, agents prioritize by guesswork. With it, they can:
- Focus content creation on high-search-volume topics
- Fix high-bounce-rate pages first
- Detect indexing gaps (pages Google hasn't crawled)
- Find high-impression, low-CTR queries to optimize for

## Architecture

```
GCP Service Account (.ga-credentials.json)
  ├── GA4 Data API → page views, bounce rates, sessions, geography, search terms
  └── Search Console API → indexing status, search queries, clicks, impressions, CTR
          ↓
  scripts/pull-analytics.mjs (cron daily 1 AM)
          ↓
  src/data/analytics-report.json (gitignored)
          ↓
  @autoresearch / @planner agents read on every session
```

## Step-by-Step Setup

### 1. Create a GCP Service Account

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a project (or use existing)
3. Go to IAM & Admin → Service Accounts → Create Service Account
4. Name it (e.g., `analytics-reader`)
5. No roles needed at the project level — permissions are granted in GA4 and GSC directly
6. Create a JSON key → download as `.ga-credentials.json` → place in project root
7. **Gitignore it**: add `.ga-credentials.json` to `.gitignore`

### 2. Enable APIs in GCP

Two APIs must be enabled in the GCP project:

- **Google Analytics Data API**: `https://console.developers.google.com/apis/api/analyticsdata.googleapis.com/overview?project=YOUR_PROJECT_ID`
- **Google Search Console API**: `https://console.developers.google.com/apis/api/searchconsole.googleapis.com/overview?project=YOUR_PROJECT_ID`

Click "Enable" on each page.

### 3. Grant GA4 Access

1. Go to [Google Analytics](https://analytics.google.com/)
2. Admin → Property → Property Access Management
3. Add the service account email (from `client_email` in `.ga-credentials.json`)
4. Role: **Viewer** (read-only is sufficient)
5. Note the **Property ID** (numeric, found in Admin → Property Settings) — needed in the script

### 4. Grant GSC Access

1. Go to [Google Search Console](https://search.google.com/search-console/)
2. Settings → Users and permissions → Add user
3. Email: the service account email
4. Permission: **Full** (Viewer doesn't allow API access to search analytics)

### 5. Determine GSC Property Type

This is the most common gotcha. GSC has two property types with different API identifiers:

| GSC shows | Property type | API `siteUrl` value |
|-----------|--------------|---------------------|
| `https://example.com` | URL prefix | `https://example.com` |
| `example.com` (no protocol) | Domain property | `sc-domain:example.com` |

**How to tell**: look at the top-left dropdown in GSC. If it shows just the domain without `https://`, it's a domain property and the API needs the `sc-domain:` prefix.

**This caused a debugging session in apibstudyguide** — the script used `https://apibstudyguide.com` but the GSC property was a domain property (`sc-domain:apibstudyguide.com`), resulting in a permissions error despite the service account having access.

### 6. Install Dependencies

```bash
npm install @google-analytics/data googleapis
```

- `@google-analytics/data` — GA4 Data API client (official Google package)
- `googleapis` — Google APIs client (used for Search Console, which has no standalone package)

### 7. The Pull Script

Key code patterns:

```javascript
// GA4 client
import { BetaAnalyticsDataClient } from '@google-analytics/data';
const ga4Client = new BetaAnalyticsDataClient({ credentials });

// GSC client
import { google } from 'googleapis';
const auth = new google.auth.GoogleAuth({
  credentials,
  scopes: ['https://www.googleapis.com/auth/webmasters.readonly'],
});
const searchconsole = google.searchconsole({ version: 'v1', auth });

// GSC search analytics query
const { data } = await searchconsole.searchanalytics.query({
  siteUrl: 'sc-domain:example.com',  // or 'https://example.com' for URL prefix
  requestBody: {
    startDate: '2026-03-05',
    endDate: '2026-04-02',
    dimensions: ['query'],
    rowLimit: 30,
  },
});
```

### 8. Cron Schedule

```bash
# Daily at 1 AM — pull GA4 + GSC data
0 1 * * * cd /path/to/project && node scripts/pull-analytics.mjs >> .claude/analytics-cron.log 2>&1
```

Weekly is too infrequent during high-traffic periods (e.g., exam season). Daily gives agents fresh data every morning.

### 9. Agent Configuration

Tell agents to read the report in their instructions:

```markdown
## Inputs
- Read `src/data/analytics-report.json` — GA4 traffic data + GSC search performance
- `searchConsole.searchQueries` — what people search to find the site
- `searchConsole.indexingStatus` — how many pages Google has indexed
```

## Report JSON Structure

```json
{
  "generated": "2026-04-05T...",
  "period": "last 28 days",
  "pageViews": [{ "page": "/", "views": 412, "bounceRate": "83.3%" }],
  "devices": [{ "device": "desktop", "sessions": 83 }],
  "topLandingPages": [...],
  "geography": [{ "country": "United States", "sessions": 45 }],
  "searchTerms": [...],
  "trafficSources": [{ "channel": "Direct", "sessions": 110 }],
  "searchConsole": {
    "searchQueries": [{ "query": "ib bio hl notes", "clicks": 5, "impressions": 120, "ctr": "4.2%", "position": 12.3 }],
    "topPages": [{ "page": "/ib-bio-hl/enzymes/", "clicks": 3, "impressions": 80 }],
    "indexingStatus": { "submitted": 79, "indexed": 9 }
  }
}
```

## Gotchas

1. **Domain vs URL prefix property** — `sc-domain:` prefix required for domain properties in GSC API. Most common setup error.
2. **GSC permission propagation** — After adding service account, API access can take up to 1 hour to work.
3. **GSC needs Full, not Viewer** — Viewer permission in GSC doesn't grant API access to search analytics data.
4. **"Discovered" ≠ "Indexed"** — GSC shows pages as "Discovered" when Google knows about them but hasn't added them to search results. Request indexing manually for priority pages.
5. **analytics-report.json should be gitignored** — contains real traffic data that changes daily; agents read it locally.
6. **GA4 API uses property ID (numeric)** — not the measurement ID (G-XXXXXXX). Find it in GA4 Admin → Property Settings.

## Related

- [[apibstudyguide]] — where this was implemented
- [[multi-agent-architecture]] — the agent pipeline that consumes this data
- [[compare-api-economics]] — API cost considerations (GA4 and GSC APIs are free)
