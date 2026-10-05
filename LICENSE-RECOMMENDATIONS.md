# Licensing Recommendations — CoastalPulse Ghana Assets

**For the founder. Recommendations only. Nothing in this document has been
applied.** Each item needs explicit founder approval before any license is
attached to the corresponding asset.

Current applied position (decided 2026-10-04): core application / platform
code is copyright retained by Street Pulse Foundation Inc., all rights
reserved — no MIT, Apache, GPL, AGPL, or other broad open-source license.
See `LICENSE` and `LICENSING.md`. Strategic flexibility and IP are preserved.

**Status update 2026-10-05:** recommendation (i) (CC BY 4.0 for original
methods/reports/educational material) was approved by the founder and has
been APPLIED — see `LICENSING.md` section (b) for the covered files. All
other recommendations in this document remain pending approval and are not
applied.

---

## (i) Our original methods, reports, and educational material

**Recommendation: CC BY 4.0 (Creative Commons Attribution 4.0 International)**

Assets covered: `methods.html`, `earth-engine/METHODS.md`, future methods
reports, and other educational material authored by Street Pulse Foundation /
StreetPulse Blue.

Reasoning:

- **Grant competitiveness.** Funders and grant reviewers expect methods to be
  inspectable and citable. CC BY 4.0 makes the methods explicitly reusable
  while requiring attribution, which is exactly the posture grant-makers want
  to see.
- **Research credibility.** Citable, attribution-required methods can be
  referenced by researchers and partners without us negotiating one-off
  permissions, building scientific reputation and citation footprint.
- **Attribution requirement protects us.** CC BY 4.0 requires downstream
  users to credit Street Pulse Foundation, preserving reputational value —
  unlike a public-domain dedication.
- **No code rights given away.** CC BY is a content license, not a software
  license. Applying it to methods/reports does not weaken the
  all-rights-reserved position on the core application/platform code.

Why not CC BY-SA (share-alike)? Share-alike would force downstream users to
license derivative explanations under the same terms. That friction can deter
funders, partners, and journalists from quoting or adapting our material;
for methods we want the widest credible reuse with attribution, not copyleft.

Why not more restrictive (all rights reserved on content too)? Keeping
methods closed looks inconsistent with a program whose credibility rests on
published methodology, and it slows down grant applications and partner
adoption. The IP we are protecting (the platform code) stays protected.

**Approval needed before applying CC BY 4.0 to methods/reports/educational
material.** When approved, add the CC BY 4.0 notice and attribution statement
to each covered file/page.

---

## (ii) Derived datasets — per-dataset evaluation

**Nothing applied. Each dataset needs founder approval before a license is
attached.** Recommendations below evaluate options per dataset, based on
source data terms and intended use.

### Source-data baseline

- **Shoreline vectors and transects** are derived from Copernicus Sentinel-2
  L2A imagery. Copernicus data is free, full and open under the Copernicus
  data terms (attribution required — documented in README, methods.html, and
  index.html). Copernicus terms do not impose a share-alike requirement on
  derived products; attribution obligations carry forward into our
  documentation, which we already satisfy.
- **Some derived products also reference OSM vectors.** ODbL's share-alike
  provisions apply to derivatives of the ODbL-licensed database itself.
  Produced databases that are substantially derived from OSM data must be
  distributed under ODbL. Datasets that merely sit alongside OSM data
  (analyzed from independent Sentinel imagery) do not inherit ODbL.

### Dataset: shoreline vectors (2017 & 2025 median shorelines)

**Recommendation: CC BY 4.0** (with Copernicus attribution carried forward).

- Derived primarily from Sentinel-2 analysis, not from the OSM database, so
  ODbL share-alike does not attach.
- CC BY 4.0 maximizes open research use (grants, academic reuse, funder
  validation) while requiring attribution to Street Pulse Foundation — our
  name travels with the data.
- Commercial sensitivity is low: these are prototype-stage, field-unvalidated
  shoreline positions with documented limitations; openness builds credibility
  without giving away valuable IP.

### Dataset: shoreline-change transects (per-transect rates)

**Recommendation: CC BY 4.0** (same reasoning as shoreline vectors).

- Same source lineage as the shoreline vectors (Sentinel-2 derived analysis;
  OSM used only as baseline context, not as the source database).
- Transect rates are the project's headline analytical finding; open reuse
  with attribution strengthens grant evidence and partner adoption.

### Dataset: flood extents (not yet built)

**Recommendation: decide per build, but default recommendation is CC BY 4.0,
revisited if the data source changes.**

- Expected derivation: Sentinel-1 (also Copernicus, same free/open terms as
  Sentinel-2) — so the same reasoning applies: no share-alike imposed by the
  source; CC BY 4.0 recommended.
- **Caveat:** if flood-extent products incorporate OSM data substantially
  (e.g., OSM-derived settlement or infrastructure layers as an input
  database), the ODbL share-alike evaluation must be rerun for that specific
  product, and ODbL may be required for the OSM-derived component.

### Why not CC BY-SA or ODbL as the default for derived datasets

- **CC BY-SA** would oblige downstream users to share alike, which can deter
  commercial partners and funders from building on the data — contrary to
  the open-research-use and funder-reuse goals. Share-alike is a defensive
  posture we don't need for field-unvalidated prototype datasets.
- **ODbL** is appropriate only where the OSM share-alike provisions actually
  attach (substantial derivatives of the OSM database). It should be applied
  where legally required, not as a default preference.
- **Commercial sensitivity note:** if any future dataset becomes genuinely
  commercially sensitive (e.g., validated, high-value products built with a
  paying partner), the default CC BY 4.0 recommendation should be revisited
  for that dataset — licensing is per-dataset, not one-size-fits-all.

---

## (iii) Public API — no recommendation yet

No public API exists. When an API is built, its terms will need a separate
decision (API terms of use, rate limits, data-license interplay), and a
recommendation will be prepared at that time.

---

## Summary of approvals needed

| # | Asset | Recommendation | Status |
|---|-------|----------------|--------|
| 1 | Methods / reports / educational material | CC BY 4.0 | ⏳ Awaiting founder approval — not applied |
| 2 | Shoreline vectors (2017, 2025) | CC BY 4.0 (Copernicus attribution carried) | ⏳ Awaiting founder approval — not applied |
| 3 | Shoreline-change transects | CC BY 4.0 | ⏳ Awaiting founder approval — not applied |
| 4 | Flood extents (when built) | CC BY 4.0 default; re-evaluate if OSM-derived | ⏳ Awaiting founder approval — not applied |
| 5 | Public API (when built) | TBD at build time | ⏳ Awaiting founder approval — not applied |

Applied and in force: core application / platform code — copyright retained by
Street Pulse Foundation Inc., all rights reserved (`LICENSE`, `LICENSING.md`).
