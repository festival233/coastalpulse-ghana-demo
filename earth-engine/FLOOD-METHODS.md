> **License — CC BY 4.0**
>
> © 2026 Street Pulse Foundation Inc. Licensed under CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/).
> Attribution: Street Pulse Foundation / StreetPulse Blue — CoastalPulse Ghana.

# FLOOD-METHODS.md — Historical flood analysis: 2021-11-07 Keta event

CoastalPulse Ghana · earth-engine workstream · produced 2026-10-05
Pipeline: `04_flood_analysis.py` · optical cross-check: `05_optical_xcheck.py` ·
exposure: `06_exposure.py`

## 0. Framing (read first)

The product is **indicative co-event water extent versus normal-condition extent**.
It is **not** "surge-only flood extent": on this ~1 m micro-tidal coast, storm
surge, high tide, and normal waterline variability are unresolvable from this
data, and no attempt is made to separate them. Flooded area is reported
**as a range only** (sensitivity sweep, no single number). **No depth estimates,
ever.** Nothing in this analysis is framed or suitable for compensation,
resettlement, insurance, or engineering use. No confusion matrix is possible:
there is no ground truth for this event.

## 1. Event and gating

- Documented event: storm surge affecting Keta-area coastal communities,
  Volta Region, Ghana, on **2021-11-07**.
- STAC gating (Element84 Earth Search, `sentinel-1-grd`, AOI lon 0.93–1.07,
  lat 5.86–6.00): **PASSED 2026-10-05**.
  - Co-event: `S1B_IW_GRDH_1SDV_20211108T180927_20211108T180952_029500_038554`
    (2021-11-08T18:09:40Z, Sentinel-1B, relative orbit 74) — **+1 day** after
    the event, within the ±3 day gate.
  - Reference: `S1B_IW_GRDH_1SDV_20211015T180928_20211015T180953_029150_037A90`
    (2021-10-15T18:09:40Z, Sentinel-1B, relative orbit 74) — 24 days prior,
    **same satellite, same relative orbit, same ~18:09 UTC (evening
    descending) acquisition geometry**.
  - Quiet-pair second scene: `S1A_IW_GRDH_1SDV_20211021T181012_20211021T181037_040221_04C3B7`
    (2021-10-21T18:10:25Z, Sentinel-1A, relative orbit 74) — 6 days after the
    reference, used for the empirical no-flood change floor.
- Both Sentinel-1A and 1B were operational in Nov 2021 (S1B anomaly was
  2021-12-23), giving the ~6-day combined revisit that makes this triple
  available. All three scenes share relative orbit 74 → near-identical imaging
  geometry (measured co-registration shift: 0.00 px, §4).

## 2. Preprocessing (per scene, VV and VH)

Source: Sentinel-1 IW GRDH COGs (`sentinel-s1-l1c` AWS Open Data bucket,
`measurement/iw-vv.tiff`, `iw-vh.tiff`; uint16 amplitude DN, tiled 1024 px).
The COGs carry **no CRS/geotransform**, so georeferencing is built from the
product annotation XMLs (`annotation/iw-vv.xml` geolocation grid, 10×21
points) — see §7 for the error budget.

1. **Thermal-noise subtraction + radiometric calibration to sigma0** using the
   product LUTs (`annotation/calibration/calibration-iw-*.xml` sigmaNought,
   `noise-iw-*.xml` range noise): `sigma0 = (DN² − η) / A²`, per-pixel via
   LUT interpolation. Azimuth-noise vectors (≈1.0 multiplicative) omitted and
   documented. Calibration sanity: typical land DN → VV ≈ −10 dB, VH ≈ −21 dB.
2. **Speckle filter**: simple Lee filter (5×5 window) applied in the linear
   sigma0 domain, then converted to dB.
3. **Terrain correction: not applied.** The AOI is a flat coastal plain/lagoon
   (Copernicus GLO-30: 97.5% of the grid ≤ 12 m); geometric distortion from
   relief is negligible at 10 m pixels, and all scenes share one orbit so
   relative geometry is preserved. Stated as a limitation, not a correction.
4. **Warp to common grid** via 9×9 ground-control points from a 2nd-order
   polynomial fit of each scene's geolocation grid, reprojected (bilinear) to
   EPSG:32631, 10 m, 1637×1635 px covering the AOI + 400 m buffer.
