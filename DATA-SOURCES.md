# Data sources

Every layer in this app comes from a published government or NGO dataset. Nothing here is
modeled, estimated, or synthesized by the app — the only processing is format conversion
(GeoParquet / PMTiles / H3 hex) and, in some cases, a filter to Utah.

All layers are served from the public STAC catalog at
[`s3-west.nrp-nautilus.io/public-data/stac/catalog.json`](https://s3-west.nrp-nautilus.io/public-data/stac/catalog.json)
and processed by the [Boettiger Lab](https://github.com/boettiger-lab) `cng-datasets` pipeline.
The catalog entry for each layer carries the full column schema, feature counts, and provenance;
the assistant reads it at runtime, so you can also just ask it "where does this layer come from?".

The sidebar is organized by **what the data describes**, not by which agency publishes it — so
federal (BLM, USGS, USFS, NPS) and state (UGS, UDOGM) sources sit side by side in the same group
when they describe the same thing.

---

## Bears Ears boundaries / Grand Staircase-Escalante boundaries

One toggleable outline per redesignation, so eras can be overlaid and compared.

| | |
|---|---|
| **Layers** | Bears Ears: 2016 original, 2017 reduced, 2021 restored, 2026 proposed<br>Grand Staircase-Escalante: 1996 original, 2017 reduced, 2021 restored, 2026 proposed |
| **Published by** | BLM proclamation boundaries, [Utah SGID](https://gis.utah.gov/), and [USGS PAD-US](https://www.usgs.gov/programs/gap-analysis-project/science/pad-us-data-overview) (2.1 / 4.1), compiled per era |
| **License** | Public domain |
| **STAC** | [`benm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/bears-ears/stac-collection.json) · [`gsenm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/grand-staircase-escalante/stac-collection.json) |

Each polygon carries `acres` (the acreage stated in the proclamation) and `gis_acres`
(measured from the boundary geometry) — these differ, and the app reports which one it used.

⚠️ **The 2026 boundary is a *proposed* reduction (announced 13 July 2026), not enacted law.**

---

## Mineral & energy resources — what is in the ground

Geologic occurrence and resource-extent data. These layers say what the resource *is*, not who
holds a right to it or who is operating.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Coal deposit areas · UGS 1988 | [Utah Geological Survey](https://geology.utah.gov/), hosted by UGRC / SGID | Utah statewide, 94 polygons across 12 coal deposit areas — includes the Kaiparowits Plateau field | 1988 | CC-BY-4.0 |
| Mineral occurrences · Utah Geological Survey | UGS [Utah Mineral Occurrence System (UMOS)](https://geology.utah.gov/), hosted by UGRC / SGID | Utah only, 7,388 points (occurrences, prospects, mines, some energy resources) | Current UMOS release | CC-BY-4.0 |
| Mineral deposits · USGS MRDS | [USGS Mineral Resources Data System](https://mrdata.usgs.gov/mrds/) | US-wide (266,593 points); **map filtered to `state = 'Utah'`** | Last MRDS release, 2011 | Public domain |

MRDS is a legacy compilation last updated in 2011 — treat it as historical, and prefer UGS UMOS
for Utah-specific questions. The two overlap; do not add their counts together.

---

## Leases & claims — who holds the rights

Legal interests recorded on federal land. A lease or claim is a *right*, not evidence that
anything is being extracted.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Oil & gas leases, 2015+ · BLM | [BLM National MLRS / EGIS](https://gbp-blm-egis.hub.arcgis.com/datasets/BLM-EGIS::blm-natl-mlrs-oil-and-gas-leases/about) | Nationwide (466,415 lease parcels); **map filtered to `ADMIN_STATE = 'UT'` and `lease_year >= 2015`** | Current MLRS extract | Public domain |
| Hard-rock mining claims · BLM | [BLM National MLRS / EGIS](https://catalog.data.gov/dataset/blm-natl-mlrs-mining-claims-not-closed-f621b) | Nationwide (655,792 features: 575,287 not-closed + 80,505 closed); **map filtered to `admin_state = 'UT'`** | Records span 2021–2026 | Public domain |

The full lease history (1920 onward, all states) is queryable via the assistant even though the
map view is filtered. `CSE_DISP = 'Authorized'` is the filter for currently active leases.

---

## Wells, mines & permits — what is actually operating

Permitted and producing operations on the ground. Utah DNR (UDOGM) is the state authority and
covers **all** Utah lands — federal, state, and private — so it is the right source for
"what is operating", while the BLM layer covers only federal hard-rock operations.

| Layer | Published by | Coverage | License |
|---|---|---|---|
| Oil & gas wells · Utah DNR (UDOGM) | [Utah DNR Division of Oil, Gas and Mining](https://www.ogm.utah.gov/), hosted by SGID | Utah, 40,344 well surface locations | CC-BY-4.0 |
| Producing oil & gas fields · Utah DNR (UDOGM) | Utah DNR / UDOGM, hosted by SGID | Utah producing field outlines | CC-BY-4.0 |
| Coal mine permits · Utah DNR (UDOGM) | Utah DNR / UDOGM, hosted by SGID | Utah coal permit boundaries | CC-BY-4.0 |
| Mineral mine permits · Utah DNR (UDOGM) | Utah DNR / UDOGM, hosted by SGID | Utah permitted non-coal mineral mines | CC-BY-4.0 |
| Hard-rock operations · BLM | [BLM National MLRS](https://www.blm.gov/services/land-records/mlrs) | 11 western states (2,399 features: 1,264 Notices + 1,135 Plans of Operations); **map filtered to `ADMIN_STATE = 'UT'`** | Public domain |

Under the General Mining Law of 1872, a BLM *Notice* covers ≤ 5 acres of disturbance and a *Plan
of Operations* covers more — the distinction is the `op_level` column.

---

## Protected areas & trails — conservation & recreation

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Protected areas · USGS PAD-US 4.1 | [USGS Gap Analysis Project](https://www.usgs.gov/programs/gap-analysis-project/science/pad-us-data-overview) | Fee-owned protected areas nationwide (296,456 features); **map filtered to `State_Nm = 'UT'`** | PAD-US 4.1 | Public domain |
| Federal trails, 2026 · USFS / NPS / BLM | USFS National Forest System Trails + NPS Public Trails + BLM Ground Transportation Linear Features | Nationwide, one row per published trail segment | 2026 compilation | Public domain |

Colors on the protected-areas layer are **GAP status codes** (1–4), which describe the strength of
the biodiversity-conservation mandate — not the managing agency. This is the *fee* layer only;
PAD-US also publishes proclamation and easement layers that are not shown here, and its polygons
can overlap for a single unit, so acreage must be deduplicated before summing.

---

## Indigenous & community lands

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Indigenous & community lands · LandMark 2025 | [LandMark](https://landmarkmap.org) — global platform of Indigenous and community land | Global, 124,616 polygons | v202509 (September 2025) | CC-BY-4.0 |

LandMark aggregates local, national, and regional mapping initiatives. Coverage completeness
varies by region, and a boundary shown here documents a mapped claim or recognized holding — it
is not a legal determination of title.

---

## What is *not* in this app

The app shows only the layers listed above. It has **no** land-cover, vegetation, wildfire,
human-modification, or carbon data, and no economic or demographic data. If you ask the
assistant a question that would need one of those, it should tell you the data is not available
rather than substituting something else.
