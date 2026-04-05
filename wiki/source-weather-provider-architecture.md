---
title: "Source Summary: Weather Provider Architecture"
type: source-summary
sources: [weather-provider-architecture-snowcheck.md]
created: 2026-04-04
updated: 2026-04-04
---

# Weather Provider Architecture & UX Patterns

Summary of SnowCheck's provider-based weather data architecture and OpenSnow-inspired UX analysis.

## Key Takeaway

The strategy/provider pattern cleanly abstracts weather data sources behind a normalized interface, with a BaseWeatherProvider providing unit conversion helpers. The UX analysis reveals that weather apps suffer from information overload — horizontal filter chips and progressive disclosure (summary first, details on tap) are the proven OpenSnow solution.

## Provider Pattern

All providers implement `getForecast()` and normalize data to a standard `SnowForecast` type. BaseWeatherProvider provides: celsiusToFahrenheit, metersToFeet, cmToInches, msToMph. New providers: create class, register, add config with priority, add API key.

Currently only Open-Meteo (free, no API key, 16-day max). Potential additions: Weather Unlocked (ski-specific), Snow-Forecast.com, OnTheSnow.

## UX Insights (OpenSnow Analysis)

**High-impact improvements:**
1. Filter chips (Weather/Snow/Forecast/Cams) to reduce cognitive load
2. Compact weather card (temp + icon + feels-like + wind in one glance)
3. Resort status badge ("Open 85%" in green)

**The multi-resort problem:** Users need to compare resorts but can only view one at a time. OpenSnow solves this with a scrollable favorites list showing summary cards.

**Webcam optimization:** Embedded YouTube videos are heavy and slow. Horizontal scrollable thumbnails load faster and show more at once.

## Snow Domain Knowledge

Key metrics: snowfall, snow depth, freezing level, precipitation confidence. Supported resorts across Washington, Colorado, French Alps. Season comparison shows current snowpack vs. historical average.

See also: [[provider-pattern]], [[source-claude-usage-patterns]]