5. **Reference**: pixel-wise median of the two pre-event scenes' dB stacks
   (2021-10-15 S1B + 2021-10-21 S1A) — halves reference speckle.

## 3. Water mask ("normal-condition extent")

The Keta Lagoon is adjacent and enormous; masking normally-wet area is the
single most consequential choice. Water = pre-event VV < **−15 dB**.
Mask = **union of water in the two pre-event scenes** (wet in EITHER October
scene ⇒ normally wet at least sometimes, including the intertidal zone).
A pixel is a flood candidate only if dry in BOTH pre-event scenes.

Why union, not intersection ("persistent water"): the intersection variant
left a 9–46 ha false-positive floor on the quiet pair from normal tidal
waterline movement; the union variant drives the empirical no-flood floor to
≈0 ha (−3/−4 dB) while the event signal remains 31–66 ha. Both variants are
reported in `flood_sensitivity_table.json`.

Water-threshold robustness (union mask): varying the threshold −14/−15/−16 dB
moves the −3 dB area 33.0 → 44.3 → 55.4 ha and the −4 dB area
22.6 → 30.8 → 36.7 ha — the signal persists at <2× variation.

## 4. Change detection and post-processing

- `ΔVV = co-event − reference` (dB), `ΔVH` likewise. Flood candidate:
  land pixel (union water mask) with `ΔVV ≤ threshold`.
- Sensitivity sweep, threshold NOT fixed in advance: **−2 / −3 / −4 dB**.
- Copernicus DEM GLO-30 coarse lowland mask (≤ 12 m; DSM with meter-scale
  error — coarse mask only, never depth).
- Morphological opening (3×3), minimum connected component 50 px (0.5 ha),
  vectorized to polygons.
- VH agreement: 88–89% of VV-flagged flood pixels also satisfy `ΔVH ≤
  threshold` (independent-polarization QA).

### Results (primary, union water mask)

| threshold | indicative co-event water extent | polygons | quiet-pair (no-flood) floor | VH agreement |
|---|---|---|---|---|
| −2 dB | 65.9 ha | 43 | 2.3 ha | 88% |
| −3 dB | 44.3 ha | 31 | 0.0 ha | 89% |
| −4 dB | 30.8 ha | 22 | 0.0 ha | 88% |

**Indicative co-event water extent vs normal-condition extent: 30.8–65.9 ha
(0.31–0.66 km²), reported as a range.** The quiet-pair floor (same pipeline
run on 2021-10-21 minus 2021-10-15, no event between) is 0–2.3 ha: the event
signal stands 19–29× above normal change variability at −3/−4 dB.

### Spatial character

Flood polygons ring the **Keta Lagoon margins** (median 32 m from the October
union-water edge; median 2.3 km from the v0.1 2024–2025 median *ocean*
waterline — i.e., this is lagoon-margin expansion, not open-beach waterline
shift). Largest polygon 12.07 ha at −3 dB (verified against shipped GeoJSON 2026-10-05; an earlier draft figure of 13.9 ha was incorrect), lagoon south shore near
Kedzi. Cross-check against the v0.1 long-term median waterline: polygons lie
landward of it on normally-dry land per the union mask definition.

## 5. Optical cross-check (opportunistic)

Clouds did not deny it. Nearest clear post-event scene: **2021-11-14**
(`S2B_31NBG_20211114_0_L2A`, cloud 10.0%) — 7 days after the event, so
floodwater may have partially receded. Baseline: pixel-wise median NDWI of
three October 2021 clear scenes (2021-10-05/15/25, cloud<30, SCL cloud/shadow
masked). Anomaly = NDWI_post − NDWI_baseline, evaluated on the flood grid
inside the morphologically-cleaned −3 dB polygons:

| zone | n px | mean NDWI anomaly | frac > 0.05 |
|---|---|---|---|
| inside indicative flood polygons | 4,255 | **+0.373** | 0.92 |
| dry land > 500 m from polygons | 145,286 | −0.027 | 0.07 |
| lagoon margin ≤ 500 m, not flooded | 63,216 | +0.035 | 0.24 |

The inside anomaly is an order of magnitude above the dry-land background and
well above the unflooded lagoon margin — consistent with standing water / very
wet ground 7 days post-event. This is opportunistic supporting evidence, not a
validation: there is no ground truth, and receded water weakens the test.

## 6. Exposure overlays (indicative only)

Labels are exact and non-negotiable (see §0).

