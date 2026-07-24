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
federal and state sources sit side by side in the same group when they describe the same thing.

## How to read a layer label

Every layer label follows one form: **`what it is · PUBLISHER vintage`** — for example
`Coal deposit areas · UGS 1988`. The trailing vintage is either a **year** or, where the source
publishes numbered releases, a **version** (`PAD-US 4.1` is the only one here).

The year means one of two things, and the **Vintage** column in the tables below says which for
every layer:

- a **fixed vintage** — the source was published or frozen that year and is not being updated
  (`UGS 1988`, `USGS MRDS 2011`);
- a **snapshot** — the source is a continuously-updated live service that publishes no version or
  release date, so the year is when this copy was pulled (`BLM 2026`, `UDOGM 2026`). The tables give
  the exact date; every snapshot here was pulled 22–23 July 2026.

The difference changes how you read a number: `USGS MRDS 2011` is as current as that dataset will
ever get, while `UDOGM 2026` is a snapshot of a feed that has kept moving since.

Where a row says "Converted <date>", that is when the cloud-native copy was written — it is *not* the
data's vintage. For a fixed-vintage or versioned source the two are unrelated: PAD-US 4.1 was
released March 2025 and converted here in February 2026.

## Publishers

Acronyms are used in layer labels for space; each one means:

