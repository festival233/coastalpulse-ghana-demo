# CoastalPulse Ghana — Roadmap

## The goal

CoastalPulse Ghana is a satellite-derived shoreline-change monitoring platform for
Ghana's coast. Version 0.1 is a working **prototype** — real Sentinel-2 shoreline
vectors (2017 vs 2025) over the Keta Lagoon zone, rendered as public open data.

The goal now is the transition from **prototype → validated pilot**: one coastal
zone, real community findings, and the field validation needed to stand behind the
numbers. Funders are invited to help finance that transition.

**Built on real data, not placeholders.** Every erosion rate shown is
computed from genuine satellite composites (Shoreline Analysis v2 — Sentinel-Anchored:
39 of 97 transects eroding beyond the ±1 m/yr noise floor; worst observed erosion
−10.74 m/yr in the western/down-drift hotspot; west-half median −2.59 vs east-half
+0.91 m/yr; Keta town frontage accretion signal; broad medians within method noise).
Methods, assumptions, and noise floors are published alongside the data
(see `methods.html`).

---

## v0.2 — In scope (public presentation layer)

These items ship the v0.1 findings to a public audience properly. No new data
collection, no new product surface.

- Presentation cleanup: copy, structure, and navigation for non-technical readers
- Branding: consistent StreetPulse Blue / CoastalPulse Ghana identity
- Overview panel: a plain-language summary of what the data shows
- Evidence dashboard: the computed transects, rates, and download links in one view
- Methods: full documentation of the satellite pipeline, assumptions, and limits
- Community discovery: who works, fishes, and lives along this stretch of coast —
  documented as desk research only, feeding pilot design

## v0.3 — Decided by genuine community findings

v0.3 does not have a feature list yet. It will be scoped **after** community
discovery and the first pilot partner conversations — built around what coastal
communities and Ghanaian partners actually need, not what looks impressive in a demo.

---

## Hold list — do NOT build without a specific funding or pilot reason

The items below are deliberately held. Each is desirable, each is expensive, and
none is needed to validate the pilot. A funder, partner, or pilot milestone may
unlock any of them — the trigger is named for each.

| Item | Justification trigger |
|---|---|
| Predictive AI (erosion forecasting) | A funded pilot explicitly requires forecast products, with field-validated training data in hand |
| Machine-learning erosion models | A research partner funds model development AND the GPS/survey ground-truth needed to train it |
| IoT sensor network (buoys, tide gauges, weather stations) | A pilot grant covers hardware procurement, installation, and 2+ years of maintenance |
| Live sensor dashboards | Sensors are deployed and streaming under a funded pilot (no dashboards for hypothetical feeds) |
| SMS alerts | A pilot community requests alerts AND a telco/NGO partner funds per-message costs at scale |
| WhatsApp bot | A partner community adopts the platform and requests WhatsApp as a channel, with funding for operations |
| Fisheries forecasting | A fisheries ministry, research institute, or grant funds species-specific modeling AND catch-data partnerships |
| Mobile app | A pilot community has a validated use case requiring offline mobile access, with funding for native development |
| National Ghana rollout | The pilot validates in one zone: data accuracy confirmed, partner adoption demonstrated, funding committed for scale |
| Cross-country rollout | National rollout succeeds and a regional partner funds replication in a second country |

**The rule:** if it isn't in v0.2, isn't decided by community findings, and isn't
tied to a funding or pilot trigger — it waits.

---

*CoastalPulse Ghana — a StreetPulse Blue project · Street Pulse Foundation*
*Contact: info@streetpulsefoundation.org*
