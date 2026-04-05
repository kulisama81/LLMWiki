---
title: Trip Data Model
type: concept
sources: [trip-data-model-schema.md]
created: 2026-04-04
updated: 2026-04-04
---

# Trip Data Model

A formal YAML-frontmatter schema for representing travel itineraries in markdown, designed for the `withcode` app template.

## Three Entry Types

| Type | Purpose | Example |
|---|---|---|
| **stay** | One sleeping location | Campground, hotel, backcountry camp |
| **trip** | Parent itinerary with multiple stops | Multi-day road trips, international travel |
| **transport** | Flight/train/drive tied to a parent trip | Always a child of a `trip` via shared `tripId` |

## Key Design Decisions

- Standalone single-night stays use `stay`, not `trip` — avoid overengineering simple entries
- Only use `trip` when there are multiple stops or transport legs
- `tripId` links parent and children (e.g. `japan:2026:japan-summer-2026`)
- Only reserved nights need their own `stay` files; FCFS/dispersed nights live in the parent itinerary
- Day dates derived from `StartDate + (day - 1)` — no date field per day, avoiding sync issues

## Itinerary YAML Block

Every `trip` must end with a structured `itinerary:` block for map/table rendering:
- `stops[].coords`: [lat, lng] from trusted geocoder (never guessed)
- `stay.status`: confirmed, tbd, fcfs, dispersed
- `note`: one-line summary for previews

## Coordinate Rule

Coordinates must come from a trusted geocoder (e.g. Google Places API). Never guess coordinates — this is a hard rule.

## Body Conventions

Common sections: Notes, Reservation, Campground/Hotel, Activities, Directions, Amenities. Day headings use anchors for deep-linking: `### Day 5 - Sat Mar 28 {#day-5}`.

Reports link back via `reportFor: <tripId>`.

See also: [[source-japan-summer-2026]]