| | |
|---|---|
| **BLM** | [U.S. Bureau of Land Management](https://www.blm.gov/) — federal land only |
| **NPS** | [U.S. National Park Service](https://www.nps.gov/) |
| **UDOGM** | [Utah DNR Division of Oil, Gas and Mining](https://www.ogm.utah.gov/) — all Utah lands: federal, state, and private |
| **UGRC / SGID** | [Utah Geospatial Resource Center](https://gis.utah.gov/) and the State Geographic Information Database — the state's distribution host, not the producer |
| **UGS** | [Utah Geological Survey](https://geology.utah.gov/) |
| **USFS** | [U.S. Forest Service](https://www.fs.usda.gov/) |
| **USGS** | [U.S. Geological Survey](https://www.usgs.gov/) |
| **LandMark** | [LandMark](https://landmarkmap.org) — global platform of Indigenous and community land |

---

## Bears Ears boundaries / Grand Staircase-Escalante boundaries

One toggleable outline per redesignation, so eras can be overlaid and compared. Here the era year
*is* the vintage, so these labels carry no separate publisher token.

| | |
|---|---|
| **Layers** | Bears Ears: 2016 original, 2017 reduced, 2021 restored, 2026 proposed<br>Grand Staircase-Escalante: 1996 original, 2017 reduced, 2021 restored, 2026 proposed |
| **Published by** | One source per era: **originals** from Utah SGID *BLM Monuments & NCAs Historic*; **2017 reduction** from USGS [PAD-US](https://www.usgs.gov/programs/gap-analysis-project/pad-us-data-history) 2.1 (released Sept 2020); **2021 restoration** from PAD-US 4.1 (released Mar 2025); **2026 proposed** from the proposed-reduction boundaries |
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
| Coal deposit areas · **UGS 1988** | UGS, hosted by UGRC / SGID | Utah statewide, 94 polygons across 12 coal deposit areas — includes the Kaiparowits Plateau field | Areas **as defined in 1988**; SGID layer `CoalDepositAreas1988`. Converted 23 Jul 2026. | CC-BY-4.0 |
| Mineral occurrences · **UGS 2026** | UGS [Utah Mineral Occurrence System (UMOS)](https://webmaps.geology.utah.gov/arcgis/rest/services/Energy_Mineral/UMOS/MapServer/0), hosted by UGRC / SGID | Utah only, 7,388 points (occurrences, prospects, mines, some energy resources) | **Snapshot, 23 Jul 2026.** Live MapServer feed publishing no version or release date. | CC-BY-4.0 |
| Mineral deposits · **USGS MRDS 2011** | USGS [Mineral Resources Data System](https://mrdata.usgs.gov/mrds/) | US-wide (266,593 points); **map filtered to `state = 'Utah'`** | **Systematic updates ceased 2011** — USGS states it "has ceased systematic updates to MRDS". Converted 23 Jul 2026. | Public domain |

UMOS is *itself* undated at the feature level — it has no uniform occurrence-date field, so there
is no per-feature year to trend on. MRDS is a legacy compilation last released in 2011; prefer UMOS
for Utah-specific questions. The two overlap, so do not add their counts together.

---

## Leases & claims — who holds the rights

Legal interests recorded on federal land. A lease or claim is a *right*, not evidence that
anything is being extracted.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Oil & gas leases (2015+) · **BLM 2026** | BLM [National MLRS / EGIS](https://gbp-blm-egis.hub.arcgis.com/datasets/BLM-EGIS::blm-natl-mlrs-oil-and-gas-leases/about) | Nationwide (466,415 lease parcels); **map filtered to `ADMIN_STATE = 'UT'` and `lease_year >= 2015`** | **Snapshot, 22 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Hard-rock mining claims · **BLM 2026** | BLM [National MLRS / EGIS](https://catalog.data.gov/dataset/blm-natl-mlrs-mining-claims-not-closed-f621b) | Nationwide (655,792 features: 575,287 not-closed + 80,505 closed); **map filtered to `admin_state = 'UT'`** | **Snapshot, 23 Jul 2026.** Live MLRS service, no version; record dates span 2021–2026. | Public domain |

The full lease history (1920 onward, all states) is queryable via the assistant even though the map
view is filtered. `CSE_DISP = 'Authorized'` is the filter for currently active leases.

The claims layer has **no claim-staking date** — its only dates are MLRS record-management
timestamps from the digital-migration window, not when a claim was located.

---

## Wells, mines & permits — what is actually operating

Permitted and producing operations on the ground. UDOGM is the state authority and covers **all**
Utah lands — federal, state, and private — so it is the right source for "what is operating", while
the BLM layer covers only federal hard-rock operations.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Oil & gas wells · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 40,344 well surface locations | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Producing oil & gas fields · **UDOGM 2026** | UDOGM & UGRC, hosted by SGID | Utah, 153 producing field outlines | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Coal mine permits · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 32 coal permit boundaries | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Mineral mine permits · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 1,504 permitted non-coal mineral mines | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Hard-rock operations · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | 11 western states (2,399 features: 1,264 Notices + 1,135 Plans of Operations); **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 23 Jul 2026.** Live MLRS service, no version; records span 1975–2026. | Public domain |

Under the General Mining Law of 1872, a BLM *Notice* covers ≤ 5 acres of disturbance and a *Plan of
Operations* covers more — the distinction is the `op_level` column.

Coal permits carry **no permit-issue date** in the source (only GIS edit timestamps), so their
`year` column is null for every feature.

---

## Protected areas & trails — conservation & recreation

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Protected areas · **USGS PAD-US 4.1** | USGS [Gap Analysis Project](https://www.usgs.gov/programs/gap-analysis-project/science/pad-us-data-overview) | Fee-owned protected areas nationwide (296,456 features); **map filtered to `State_Nm = 'UT'`** | **Version 4.1, released March 2025** — the current PAD-US version; content through 2024. Converted Feb 2026. | Public domain |
| Federal trails · **USFS / NPS / BLM 2026** | USFS National Forest System Trails + NPS Public Trails + BLM Ground Transportation Linear Features | Nationwide, one row per published trail segment | **Version 2026** (recorded as `summaries.version` in the STAC record) — a 2026 compilation of three live agency services. | Public domain |

Colors on the protected-areas layer are **GAP status codes** (1–4), which describe the strength of
the biodiversity-conservation mandate — not the managing agency. This is the *fee* layer only;
PAD-US also publishes proclamation and easement layers that are not shown here, and its polygons
can overlap for a single unit, so acreage must be deduplicated before summing.

---

## Indigenous & community lands

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Indigenous & community lands · **LandMark 2025** | LandMark | Global, 124,616 polygons | **September 2025 release** ([data update](https://landmarkmap.org/blog/data-update-september-2025); upstream stamp `v202509`). Converted Mar 2026. | CC-BY-4.0 |

LandMark aggregates local, national, and regional mapping initiatives. Coverage completeness
varies by region, and a boundary shown here documents a mapped claim or recognized holding — it
is not a legal determination of title.

---

## What is *not* in this app

The app shows only the layers listed above. It has **no** land-cover, vegetation, wildfire,
human-modification, or carbon data, and no economic or demographic data. If you ask the
assistant a question that would need one of those, it should tell you the data is not available
rather than substituting something else.
