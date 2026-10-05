# CoastalPulse Ghana

**Public Prototype v0.2.1 — not field-deployed.**

CoastalPulse Ghana is a prototype initiative of StreetPulse Blue, a program of
Street Pulse Foundation, building a **repeatable coastal intelligence system**:
combining satellite, environmental and community data to understand changing
coastal risk across hazards — erosion, flooding, water quality and plastic
pollution.

CoastalPulse Ghana is a **prototype**: a working technical demo for Ghana's
Volta coastline and the Keta Lagoon area, not an operating program. This
repository exists to support grant applications and partner demonstrations —
it is not field-deployed, no community engagement has taken place yet, field
validation is still pending, and nothing in this prototype represents a
published research product.

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
- **Historical flood-extent analysis**
  (`earth-engine/data/flood_extent_2021-11.geojson`) — indicative co-event
  water extent versus normal-condition extent for the 2021-11-07 Keta event
  (Sentinel-1 SAR change detection; 31 polygons; 30.8–65.9 ha across the
  −2/−3/−4 dB threshold sweep). Framed as INDICATIVE, field validation
  pending. The first entry in a planned multi-event flood library — not a
  one-off map. See `methods.html` (flood section) and
  `earth-engine/FLOOD-METHODS.md`.
- **Reporting prototype** — the site includes example report content to
  demonstrate the format of a future field-reporting flow.
- **Dashboard concept** — `index.html` is a front-end concept showing how the
  map, layers, transects, and reporting would come together in a future
  operational dashboard.

## Data sources

- **Copernicus Sentinel-2 L2A** satellite imagery, accessed via the
  Element84 Earth Search STAC API and AWS Open Data
  (`https://earth-search.aws.element84.com/v1`).
- **Copernicus Sentinel-1 IW GRD** SAR imagery (flood analysis), accessed via
  the Element84 Earth Search STAC API and the AWS Open Data
  `sentinel-s1-l1c` bucket. WorldPop Ghana 2020, Microsoft Global ML Building
  Footprints, Copernicus DEM GLO-30 (exposure overlays only).
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
  flood_extent_2021-11.geojson — indicative flood extent, 2021-11-07 event (Sentinel-1)
```

To run locally for development, serve the repository root with any static
file server (e.g., `python3 -m http.server`) and open `index.html`. Fetch
requests to the GeoJSON files work over `http://localhost`; opening via
`file://` may fail to load the data files depending on the browser.

## License

**Copyright retained by Street Pulse Foundation Inc. All rights reserved
unless specifically stated otherwise.**

This repository contains the CoastalPulse Ghana prototype demo. The core
application / platform code in this repository is **not** released under MIT,
Apache, GPL, AGPL, or any other broad open-source license. Public viewability
of this repository and demo does **not** authorize unrestricted reuse of the
platform code — see `LICENSE` at the repository root.

Third-party data and materials included in or referenced by this repository
remain under their own licenses and terms (see "Data licenses and
attribution" below and `LICENSING.md`). Nothing here relicenses third-party
data.

See `LICENSING.md` for the per-asset licensing position. Per-asset licensing
recommendations that are still awaiting founder approval are documented in
`LICENSE-RECOMMENDATIONS.md` — they are recommendations only and have not
been applied.

## Data licenses and attribution

Verified 2026-10-04. These third-party sources remain under their own
licenses; nothing in this repository relicenses them.

- **ESA Copernicus Sentinel data:** Contains modified Copernicus Sentinel
  data (2017, 2025). Copernicus data is free, full and open under the
  applicable Copernicus data terms; use of this imagery is subject to the
  ESA attribution requirements. Attribution is shown in `methods.html` and
  on the map layer attributions in `index.html` (EOX Sentinel-2 cloudless
  layer, which itself notes "modified Copernicus Sentinel data, ESA").
- **OpenStreetMap:** baseline vectors and map tiles © OpenStreetMap
  contributors, available under the Open Database License (ODbL). Attributed
  in `index.html` tile layer controls and `methods.html`.
- **EOX tile services:** Sentinel-2 cloudless and terrain tiles are
  attributed in `index.html`; SRTM-derived terrain is elevation context only,
  not a flood-risk model.
- **Keta Lagoon Ramsar boundary:** shown for reference.

**Derived shoreline vectors and transects**
(`earth-engine/data/shoreline_early_median.geojson`,
`shoreline_late_median.geojson`, `shoreline_change_transects.geojson`) are
this project's analysis of the open data above, produced by StreetPulse Blue
(a program of Street Pulse Foundation), and carry the limitations described
under "Prototype limitations". Their license is **to be determined**
per-dataset — see `LICENSING.md` and the recommendations in
`LICENSE-RECOMMENDATIONS.md` (pending founder approval).
