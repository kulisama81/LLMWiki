# Japan Sum 2026

Collaborative trip content consumed by the `withcode` app template.

## Repo structure

```text
.
├── <year>/
│   └── <Name>.md          # Trip, stay, or transport entry
├── reports/
│   └── <year>/
│       └── <slug>.md      # Post-trip writeups
└── attachments/           # Images and screenshots
```

- One markdown file per stay, trip, or transport leg under `<year>/`.
- Only markdown files directly under `<year>/*.md` are loaded by the app.
- File names should be the place or trip name (e.g. `Salmon La Sac.md`, `Utah Trip.md`).

## Entry types

| `entryKind` | Purpose | When to use |
|---|---|---|
| `stay` | One place you are sleeping | Campground, hotel, backcountry camp |
| `trip` | Parent itinerary spanning multiple stops or transport legs | Multi-day road trips, international travel; must include an `itinerary:` YAML block |
| `transport` | A flight, bus, train, ferry, or drive tied to a parent trip | Always a child of a `trip` via shared `tripId` |

- Standalone overnight trips (e.g. a single campground weekend) use `stay`, not `trip`.
- Backpacking overnights use `stay` with `Type: Backpacking`.
- Only use `trip` when there are multiple stops or transport legs that belong together.
- Every `trip` entry must end with an `itinerary:` YAML block. This is required by the app renderer.

## Core frontmatter

| Field | Used on | Values / format | Notes |
|---|---|---|---|
| `entryKind` | all entries | `trip`, `stay`, `transport` | Required |
| `stayType` | `stay` only | `campground`, `hotel` | Required for stays |
| `transportType` | `transport` only | `flight`, `bus`, `train`, `ferry`, `drive` | Required for transport |
| `tripId` | parent trips and their children | `<namespace>:<year>:<slug>` | Shared by all legs of one trip |
| `propertyName` | stays | free text | Campground, hotel, or camp name |
| `unitLabel` | stays | free text | Site number, room, loop (e.g. `Site 32`) |
| `StartLocation` | transport only | free text | Origin (e.g. `Seattle`) |
| `EndLocation` | transport only | free text | Destination (e.g. `London Heathrow`) |
| `Confirmed` | all entries | `Yes`, `No` | Whether the booking or plan is firm |
| `StartDate` | all entries | `YYYY-MM-DD` or `YYYY-MM-DD HH:MM` | Date-only for trips and stays; local datetime for transport |
| `EndDate` | all entries | `YYYY-MM-DD` or `YYYY-MM-DD HH:MM` | Date-only for trips and stays; local datetime for transport |
| `location` | trips and stays | free text | Region, city, or area |
| `Type` | trips and stays | `RV`, `Backpacking`, `Travel` | Trip style |
| `tags` | all entries | YAML list | Freeform tags (e.g. `rv`, `backpacking`, `travel`) |

Transport entries use local datetime format: `YYYY-MM-DD HH:MM`.

## Templates

### Campground stay

```yaml
---
entryKind: stay
stayType: campground
propertyName: Salmon La Sac Campground
tags:
  - rv
  - cle-elum
StartDate: 2026-07-12
EndDate: 2026-07-15
location: Cle Elum
Type: RV
Confirmed: Yes
unitLabel: Site 32
---

## Notes

Quick summary of why this stop matters.

## Reservation

- Confirmation link or number
- Check-in / check-out
- Cost

## Campground

- Amenities
- Distance from home
- Useful links

## Activities

- Nearby hikes or paddling
```

### Hotel stay

```yaml
---
entryKind: stay
stayType: hotel
tripId: example:2026:uk-trip
propertyName: Hyatt Place London City East
unitLabel: Standard Room, 1 King Bed
StartDate: 2026-05-01
EndDate: 2026-05-02
location: London
Confirmed: Yes
tags:
  - travel
---
```

### Backpacking overnight

Use `stay` with `Type: Backpacking` -- not `trip`.

```yaml
---
entryKind: stay
stayType: campground
propertyName: Waptus Lake
tags:
  - backpacking
  - alpine-lakes
StartDate: 2026-06-20
EndDate: 2026-06-22
location: Alpine Lakes Wilderness
Type: Backpacking
Confirmed: No
---
```

