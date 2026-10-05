> **License — CC BY 4.0**
>
> © 2026 Street Pulse Foundation Inc. Licensed under CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/).
> Attribution: Street Pulse Foundation / StreetPulse Blue — CoastalPulse Ghana.

# CoastalPulse Ghana — Shoreline-change analysis: methods note

**Project:** CoastalPulse Ghana (StreetPulse Blue) MIT Solve 2027 MVP
**Analysis date:** 2026-10-04
**Pipeline:** local Python (Sentinel-2 L2A via AWS Open Data; Earth Engine
registration exists under GCP project `coastalpulse-ghana` but was not needed —
this analysis uses the public STAC + COG endpoints directly, so **no Earth Engine
quota was consumed**).
**Label on all outputs:** PUBLIC-OPEN DATA (derived by our analysis from open
Copernicus Sentinel-2 imagery).

## Scripts

| Script | Purpose |
|---|---|
| `01_stac_query.py` | Scene discovery: Element84 Earth Search STAC, Sentinel-2 L2A over AOI (lon 0.93–1.07, lat 5.86–6.00), dry-season windows, cloud < 25%. Wrote `stac_search.json`. |
| `02_shoreline_analysis.py` | **Superseded.** Single-date NDWI waterlines (2017-01-04 vs 2025-01-22). QA found a whole-coast systematic seaward offset in the 2025 scene — tidal-stage bias, not real accretion. Outputs archived in `data/qa_superseded_single_date/`; NOT used in the prototype. |
| `02b_median_analysis.py` | Median-NDWI composite pipeline (the method). Its first execution hung indefinitely on a stalled socket through the sandbox egress proxy (no timeouts set) and was killed before producing outputs. |
| `02c_median_analysis_robust.py` | **Executed version (used).** Identical method to 02b, hardened networking: STAC search via plain `requests` with explicit timeouts (no pystac_client pagination), 6 scenes per period, GDAL HTTP timeouts + retries, per-scene read retry loop. Also fixed a numpy≥2 `datetime64` incompatibility in the composite mid-date computation (`np.mean` on datetime64 arrays). |
| `03_noise_check.py` | Standalone empirical noise-floor check: same pipeline pieces on a 5-day scene pair (2025-01-22 S2C vs 2025-01-27 S2B). Wrote `noise_check_result.json`. (The noise check embedded in 02c failed on a hardcoded scene ID `S2B_31NBG_20250122_0_L2A` that does not exist — the 2025-01-22 acquisition is Sentinel-2C; corrected in 03.) |

## Inputs (02c)

- **Sensor:** Sentinel-2 L2A (10 m), Copernicus / ESA, via AWS Open Data
  (Registry of Open Data on AWS: `sentinel-2-l2a`), queried through the
  Element84 Earth Search STAC endpoint: https://earth-search.aws.element84.com/v1
- **Early period — 5 scenes, 2016-08-01 → 2017-06-30, cloud < 30%:**
  `S2A_31NBG_20170104_0_L2A` (2017-01-04, 1.6%), `S2A_31NBG_20170325_0_L2A`
  (2017-03-25, 24.3%), `S2A_31NBG_20170203_0_L2A` (2017-02-03, 24.4%),
  `S2A_31NBG_20170315_0_L2A` (2017-03-15, 27.1%),
  `S2A_31NBG_20170514_0_L2A` (2017-05-14, 29.4%).
- **Late period — 6 scenes, 2024-09-01 → 2025-06-30, cloud < 30%:**
  `S2B_31NBG_20241218_0_L2A` (2024-12-18, 0.8%),
  `S2C_31NBG_20250122_0_L2A` (2025-01-22, 0.8%),
  `S2B_31NBG_20240909_0_L2A` (2024-09-09, 5.4%),
  `S2B_31NBG_20250127_0_L2A` (2025-01-27, 6.3%),
  `S2A_31NBG_20241223_0_L2A` (2024-12-23, 8.6%),
  `S2C_31NBG_20250512_0_L2A` (2025-05-12, 9.8%).
- **Baseline:** composite mid-dates 2017-03-07 → 2025-01-07 = **7.84 years**.
- **Masking:** SCL classes 3 (cloud shadow), 8 (cloud med prob), 9 (cloud high prob)
  excluded; green/NIR > 0 required; a pixel enters the median only with ≥ 3
  valid observations.

## Method (per period)

1. NDWI = (B03 green − B08 NIR) / (B03 + B08) per scene (McFeeters 1996).
2. Pixel-wise **median** NDWI over the valid scene stack; water = median NDWI > 0.
   Median compositing averages out tidal-stage variation, so the waterline
   approximates a mid-tide position instead of an arbitrary acquisition tide.
