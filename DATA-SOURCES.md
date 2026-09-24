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
  the exact date; the snapshots here were pulled in July and September 2026.

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
| **Layers** | Bears Ears: `2016 · 1.35M ac`, `2017 · 202k ac`, `2021 · 1.36M ac`, `2026 · 121k ac`<br>Grand Staircase-Escalante: `1996 · 1.88M ac`, `2017 · 1.00M ac`, `2021 · 1.87M ac`, `2026 · 182k ac` |
| **Published by** | One source per era: **originals** from Utah SGID *BLM Monuments & NCAs Historic*; **2017 reduction** from USGS [PAD-US](https://www.usgs.gov/programs/gap-analysis-project/pad-us-data-history) 2.1 (released Sept 2020); **2021 restoration** from PAD-US 4.1 (released Mar 2025); **2026 reduction** from the Proclamation 11043 / 11044 boundaries |
| **License** | Public domain |
| **STAC** | [`benm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/bears-ears/stac-collection.json) · [`gsenm-boundaries`](https://s3-west.nrp-nautilus.io/public-utah/grand-staircase-escalante/stac-collection.json) |

These labels carry an acreage rather than a word like "reduced" or "restored", so the size change is
stated as a figure instead of as a judgement. No label carries a legal-status marker: which boundary
is in force is recorded below and in the system prompt, not in the panel.

**The acreage shown is `acres`, the official proclamation acreage.** Each polygon also carries
`gis_acres` measured from the geometry, and the two differ — by ~9% on Bears Ears, where the 2016
boundary is 1,351,850 official acres against 1,413,100 measured. Grand Staircase's 2026 boundary is
**three separate polygons** totalling 181,591 ac, so deduplicate by `_cng_fid` before summing.

**The 2026 boundary is the one in effect.** Proclamation 11043 (Bears Ears, 121,096 ac in the Indian
Creek and Shash Jáa units) and Proclamation 11044 (Grand Staircase-Escalante, 181,541 ac in the
Canyons of the Escalante and Kaiparowits Horizon units) were issued 13 July 2026 and published in the
Federal Register on 17 July 2026. Each delayed the consequences by 60 days: at 9:00 a.m. EDT on
**11 September 2026** the excised lands opened to entry, location, selection and sale under the public
land laws, to mineral and geothermal leasing, and to location and patent under the mining laws.

The source data's `era = '2026 reduced'` and `status = 'reduced'` therefore match the legal position.

⚠️ **The reduction is being litigated and the boundaries could change again.** Two *separate*
cases are running in parallel and should not be conflated:

- **The Antiquities Act challenge to the reductions**, in the U.S. District Court for the District
  of Columbia. Conservation groups filed supplemental complaints on 2 September 2026, reviving the
  2017 challenge to the first Trump reduction and extending it to the 2026 proclamations.
- **Utah's own suit against the 2021 restoration**, in the Tenth Circuit. On 23 June 2026 the Tenth
  Circuit reversed the district court's dismissal of the challenges brought by Utah and other
  plaintiffs and remanded that case to federal district court.

No court has stayed or enjoined the 2026 proclamations, so the 2026 boundaries are in force while
both cases are unresolved. This file records the position as of its last edit — check the dockets
before relying on it.

The acreage this app shows for the 2026 Grand Staircase boundary is **181,591 ac**, measured as the
`acres` column over the layer's three polygons; Proclamation 11044 states approximately 181,541 ac.
The ~50-acre difference is in the source data and is not corrected here.

---

## Mineral & energy resources — what is in the ground

Geologic occurrence and resource-extent data. These layers say what the resource *is*, not who
holds a right to it or who is operating.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Undiscovered oil & gas · **USGS 2026** | USGS [National and Global Oil and Gas Assessment Project](https://www.usgs.gov/centers/central-energy-resources-science-center/science/united-states-assessments-undiscovered-oil), via ScienceBase | US-wide (240 assessment units); **map filtered to the three USGS provinces that reach Utah** — Eastern Great Basin, Uinta-Piceance Basin and Southwestern Wyoming, 14 units | **Snapshot, 21 Sep 2026.** Merged from 57 per-province releases published 2018–2026; USGS publishes no national compilation and no version, so each unit carries its own release date. | Public domain |
| Coal deposit areas · **UGS 1988** | UGS, hosted by UGRC / SGID | Utah statewide, 94 polygons across 12 coal deposit areas — includes the Kaiparowits Plateau field | Areas **as defined in 1988**; SGID layer `CoalDepositAreas1988`. Converted 23 Jul 2026. | CC-BY-4.0 |
| Special Tar Sand Areas · **BLM 2007** | BLM, distributed in the 2012 [Oil Shale and Tar Sands PEIS](https://web.archive.org/web/20130216005950/http://ostseis.anl.gov/guide/maps/gis/2012_OSTS_PEIS_Geospatial_Data.zip) geospatial package (Argonne National Laboratory) | Utah, the 11 areas designated in 1980–81 as containing substantial tar sand deposits, about 1,026,000 acres. **37 polygon parts, not 11 rows**: seven areas have several parts. | **Fixed vintage, 2007.** Boundaries as compiled by BLM for the PEIS. The Argonne site is offline; retrieved 24 Sep 2026 from the Internet Archive capture, with the package kept unmodified beside the data. | Public domain |
| Kaiparowits coal assessment area · **USGS 1997** | USGS [Open-File Report 97-709](https://pubs.usgs.gov/of/1997/ofr-97-0709/) (`csb` coverage) | Kaiparowits Plateau, southern Utah — 6 polygons outlining the outcrop of the Calico sequence boundary. This is the extent every other coverage in the report was clipped to. | **Fixed vintage, 1997.** Coverage files dated 7 Nov 1997; upstream labels it version 1 and has never revised it. Converted 22 Sep 2026. | Public domain |
| Kaiparowits net coal thickness · **USGS 1997** | USGS [Open-File Report 97-709](https://pubs.usgs.gov/of/1997/ofr-97-0709/) (`allcoal` coverage) | Kaiparowits Plateau — 5,222 polygons carrying **72,129.6 million short tons of coal in place** in the John Henry Member of the Straight Cliffs Formation, attributed by net coal thickness, overburden, reliability, dip, coal and surface ownership, county, quadrangle and township-range | **Fixed vintage, 1997.** As above. | Public domain |
| Kaiparowits ground favorable for mining · **USGS 1997** | USGS [Open-File Report 97-709](https://pubs.usgs.gov/of/1997/ofr-97-0709/) (`fig22` coverage) | Kaiparowits Plateau — 815 polygons meeting the report's geologic criteria for mid-1990s underground mining: beds over 3.5 ft thick, under 3,000 ft deep, dipping under 12° | **Fixed vintage, 1997.** As above. | Public domain |
| Kaiparowits coal mine adits · **USGS 1997** | USGS [Open-File Report 97-709](https://pubs.usgs.gov/of/1997/ofr-97-0709/) (`m_adit` coverage) | Kaiparowits Plateau — 50 lines marking historic coal mine adits. **No attributes at all** in the source: no mine name, date or status. | **Fixed vintage, 1997.** As above. | Public domain |
| Kaiparowits coal drill holes · **USGS 1997** | USGS [Open-File Report 97-709](https://pubs.usgs.gov/of/1997/ofr-97-0709/) (`kaipcoal` coverage) | Kaiparowits Plateau — the 209 drill holes and measured sections every thickness and tonnage figure in the report is interpolated from | **Fixed vintage, 1997.** As above. | Public domain |
| Mineral occurrences · **UGS 2026** | UGS [Utah Mineral Occurrence System (UMOS)](https://webmaps.geology.utah.gov/arcgis/rest/services/Energy_Mineral/UMOS/MapServer/0), hosted by UGRC / SGID | Utah only, 7,388 points (occurrences, prospects, mines, some energy resources) | **Snapshot, 23 Jul 2026.** Live MapServer feed publishing no version or release date. | CC-BY-4.0 |
| Uranium areas · **UGS 2026** | UGS, hosted by UGRC / SGID ([SGID Energy theme](https://gis.utah.gov/products/sgid/energy/)) | Utah, 15 broad Colorado Plateau uranium areas | **Snapshot, 22 Sep 2026.** Live feature service publishing no version; the underlying compilation is UGS Map 215 (2005). | CC-BY-4.0 |
| Uranium districts · **UGS 2026** | UGS, hosted by UGRC / SGID | Utah statewide, 58 districts carrying the survey's uranium potential class | **Snapshot, 22 Sep 2026.** As above — UGS Map 215 (2005). | CC-BY-4.0 |
| Past uranium producers · **UGS 2026** | UGS, hosted by UGRC / SGID; attributes in USGS [CRIB](https://doi.org/10.3133/cir755B) record format | Utah, 748 past producing sites — **722 carry attributes, 26 are position-only** | **Snapshot, 22 Sep 2026.** Live feature service publishing no version or release date. | CC-BY-4.0 |
| Mineral deposits · **USGS MRDS 2011** | USGS [Mineral Resources Data System](https://mrdata.usgs.gov/mrds/) | US-wide (266,593 points); **map filtered to `state = 'Utah'`** | **Systematic updates ceased 2011** — USGS states it "has ceased systematic updates to MRDS". Converted 23 Jul 2026. | Public domain |

UMOS is *itself* undated at the feature level — it has no uniform occurrence-date field, so there
is no per-feature year to trend on. MRDS is a legacy compilation last released in 2011; prefer UMOS
for Utah-specific questions. The two overlap, so do not add their counts together.

`Special Tar Sand Areas · BLM 2007` is a **designation**, not a deposit map. Interior designated
these areas in 1980 and 1981 as containing substantial tar sand, and within them federal oil and gas
and tar sand rights can be leased together under the Combined Hydrocarbon Leasing Act of 1981. The
boundaries show where that leasing framework applies, not where tar sand is present or how much.

The five **Kaiparowits** layers are one 1997 USGS report, not five independent sources. They cover
only the Kaiparowits Plateau, and they describe the John Henry Member of the Straight Cliffs
Formation — the coal body that sat inside the original 1996 Grand Staircase-Escalante boundary.
They are a different thing from `Coal deposit areas · UGS 1988`, which draws coarse field outlines
for the whole state; the two overlap over the plateau and must not be counted together.

Four things about them change how the numbers may be used:

- **These are in-place resources, not reserves.** The 72,129.6 million short tons estimate coal in
  the ground at the report's thickness and overburden cutoffs, with nothing deducted for what is
  recoverable, mineable or economic, and the total spans every overburden class including more than
  6,000 ft. The authors state the data set "cannot be used for mine planning or to calculate
  reserves".
- **Identified and hypothetical are not interchangeable.** The `REL` column separates resources
  within three miles of a data point (`iden`, 54,878.8 million short tons) from those further away
  (`hypo`). A single total that mixes them should say so.
- **`-99` and blank are no-data markers, not zeroes.** Five `allcoal` polygons are map holes where
  no coal exists (`REL = 'hole'`, `OVERB = '-99'`, blank ownership), and eight of the seventeen
  numeric columns on the drill-hole layer use `-99` for missing values. An unfiltered average over
  those columns is wrong.
- **"Favorable" is geologic, not economic.** The favorable-for-mining layer applies mid-1990s
  longwall criteria and says nothing about present-day economics or permitting. It also carries no
  tonnage — intersect it with the thickness layer for that.

Each polygon in the thickness layer is an intersection of ten mapped attributes, so tonnage sums
directly with no deduplication. The adit and assessment-area layers carry no attributes at all and
are map context only.

The three **uranium** layers come from the same UGS/UDOGM release as the two in *Wells, mines &
permits* below, and they are not a hierarchy. Districts and areas share no key and no name: 47 of
the 58 districts sit wholly inside one area and 4 more sit 93–98% inside one, but 7 districts (Spor
Mountain, Honeycomb Hills, East Erickson, Blawn Mountain, Silver Reef, Marysvale, Newton) lie
outside every area, because the areas cover the Colorado Plateau and those districts are in western
Utah. Rolling districts up to areas silently drops those seven — relate them with a spatial join.

The map colours districts by `POTENTIAL`, the survey's own judgement of uranium potential: High
(7), Moderate (12), Low (29) and `None` (7). ⚠️ **`None` means no potential was assigned, not zero
potential**, and it is distinct from the 3 districts that are genuinely `NULL`. The criteria behind
the classes are not published anywhere — not in the ArcGIS item metadata, the FGDC record, the UGRC
product page, or Map 215 itself.

Past-producer **production and reserve figures need parsing before any arithmetic.** The `*_AMT`,
`*_U`, `*_ITEM`, `*_ACC`, `*_YEAR` and `*_GRADE` columns follow the USGS CRIB record format: each
holds several entries packed into one string separated by `ý` (U+00FD), each amount is in
*thousands* of the unit named in the parallel `*_U` entry, units and spellings vary within and
between records (`LB`, `LBS`, `ST`, `TONS`), and entries cover different commodities (`U`, `V`,
`ORE U`, `CON U`). Reserves appear in three overlapping tables — `RPR_*` is the total, `R_*` its
reserves part and `PR_*` its potential-resources part — so adding all three double-counts the same
material. Dates are nearly absent: only 55 of the 748 records carry a first-production year and one
carries a last-production year, so **there is no uranium production time series here**.

Past producers overlap `Mineral occurrences · UGS 2026` and `Mineral deposits · USGS MRDS 2011`
with no shared identifier — one mine can appear in all three. Never add counts across them.

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

> **This snapshot predates the reopening.** It was pulled 23 July 2026, before the excised lands
> opened to mining location on 11 September 2026, so it contains **nothing staked since** — the 16
> claims located in San Juan County included. A later pull would still lag: a claimant has 90 days
> from location to record a claim with BLM, so claims staked at the reopening need not appear in
> BLM's database until early December 2026. Conversely, every claim this layer shows inside an
> excised area was located **before** the reduction and stands under the valid existing rights the
> proclamations carve out — it is not a response to the reduction.

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
| Permitted uranium mines · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID, published in the UGS uranium release | Utah, 25 permitted uranium / uranium-vanadium mines | **Snapshot, 22 Sep 2026.** Live feature service publishing no version or release date. | CC-BY-4.0 |
| Uranium mills · **UDOGM 2026** | UDOGM, hosted by UGRC / SGID, published in the UGS uranium release | Utah, both of the state's uranium processing mills — White Mesa (San Juan) and Shootaring Canyon (Garfield) | **Snapshot, 22 Sep 2026.** Live feature service publishing no version or release date. | CC-BY-4.0 |

Under the General Mining Law of 1872, a BLM *Notice* covers ≤ 5 acres of disturbance and a *Plan of
Operations* covers more — the distinction is the `op_level` column.

Coal permits carry **no permit-issue date** in the source (only GIS edit timestamps), so their
`year` column is null for every feature.

⚠️ **The two uranium layers here are a subset of `Mineral mine permits · UDOGM 2026`, not an
addition to it.** They are the same UDOGM permit database read through a different SGID service —
the mineral mine permit layer holds 115 uranium- or vanadium-bearing permits across all permit
statuses, and both mills appear in it as well. `MINEID` / `MILL_ID` are the same permit numbers as
its `Permit` column but zero-padded differently (`S370103` vs `M0350004`), so a naive equality join
returns nothing. Never add the uranium counts to the mineral mine permit count.

Mine `STATUS` is `ACT` (active, 2 mines) or `SUS` (suspended, 23). `PERM_STAT` is `APP`
(approved) on 7 records and null on the other 18, and `MIN_TYPE` distinguishes `BM` (large mining
operation) from `EM` (small mining operation).

---

## Land use & tenure — how the land is used and held

Who owns the mineral estate, where livestock grazing is authorised, what other non-extractive
authorizations sit on BLM land, and how BLM came to hold the land in the first place. Distinct from
**Mineral leases & claims** (which is mineral *rights granted*) and from **Wells, mines & permits**
(which is extraction activity): the mineral estate is who owns the minerals before any right is
granted, a grazing allotment is a rangeland management unit, a right-of-way is a road or powerline
crossing public land, and an acquisition is a parcel BLM bought or was given.

| Layer | Published by | Coverage | Vintage | License |
|---|---|---|---|---|
| Grazing allotments · **BLM 2026** | BLM [National Grazing Allotment MapServer](https://gis.blm.gov/arcgis/rest/services/range/BLM_Natl_Grazing_Allotment/MapServer/12), layer 12 | Ten western state offices (21,252 polygons / 20,974 allotments); **map filtered to `ADMIN_ST = 'UT'`** — 1,414 polygons, 1,401 allotments, 27.5M acres | **Snapshot, 22 Sep 2026.** Live map service publishing no version; edit stamps in this snapshot run 13 Aug – 21 Sep 2026. | Public domain |
| Federal mineral estate · **BLM 2026** | BLM [Utah State Office](https://gis.blm.gov/utarcgis/rest/services/Lands/BLM_Utah_Federal_Minerals_Map_Service/FeatureServer) | Utah statewide, 101,585 PLSS parcels — **no filter needed, the source is Utah-only** | **Snapshot, 22 Sep 2026.** Live FeatureServer publishing no version; publisher metadata states content current as of 1 Mar 2026. | Public domain |
| Land-use leases, permits & easements · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (39,598 case records; 37,477 geocoded); **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1911–2026. | Public domain |
| Rights-of-way · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (196,751 case records; 191,959 geocoded) — the largest MLRS layer; **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1866–2026. | Public domain |
| Acquired lands & interests · **BLM 2026** | BLM [National MLRS](https://www.blm.gov/services/land-records/mlrs) | Nationwide (97,529 case records; 96,777 geocoded), reaching 34 states; **map filtered to `ADMIN_STATE = 'UT'`** | **Snapshot, 24 Jul 2026.** Live MLRS service, no version; disposition dates span 1855–2026. | Public domain |

⚠️ **A grazing allotment is a management unit, not a parcel of federal land.** An allotment
boundary can enclose private, state and other federal land alongside the BLM land inside it, so its
area is not the area of public land being grazed. Answering how much federal land an allotment
covers means intersecting it with a surface-ownership layer such as PAD-US.

**Animal unit months, permittees and seasons of use are not in this layer.** BLM describes it as
supplemental to the Rangeland Administration System (RAS), which is authoritative; what is released
publicly is geometry and administrative identifiers only. The stocking and permittee figures live
in RAS as tabular reports at [reports.blm.gov](https://reports.blm.gov), joined on `ST_ALLOT`
(equivalently `ALLOT_NO` with `ADMIN_ST`). This app does not carry them, so it cannot answer how
many cattle graze anywhere.

`ADMIN_ST` — used for the Utah map filter — is the administering state office, not where the land
lies. It is close in Utah but not exact: 1,401 allotments are administered by the Utah office,
while about 1,427 allotments actually sit inside Utah, the remainder administered from the Idaho,
Arizona, Colorado and Wyoming offices. A precise Utah figure needs an intersection with Utah
geometry.

Two more quirks worth knowing before counting: 135 allotment identifiers span 404 polygons, each
carrying its own `GIS_ACRES`, so an allotment's acreage is the **sum** over its polygons and
deduplicating by `ST_ALLOT` undercounts; and ⚠️ **`ACTIVE_DT` is not a per-allotment date** — 11,892
polygons have none, 4,648 carry 1934-06-28 (the Taylor Grazing Act), 3,493 carry 1899-12-30 and 237
carry 1946-01-01, leaving roughly 1,000 with a plausible date. There is no grazing time series here.

The **federal mineral estate** layer is the odd one out in this group and the only layer in the app
that describes *ownership* rather than an authorization. Its grain is the PLSS survey subdivision —
each parcel is an aliquot part identified by `GCDBDIVID`, commonly about 40 acres — not a case or a
lease. Utah is heavily split estate: **12,183 parcels carry federally owned minerals under surface
administered by someone other than the federal government or BLM**, so a parcel appearing here says
nothing about who manages the surface above it.

Commodity membership is carried as **ten flag columns**, not ten layers. BLM publishes one feature
class through eleven definition-query views, and a parcel can appear in several of them — the
per-commodity view counts total 106,444 against 101,585 parcels because 3,247 parcels carry more
than one flag. A flag is `X` for **Federal Minerals** or `I` for **Indian Minerals**, and null when
the parcel is not flagged for that commodity; 158 parcels carry no flag at all. Filtering one column
reproduces BLM's own layer exactly — `WHERE Coal IS NOT NULL` returns the 4,041 parcels in BLM's
Coal layer.

The map colours parcels by whether the whole mineral estate is federal (`ALL_MIN`, 86,271 parcels /
36.0M ac), whole-estate Indian minerals (5,071 / 2.1M ac), specific commodities only (10,085 / 765k
ac), or unflagged (158 / 76k ac).

⚠️ One parcel has **no geometry** in the source. It is present in the GeoParquet with a null geometry
but absent from the map and the hex, so the map covers 101,584 of the 101,585 parcels. `GIS_Acres`
is a per-parcel total repeated on every hex cell the parcel covers — deduplicate by `_cng_fid`
before summing it on the hex asset.

The three MLRS layers below carry **no lease dates** — no effective, expiration or sale date. Their only
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

**There are no grazing stocking figures.** The grazing allotment layer is boundaries and
identifiers only — animal unit months, permittees and seasons of use are held in BLM's Rangeland
Administration System and are not published in this feature class, so the app cannot say how much
livestock any allotment carries.

**There is no visitation or tourism-economy data.** No recreation visitor counts, no gateway-town
spending, and no employment by industry — nothing from NPS, BLM, BEA or BLS. The CDC layer carries a
resident unemployment rate, which is not a measure of recreation employment. Answering whether the
boundary reductions would affect the recreation economy would require NPS visitor statistics, BLM
recreation reporting, and BLS employment data, none of which are here.
