# Licensing — CoastalPulse Ghana Prototype Demo

This document records the **per-asset licensing position** for the
CoastalPulse Ghana prototype demo repository
(`festival233/coastalpulse-ghana-demo`). Status verified 2026-10-05.

Founder decision (implemented 2026-10-04): the core CoastalPulse Ghana
application is **not** placed under MIT, Apache, GPL, AGPL, or any broad
open-source license. Strategic flexibility and intellectual property are
preserved. See `LICENSE` at the repository root.

## Per-asset position

### (a) Core application / platform code — ALL RIGHTS RESERVED

Copyright (c) 2026 Street Pulse Foundation Inc. All rights reserved unless
specifically stated otherwise.

The core CoastalPulse Ghana application/platform code (including `index.html`
and any app logic in this repository) is copyrighted and not
released under any open-source license. `methods.html` is methods
documentation, not platform code, and is licensed under CC BY 4.0 (see
section (b)) — it is **not** covered by this all-rights-reserved section.
Public viewability of this repository
or demo does not authorize unrestricted reuse of the platform code. (GitHub's
own Terms of Service let users view and fork a public repository through
GitHub's platform features; that is not a copyright license granting the
right to reuse, redistribute, modify, or build on the code.)

Written permission from Street Pulse Foundation Inc. is required for reuse,
redistribution, modification, or derivative work based on this code.
Contact: info@streetpulsefoundation.org.

### (b) Our original methods, reports, and educational material — CC BY 4.0 APPLIED

**Applied 2026-10-05, per founder approval (2026-10-05):** CC BY 4.0
(Creative Commons Attribution 4.0 International) now covers the Foundation's
original methods, reports, diagrams, and educational content listed below.
This is the recommendation from `LICENSE-RECOMMENDATIONS.md` (item 1), now
approved and applied. Each file carries its own CC BY 4.0 notice with the
required attribution line:

> © 2026 Street Pulse Foundation Inc. Licensed under CC BY 4.0
> (https://creativecommons.org/licenses/by/4.0/).
> Attribution: Street Pulse Foundation / StreetPulse Blue — CoastalPulse Ghana.

**Files licensed in this repository:**

- `methods.html` — plain-language methods documentation page
- `earth-engine/METHODS.md` — shoreline-change analysis methods note
- `earth-engine/FLOOD-METHODS.md` — historical flood analysis methods note
- `earth-engine/PIPELINE.md` — satellite pipeline documentation

**Also licensed (in the Foundation workspace; sources of the above):**

- `prototype/coastalpulse-mvp/review-v0.2/architecture-diagram.svg` (+ `.png` companion, same license — binary, notice recorded here)
- `prototype/coastalpulse-mvp/review-v0.2/prototype-today-pilot-tomorrow.svg` (+ `.png` companion, same license — binary, notice recorded here)
- `grant-evidence/methods/METHODS.md`, `grant-evidence/methods/FLOOD-METHODS.md`, `grant-evidence/methods/EXPECTED.md`
- `grant-evidence/technical-summaries/METHODS.md`, `grant-evidence/technical-summaries/EXPECTED.md`
- `grant-evidence/data-sources/DATA-SOURCES.md`

CC BY 4.0 is a content license, not a software license: applying it to
methods/reports/educational material does not weaken the all-rights-reserved
position on the core application/platform code (section (a)). Third-party
content is never relicensed by us — see section (c).

### (c) Third-party data — ORIGINAL LICENSES INTACT

Third-party data and materials are under their own licenses. We relicense
nothing belonging to others:

- **ESA Copernicus Sentinel satellite data:** free, full and open under the
  applicable Copernicus data terms; subject to ESA attribution requirements.
  Attributed in `methods.html` and in the map layer attributions in
  `index.html` ("Contains modified Copernicus Sentinel data (ESA)").
- **OpenStreetMap data and tiles:** © OpenStreetMap contributors, under the
  Open Database License (ODbL). Attributed in `index.html` and `methods.html`.
- **EOX tile services** (Sentinel-2 cloudless, terrain): attributed in
  `index.html` per provider terms; terrain is elevation context only.
- **Keta Lagoon Ramsar boundary:** shown for reference.

### (d) Our derived datasets — LICENSE TBD PER DATASET

The derived datasets produced by this project are copyrighted works of
Street Pulse Foundation Inc., but **no distribution license has been applied
to them yet**:

- Shoreline vectors:
  `earth-engine/data/shoreline_early_median.geojson` (2017-03-07),
  `earth-engine/data/shoreline_late_median.geojson` (2025-01-07)
- Transects: `earth-engine/data/shoreline_change_transects.geojson`
- Flood extents: `earth-engine/data/flood_extent_2021-11.geojson` (2021-11-07
  Keta event, 31 indicative polygons at −3 dB; built 2026-10-05).
  **No distribution license applied yet.** Per founder decision (2026-10-05),
  derived datasets are licensed source-by-source under a per-dataset matrix,
  not a blanket license — recommendations are in
  `LICENSE-RECOMMENDATIONS.md` and await founder approval per dataset.
  Nothing is applied until approved.

Per-dataset licensing recommendations are in `LICENSE-RECOMMENDATIONS.md`
and await founder approval. Nothing is applied until approved. Note the
Copernicus source data is free and open under Copernicus terms (attribution
required); the derived datasets' license choice will take the source terms
into account.

### (e) Public API — TBD

No public API exists for this prototype yet. If an API is created, its
terms of use / license will be decided and documented at that time.

## What is applied vs. what is pending

- **Applied (decided, in force):** section (a) — core application/platform
  code, copyright retained, all rights reserved. See `LICENSE`. Section (b) —
  original methods/reports/diagrams/educational material, CC BY 4.0 applied
  2026-10-05 with founder approval (notices embedded in each file).
- **Pending founder approval:** sections (d), (e). Per-dataset licensing
  recommendations are documented in `LICENSE-RECOMMENDATIONS.md`. Nothing
  from that document has been applied except (b), as recorded here.
