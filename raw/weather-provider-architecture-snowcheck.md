# Weather Provider Architecture & UX Patterns

Combined from kulisama81/SnowCheck — README_PROVIDERS.md, Claude.md, docs/UX_IMPROVEMENTS.md
Date: 2026-01-20

## Overview

SnowCheck is a Next.js 14 snow forecast app using a provider-based architecture for weather data sources. Supports multiple weather APIs through a normalized provider interface.

## Provider Pattern Architecture

### Provider Interface
All providers implement:
- name: string
- getForecast(location, days): Promise<SnowForecast[]>
- getForecastByCoordinates(...): Promise<SnowForecast[]>
- isAvailable(): Promise<boolean>

### BaseWeatherProvider Abstract Class
Provides unit conversion helpers:
- celsiusToFahrenheit(celsius)
- metersToFeet(meters)
- cmToInches(cm)
- msToMph(ms) / kmhToMph(kmh)
- generateForecastId(provider, location, date)

### File Structure
```
lib/providers/
  base.ts           — BaseWeatherProvider abstract class
  open-meteo.ts     — Open-Meteo implementation (default, no API key needed)
  index.ts          — Provider registry
lib/config/
  providers.ts      — Provider configuration with priority
lib/api.ts          — Main API functions
```

### Adding a New Provider
1. Create class extending BaseWeatherProvider
2. Implement getForecast and getForecastByCoordinates
3. Register in provider index
4. Add config with priority (lower = higher priority)
5. Add API key to .env.local

### Current Provider: Open-Meteo
- Free, no API key required
- Features: snowfall, snow depth, freezing level, temperature, wind, humidity
- Limitation: 16-day forecast maximum

### Potential Providers
- Weather Unlocked: ski-specific with top/mid/bottom elevations
- World Weather Online: 7-day ski weather API
- Snow-Forecast.com: dedicated ski resort forecasts
- OnTheSnow: partner API with detailed mountain weather

## SnowForecast Data Model
Normalized forecast format that all providers must output:
- Snowfall, snow depth, freezing level
- Temperature (F), wind speed (mph), humidity
- Weather code (maps to icon files)
- Location metadata (name, elevation, coordinates)

## Weather Icon System
- Static icons: /public/imgs/static/ (non-animated)
- Animated icons: /public/imgs/animated/ (snow animations)
- Weather codes map to specific SVG files (code 0 = day.svg, code 73 = snowy-2.svg)
- Snow icons (codes 71-86) use animated versions

## UX Improvements Analysis (Based on OpenSnow)

### Current vs. Target
Current: single resort dropdown, all data stacked, embedded YouTube webcams.
Target (OpenSnow-style): multi-resort favorites, filter chips, compact cards, bar charts, thumbnail webcams.

### Proposed Features (Priority Order)

**High Priority:**
1. Horizontal filter chips — toggle between Weather/Snow/Forecast/Cams to reduce information overload
2. Compact weather card — large temp + weather icon + "feels like" + wind + multi-day mini forecast in one glance
3. Resort status badge — green "Open (85%)" with terrain percentage and conditions icon

**Medium Priority:**
4. Snow forecast bar chart — visual bars showing previous 6-10 days, previous 1-5 days, last 24 hours
5. Base vs. average comparison badge — percentage in red/green showing how season compares to historical average
6. Data reporting timestamp — "Reported at Sat Jan 10 8:48am PST"

**Lower Priority:**
7. Multi-resort dashboard — scrollable list of favorite resorts with summary cards for quick comparison
8. Webcam thumbnail gallery — horizontal scrollable thumbnails instead of heavy embedded YouTube videos

### Component Architecture Migration
From flat stacked sections to filter-based conditional rendering:
```
Header > FilterChips > ResortCard(s) > [WeatherView | SnowView | ForecastView | CamsView] > WSDOT
```

### Mobile-First Considerations
- 44x44px minimum touch targets
- Horizontal scroll with momentum and snap points
- Critical actions in lower half of screen (thumb reach)
- Progressive disclosure (summary first, details on tap)

## Snow Domain Knowledge
- Supported resorts: Washington (Snoqualmie, Stevens Pass, Crystal Mountain), Colorado, French Alps
- Key metrics: snowfall amount, snow depth, freezing level, precipitation confidence
- Season comparison: current snowpack vs. historical average
- Real-time data: webcams, WSDOT road conditions, mountain reports