### Parent trip

Use a parent `trip` when you have multiple stays or transport legs that belong together.

````markdown
---
entryKind: trip
tripId: example:2026:uk-trip
tags:
  - international
  - uk
StartDate: 2026-04-29
EndDate: 2026-05-06
location: UK
Type: Travel
Confirmed: Yes
---

## Itinerary

| Date | Day | Plan |
|---|---|---|
| Apr 29 | Travel | Fly SEA -> LHR |
| Apr 30 | Day 1 | Arrive London |
| May 1 | Day 2 | Hyatt free night |

## Itinerary Data

```yaml
itinerary:
  - day: 1
    stops:
      - name: Seattle
        coords: [47.6324, -122.3569]
      - name: London Heathrow
        coords: [51.4700, -0.4543]
    stay:
      name: London
      coords: [51.5072, -0.1276]
      type: hotel
      status: confirmed
    note: Overnight flight to London, arrive next day
  - day: 2
    stops:
      - name: London
        coords: [51.5072, -0.1276]
    stay:
      name: Hyatt Place London City East
      coords: [51.5181, -0.0604]
      type: hotel
      status: confirmed
    note: Arrive in London and check into hotel
```
````

### Detailed parent trip example

All parent trips require the YAML itinerary block. For longer or more complex trips, the parent `trip` body can also include:

- Narrative day-by-day sections with `{#day-N}` anchors for deep-linking
- A fenced YAML itinerary block for structured rendering (map, summary table)

Only reserved child nights need their own `stay` files. FCFS or dispersed nights live only inside the parent itinerary.

````markdown
---
entryKind: trip
tripId: example:2026:utah-trip
tags:
  - rv
  - utah
StartDate: 2026-03-24
EndDate: 2026-04-13
location: Southeast Utah
Type: RV
Confirmed: Yes
---

# Eastern Utah Loop

Clockwise loop through southeast Utah.

# Day-by-Day Itinerary

## Transit In

### Day 1 -- Tue Mar 24 {#day-1}
Seattle -> Deschutes River SRA (~4 hrs)
- Columbia Gorge route, avoids Cascades passes
- Overnight at Deschutes River State Recreation Area

### Day 2 -- Wed Mar 25 {#day-2}
Deschutes -> Thousand Springs State Park (~5-6 hrs)
- I-84 east through Pendleton, cross Blue Mountains, into Idaho
- Reserved -- $54.59

## Stop 1: San Rafael Swell (2 nights)

### Day 3 -- Thu Mar 26 {#day-3}
Thousand Springs -> San Rafael Bridge Campground (~5-6 hrs)
- South through SLC, I-70 east to northern Swell

...

## Notes

- Resupply options: Moab, Blanding, Mexican Hat
- Dog considerations: all hikes under 5mi

## Itinerary Data

```yaml
itinerary:
  - day: 1
    stops:
      - name: Seattle
        coords: [47.6324, -122.3569]
      - name: Deschutes River SRA
        coords: [45.6334, -120.9088]
    stay:
      name: Deschutes River SRA
      coords: [45.6334, -120.9088]
      type: campground
      status: fcfs
    note: Columbia Gorge route, avoids Cascades passes
  - day: 2
    stops:
      - name: Deschutes River SRA
        coords: [45.6334, -120.9088]
      - name: Thousand Springs State Park
        coords: [42.8331, -114.8918]
    stay:
      name: Thousand Springs State Park
      coords: [42.8331, -114.8918]
      type: campground
      status: confirmed
    note: I-84 east, cross Blue Mountains into Idaho
```
````

### Child stay (inside a parent trip)

```yaml
---
entryKind: stay
stayType: campground
tripId: example:2026:utah-trip
propertyName: Utahraptor State Park
tags:
  - rv
  - utah
StartDate: 2026-03-28
EndDate: 2026-03-30
location: Moab
Type: RV
Confirmed: Yes
unitLabel: Site 118 / P11
---
```

### Child transport (inside a parent trip)

