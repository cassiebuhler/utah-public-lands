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
| **USFWS** | [U.S. Fish and Wildlife Service](https://www.fws.gov/) — administers the Endangered Species Act |
| **USGS** | [U.S. Geological Survey](https://www.usgs.gov/) |
| **CDC** | [Centers for Disease Control and Prevention](https://www.atsdr.cdc.gov/place-health/php/svi/index.html) / ATSDR |
| **NatureServe** | [NatureServe](https://www.natureserve.org/) — Map of Biodiversity Importance (MOBI), with Esri and The Nature Conservancy |
| **LandMark** | [LandMark](https://landmarkmap.org) — global platform of Indigenous and community land |

---

## Bears Ears boundaries / Grand Staircase-Escalante boundaries

One toggleable outline per redesignation, so eras can be overlaid and compared. Here the era year
*is* the vintage, so these labels carry no separate publisher token.

| | |
|---|---|
| **Layers** | Bears Ears: `2016 · 1.35M ac`, `2017 · 202k ac`, `2021 · 1.36M ac — in effect`, `2026 · 121k ac — PROPOSED`<br>Grand Staircase-Escalante: `1996 · 1.88M ac`, `2017 · 1.00M ac`, `2021 · 1.87M ac — in effect`, `2026 · 182k ac — PROPOSED` |
| **Published by** | One source per era: **originals** from Utah SGID *BLM Monuments & NCAs Historic*; **2017 reduction** from USGS [PAD-US](https://www.usgs.gov/programs/gap-analysis-project/pad-us-data-history) 2.1 (released Sept 2020); **2021 restoration** from PAD-US 4.1 (released Mar 2025); **2026 proposed** from the proposed-reduction boundaries |
| **License** | Public domain |
| **STAC** | [`benm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/bears-ears/stac-collection.json) · [`gsenm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/grand-staircase-escalante/stac-collection.json) |

These labels carry an acreage rather than a word like "reduced" or "restored", so the size change is
stated as a figure instead of as a judgement, and legal status is its own field.

**The acreage shown is `acres`, the official proclamation acreage.** Each polygon also carries
`gis_acres` measured from the geometry, and the two differ — by ~9% on Bears Ears, where the 2016
boundary is 1,351,850 official acres against 1,413,100 measured. Grand Staircase's 2026 proposal is
**three separate polygons** totalling 181,591 ac, so deduplicate by `_cng_fid` before summing.

⚠️ **The 2026 boundary is a *proposed* reduction (announced 13 July 2026), not enacted law.** Note that
the source data disagrees: its 2026 features carry `era = '2026 reduced'` and `status = 'reduced'`.
That is an upstream labelling artifact, not evidence the reduction took effect — the panel label and
legend are the correct wording.

---

## Mineral & energy resources — what is in the ground

Geologic occurrence and resource-extent data. These layers say what the resource *is*, not who
holds a right to it or who is operating.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Undiscovered oil & gas · **USGS 2026** | USGS [National and Global Oil and Gas Assessment Project](https://www.usgs.gov/centers/central-energy-resources-science-center/science/united-states-assessments-undiscovered-oil), via ScienceBase | US-wide (240 assessment units); **map filtered to the three USGS provinces that reach Utah** — Eastern Great Basin, Uinta-Piceance Basin and Southwestern Wyoming, 14 units | **Snapshot, 21 Sep 2026.** Merged from 57 per-province releases published 2018–2026; USGS publishes no national compilation and no version, so each unit carries its own release date. | Public domain |
| Coal deposit areas · **UGS 1988** | UGS, hosted by UGRC / SGID | Utah statewide, 94 polygons across 12 coal deposit areas — includes the Kaiparowits Plateau field | Areas **as defined in 1988**; SGID layer `CoalDepositAreas1988`. Converted 23 Jul 2026. | CC-BY-4.0 |
| Mineral occurrences · **UGS 2026** | UGS [Utah Mineral Occurrence System (UMOS)](https://webmaps.geology.utah.gov/arcgis/rest/services/Energy_Mineral/UMOS/MapServer/0), hosted by UGRC / SGID | Utah only, 7,388 points (occurrences, prospects, mines, some energy resources) | **Snapshot, 23 Jul 2026.** Live MapServer feed publishing no version or release date. | CC-BY-4.0 |
| Mineral deposits · **USGS MRDS 2011** | USGS [Mineral Resources Data System](https://mrdata.usgs.gov/mrds/) | US-wide (266,593 points); **map filtered to `state = 'Utah'`** | **Systematic updates ceased 2011** — USGS states it "has ceased systematic updates to MRDS". Converted 23 Jul 2026. | Public domain |

UMOS is *itself* undated at the feature level — it has no uniform occurrence-date field, so there
is no per-feature year to trend on. MRDS is a legacy compilation last released in 2011; prefer UMOS
for Utah-specific questions. The two overlap, so do not add their counts together.

The **undiscovered oil & gas** layer is a different kind of thing from the other three: it is not a
record of anything found, it is an estimate of what USGS believes is probably present but has not
been discovered. Each polygon is an *assessment unit*, a geologic area assessed as a whole, and its
numbers are a probability distribution — F95 (low), F50 (median), F5 (high) and the mean, for oil
(million barrels), gas (billion cubic feet) and natural gas liquids (million barrels). There is no
finer geometry upstream: USGS does not map individual undiscovered accumulations.

Three things about it change how the numbers may be used:

- **Assessment units overlap and stack.** Conventional and continuous units, and different Total
  Petroleum Systems, cover the same ground, so several polygons sit over one location. Deduplicate
  by `ASSESSCODE` before summing anything.
- **Only the mean is additive.** F95, F50 and F5 describe one unit's own range; adding them across
  units, or area-weighting them, produces a number that means nothing.
- **Blank is not zero.** Only 66 of the 240 units carry volume estimates at all. USGS began
  publishing per-unit results tables in 2023, and the earlier releases publish the boundary plus
  the assessment input forms and no results. On the map those units are grey: the boundary is real,
  the estimate simply was not published with it.

The province filter is not a Utah clip — the source carries no state field, and the three provinces
extend into Colorado, Wyoming, Nevada and Idaho. Intersect against Utah geometry for any
Utah-specific count.

---

## Mineral leases & claims — who holds the rights

Legal interests recorded on federal land. A lease or claim is a *right*, not evidence that
anything is being extracted.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Oil & gas leases (2015+) · **BLM 2026** | BLM [National MLRS / EGIS](https://gbp-blm-egis.hub.arcgis.com/datasets/BLM-EGIS::blm-natl-mlrs-oil-and-gas-leases/about) | Nationwide (466,415 lease parcels); **map filtered to `ADMIN_STATE = 'UT'` and `lease_year >= 2015`** | **Snapshot, 22 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
<!-- 486-mlrs-minerals -->
| Oil & gas agreements · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (32,787 cases); **map filtered to `ADMIN_STATE = 'UT'`** (1,193 in Utah). 443 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Oil & gas participating areas · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (2,562 cases); **map filtered to `ADMIN_STATE = 'UT'`** (364 in Utah). 17 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Coal cases · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (3,857 cases); **map filtered to `ADMIN_STATE = 'UT'`** (414 in Utah). 971 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Oil shale leases · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (42 cases); **map filtered to `ADMIN_STATE = 'UT'`** (11 in Utah). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Geothermal leases · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (7,394 cases); **map filtered to `ADMIN_STATE = 'UT'`** (576 in Utah). 195 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Non-energy leasable minerals · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (7,106 cases); **map filtered to `ADMIN_STATE = 'UT'`** (1,182 in Utah). 212 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
| Mineral materials (sand & gravel) · **BLM 2026** | BLM [National MLRS / EGIS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (35,670 cases); **map filtered to `ADMIN_STATE = 'UT'`** (2,530 in Utah). 1,393 of them have no geometry and so do not appear on the map (see note below). | **Snapshot, 24 Jul 2026.** Live MLRS service publishing no version or release date. | Public domain |
<!-- 486-mlrs-minerals -->
| Hard-rock mining claims · **BLM 2026** | BLM [National MLRS / EGIS](https://catalog.data.gov/dataset/blm-natl-mlrs-mining-claims-not-closed-f621b) | Nationwide (655,792 features: 575,287 not-closed + 80,505 closed); **map filtered to `admin_state = 'UT'`** | **Snapshot, 23 Jul 2026.** Live MLRS service, no version; record dates span 2021–2026. | Public domain |

The seven MLRS mineral case-record layers between the two markers above are the *leasable* and
*salable* mineral estate — the rights BLM grants to extract a mineral, as opposed to the mining
claims in the last row (which are *located* by a claimant under the 1872 Mining Law) and the
operations in the next group (which are the work actually authorized on the ground).

All seven carry a uniform numeric **`case_year`** and a **`case_year_src`** flag saying whether
that year is the case's *effective* date or its *disposition* date. They are not
interchangeable — an effective year is when a case started, a disposition year is usually when
it closed — so "cases active in year X" should filter `case_year_src = 'effective'` together
with `CSE_DISP = 'Authorized'`.

> **Geocoding gaps — read acreage off the map with care.** BLM derives these polygons from each
> case's Legal Land Description via the PLSS, and where that fails the case has no geometry at
> all. By far the worst is **Coal cases, where 971 of 3,857 cases (25%) are unmapped**; the
> other six range from under 1% to about 4%. Those cases are still in the data and the
> assistant can answer on them, but they are absent from the map, so any acreage or overlap
> measured on the map understates the true total.

> **Geothermal leases and Oil & gas agreements deliberately have no year slider.** A cumulative
> slider filters `case_year <= value`, and that test is false for a null — so attaching one
> would silently hide every case with no recorded year. Utah coverage is only 67% for
> geothermal leases and 88% for oil & gas agreements, too large a share to drop from the map
> without warning. Use the assistant for time questions about them.

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
| Hard-rock operations · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | 11 western states (2,399 features: 1,264 Notices + 1,135 Plans of Operations); **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 23 Jul 2026.** Live MLRS service, no version; records span 1975–2026. | Public domain |
| Producing oil & gas fields · **UDOGM 2026** | UDOGM & UGRC, hosted by SGID | Utah, 153 producing field outlines | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Coal mine permits · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 32 coal permit boundaries | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Oil & gas wells · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 40,344 well surface locations | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |
| Mineral mine permits · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID | Utah, 1,504 permitted non-coal mineral mines | **Snapshot, 23 Jul 2026.** Live FeatureServer publishing no version or release date. | CC-BY-4.0 |

Under the General Mining Law of 1872, a BLM *Notice* covers ≤ 5 acres of disturbance and a *Plan of
Operations* covers more — the distinction is the `op_level` column.

Coal permits carry **no permit-issue date** in the source (only GIS edit timestamps), so their
`year` column is null for every feature.

---

## Land use & tenure — how the land is used and held

Non-extractive authorizations on BLM land, and how BLM came to hold the land in the first
place. Distinct from **Mineral leases & claims** (which is mineral rights) and from **Wells, mines &
permits** (which is extraction activity): a right-of-way is a road or powerline crossing public
land, and an acquisition is a parcel BLM bought or was given.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Land-use leases, permits & easements · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (39,598 case records; 37,477 geocoded); **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1911–2026. | Public domain |
| Rights-of-way · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (196,751 case records; 191,959 geocoded) — the largest MLRS layer; **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1866–2026. | Public domain |
| Acquired lands & interests · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (97,529 case records; 96,777 geocoded), reaching 34 states; **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1855–2026. | Public domain |

These three layers carry **no lease dates** — no effective, expiration or sale date. Their only
date is the *case disposition* date, so the derived `disp_year` is a disposition year, not the
year an authorization began. It is near-complete on the two land-use layers (99.8% and 99.5%)
but ⚠️ **null on 69% of acquisitions records** (only 30,438 of 97,529 are dated), so a time
series over acquisitions covers a dated minority. A few disposition dates are also implausible
(up to 3023 on rights-of-way) and are kept verbatim rather than silently corrected.

Rights-of-way footprints are long, thin corridors recorded as a width × length; `CSE_WIDTH` and
`CSE_LGTH` are populated on about two thirds of them but are **free text with no stated units**.

On acquisitions, `PAT_NR` is a General Land Office patent volume/serial string (`'3 1206'`) —
**not a date**, and present on only 150 records — so no acquisition or patent year is derivable.

`ADMIN_STATE` (used for the Utah map filter) is the *administering* BLM office, not where the
land lies; `GEO_STATE` is the location. They usually agree in Utah but diverge for the Eastern
States office.

---

## Protected areas — conservation status

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Protected areas · **USGS PAD-US 4.1** | USGS [Gap Analysis Project](https://www.usgs.gov/programs/gap-analysis-project/science/pad-us-data-overview) | Fee-owned protected areas nationwide (296,456 features); **map filtered to `State_Nm = 'UT'`** | **Version 4.1, released March 2025** — the current PAD-US version; content through 2024. Converted Feb 2026. | Public domain |

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

## Species & habitat

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Imperiled species richness · **NatureServe 2023** | NatureServe [Map of Biodiversity Importance](https://www.natureserve.org/products/map-biodiversity-importance) (MOBI), with Esri and The Nature Conservancy | Contiguous US raster, ~2,400 imperiled and endemic species | **2023 release** | **CC-BY-NC-4.0 — non-commercial use only** |
| ESA critical habitat · **USFWS 2026** | USFWS [ES Critical Habitat service](https://www.fws.gov/program/endangered-species) (HQ item `794de45b9d774d21aed3bf9b5313ee24`, layer 0) | Nationwide, 728 polygons across 462 species — **not filtered to Utah** | **Snapshot, 24 Jul 2026.** Live ArcGIS service publishing no version; designations date from 1973 onward. | Public domain |
| Mule deer migration range · **USGS 2020–2022** | USGS Fort Collins Science Center, *Ungulate Migrations of the Western United States* (Kauffman et al.) | 8 western states; **map filtered to `state = 'UT'`** | Utah content comes from **volumes 1 (2020) and 2 (2022)** of a six-volume series. | Public domain |

Only **final** critical-habitat designations are shown — those legally in effect under the
Endangered Species Act. Proposed designations are a separate upstream dataset and are not mapped.
The `unit`, `subunit` and `accuracy` columns are placeholder text in this aggregated layer.

The mule deer layer covers **Grand Staircase-Escalante only**. Utah's content is two herds,
Paunsaugunt and Kaibab North; **Bears Ears has no mapped migration range** in the USGS series, and
there is no elk or pronghorn data for Utah. Corridor, winter-range and stopover polygons are nested
utilization contours that overlap in space — they cannot be added together.

Species richness is a **modeled** surface, not an observation count: NatureServe combines habitat
models for about 2,400 imperiled and endemic species. It therefore covers only those species, not all
biodiversity, and it shows predicted suitable habitat rather than recorded sightings. The raster is
stretched **0–10** for this region rather than the national 0–32, because local values top out at 8
(Bears Ears) and 10 (Grand Staircase-Escalante); the national range would render the map nearly flat.

---

## Rivers & recreation — recreation access

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Federal trails · **USFS / NPS / BLM 2026** | USFS National Forest System Trails + NPS Public Trails + BLM Ground Transportation Linear Features | Nationwide, one row per published trail segment | **Version 2026** (recorded as `summaries.version` in the STAC record) — a 2026 compilation of three live agency services. | Public domain |
| Inventoried river reaches · **NPS 2024** | NPS [Nationwide Rivers Inventory](https://www.nps.gov/subjects/rivers/nationwide-rivers-inventory.htm) | All 50 states + territories, 4,496 segments; **map filtered to `State1 = 'Utah'`** (319 Utah reaches) | **2024 update**, a substantial expansion of the 2016 version. | Public domain |

The NRI lists free-flowing segments with outstanding natural, cultural or recreational values that
are **potentially eligible** for Wild and Scenic designation. The `Classifica` field (Wild / Scenic /
Recreational) is the inventory's proposed class — **not** legal Wild and Scenic status. Utah has only
two designated Wild and Scenic rivers, the Virgin and the Green, and neither is in either monument.

---

## People — resident population

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Social vulnerability · **CDC 2022** | [CDC/ATSDR Social Vulnerability Index](https://www.atsdr.cdc.gov/place-health/php/svi/index.html) | US census tracts (84,120); **map filtered to `ST_ABBR = 'UT'`** | **2022 release**, built on ACS 2018–2022 estimates. | Public domain |

`RPL_THEMES` is an **overall national percentile rank from 0 to 1** — not a rate, count or
percentage. `-999` is the nodata sentinel and must be excluded, not read as a low score. Only nine
census tracts cover San Juan, Garfield, Kane and Wayne counties, so this layer is coarse relative to
a monument boundary, and it describes **residents, not visitors**. The map tiles carry only `COUNTY`,
`FIPS`, `RPL_THEMES` and `ST_ABBR`; the other 158 variables are available via SQL.

---

## What is *not* in this app

The app shows only the layers listed above. It has **no** land-cover, vegetation, wildfire,
human-modification, or carbon data. If you ask the assistant a question that would need one of
those, it should tell you the data is not available rather than substituting something else.

**There is no visitation or tourism-economy data.** No recreation visitor counts, no gateway-town
spending, and no employment by industry — nothing from NPS, BLM, BEA or BLS. The CDC layer carries a
resident unemployment rate, which is not a measure of recreation employment. Answering whether the
boundary reductions would affect the recreation economy would require NPS visitor statistics, BLM
recreation reporting, and BLS employment data, none of which are here.