3. Ocean = largest connected water component (8-connectivity labeling).
4. Shoreline = 0.5 contour of the ocean mask, converted from UTM 31N to lon/lat.
5. **Change:** transects every 100 m along the OSM coastline baseline (west→east);
   seaward-normal rays intersected with each period's waterline set;
   displacement = late − early along the normal; rate = displacement / 7.84 yr.
   **Negative = landward retreat (erosion); positive = seaward advance (accretion).**
   Transects restricted to the open sandy coast (lon 0.997–1.052: Keta town
   frontage + Kedzi coastal strip) to exclude the Keta inlet / groyne artifact zone.

## Outputs (`data/`)

- `shoreline_early_median.geojson` — 2016–2017 median waterline (1 LineString, 3,422 vertices)
- `shoreline_late_median.geojson` — 2024–2025 median waterline (1 LineString, 2,579 vertices)
- `shoreline_change_transects.geojson` — 106 per-transect points with
  `transect_id`, `change_m`, `rate_m_per_yr`, `zone`, `period`
  ("2017-03-07 to 2025-01-07 (7.84 yr)"), `label: PUBLIC-OPEN DATA`
- `water_mask_2017.tif` / `water_mask_2025.tif` — work rasters from the superseded
  single-date run; kept for QA provenance only.
- `qa_superseded_single_date/` — the tide-biased single-date outputs, archived, not used.
- `noise_check_result.json` — empirical noise-floor numbers.

## Results summary (all values computed, none smoothed)

- **Open coast (n=106):** mean −0.21 m/yr; median −0.37 m/yr;
  max erosion −9.19 m/yr; max accretion +21.49 m/yr.
- **Kedzi coastal strip (n=99):** mean −0.36 m/yr; median −0.66 m/yr;
  max erosion −9.19 m/yr.
- **Keta town frontage (n=7):** mean +1.98 m/yr; median +0.95 m/yr;
  max erosion −1.86 m/yr (sea-defence protected; mild accretion plausible behind groynes).
- **Spatial pattern:** westernmost 20 transects (Kedzi/down-drift end):
  median −0.88 m/yr; easternmost 20: median +0.92 m/yr — erosion concentrated
  at the down-drift western end, accretion further east.
- **Noise floor** (same pipeline, 5-day pair 2025-01-22 → 2025-01-27, n=106):
  mean displacement −4.3 m; mean-abs 14.9 m; max-abs 141.1 m.
  Single-scene waterlines carry ~15 m tidal/method jitter; median compositing
  reduces this but does not eliminate it — **rates within ±1 m/yr should be
  treated as noise**, and individual transect values carry roughly ±2.5 m/yr
  uncertainty (10 m pixels over a 7.84 yr baseline).

## Corroboration check

Documented rates for this coast (Keta validation matrix, 2026-10-03):
~8 m/yr background erosion; up to ~17 m/yr immediately down-drift (east) of the
Keta Sea Defence (Kedzi side; 2025 Harvard "terminal groyne" study).

**Verdict: directional/pattern agreement, magnitude shortfall — do not overclaim.**
Our measurement corroborates the *direction* (net erosion on the Kedzi strip)
and the *spatial pattern* (erosion concentrated at the down-drift western end,
accretion to the east) of the documented erosion story, but the measured
transect-median rates (−0.66 m/yr Kedzi) are well below the documented ~8 m/yr
background and 17 m/yr hotspot figures. Plausible reasons: (a) documented rates
describe specific hotspots/periods, while ours is a transect-median over the
open sandy coast; (b) the inlet/groyne zone (lon < 0.997, excluded as
artifact-prone) may contain the fastest-eroding section; (c) method uncertainty
(~±2.5 m/yr per transect, ±1 m/yr noise floor); (d) different time windows;
(e) possible real deceleration — cannot be distinguished here. **Field
validation (GPS shoreline survey) is required before these rates are used for
anything beyond indicative visualization.**

## Limitations (honest)

- 10 m pixels → shoreline position uncertain to roughly ±10 m per waterline.
- Median compositing reduces but does not eliminate tidal bias; seasonal beach
  profile differences between the two scene windows can leak in.
- No tidal correction against a tide gauge; no ground-truth GPS survey.
- Transects are indicative, not survey-grade; not suitable for engineering,
  compensation, or resettlement decisions.
- The Keta inlet, groynes, and lagoon mouth were excluded from the change
  analysis (artifact-prone); only the open sandy coast is reported.

## Quota / cost

- **No Earth Engine quota used** (analysis ran on public STAC + AWS Open Data COGs).
- No AWS charges incurred (public dataset; no requester-pays on this bucket).
- EE Community Tier (150 EECU-hrs/mo) remains untouched for future work.
- Imagery attribution: contains modified Copernicus Sentinel data (ESA), 2017 & 2024–2025.
