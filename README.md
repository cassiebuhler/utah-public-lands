# utah-public-lands

An AI-powered interactive map exploring how **Bears Ears** and **Grand Staircase-Escalante National Monuments** in Southern Utah have changed from 2016 to 2026 — tracking the repeated boundary redesignations against measurable change on the land (land cover, vegetation, fire, human modification, carbon) and resource extraction.

Built on the [geo-agent / GLEN](https://github.com/boettiger-lab/geo-agent) framework. **No JavaScript to write** — the map, chat, agent, and tools load from CDN; this repo is just three config files plus deployment manifests.

**Framework docs:** [boettiger-lab.github.io/geo-agent](https://boettiger-lab.github.io/geo-agent/)

## Files

```
index.html          ← HTML shell — loads GLEN core (pinned @v3.22.0) + libs from CDN
layers-input.json   ← datasets, map view, time-series controls, LLM settings
system-prompt.md    ← monument-change analyst persona, timeline, guardrails
k8s/                ← Kubernetes deployment (app: utah-public-lands)
AGENTS.md           ← configuration reference for AI coding agents
```

## Time-series features

- **Wildfire perimeters** carry an animated **year slider** (1984→2020) — press play to watch fire history accumulate across the region.
- **Irrecoverable carbon** is a **version dropdown** (2018 / 2022 / 2023 / 2024).
- **Per-era monument boundaries** (2016 original → 2017 cut → 2021 restored → 2026 cut) are a planned versioned layer — see the open data-ingestion issue. The current map shows the restored (2021) extent from PAD-US 4.1.

## Local development

```bash
python -m http.server 8000
# Open http://localhost:8000 — enter your API key in the ⚙ settings panel
```

## Deployment (Kubernetes)

`main` is the live branch — the pod's init container clones this repo at startup, so **push to GitHub first, then restart**.

```bash
git add -A && git commit -m "<message>" && git push
kubectl rollout restart deployment/utah-public-lands -n biodiversity
kubectl rollout status  deployment/utah-public-lands -n biodiversity
```

- App/Service/Deployment name: `utah-public-lands`; namespace: `biodiversity`; host: `utah-public-lands.nrp-nautilus.io`.
- The LLM proxy key is injected server-side from the shared `open-llm-proxy-secrets` secret (browser never sees it); the `llm` block in `layers-input.json` is only used for local dev / GitHub Pages fallback.

See the [deployment guide](https://boettiger-lab.github.io/geo-agent/docs/guide/deployment) for details.