**WorldPop Ghana 2020 100 m constrained (maxar_v1), CC-BY-4.0**
(`gha_ppp_2020_constrained.tif` via HDX; WorldPop's own download portal was
unreachable on 2026-10-05). Zonal sum on the **native 100 m grid**
(mass-conserving; a 10 m bilinear reproject was caught inflating totals ~100×
and discarded):

| threshold | indicative population within extent | flood area |
|---|---|---|
| −2 dB | 5 | 65.9 ha |
| −3 dB | 4 | 44.3 ha |
| −4 dB | 4 | 30.8 ha |

(WorldPop total on AOI dry land: 31,395 — sanity check on the grid.)
The extent is almost entirely unpopulated lagoon margin; this is a finding,
not a failure — the water expansion did not reach settled pixels detectable
at 100 m.

**Microsoft Global ML Building Footprints** (2026-08-13 refresh,
CDLA-Permissive-2.0; quadkey 122220223 covers the whole AOI; 177,254 raw
features, 13,321 in the AOI bbox):

| threshold | buildings intersecting indicative flood extent | within 100 m |
|---|---|---|
| −2 dB | 0 | 8 |
| −3 dB | 0 | 8 |
| −4 dB | 0 | 0 |

Zero buildings intersect the indicative extent at any threshold; 8 stand
within 100 m at −2/−3 dB. (Google Open Buildings v3 was the documented
alternative; MS footprints were reachable and current, so they were used.)

**OSM communities** (11 documented): the Overpass API was unreachable from
this environment (egress proxy 406/timeouts, 2026-10-05), so verification used
the Nominatim search API against the same OSM database. Only
`osm_type=node` + `class=place` hits count as verified:

| community | OSM place node? | status vs −3 dB indicative extent |
|---|---|---|
| Kedzikope | NO (distinct node not found; nearby "Kedzi" hamlet exists — not claimed) | NOT VERIFIED — no claim |
| Abutiakope | yes, village | outside (0.8 km) |
| Dzelukope | yes, village | outside (0.6 km) |
| Dzita | yes, village | verified but outside analysis AOI (21.2 km) — no assessment |
| Anloga | yes, town | verified but outside analysis AOI (9.7 km) — no assessment |
| Agbledomi | NO | NOT VERIFIED — no claim |
| Atiteti | yes, village | verified but outside analysis AOI (25.6 km) — no assessment |
| Agokedzi | NO | NOT VERIFIED — no claim |
| Serakope | NO | NOT VERIFIED — no claim |
| Fuveme | yes, island | verified but outside analysis AOI (28.5 km) — no assessment |
| Keta Central | NO | NOT VERIFIED — no claim |

The two in-AOI verified villages (Abutiakope, Dzelukope) sit 0.6–0.8 km from
the nearest indicative flood polygon: the mapped water expansion did not reach
their mapped centers. Five names could not be verified as distinct OSM place
nodes; per the verification rule, no claim is made about them.

## 7. Error budget and limitations

1. **Absolute geolocation**: GCP warp from the product geolocation grid;
   polynomial fit RMS 38 m (lon) / 9 m (lat) for S1B scenes, 86 m / 18 m for
   the S1A scene. Flood polygons carry ≈ ±40 m absolute placement uncertainty.
   Relative co-registration between scenes: 0.00 px (same relative orbit 74).
2. **Tide vs surge unresolvable**: see §0. The Nov 8 acquisition's tidal stage
   is not separated from surge-driven water.
3. **Threshold sensitivity**: area varies ~2× across −2…−4 dB; reported as a
   range, never a point estimate.
4. **Wind-roughened floodwater**: the method detects backscatter *drops*
   (smooth standing water). Wind-roughened water can appear bright and be
   missed → extent is a lower-bound-style indicator, not a census.
5. **No depth**: SAR change detection gives extent only.
6. **No ground truth**: no confusion matrix, no accuracy percentages. The
   quiet-pair floor is an empirical *change* floor, not a validation.
7. **DEM**: Copernicus GLO-30 is a coarse DSM (meter-scale error, includes
   vegetation/structures); used only as a ≤12 m lowland mask.
8. **Reference window**: 24 days pre-event; land-cover change unrelated to the
   event (e.g., rainfall, farming) between Oct 15 and Nov 8 is not modeled,
   but the quiet-pair floor bounds its magnitude at 0–2.3 ha.
