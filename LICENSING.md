# Licensing — CoastalPulse Ghana Prototype Demo

This document records the **per-asset licensing position** for the
CoastalPulse Ghana prototype demo repository
(`festival233/coastalpulse-ghana-demo`). Status verified 2026-10-04.

Founder decision (implemented 2026-10-04): the core CoastalPulse Ghana
application is **not** placed under MIT, Apache, GPL, AGPL, or any broad
open-source license. Strategic flexibility and intellectual property are
preserved. See `LICENSE` at the repository root.

## Per-asset position

### (a) Core application / platform code — ALL RIGHTS RESERVED

Copyright (c) 2026 Street Pulse Foundation Inc. All rights reserved unless
specifically stated otherwise.

The core CoastalPulse Ghana application/platform code (including `index.html`,
`methods.html`, and any app logic in this repository) is copyrighted and not
released under any open-source license. Public viewability of this repository
or demo does not authorize unrestricted reuse of the platform code. (GitHub's
own Terms of Service let users view and fork a public repository through
GitHub's platform features; that is not a copyright license granting the
right to reuse, redistribute, modify, or build on the code.)

Written permission from Street Pulse Foundation Inc. is required for reuse,
redistribution, modification, or derivative work based on this code.
Contact: info@streetpulsefoundation.org.

### (b) Our original methods, reports, and educational material — NO LICENSE APPLIED

The methods documentation, reports, and educational content authored by
Street Pulse Foundation / StreetPulse Blue (e.g., `methods.html`,
`earth-engine/METHODS.md`) currently carry **no applied license**.
A licensing recommendation is pending founder approval — see
`LICENSE-RECOMMENDATIONS.md`. Copyright remains with the Foundation.

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
- Flood extents: **not yet built** — will be evaluated when produced.

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
  code, copyright retained, all rights reserved. See `LICENSE`.
- **Pending founder approval:** sections (b), (d), (e). Recommendations are
  documented in `LICENSE-RECOMMENDATIONS.md`. Nothing from that document has
  been applied.
