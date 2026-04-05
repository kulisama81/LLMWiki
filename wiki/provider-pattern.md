---
title: Provider Pattern
type: concept
sources: [weather-provider-architecture-snowcheck.md, college-scorecard-api-favcollege.md]
created: 2026-04-04
updated: 2026-04-04
---

# Provider Pattern (Strategy Pattern)

The provider/strategy pattern appears in two projects as a way to abstract external data sources behind a normalized interface.

## SnowCheck — Weather Providers

All weather providers implement `getForecast()` and output a standard `SnowForecast` type. A `BaseWeatherProvider` abstract class provides unit conversion helpers (celsius to fahrenheit, cm to inches, etc.). Providers are registered with priority for fallback ordering. See [[source-weather-provider-architecture]].

Currently one provider (Open-Meteo, free), but the architecture supports Weather Unlocked, Snow-Forecast.com, OnTheSnow without changing consumer code.

## FavCollege — Data Source Abstraction

A `USE_MOCK_DATA` toggle switches between mock data and Supabase (backed by College Scorecard API). The mapper layer normalizes API fields to application types, auto-generates tags from Carnegie classifications, and maps states to regions. See [[source-college-scorecard-api]].

## Why This Pattern Works

- **Swap sources without changing consumers** — key when APIs change pricing or availability (see [[source-political-trending-score]] where Twitter went write-only)
- **Normalize heterogeneous data** — different APIs return different units, formats, field names
- **Fallback chains** — if primary provider fails, try next by priority
- **Testability** — mock provider for testing, real provider for production

## Implementation Checklist

1. Define interface (getForecast, isAvailable, etc.)
2. Create base class with shared helpers (unit conversion, ID generation)
3. Implement concrete providers
4. Register in provider registry with priority
5. Add configuration with API key management

See also: [[source-weather-provider-architecture]], [[source-college-scorecard-api]]
