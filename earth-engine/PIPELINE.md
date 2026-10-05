> **License — CC BY 4.0**
>
> © 2026 Street Pulse Foundation Inc. Licensed under CC BY 4.0 (https://creativecommons.org/licenses/by/4.0/).
> Attribution: Street Pulse Foundation / StreetPulse Blue — CoastalPulse Ghana.

# CoastalPulse Ghana — Satellite Pipeline

Automated, repeatable workflow for the satellite-derived shoreline-change
analysis (and, later, flood analysis). Replaces the manual script-by-script
rebuilds used for v0.1.

**Run it:**
```bash
cd ~/workspace/streetpulse-foundation/prototype/coastalpulse-mvp/earth-engine
./venv/bin/python run_pipeline.py
```

## Prerequisites

- **Python 3.12** with the project's virtualenv: `earth-engine/venv/`
  (create with `python3 -m venv venv && venv/bin/pip install -r requirements.txt`).
  Key deps: `rasterio`, `numpy`, `scipy`, `scikit-image`, `shapely`,
  `requests`, `pystac-client`, `PyYAML` (full list in `requirements.txt`).
- **Network access** to:
  - `earth-search.aws.element84.com` — STAC scene search (port 443)
  - `*.s3.amazonaws.com` / AWS Open Data — Sentinel-2 COG reads via GDAL/Rasterio
    (note: this VM reaches them through an egress proxy; `02c` sets
    `GDAL_HTTP_TIMEOUT`/`GDAL_HTTP_MAX_RETRY` so a stalled socket fails
    loudly instead of hanging — see 02c docstring).
  - No API keys, no Earth Engine auth needed.
- **Disk:** ~1 GB free (composite cache ~2×50 MB, outputs < 1 MB).
  Scene pixels are streamed, not downloaded as files.

## The stages

`run_pipeline.py` executes, in order, the thin wrappers in `stages/`:

| Stage       | Wrapper               | What it does                                                        | Inputs                              | Outputs (in `data/`)                              |
|-------------|-----------------------|---------------------------------------------------------------------|-------------------------------------|---------------------------------------------------|
| `acquire`   | `stage_acquire.py`    | STAC search: lowest-cloud Sentinel-2 L2A scenes per period          | `config.yaml` periods/AOI           | `data/pipeline/scenes.json`                       |
| `composite` | `stage_composite.py`  | Per-scene NDWI (SCL cloud/shadow mask) → pixel-wise median (≥3 valid obs) → ocean waterline (0.5 contour) | `scenes.json`, streamed COGs | `shoreline_early_median.geojson`, `shoreline_late_median.geojson`, cached composites in `data/pipeline/cache/` |
| `change`    | `stage_change.py`     | 100 m transects along OSM coastline baseline; waterline-intersection displacement ÷ baseline years | shoreline GeoJSONs, `../data/coastline_osm.json` | `shoreline_change_transects.geojson` |
| `qa`        | `stage_qa.py`         | Empirical noise floor: same pipeline on a 5-day scene pair          | 2 fixed scene IDs                   | `noise_check_result.json`                         |
| `detect`    | `stage_detect.py`     | Change detection: new-period transect rates vs archived baseline transects → per-transect flags (skips unless enabled) | baseline + new transects GeoJSONs (`detection:` config) | `change_flags.geojson` |
| `publish`   | `stage_publish.py`    | Validate GeoJSON; regenerate `evidence_dashboard_metrics.json` from the transects (computed, never hand-edited) | all of the above | `evidence_dashboard_metrics.json` |

The dashboard (`../index.html`) reads `data/*.geojson` and
`data/evidence_dashboard_metrics.json` directly — a successful `publish`
stage *is* the dashboard update.

The wrappers call the working analysis code in
`02c_median_analysis_robust.py` (`run_acquire` / `run_composites` /
`run_change` / `run_noise` / `run_detect`); nothing in the analysis math was rewritten for
the pipeline. Every constant the analysis uses is overridable via `CP_*`
env vars, which `run_pipeline.py` translates from `config.yaml`
(`config_to_env`). Running `02c_...py` directly with no env set reproduces
v0.1 exactly (all defaults are the v0.1 values).

Superseded scripts (kept for the record, **not** run by the pipeline):
`01_stac_query.py` (hardcoded windows, hanging STAC client),
`02_shoreline_analysis.py` (single-date → tide bias),
`02b_median_analysis_robust.py` (hung on stalled sockets; 02c is the hardened version).
`03_noise_check.py` still works standalone and now delegates to `02c.run_noise()`.

## Re-running for a new period or AOI

1. Copy `config.yaml` (e.g. `config-2026h2.yaml`) and edit:
   - `periods.early` / `periods.late` — new date windows,
   - `aoi` — new bounding box,
   - optionally `imagery.max_cloud_pct`, `scenes_per_period`, `shoreline.coast_lon_min/max`, `transect_spacing_m`.
2. Run: `./venv/bin/python run_pipeline.py --config config-2026h2.yaml`
3. Outputs land in `paths.data_dir` (default `data/`). **Back up the
   current `data/*.geojson` first if the dashboard should keep serving the
   old period** — e.g. `cp data/*.geojson data/pipeline/backup-<date>/`.
   For a second published period, point `paths.data_dir` at a new directory
   and wire the dashboard to it separately.

Useful flags:

```bash
./venv/bin/python run_pipeline.py --from change        # skip to transects (uses cached composites; seconds, no downloads)
./venv/bin/python run_pipeline.py --to composite       # stop after shorelines are written
./venv/bin/python run_pipeline.py --skip qa,publish    # skip stages
./venv/bin/python run_pipeline.py --no-cache           # ignore cached composites, re-download everything
./venv/bin/python run_pipeline.py --force-acquire      # re-run the STAC search even if scenes.json exists
./venv/bin/python run_pipeline.py --analysis flood     # second analysis type (see below)
```

Per-stage logs stream to the console and append to `pipeline.log`
(`--log FILE` to override).

## Caching model

- `data/pipeline/scenes.json` — the STAC scene list (reused unless `--force-acquire`).
- `data/pipeline/cache/composite_<EARLY|LATE>_<hash>.npz` — median NDWI +
  valid-observation count + affine per period. The hash covers scene IDs,
  dates, and every config value that changes pixels, so editing `config.yaml`
  automatically invalidates stale caches.
- With caches warm, `--from change` re-runs transects + change calc in
  seconds — the fast loop for tuning transect spacing, coast bounds, etc.
- `--no-cache` forces a full re-download (e.g. after a method change).

## Expected runtimes (this VM, v0.1 config)

| Stage | Cold (no cache) | Warm (cached) |
|---|---|---|
| acquire | ~10 s (2 STAC searches) | ~0 s (`scenes.json` reused) |
| composite | **~30–60 min** (11 scenes × 3 COG bands streamed via proxy) | ~1–2 min (npz load + contour extraction) |
| change | ~1–2 min | ~1–2 min |
| qa | ~5–10 min (2 scenes × 3 bands) | ~5–10 min (no cache by design — always re-measures) |
| detect | ~3 s (no downloads; disabled → instant skip) | ~3 s |
| publish | ~5 s | ~5 s |

The QA stage deliberately has no cache: it is an independent re-measurement.
Expected dry-run fingerprints (v0.1 config): 106 transects, open-coast median
−0.37 m/yr, Kedzi median −0.66 m/yr, noise pair n=106 / mean_abs 14.91 m.

## How the flood pipeline plugs in

The flood analysis (Sentinel-1 historical flood extents, sprint Week 1) is a
**second analysis type**, not a shoreline variant. Contract (full detail in
`flood/README.md`):

1. Build stage scripts under `flood/` (e.g. `stage_acquire.py`,
   `stage_preprocess.py`, `stage_detect.py`, `stage_qa.py`,
   `stage_publish.py`).
2. Add `flood/stages.yaml` listing them as `[{name, script, description}]`.
3. Add any `CP_FLOOD_*` env vars to `run_pipeline.py::config_to_env` and
   document them in `config.yaml`'s `flood:` section.
4. Run with `./venv/bin/python run_pipeline.py --analysis flood`.

No changes to `run_pipeline.py` itself are needed — it dispatches on
`flood/stages.yaml` when present. Stage scripts must read the same `CP_*`
env convention, exit non-zero on failure, cache intermediates under
`$CP_PIPELINE_DIR/cache/`, and write public GeoJSON into `$CP_DATA_DIR`
with `"label": "PUBLIC-OPEN DATA"` and a `method` string so the dashboard
consumes them like the shoreline layers.

## Change detection (stage: `detect`)

The baseline analysis (2017→2025, 106 transects) is a snapshot. The `detect`
stage answers the monitoring question funders ask: *"has anything changed
significantly since the baseline?"* It compares per-transect annualized rates
from a **new period's** transects against the **archived baseline** transects
and flags transects whose rate shift is too large to be measurement noise.
Detection only — no alert/watch infrastructure (that's a separate workstream).

**Inputs / outputs:**
- Inputs: baseline transects GeoJSON (`detection.baseline_transects` in
  `config.yaml` — the archived baseline) + new-period transects GeoJSON
  (`detection.new_transects`, default: this run's `change`-stage output in
  `data_dir`). The two files must contain the **same transect id set**;
  a mismatch fails the stage loudly (exit non-zero) rather than silently
  comparing partial overlaps.
- Output: `change_flags.geojson` (default `<data_dir>/change_flags.geojson`):
  one feature per transect (Point, carried over from the new-period transects)
  with `baseline_rate_m_per_yr`, `new_rate_m_per_yr`, `delta_m_per_yr`,
  `flag` (`FLAG` | `WATCH` | null), `flag_reason` (code + human sentence),
  `confidence` (`INDICATIVE` on flagged/watched rows; null otherwise) and
  `confidence_rationale`. Top-level properties carry the full provenance:
  both periods, noise floor, per-period uncertainties, both thresholds,
  flag/watch counts, method references, and the `not_suitable_for` guardrails.
- The stage is **disabled by default** (`detection.enabled: false`): with no
  baseline configured it logs a skip and exits 0, so the standard pipeline is
  unaffected. A monitoring run sets `enabled: true` and points
  `baseline_transects` at the archived baseline (e.g. a copy of the v0.1
  transects kept in `data/pipeline/backup-<date>/`).

**Thresholds (defaults; all overridable in `config.yaml` → `CP_DETECT_*`):**

| Band | Rule | v0.1-vs-v0.1 value |
|---|---|---|
| `FLAG` | \|delta\| ≥ max(2 × σ_Δ, 4 × noise floor) | 7.07 m/yr |
| `WATCH` | \|delta\| ≥ max(1 × σ_Δ, 2 × noise floor) | 3.54 m/yr |

Justification against the known error budget (METHODS.md):
- Noise floor **±1 m/yr** — rates within it are treated as noise.
- Per-transect uncertainty **~±2.5 m/yr** (10 m pixels over the 7.84 yr
  baseline; scaled inversely with each period's baseline years, parsed from
  the transects' `period` string, so a shorter new period gets a proportionally
  larger uncertainty).
- A rate difference carries σ_Δ = √(σ_b² + σ_n²) ≈ **3.54 m/yr** for
  equal-period comparisons. FLAG at 2σ_Δ is ~95% confidence the shift is not
  measurement noise; it is also ~7× the noise floor, so **no flag can fire
  inside the noise floor** — the σ_Δ constraint binds, the noise-floor guard
  is slack but explicit. WATCH at 1σ_Δ (≥ 2× noise floor) is "worth review",
  not a detection claim.
- `flag_reason` codes describe the shift's shape, not its severity:
  `EROSION_ONSET` / `EROSION_ACCELERATION` / `EROSION_DECELERATION` /
  `ACCRETION_ONSET` / `ACCRETION_ACCELERATION` / `ACCRETION_DECELERATION` /
  `SIGNIFICANT_RATE_SHIFT` (fallback), assigned from baseline/new rates
  relative to the noise floor and the sign of the delta.
- Confidence ceiling is **INDICATIVE** per CONFIDENCE-FRAMEWORK.md (no ground
  truth); every flagged row says so, with the rationale citing σ_Δ and the
  noise floor. Unflagged rows carry no confidence claim (null).

**Testing (honest):**
- **Null test:** v0.1 transects as both baseline and new → 0 FLAG, 0 WATCH of
  106 transects (identical inputs cannot produce detections; validates the
  null behavior). Artifact: `data/pipeline/tests/null_test_change_flags.geojson`.
- **Synthetic test:** v0.1 transects + 6 fabricated rate perturbations
  (−9.0, −7.2, +8.5, −5.0, +4.0, −2.0 m/yr) → exactly the expected 3 FLAG /
  2 WATCH with correct reason codes; the −2.0 m/yr case correctly fires
  nothing (below the watch band, confirming no flag near the noise floor);
  all 100 unperturbed transects unflagged. Synthetic inputs/outputs are
  marked `SYNTHETIC TEST DATA - DO NOT SHIP` (top-level label, `synthetic_test:
  true`) and live only in `data/pipeline/tests/` — never in `data/`, never
  wired to the dashboard. See `data/pipeline/tests/README.md`.

**Monitoring-run recipe:**
1. Back up the current baseline: `cp data/shoreline_change_transects.geojson data/pipeline/backup-<date>/`.
2. Copy `config.yaml` → `config-<newperiod>.yaml`; set the new `periods`,
   and under `detection:`: `enabled: true`,
   `baseline_transects: data/pipeline/backup-<date>/shoreline_change_transects.geojson`.
3. Run `./venv/bin/python run_pipeline.py --config config-<newperiod>.yaml`.
4. Read `change_flags.geojson` (and its top-level flag/watch counts) for the
   monitoring signal. Point `paths.data_dir` at a fresh directory if the
   dashboard should keep serving the old period.

**Detect-only dry-run (no downloads):** the `detect` stage does not touch the
network, so the monitoring config can be exercised in isolation:

```bash
./venv/bin/python run_pipeline.py --config config-<newperiod>.yaml --from detect --to detect
```

**Synthetic dry-runs must be labeled:** the `SYNTHETIC TEST DATA - DO NOT
SHIP` label is not a config value — it comes from the environment. Export
`CP_DETECT_SYNTHETIC=1` (it propagates through `config_to_env`, which starts
from `os.environ`) whenever the "new period" input is fabricated, so the
output carries the top-level `label` and `synthetic_test: true`. Keep such
outputs in `data/pipeline/tests/` — never in `data/`, never wired to the
dashboard.

## Troubleshooting

- **Stage fails on STAC search**: check `earth-search.aws.element84.com`
  reachability; the search retries 3× with backoff, then raises.
- **Hangs during composite**: should not happen — `GDAL_HTTP_TIMEOUT=60`,
  `CONNECTTIMEOUT=20`, `MAX_RETRY=3` are set in 02c. If a run seems stuck,
  check `pipeline.log` for the last scene line; re-run with `--from composite`.
- **Different scenes picked than v0.1**: the STAC catalog is append-only in
  practice, but verify `data/pipeline/scenes.json` against the scene list in
  the shipped GeoJSONs' `properties.scenes` before trusting a re-run.
- **Outputs differ after a config edit**: expected — the cache hash covers
  config, so composites rebuild automatically; diff against
  `data/pipeline/backup-<date>/`.