```yaml
---
entryKind: transport
transportType: flight
tripId: example:2026:uk-trip
StartLocation: Seattle
EndLocation: London Heathrow
StartDate: 2026-04-29 18:20
EndDate: 2026-04-30 12:10
Confirmed: Yes
tags:
  - travel
---

## Details

- Airline / operator
- Booking reference
- Seat
- Notes
```

## Body format

After the frontmatter, the body is ordinary markdown with headings, bullet lists, links, tables, and images.

The rendering-sensitive parts are the frontmatter schema, `tripId` relationships, day-anchor pattern, and required itinerary YAML shape. Prose sections are flexible.

### Common sections

- `## Notes`
- `## Reservation`
- `## Campground` or `## Hotel`
- `## Activities`
- `## Directions`
- `## Amenities`

These are content conventions, not parser requirements.

### Day headings with anchors

For itinerary-style parent trips, use anchored headings so the app can deep-link into the day plan.

```markdown
### Day 5 - Sat Mar 28 {#day-5}
Southern Swell -> Utahraptor State Park
- Goblin Valley State Park
- Little Wild Horse Canyon
- Check into Utahraptor State Park
- Reserved - $50/night
```

Parent trips keep reservation detail brief. The child `stay` file is where full booking details belong.

### Images

Use standard markdown image syntax pointing at the top-level `attachments/` folder. In the `withcode` app template these paths are treated as repo-root-relative:

```markdown
![Site view](attachments/swift%20creek%201.jpg)
```

## Itinerary data block

Every `trip` entry must end with a fenced `itinerary:` YAML block used by the app for map and summary table rendering. This is not limited to road trips.

Each day's date is derived from the parent trip `StartDate` plus `day - 1`. Do not include a `date` field per day.

```yaml
itinerary:
  - day: 1
    stops:
      - name: Seattle
        coords: [47.6324, -122.3569]
      - name: Deschutes River SRA
        coords: [45.6334, -120.9088]
    stay:
      name: Deschutes River SRA
      coords: [45.6334, -120.9088]
      type: campground
      status: fcfs
    note: Columbia Gorge route, avoids Cascades passes
```

### Itinerary fields

| Field | Meaning |
|---|---|
| `day` | 1-based day number within the trip |
| `stops` | Places visited that day (ordered) |
| `stops[].name` | Display name for the stop |
| `stops[].coords` | `[lat, lng]` -- sourced from a trusted geocoder, never guessed |
| `stay.name` | Overnight location |
| `stay.coords` | `[lat, lng]` for the overnight location |
| `stay.type` | `campground`, `hotel`, `dispersed`, `transit` |
| `stay.status` | `confirmed`, `tbd`, `fcfs`, `dispersed` |
| `note` | One-line summary for tables or previews |

Coordinates must be sourced from a trusted dataset or geocoder (e.g. Google Places API). Never guess coordinates.

## Reports

Post-trip writeups live under `reports/<year>/`.

```yaml
---
tags:
  - outdoors
  - outdoors/tripreport
reportFor: example:2026:swift-creek
---

## Trip Report

Short summary.

## Conditions

- Weather
- Campsite quality
- Lessons learned
```

Link reports to their trip using `reportFor: <tripId>`.

## Linking rules

- Standalone stays do not need `tripId`.
- Parent trips use a stable `tripId` such as `example:2026:utah-trip`.
- Child stays and transport legs reuse the parent's `tripId`.
- Only reserved nights need their own `stay` files; FCFS and dispersed nights can remain in the parent itinerary.
- Reports link to trips via `reportFor`.

## Getting started

1. Create `<year>/`, `reports/<year>/`, and `attachments/`.
2. Put one markdown file per stay, trip, or transport leg into `<year>/`.
3. Use `stay` for single sleeping locations, `trip` for multi-stop parents, and `transport` for child travel legs.
4. Give every multi-leg trip a stable `tripId` like `example:2026:utah-trip`.
5. Use date-only `StartDate` and `EndDate` for trips and stays. Use local datetime strings for transport.
6. Keep the body in normal markdown with clear sections and image references into `attachments/`.
7. Add a report under `reports/<year>/` after the trip.
