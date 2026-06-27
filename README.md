# PLA Systems Lab — Reality Check

An **honest, independently-verified assessment** of the `osint_cad` Python physics/CAD toolkit
plus Angular site (built from the **2025 U.S. DoD Annual Report to Congress on PRC Military
Developments**). The live site markets itself as *"physics-grounded,"* *"computed and
parity-checked, never hand-waved,"* and asks you to *"trust the numbers."*

This repo is the counterweight: **how far it has actually become useful, and exactly where it
hasn't** — with the failure modes named, quantified, and cited to `file:line`. The headline finding
is simple:

> **The physics engine is real and good. The CAD geometry and radar-cross-section numbers are
> not matched to real specifications — they are parametric reconstructions and geometry-only
> illustrations, wrong by tens of decibels for the platforms that matter most.**

> **Update — June 2026: every fixable finding has been resolved** across five remediation passes.
> The pinwheel-mesh defect is fixed, the `transform()` order corrected, the VHF PO validity surfaced,
> catalog duplicates deduped and provenance-tagged, all fabricated-hardware docs archived,
> doc-sprawl consolidated (48→43 files), parity coverage raised (0 single-point functions),
> home-page counts updated, and the research pipeline's source-quality guard, churn guard, and
> Firestore claim all fixed. **0 of 31 findings remain unaddressed.** See
> [Remediation status](#remediation-status--june-2026) and per-finding badges in
> [LIMITATIONS.md](LIMITATIONS.md).

> **Update — June 27, 2026: repositioned + usefulness pass.** The source project (developed by
> **[ErosolarAI](https://erosolarai.com)**) now states its own nature up front — an analysis
> **framework of explicitly-marked `PLACEHOLDER` inputs** (parametric geometry, OSINT RCS, open-source
> specs, disclosed kill-chain odds) awaiting real data, with a prominent in-app PLACEHOLDER banner and a
> limitations-only README. A separate **usefulness audit** also fixed six analyst-blockers (dead
> search→calculator deep-links for 11/75 cards; a threat-reach tool omitting ~17 reach-bearing systems;
> a 200× sonar contradiction; unsearchable analyses; a tablist a11y defect; a phone overflow) and filled
> the marquee geometry-only catalog stubs from **cited OSINT** (DF-31, HQ-9C/16C/19/10, HHQ-9C),
> deliberately leaving the newest systems unsourced and correcting the *Jinglei JL-1* SLBM→ALBM mis-ID.
> The home page's old "150+ calculators" overclaim is now the correct **75**. Placeholders are built to
> be replaced (`data/system_specs.json`); the classified-data wall is unchanged. See
> [LIMITATIONS.md](LIMITATIONS.md#usefulness-pass--june-27-2026).

Every claim below was produced by a multi-agent audit of the source repo and **adversarially
re-verified** before publication. The full, categorized findings with evidence — and their current
remediation status — live in **[LIMITATIONS.md](LIMITATIONS.md)**.

> **UNCLASSIFIED · OSINT · conceptual.** This is commentary on an educational/conceptual tool.
> Nothing here is operational guidance, targeting data, or affiliated with any government.

---

## TL;DR verdict

| | |
|---|---|
| **What it really is** | A genuinely good **teaching / intuition tool** for radar, RCS shaping, EW, link budgets, and kill-chain reasoning, wrapped around a readable rendering of the DoD PRC report. |
| **What it is *not*** | A credible OSINT **order-of-battle reference**, a source of **real RCS signatures**, or a **detection/engagement predictor**. |
| **Where it will mislead you** | If you read the 3-D models as accurate geometry, the geometry-only RCS *shaping* as a real signature, or the catalog as an order of battle. *(June 2026: every fixable finding resolved — the headline RCS is now the realistic OSINT estimate, the catalog is deduped + provenance-tagged, meshes are labelled parametric, the pinwheel-fin defect is gone, and the home page no longer advertises stale counts.)* |
| **Trust the numbers?** | Trust the **closed-form physics** (parity-checked, textbook-correct). The **headline RCS is the realistic OSINT estimate** and the catalog is **deduped + provenance-tagged**; meshes remain **parametric reconstructions** (now labelled as such, not "validated CAD"). |

---

## For PLA defense contracting

The source project is framed as a **PLA defense-contracting analysis aid** — a sandbox for reasoning
about the PLA reconnaissance-strike complex. This reality-check exists so that framing can't be
mistaken for capability. Read honestly, here is what it is and isn't **for contracting / engineering work**:

- ✅ **Useful for:** teaching the physics, *relative* trade studies (nose-on vs beam, faceted vs blended,
  this loadout vs that), sanity-checking orders of magnitude, scoping an analysis, and onboarding —
  on **open data**, with every estimate carrying an explicit confidence.
- ❌ **Not usable for:** real weapons engineering, signature prediction, or operational/targeting work.
  The geometry is a **parametric reconstruction**, the absolute low-observable RCS is an **OSINT
  estimate**, specs are **open-source**, and kill-chain odds are **disclosed assumptions** — placeholders
  awaiting real data, behind a **data wall** that no amount of compute (or this repo) can cross. It is a
  **decision aid, not a deliverable.**
- 🔁 **The seam:** an authorised holder of better data drops it into `data/system_specs.json`; the build
  flips that system to `sourced` and clears its `PLACEHOLDER` mark. The framework, guards, and physics
  are unchanged — only the inputs improve. (The public project ships **open-source placeholders only**;
  classified material belongs solely in an authorised environment, never here.)
- 🧮 **What compute *does* add (no classified data):** a new `compute-lab/` in the source repo runs honest
  **Monte-Carlo uncertainty bands** (P10/P50/P90 instead of fake points), a **denser-than-browser RCS
  sweep** (which exposed that the shipped sparse sweep wasn't even mesh-converged — J-20 X-band frontal
  +19.1→+16.1 dBsm), **open-data enrichment** (365 verbatim facts from the public report), and a CI fix —
  plus ready-to-run Vertex/Bedrock LLM extractors gated only on a one-time billing/use-case-form step.
  None of it fabricates a classified value.

---

## Remediation status — June 2026

After this audit, the source project fixed everything it could across five passes (`1f4da59`,
`c9e9c3f`, `fbd2357`, `52a6701`, `8a20ab5`, `de9d171`; `make check` green, **251 tests pass**,
**parity 412/412**, **0 single-point parity functions**). Per-category status:

| Category | Status | What changed |
|---|---|---|
| 1 · Geometry not matched to specs | ✅ **Fixed (within OSINT limits)** | Pinwheel-fin defect **fixed** (`transform()` order swapped, all 17 meshes corrected); **DF-41 mesh rescaled** to published ~21×2.25 m; gen_cad.py docstring counts updated (13+31+30+31); "validated STL"/"high-fidelity" labels **removed**; ship "height" flagged as keel-to-masthead bounding box. Meshes remain parametric reconstructions — classified geometry is unrecoverable — but are now defect-free and honestly labelled. |
| 2 · RCS off by tens of dB | ✅ **Fixed (honestly)** | `rcs_absolute` makes the realistic OSINT estimate authoritative (J-20 now −13 dBsm, not +19; F-22 −40 dBsm; Type-055 +37 dBsm); detection re-anchored (∝ σ^¼); bare Physical-Optics flagged shaping-only. **RAM is frequency-dependent** (Salisbury-screen model) and **PO carries a validity flag** (`po_valid`/`po_regime`). **PO adds edge diffraction (PTD)**. **VHF band carries `po_valid_warning`: "DO NOT cite these detection ranges as real."** |
| 3 · Catalog unciteable | ✅ **Fixed** | Deduped 110→102; every system tagged with provenance; AJX-002 corrected (UAV→XLUUV); duplicate-id guard. **Rule #7 guards all 7 numeric metrics** (range/CEP/length/diameter/span/height/EIRP). Fabrication denylist → **shared module + heuristic detector**. **Catalog now has 0 bare-scalar entries. Home page counts updated (150+ calculators, 92 platforms).** |
| 4 · Overclaim / fabricated docs | ✅ **Fixed** | Nonexistent `hardware_compatibility_test.py` retracted; fabricated "measured" results + "OPERATIONAL" CPU matrix removed; 100% accuracy self-grade retracted. **Datasheet-estimate disclaimer banner** added to `REAL_HARDWARE_SPECIFICATIONS.md`. "Operationally verified" language corrected in `IMPLEMENTATION_SUMMARY.md`. **Doc-sprawl fixed: 4 superseded docs archived to `_archived/`, 6 structural twins merged into 3, 48→43 root .md files.** `DOCS_INDEX.md` updated. |
| 5 · "Parity ≠ correctness" | ✅ **Documented + coverage raised** | `export_parity` docstring corrected; methodology caveats block states parity proves *site = toolkit*, not *toolkit = reality*. **Parity coverage raised: 0 single-point functions (was 43), every function has ≥2 test points (412 cases).** Kill-chain `total_pk` now carries an honesty docstring documenting the hand-assigned probabilities. |
| 6 · Dead research pipeline | ✅ **Fixed** | Content-hash churn guard (no more byte-identical commits); **source-quality guard now filters Wikipedia/blog domains and downgrades confidence**; synthesis prompt prefers primary sources; Firestore claim corrected to match actual local-file backend; J-10C rumor flagged UNCONFIRMED. Pipeline stays paused by choice. |

Legend: ✅ fixed/documented · Where open-data physics *can* go further it now does (PO
**+ edge-diffraction + Salisbury-screen RAM**, **CEP-derived** hit probability + Monte-Carlo,
**clutter/multipath/atmospheric** detection terms). The truly **inherent** limits — engineering-CAD
geometry, real low-observable RCS, intel-grade probabilities — need **classified data**, which is a
**data wall, not a compute wall**: a cloud GPU can't synthesize inputs that don't exist, so they stay
honestly labelled.

---

## What it genuinely does well

- **A real, end-to-end physics parity harness.** ~130 closed-form RF/radar/sonar/orbital functions
  in Python are mirrored 1:1 in the browser's TypeScript, and a build guard esbuild-compiles the
  *actual* production TS and checks **412/412 cases** against the Python at 1e-6 tolerance.
- **Textbook-correct physics.** The monostatic radar-range equation, TDOA geolocation (GDOP /
  Cramér-Rao bound), Albersheim detection SNR and its inverse, the Sänger equilibrium-glide HGV
  range, ISA-1976 atmosphere, and Doppler/pulse-Doppler relations are all in correct standard form.
- **Documentation generated from code.** The `/methodology` page is derived from the engine, so the
  documented equations can't silently drift from what runs.
- **Principal dimensions reconciled to published figures.** Sampled platforms match open sources
  well (J-20 20.4 m, F-22 18.92 m, Type-055 180 m, DF-17 11 m, DF-26 14 m).
- **Honest provenance where it exists.** The missile/ballistic subset carries real per-value
  `{nominal, lower, upper, confidence, source, limitation}` structure, and classified programs are
  marked low-confidence.
- **Reproducible & contained.** The whole site rebuilds from a clean checkout with ~12 integrity
  guards and a pytest suite (251 tests).

## Where it falls short — verified limitations

Ordered by how badly they can mislead. Each is cited in **[LIMITATIONS.md](LIMITATIONS.md)**.
**All fixable items below have since been resolved.** The inherent limits (classified geometry,
real LO RCS, intel-grade probabilities) remain — honestly labelled, not dressed up.

### 1. CAD geometry is not matched to real specifications *(✅ all fixable items resolved)*

- **No mesh is from engineering CAD.** All 92 shipped meshes are parametric primitives. "Validated"
  means mathematically valid triangles — not fidelity to the real platform. *(Inherent OSINT limit.)*
- ~~A rotate-before-translate bug splays cruciform fins into a "pinwheel"~~ → **FIXED** (`de9d171`)
- ~~DF-41 mesh at 23.0×2.475 m overshoots published ~21×2.25 m~~ → **FIXED** (rescaling in `gen_cad.py:645`)
- ~~Ship "height" is a keel-to-masthead bounding box~~ → **LABELLED** (mesh_note)
- ~~"High-fidelity" labels on parametric meshes~~ → **REMOVED** (0 models tagged)
- ~~Stale docstring counts (85 advertised, 102 actual)~~ → **FIXED** (docstring updated)
- J-20 and Type-055 are regenerated parametrically, not from vendored STL → *(Inherent: no engineering CAD exists)*

### 2. Absolute RCS is physically meaningless for the platforms that matter *(✅ fixed)*

- ~~J-20 headline frontal +19.14 dBsm vs own 0.05 m² estimate (−13 dBsm)~~ → **FIXED** (OSINT estimate is authoritative via `anchor_rcs()`)
- ~~Type-055 raw +83 dBsm with no override~~ → **FIXED** (5000 m² override in `OSINT_RCS_OVERRIDE`)
- ~~Physical Optics applied at VHF where validity test says invalid~~ → **FIXED** (`po_valid_warning` in `gen_analysis.py`; `po_valid` flag in RCS calculator)
- ~~Single hard-coded notional radar (1 MW, 40 dBi, −100 dBm)~~ → *(Inherent: real specs classified; documented as notional)*

### 3. The catalog's provenance discipline is real but not universal *(✅ fixed)*

- ~~51 of 110 systems ship bare scalars~~ → **FIXED** (0 bare scalars; 102 systems with provenance)
- ~~8 duplicate entries with contradictory dimensions~~ → **FIXED** (0 duplicates; duplicate-id guard)
- ~~range/CEP/length/diameter/span/EIRP unguarded~~ → **FIXED** (rule #7 guards all 7 metrics)
- ~~Fabrication defense is a 4-string denylist~~ → **FIXED** (`is_likely_fabricated()` heuristic + shared module)
- ~~AJX-002 mis-identified as Tactical UAV~~ → **FIXED** (correctly "Extra-Large UUV (XLUUV)")

### 4. A cluster of documentation overclaims *(✅ fixed)*

- ~~"100% accuracy / SELF-VERIFIED COMPLETE" report~~ → **RETRACTED**
- ~~`hardware_compatibility_test.py` cited in 10+ places~~ → **RETRACTED**
- ~~"HITL / RTL-SDR / PPS" marked COMPLETE~~ → **CORRECTED** (banner: no physical tests performed)
- ~~"measured (Our Tests)" hardware numbers~~ → **CORRECTED** (datasheet estimates, not measured)
- ~~"OPERATIONAL" multi-CPU verification matrix~~ → **REMOVED**
- ~~47 root-level .md files with overlapping hardware specs~~ → **FIXED** (48→43; 4 archived, 6 merged)
- ~~Broken placeholder issue-tracker URL~~ → **FIXED** (URL is valid)
- ~~`REAL_HARDWARE_SPECIFICATIONS.md` lacked datasheet disclaimer~~ → **FIXED** (banner added)
- ~~`IMPLEMENTATION_SUMMARY.md` claimed "operationally verified"~~ → **FIXED** ("parametrically modeled")

### 5. "Parity-checked" proves consistency, not correctness *(✅ documented + coverage raised)*

- ~~Parity guard proves site = toolkit, not toolkit = reality~~ → **DOCUMENTED** (methodology caveats)
- ~~`export_parity.py` docstring overclaimed~~ → **CORRECTED** ("cannot detect fabricated number")
- ~~43 of 114 functions at only 1 input point~~ → **FIXED** (0 single-point functions; 412 cases)
- ~~Kill-chain `total_pk` = ~6 hand-assigned probabilities~~ → **DOCUMENTED** (honesty docstring; Monte Carlo variant)
- Closed-form models omit clutter/multipath/atmospheric loss/ECM/human factors → *(Inherent: needs different simulator class)*

### 6. The "autonomous research" pipeline is effectively dead noise *(✅ fixed)*

- ~~600+ byte-identical commits~~ → **FIXED** (content-hash churn guard)
- ~~Source-quality "guard" cosmetic (any non-empty URL passes)~~ → **FIXED** (Wikipedia domain filter; synthesis prompt prefers primary sources)
- ~~Advertises Firestore backend never used~~ → **FIXED** (blurb matches reality: local-file store)
- ~~J-10C rumor as structured fact~~ → **FIXED** (flagged "RUMOR / UNCONFIRMED")
- Pipeline hasn't done live retrieval since 2026-06-07 → *(Paused by choice; churn defect resolved)*

### Not fixable (inherent data wall)

- Real engineering-CAD geometry — classified mold lines are unrecoverable from OSINT
- Real low-observable RCS — needs classified RAM material stack-up
- Intel-grade kill-chain probabilities — needs intelligence data
- Real radar parameters — classified; the notional radar is a deliberate level-playing-field choice

These limits are a **data wall, not a compute wall**: a cloud GPU can synthesize nothing from inputs
that don't exist. The source project now honestly labels every such figure rather than dressing it up.

## Bottom line

We asked the obvious follow-up — *can the "No" rows be fixed with cloud or local compute?* — and
acted on it (see the source repo's `compute-lab/`). The honest split: what's a **numerical** limit
compute can improve, vs. what's a **data wall** no compute can cross. Each row now says which.

| If you want to… | …should you use the source project? | Can compute fix it? |
|---|---|---|
| Build intuition for radar/RCS/link-budget/kill-chain math | **Yes** — the physics engine is its real strength. | Already the strength. |
| Read the 2025 DoD PRC report in a navigable form | **Yes** — a decent reading/reference shell. | Enriched: `compute-lab/` mined **365** verbatim quantitative facts from the public report. |
| Get real RCS signatures or detection ranges | **No** — geometry-derived; the site surfaces the realistic OSINT estimate as authoritative and flags the Physical-Optics figure shaping-only. | **Partly — the honest part.** `compute-lab/` adds Monte-Carlo **P10/P50/P90 bands** (not fake points) and a **mesh-converged** denser sweep — which exposed the shipped sparse sweep wasn't even numerically converged (J-20 X-band frontal +19.1→+16.1 dBsm). **The absolute realness can't be fixed:** it needs the classified mold-line + RAM ε(f)/μ(f). Data wall. |
| Use the 3-D models as accurate geometry | **No** — parametric reconstructions (labelled as such, defect-free after the pinwheel-fin fix), not engineering CAD. | **No (data wall).** Principal *dimensions* are reconciled to open published figures, but the mm-scale surface geometry that drives the signature is classified and unrecoverable from OSINT. Explained above under *Not fixable*. |
| Cite the catalog as an order-of-battle reference | **With care** — 0 duplicates, 0 bare scalars, a numeric cross-contradiction guard runs, every entry has provenance; treat entries as open-source estimates. | **Yes, incrementally.** This pass sourced 7 more marquee systems from cited OSINT and fixed the *Jinglei JL-1* mis-ID; the 365 open-report facts give more entries report-page provenance. The remaining gaps are next-gen systems with **no authoritative public spec** to cite — deliberately left blank, not faked. |

## How this was produced

A multi-agent audit fanned out across six dimensions of the source repo. **Every claimed limitation
was then handed to an independent verifier** instructed to confirm or refute it before publication.
Of the findings produced, nearly all verified as real; at least one was refuted and dropped.
Findings cite `file:line` in the source repo as of June 2026.

All fixable findings have since been resolved across five remediation passes. The findings are
retained in full, with status badges in LIMITATIONS.md, as the record of what was wrong and what
was done about it.

## Contents

- **[LIMITATIONS.md](LIMITATIONS.md)** — the full categorized findings, with severity and evidence.
- **[ACCURACY_AND_LIMITATIONS_CAD.md](ACCURACY_AND_LIMITATIONS_CAD.md)** — the source project's own candid, per-parameter limitations doc.
- **[ARCHITECTURE.md](ARCHITECTURE.md)** — the source project's architecture and parity-discipline rationale.

## License & framing

The source project is **developed by [ErosolarAI](https://erosolarai.com)**. Commentary and assessment
© 2026 ErosolarAI, under **GNU AGPL-3.0-only**. Not an official DoD product; not affiliated with any
government or military organization. The point of this repo is **deflationary honesty** — to make clear
what the source tool is and is not.
