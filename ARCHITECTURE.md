> **Reproduced as supporting context** from the private source project (`dod-report-to-congress-on-pla`).
> See [README.md](README.md) and [LIMITATIONS.md](LIMITATIONS.md) for the independent reality-check this repo exists to provide.

---

# Architecture

A physics-grounded **systems engineering and analysis platform** for the People's Liberation
Army's offensive weapons and kill chains. Uses the **2025 DoD Annual Report to Congress on the
PRC** as an order-of-battle source — not a narrative authority — and layers on computed kill-chain
physics, radar cross-section from geometry, and doctrine-to-system mapping. **UNCLASSIFIED //
CONCEPTUAL // PUBLIC RELEASE** — OSINT for engineering analysis, not operational guidance.

Live: https://dod-congress-on-pla.web.app · https://defense-osint.org/cad

## Shape of the repo

```
  osint_cad/            Physics-grounded CAD/RCS/kill-chain toolkit (the single source of truth).
                          ├── analysis/        closed-form equation functions (114 in the Methodology reference)
                          ├── geometry/         mesh primitives, Physical-Optics RCS
                          ├── physics/          RF propagation (ITU-R), RCS models, VLBI
                          ├── sensors/          signal processing, TDOA/FDOA geolocation
                          ├── targeting/        ballistic/HGV kinematics, BVR link budgets
                          ├── platforms/        PLA weapon systems (primary) + US adversary models
                          ├── engagements/      PLA offensive kill chains, BVR, ASBM, HGV, SEAD
                          ├── doctrine/         PLA offensive strategy/doctrine (OSINT, primary) + US adversary concepts
                          └── util/             calculation logging
tests/                pytest suite (250+ tests; parity/regression/physics).
scripts/              Pipeline: generators (gen_*) write web/public/data + web/public/models;
                        guards (check_*) validate; render_stl/preview_aircraft are visual QA.
web/                  Angular 20 app (standalone components, signals, zoneless). Deployed to
                        Firebase Hosting. Reads the generated JSON at runtime.
web/src/app/core/
  osint-physics.ts    1:1 TS port of calculators.py (parity-checked, 349/349).
data/                 fact_checks.json, proposed_systems.json
cad_meshes/           Validated reference STL meshes.
extracted/            Parsed source-report content (sections, figures, enrichment).
*.md                  CAD analysis documents (F-22, F-35, J-20, etc.).
Makefile              One-command local build/deploy.
pyproject.toml        Package config — `pip install -e .` for editable install.
```

## Data flow

```
osint_cad (Python)
   │  scripts/gen_cad.py        → web/public/data/cad_{models,systems,strategy,index}.json
   │                             + web/public/models/*.stl   (content-hash ?v= cache-buster)
   │  scripts/gen_analysis.py   → web/public/data/cad_analysis.json   (engagement analyses)
   │  scripts/build_web_data.py → document/sections/figures/enrichment.json (+ S3 asset URLs)
   ▼
web/src/app/core/osint-physics.ts   1:1 TS port of calculators.py (parity-checked)
   │  scripts/gen_methodology.mjs    → web/public/data/methodology.json  (equations reference)
   │  scripts/gen_workbench_index.mjs→ web/public/data/workbench_index.json (search index)
   ▼
Angular app  →  DataService / CadService load the JSON  →  pages render
```

The **physics is defined once** (`calculators.py`) and mirrored in the browser
(`osint-physics.ts`). `scripts/check_parity.mjs` esbuild-compiles the real TS and asserts every
case matches the Python to displayed precision — so a number on the site is always exactly what
the physics says. The `/methodology` page is generated from that same TS, so the documented
equations cannot drift from the running code.

## Front-end (web/src/app)

- `core/` — `DataService` (report content), `CadService` (systems/models/analysis), the
  `osint-physics.ts` engine, typed `models.ts` / `cad-models.ts` contracts, search + analytics.
- `pages/` — Home, Read, Dashboard, **CAD Lab** (PLA offensive catalog + 3D viewer + analyst Workbench),
  **Methodology**, Figures, Source, Search.
- `shared/` — reusable components: `model3d` (three.js STL/glTF viewer), `chart`, `killweb`, icons.

## Integrity guards (run before any data/mesh/physics commit)

| guard | asserts |
|---|---|
| `check_parity.mjs` | TS physics == Python physics, 349/349 cases |
| `test_physics.mjs` | physics unit tests, 90 passing (114 fns exported) |
| `check_geometry.py` | fighter mesh part placement (engines internal, fins attached, …) |
| `check_platforms.py` | ship/sub/missile components attached to the body |
| `check_stl_sync.py` | every model's served `?v=` hash matches its STL bytes |
| `check_data_consistency.py` | cross-section spec values agree; no fabricated/contradictory data |
| `check_research.py` | autonomous research findings are sourced + not mis-identified |
| `check_method_links.mjs` | all 81 method drawer deep-links resolve |
| `check_kc_alignment.mjs` | all 23 analysis deep-link keys are index-aligned |
| `check_exports_fresh.mjs` | CSV exports match source data |
| `gen_doc_stats.mjs --check` | README/ARCHITECTURE trust-counts match the data |
| `check_tracked.mjs` | no reproducibility-critical file (source / STL / dataset / config) is left untracked |
| pytest `tests/` | 250+ physics/regression/doctrine tests (`make test`) |

## Build & deploy — **no CI**

GitHub Actions was removed (account billing). Build and deploy run **locally** via the Makefile:

```
make setup     # once: python venv + npm install
make all       # regenerate data, run all guards, build
make ship      # all + deploy to Firebase Hosting (production)
make preview   # build + serve locally with SPA fallback at :8100
```

Deploying touches production hosting directly — do it deliberately. The hourly autonomous cloud
routine commits verified data/code to the `cad-lab-and-interactive-site` branch but **does not
deploy** (its sandbox has no Firebase credentials); a maintainer runs `make ship` to publish.

## Provenance discipline

Never assert a figure that isn't computed by `osint_cad` (Python) or the parity-checked
`osint-physics.ts`, or sourced with a confidence/limitation. Generators carry per-value source +
confidence; the catalog surfaces them; `check_data_consistency.py` + `check_research.py` are the
backstops. Dimensions matched to published figures; classified programs flagged as estimates.
