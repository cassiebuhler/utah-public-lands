# utah-public-lands

An AI-powered interactive map exploring how **Bears Ears** and **Grand Staircase-Escalante National Monuments** in Southern Utah have been redrawn from 1996 to 2026 — and what the land inside and outside each boundary holds: mineral and energy resources, federal leases and claims, permitted wells and mines, protected areas, trails, and Indigenous and community lands.

Built on the [geo-agent / GLEN](https://github.com/boettiger-lab/geo-agent) framework. **No JavaScript to write** — the map, chat, agent, and tools load from CDN; this repo is just three config files plus deployment manifests.

**Framework docs:** [boettiger-lab.github.io/geo-agent](https://boettiger-lab.github.io/geo-agent/)

## Files

```
index.html          ← HTML shell — loads GLEN core (pinned @v3.24.0) + libs from CDN
layers-input.json   ← datasets, grouping, map view, LLM settings
system-prompt.md    ← monument-change analyst persona, timeline, guardrails
DATA-SOURCES.md     ← publisher, coverage, vintage and license for every layer
k8s/                ← Kubernetes deployment (app: utah-public-lands)
AGENTS.md           ← configuration reference for AI coding agents
```

## Layer organization

Layers are grouped by **what the data describes**, not by which agency publishes it, so federal
(BLM, USGS, USFS, NPS) and state (UGS, UDOGM) sources sit together when they describe the same
thing.

Every layer label follows one form — **`what it is · PUBLISHER vintage`** — where the vintage is a
year, or a version where the source publishes one (`PAD-US 4.1` is the only one). A year is either a
final release (`UGS 1988`, `USGS MRDS 2011`) or, for live services that publish no version, the year
the snapshot was pulled (`BLM 2026`, `UDOGM 2026`); [DATA-SOURCES.md](DATA-SOURCES.md) says which for
every layer, and expands the publisher acronyms.

| Group | Layers |
|---|---|
| **Bears Ears boundaries** | 2016 original → 2017 reduced → 2021 restored → 2026 proposed |
| **Grand Staircase-Escalante boundaries** | 1996 original → 2017 reduced → 2021 restored → 2026 proposed |
| **Mineral & energy resources** *(what's in the ground)* | Coal deposit areas · UGS 1988; mineral occurrences · UGS 2026; mineral deposits · USGS MRDS 2011 |
| **Leases & claims** *(who holds the rights)* | Oil & gas leases (2015+) · BLM 2026; hard-rock mining claims · BLM 2026 |
| **Wells, mines & permits** *(what's operating)* | Oil & gas wells, producing fields, coal permits, mineral mine permits · UDOGM 2026; hard-rock operations · BLM 2026 |
| **Protected areas & trails** | Protected areas · USGS PAD-US 4.1; federal trails · USFS / NPS / BLM 2026 |
| **Indigenous & community lands** | Indigenous & community lands · LandMark 2025 |

The two monument groups are expanded on load with the 2021 restored + 2026 proposed outlines
visible; every other group starts collapsed and off, so the map opens on the boundary story rather
than 21 layers at once.

The **resource / rights / activity** split matters analytically: a coal deposit is geology, a lease
is a right someone holds, and a permitted well is activity on the ground. They are not
interchangeable and their counts don't add up.

Full provenance — publisher, coverage, vintage, license, and which layers are filtered to Utah — is
in **[DATA-SOURCES.md](DATA-SOURCES.md)**, which the app links as "About" in its footer. The app
holds no land-cover, vegetation, wildfire, human-modification, carbon, economic, or demographic
data; the layer panel is the complete inventory.

## Local development

```bash
python -m http.server 8000
# Open http://localhost:8000 — enter your API key in the ⚙ settings panel
```

## Deployment (Kubernetes)

`main` is the live branch — the pod's init container clones this repo at startup, so **push to GitHub first, then restart**.

```bash
git add -A && git commit -m "<message>" && git push
kubectl rollout restart -f k8s/deployment.yaml
kubectl rollout status  -f k8s/deployment.yaml
```

- App/Service/Deployment name: `utah-public-lands`; namespace: `schmidtdse`; host: `utah-public-lands.nrp-nautilus.io`.
- The LLM proxy key is injected server-side from the shared `open-llm-proxy-secrets` secret (browser never sees it); the `llm` block in `layers-input.json` is only used for local dev / GitHub Pages fallback.

## Data hosting

No spatial data lives in this repo. Every layer is served as cloud-native GeoParquet / PMTiles / H3
hex from the public STAC catalog at `s3-west.nrp-nautilus.io/public-data/stac/catalog.json`. Keep it
that way — `.gitignore` blocks geospatial file types so source extracts don't get committed here.

See the [deployment guide](https://boettiger-lab.github.io/geo-agent/docs/guide/deployment) for details.