9. **Acquisition timing**: the co-event scene is 2021-11-08 18:09 UTC, ~1 day
   after the documented 2021-11-07 surge. Water may have partially receded by
   acquisition; the mapped extent is an indicator of co-event water presence,
   not a maximum-inundation census.
10. **S1A/S1B mixing**: the reference median mixes S1B (Oct 15) and S1A
    (Oct 21). The quiet-pair difference is spatially patchy (tidal/moisture),
    not a uniform sensor bias; residual inter-sensor bias, if any, is absorbed
    in the reported range.

## 8. Files

- `data/flood_extent_2021-11.geojson` — 31 indicative polygons at −3 dB
  (EPSG:32631) with per-polygon threshold attributes: `area_ha_at_minus3dB`
  (the polygon), `area_ha_at_minus4dB` (stricter −4 dB core surviving inside
  it), `area_ha_at_minus2dB` (area of the looser −2 dB connected component
  containing it — shows merger behavior, not a second polygon set).
- `data/flood_mask_t{2,3,4}db.tif` — binary rasters per threshold.
- `data/flood_sensitivity_table.json` — threshold sweep + quiet-pair floor.
- `data/flood_arrays.npz` — ΔVV/ΔVH grids, masks, transform (reproducibility).
- `data/flood_optical_xcheck.json`, `data/flood_ndwi_anomaly.tif`
- `data/flood_exposure.csv`, `data/flood_communities.csv`
- `data/xml/` — product annotation/calibration/noise XMLs (cached inputs).
- `data/qa_superseded_flood/` — superseded iterations (see README inside).

## 9. Verdict

**SHIPPABLE — within the framing of §0, and only within it.**

The data support a defensible "indicative co-event water extent versus
normal-condition extent" layer for the 2021-11-07 Keta event:

- Gating passed cleanly: co-event scene +1 day, same satellite/orbit/geometry
  reference −24 days, 0.00 px co-registration shift.
- The event signal (30.8–65.9 ha across the −2…−4 dB sweep) stands 19–29×
  above the empirical no-flood change floor (0–2.3 ha) at −3/−4 dB, once the
  intertidal zone is honestly excluded via the union water mask.
- 88–89% VH cross-polarization agreement; optical NDWI anomaly inside the
  polygons (+0.373) an order of magnitude above background 7 days post-event.
- Spatially coherent: lagoon-margin expansion, not scattered speckle.

What it is not: not surge-only (tide unresolvable), not a depth product, not
validated against ground truth (none exists), not evidence of impact on any
specific community (nearest verified villages are 0.6–0.8 km away; zero
buildings intersect; indicative population within extent is 4–5 people).
Absolute polygon placement carries ≈ ±40 m uncertainty.

Ship conditions: keep the "indicative co-event water extent" label verbatim,
always show the 30.8–65.9 ha range (never a point estimate), never attach it
to compensation/resettlement/engineering narratives, and link this methods
document from the layer.

## 10. Flood event library — gating log (2026-10-05)

Per the founder directive ("only defensible events, 3 strong > 10 weak"),
four additional documented Keta flood events were researched and satellite-
gated (Element84 Earth Search STAC, `sentinel-1-grd`, same AOI; co-event
scene within ±3 days **and at/after documented onset**; same-orbit reference
12–24 days prior; quiet-pair scene; bar = CLEARLY pass):

- **2017-06-11** — FAIL: only in-window scene (2017-06-08) predates the
  documented Jun 10–11 onset; next acquisition +9d, outside gate.
- **2022-03-03** — FAIL: scene 2022-03-02 18:10 UTC is ~10 h before the
  documented ~04:30 flooding; event date itself approximate (source weekday
  inconsistent with calendar).
- **2022-04-03** — FAIL: no scene within ±3d (nearest +4d; gate not bent).
- **2025-02/03 episode** — FAIL: recurrent Jan–Mar 2025 episode, onset
  ambiguous; the 2025-02-26 scene cannot be shown to be post-onset.

Seven further documented events were rejected without gating (pre-Sentinel-1;
outside the analysis AOI; single-source/uncorroborated; or month-level date
only). **Result: NULL — no additional event clearly passes; nothing was
built.** The Nov 2021 event above remains the library's only gated event.
Full research, per-candidate scene lists, and re-gating notes:
`FLOOD-EVENT-LIBRARY-GATING.md`; machine record: `flood_event_gating.json`;
gating script: `gate_flood_events.py`.
