# CoastalPulse Ghana

**Public Prototype v0.1 — not field-deployed.**

CoastalPulse Ghana is a prototype initiative of StreetPulse Blue, a program of
Street Pulse Foundation.

CoastalPulse Ghana is a **prototype**: a coastal data and community
conservation concept for Ghana's Volta coastline and the Keta Lagoon area. It
is not an operating program. This repository exists to support grant
applications and partner demonstrations — it is not field-deployed, no
community engagement has taken place yet, and nothing in this prototype
represents a published research product.

## Organization

- **Street Pulse Foundation** — the nonprofit organization (legal name:
  "Street Pulse Foundation Inc." where legally necessary)
- **StreetPulse Blue** — a program of Street Pulse Foundation
- **CoastalPulse Ghana** — a prototype initiative of StreetPulse Blue

"StreetPulse Blue" is the one-word program name; "Street Pulse Foundation"
(the organization) is always written as two words.

## What this prototype contains

- **Interactive map** (`index.html`) — a static, read-only prototype map of the
  Keta coastline and Keta Lagoon area with toggleable layers.
- **Shoreline-change analysis** (`earth-engine/data/shoreline_early_median.geojson`
  and `shoreline_late_median.geojson`) — satellite-derived median shoreline
  positions for two epochs, 2017-03-07 and 2025-01-07 (7.84 years apart).
- **Transects** (`earth-engine/data/shoreline_change_transects.geojson`) —
  per-transect shoreline-change rates computed from the two shoreline
  positions. Findings are summarized in the methods page (see below).
- **Reporting prototype** — the site includes example report content to
  demonstrate the format of a future field-reporting flow.
- **Dashboard concept** — `index.html` is a front-end concept showing how the
  map, layers, transects, and reporting would come together in a future
  operational dashboard.

## Data sources

- **Copernicus Sentinel-2 L2A** satellite imagery, accessed via the
  Element84 Earth Search STAC API and AWS Open Data
  (`https://earth-search.aws.element84.com/v1`).
- **OpenStreetMap** — baseline coastline and lagoon boundary vectors
  (`data/coastline_osm.json`, `data/lagoon_osm.json`).
- **Keta Lagoon Ramsar boundary** — shown for reference.

Derived shoreline-change vectors and transects are this project's own
analysis of the open data above (see attribution notes).

## Methods

The methods page shipped with the prototype describes how the shoreline
positions and transect rates were computed:

- **In this repository:** `methods.html` (open it in a browser alongside
  `index.html`).
- **Full methods record:** `earth-engine/METHODS.md` in the project workspace
  (not all details are duplicated in `methods.html`).

## Prototype limitations

Read these before citing anything from this repository:

- **No field validation.** All rates are satellite-derived; ground-truth GPS
  field validation is still pending.
- **Noise floor.** Change rates carry an estimated noise floor of about
  **±1 m/yr**; small rates may reflect measurement noise, not real change.
- **Magnitudes vs. published hotspot rates.** Computed erosion magnitudes sit
  **below published hotspot rates** for the area (8–17 m/yr). Direction and
  spatial pattern corroborate the documented story; magnitudes are not yet
  validated.
- **Possible residual tidal bias.** The two-epoch median compositing reduces
  but does not fully remove tidal influence.
- **Demonstration data is labeled as such.** Example reports and dashboard
  content are clearly marked as demonstration, not real observations.
- **No community engagement yet.** Nothing in this prototype reflects input
  from, or engagement with, local communities.

## Architecture and development

This is a **static site**. No build step, no backend, no dependencies to
install.

```
index.html                      — prototype map (open directly in a browser)
methods.html                    — methods page
data/coastline_osm.json         — OSM baseline coastline
data/lagoon_osm.json            — OSM lagoon boundary
earth-engine/data/
  shoreline_early_median.geojson — 2017-03-07 median shoreline
  shoreline_late_median.geojson  — 2025-01-07 median shoreline
  shoreline_change_transects.geojson — per-transect change rates
```

To run locally for development, serve the repository root with any static
file server (e.g., `python3 -m http.server`) and open `index.html`. Fetch
requests to the GeoJSON files work over `http://localhost`; opening via
`file://` may fail to load the data files depending on the browser.

## License

**License status: to be determined — founder decision pending.**

No license has been selected for this repository yet. This project is
**not** claimed to be open source until a license is chosen and applied.

> **FOUNDER DECISION NEEDED:** select a license for the CoastalPulse Ghana
> prototype repository.

## Data licenses and attribution

- **ESA Copernicus Sentinel data:** Contains modified Copernicus Sentinel
  data (2017, 2025). Copernicus data is free, full and open under the
  applicable Copernicus data terms; use of this imagery is subject to the
  ESA attribution requirements.
- **OpenStreetMap:** baseline vectors © OpenStreetMap contributors,
  available under the Open Database License (ODbL).
- **Derived shoreline vectors and transects** are this project's analysis of
  the open data above, produced by StreetPulse Blue (a program of Street
  Pulse Foundation), and carry the limitations described under "Prototype
  limitations".
