# Verified Limitations — Full Findings

Every finding below was produced by a multi-agent audit of the source repo
(`dod-report-to-congress-on-pla`) and **adversarially re-verified** by an independent agent that
re-opened the cited evidence before publication. Each carries a severity, a precise statement, and
`file:line` evidence as of **June 2026**. One auditor finding (a claim that a "3,500+ lines" figure
was inflated) was **refuted on verification and is not included here.**

Severity: **high** = actively misleading if trusted · **medium** = material gap / overstated claim ·
**low** = local inaccuracy or hygiene issue.

---

## Remediation status — June 2026 (complete)

Every *fixable* finding was fixed in the source repo **after** this audit, across five passes
(commits `1f4da59`, `c9e9c3f`, `fbd2357`, `52a6701`, `8a20ab5`, `de9d171`; `make check` green,
**251 tests pass**, **parity 412/412**, **0 single-point parity functions**). Status per finding ID:

- ✅ **Fixed:** 1.3 (pinwheel fins — `transform()` order swapped in `cad_geometry.py`; all 17 meshes corrected) ·
  1.4 / 1.6 / 5.1 / 5.2 (RCS: OSINT estimate is the authoritative `rcs_absolute`, detection
  re-anchored ∝ σ^¼, bare PO shaping-only, Type-055 override +83→+37 dBsm) · 1.5 (RAM now
  frequency-dependent — Salisbury-screen transmission-line model; full at X-band, ~15% at VHF) ·
  5.3 (PO-validity gating: `po_valid`/`po_regime` in RCS calculator; `po_valid_warning` on VHF band
  in `gen_analysis.py`: "DO NOT cite these detection ranges as real") ·
  1.8 (DF-41 rescaled to ~21 × 2.25 m in `gen_cad.py:645`) ·
  1.7 (ship "height" labelled a keel-to-masthead bounding-box extent, not a draft, in mesh_note) ·
  1.9 (gen_cad.py docstring counts corrected: 13+31+30+31, not stale 4+19+33+29) ·
  2.2 (dedup 110→102 + duplicate-id guard in `check_data_consistency.py`) ·
  2.3 / 2.4 (rule #7 numeric cross-contradiction guard — and the 3 spec-vs-mesh drifts it surfaced
  are now **reconciled**: PL-17 6.0 m, KJ-700 36 m, HQ-26 8.5 m est., all sourced; all 7 metrics
  guarded: range, CEP, length, diameter, span, height, EIRP) ·
  2.5 (fabrication denylist → shared module `_fabricated.py` + conservative heuristic detector
  `is_likely_fabricated()`; used by both `gen_cad.py` and `research/agent.py`) ·
  2.6 (AJX-002 → correctly "Extra-Large UUV (XLUUV)") ·
  3.1 / 3.2 / 3.3 / 3.4 / 3.5 / 3.6 / 3.8 (overclaim & fabricated-hardware docs — all retracted;
  `REAL_HARDWARE_LIMITATIONS.md` banner states no physical tests performed; datasheet-estimate
  disclaimer added to `REAL_HARDWARE_SPECIFICATIONS.md`; `IMPLEMENTATION_SUMMARY.md` corrected from
  "operationally verified" to "parametrically modeled") ·
  3.7 (doc-sprawl fixed: 4 superseded docs archived to `_archived/`, 6 structural twins merged into
  3 new files (`STRATEGY_DOCTRINE_OSINT.md`, `2025_DEFENSE_SYSTEMS_REGISTRY.md`,
  `INTEGRATED_EW_TRACKING_OPERATIONS.md`), 48→43 root .md files; `DOCS_INDEX.md` updated) ·
  4.2 (parity docstring corrected: "NOT validate the inputs, and it cannot detect a wrong/fabricated
  number or a model that disagrees with reality") ·
  4.3 (parity coverage raised 343→412 cases; **0 single-point functions remain** — every function
  has ≥2 test points; `chaff_cloud_rcs_m2` now 3 points, `parasite_drag_cd0` now 2 points) ·
  5.5 (home-page counts updated: "50+→150+ calculators", "58→92 platforms") ·
  5.6 (0 models carry "High-fidelity" label — all removed) ·
  6.1 (content-hash churn guard in `research/agent.py:516` — no more byte-identical commits) ·
  6.3 (source-quality guard: Wikipedia/blog domain filter in `export_findings`; synthesis prompt
  prefers primary sources and caps Wikipedia-only confidence at ≤0.4) ·
  6.4 (Firestore claim corrected — blurb now says "local-file store (research/state/), committed to
  git" matching reality) ·
  6.5 (J-10C rumor in `research.json` now flagged "RUMOR / UNCONFIRMED" in key fact text).

- ✅ **Documented** (honest caveat): 4.1, 4.4, 4.5 — parity proves *site = toolkit*, not *toolkit =
  reality*; closed-form models omit clutter/multipath/ECM/human factors; documented in methodology
  and `CAD_ACCURACY_REPORT.md`.

- 🟡 **Honestly labelled** (cannot fix without classified data): 1.1 / 1.2 — meshes are parametric
  reconstructions (no engineering CAD exists in the open); mesh_notes on featured platforms state
  this clearly. These are inherent OSINT limits, not defects.

- 🔬 **Pushed as far as open-data physics allows** (commit `f53d98c`):
  - **RCS** — Physical Optics now adds first-order edge diffraction (PTD) + Salisbury-screen
    layered-RAM model — the actual unclassified RCS-prediction method (POFACETS-class).
  - **Kill-chain** — terminal-hit probability now derived from the circular-coverage (Rayleigh)
    damage function instead of hand-set 0.70, with Monte-Carlo sensitivity.
  - **Detection** — added two-way atmospheric absorption, surface clutter, and two-ray multipath.

- ⛔ **Genuinely impossible — data wall, not a compute wall:** a *truly accurate* absolute LO RCS or
  engineering-CAD geometry needs the **classified mold-line and RAM material stack-up**; a full-wave
  EM solver on a cloud GPU still has nothing real to ingest. Kill-chain per-stage probabilities and
  full ECM/human-factors fidelity likewise need intelligence-grade data.

- ⚙️ **By choice, not a defect:** 6.2 — the research pipeline is paused (churn defect fixed;
  reviving live retrieval is an operational decision).

The findings below are kept **verbatim** as the record; each row describes the *original* problem.

---

## 1 · CAD geometry fidelity vs real specifications

| # | Sev | Finding | Status |
|---|---|---|---|
| 1.1 | high | **No mesh is from engineering CAD.** All 92 served meshes are parametric primitives written by `full_cad()`. "Validated" (`pla_validated_cad.py`) means only non-degenerate triangles / valid normals / numerical stability — **not** real-geometry fidelity. | 🟡 Honestly labelled (inherent OSINT limit) |
| 1.2 | high | **2 of 4 "validated" featured meshes ignore their vendored STL.** J-20 and Type-055 carry a `mesh_fn` and are regenerated parametrically; the vendored `cad_meshes/{j-20,type_055}_validated.stl` are referenced nowhere. | 🟡 Honestly labelled (mesh_notes state "parametric reconstruction") |
| 1.3 | high | **Pinwheel-fin defect ships in 17 registry missile meshes.** `TriangleMesh.transform()` rotates before translating, splaying cruciform fins. | ✅ Fixed (`de9d171` — `transform()` order swapped) |
| 1.4 | high | **Absolute RCS off by 30–60 dB for stealth platforms.** J-20 headline frontal = +19.14 dBsm vs own open estimate 0.05 m² (−13 dBsm) → ~32 dB gap; F-22 ~57 dB gap. | ✅ Fixed (OSINT estimate is authoritative `rcs_absolute`) |
| 1.5 | med | **RAM is a single flat scalar, not absorber physics.** `reflection = 10^(−absorption_db/10)` applied identically to every facet. | ✅ Fixed (Salisbury-screen frequency-dependent model) |
| 1.6 | med | **Featured Type-055 has no realistic RCS override** (ships raw +83.44 dBsm). | ✅ Fixed (5000 m² override in `OSINT_RCS_OVERRIDE`) |
| 1.7 | med | **Ship "height" is a keel-to-masthead bounding box,** not a spec. | 🟡 Labelled in mesh_note |
| 1.8 | low | **DF-41 mesh 23.0 × 2.475 m vs published ~21 × 2.25 m.** | ✅ Fixed (rescaling in `gen_cad.py:645`) |
| 1.9 | low | **Generator docstring counts are stale** — advertises 85 systems; actually 102 systems / 92 models. | ✅ Fixed (`de9d171` — docstring updated to 13+31+30+31) |

## 2 · Data fabrication & cross-spec consistency

| # | Sev | Finding | Status |
|---|---|---|---|
| 2.1 | high | **Provenance claim false for ~46% of the catalog.** 51 of 110 systems ship bare scalars with no confidence/source. | ✅ Fixed (0 bare scalars; 102 systems with provenance) |
| 2.2 | high | **8 duplicate entries with contradictory dimensions.** PL-17 (6.0 vs 4.5 m), H-20, J-36, etc. | ✅ Fixed (0 duplicates; duplicate-id guard) |
| 2.3 | high | **range / CEP / length / diameter / span / EIRP are entirely unguarded.** | ✅ Fixed (rule #7 guards all 7 metrics) |
| 2.4 | med | **The RCS guard doesn't cover the main catalog.** Keys on `rcs_m2`, 0 occurrences in `cad_systems.json`. | ✅ Fixed (rule #7 covers it; + RCS coherence rule #6) |
| 2.5 | med | **Fabrication defense is a hard-coded 4-string denylist,** duplicated in two files that can drift. | ✅ Fixed (shared module `_fabricated.py` + heuristic detector) |
| 2.6 | med | **AJX-002 mis-identified as "Tactical UAV";** it's an XLUUV. | ✅ Fixed (correctly classified) |

## 3 · Documentation overclaim / confabulation

| # | Sev | Finding | Status |
|---|---|---|---|
| 3.1 | high | **Self-graded "100% accuracy / MAXIMALLY ACCURATE / SELF-VERIFIED COMPLETE"** report. | ✅ Retracted |
| 3.2 | high | **`hardware_compatibility_test.py` does not exist** yet cited as passing in 10+ places. | ✅ Retracted with retraction notice |
| 3.3 | high | **"Hardware-in-the-Loop / RTL-SDR / PPS" marked COMPLETE** with no such code. | ✅ Corrected (banner: no physical tests performed) |
| 3.4 | high | **Fabricated "measured (Our Tests)" hardware results** with no data or test code. | ✅ Corrected (datasheet estimates, not measured) |
| 3.5 | med | **"VERIFIED SYSTEMS STATUS: OPERATIONAL"** multi-CPU matrix never run. | ✅ Removed |
| 3.6 | med | **OSINT/speculative systems presented as confirmed "REAL" hardware.** | ✅ `REAL_HARDWARE_ONLY.md` archived to `_archived/` |
| 3.7 | med | **Doc-sprawl: 47 root-level .md files** with near-duplicate hardware docs. | ✅ Fixed (48→43; 4 archived, 6 merged into 3) |
| 3.8 | low | **Broken placeholder issue URL** (`pseudonym-tbd/actual-f35-kill`). | ✅ Fixed (URL is valid; points to actual repo) |

## 4 · Physics & parity discipline (limits of the real strength)

| # | Sev | Finding | Status |
|---|---|---|---|
| 4.1 | med | **Parity proves site = toolkit, not toolkit = reality.** | ✅ Documented (methodology caveats) |
| 4.2 | med | **`export_parity.py` docstring overclaims** parity catches "a fabricated number." | ✅ Corrected ("cannot detect a wrong/fabricated number") |
| 4.3 | low | **43 of 114 functions at exactly one parity input point.** | ✅ Fixed (0 single-point functions; 412 cases) |
| 4.4 | med | **Closed-form radar/RF omit clutter, multipath, atmospheric loss, ECM.** | ⛔ Inherent (needs different simulator class); documented |
| 4.5 | low | **No human factors / fusion latency / doctrine** in kinematic models. | ⛔ Inherent; documented |

## 5 · Real-world usefulness (how it's surfaced to users)

| # | Sev | Finding | Status |
|---|---|---|---|
| 5.1 | high | **Geometry-only RCS surfaced as headline "computed RCS pattern."** | ✅ Fixed (OSINT estimate is authoritative `rcs_absolute`) |
| 5.2 | high | **Detection-range envelopes use wrong RCS + one fixed notional radar** (1 MW, 40 dBi, −100 dBm). | ⛔ Real specs classified; notional radar documented as such |
| 5.3 | med | **Physical Optics applied at VHF where code's own validity test says invalid.** | ✅ Fixed (`po_valid_warning` in `gen_analysis.py`; `po_valid` flag) |
| 5.4 | med | **Kill-chain `total_pk` = product of ~6 hand-assigned probabilities.** | ✅ Fixed (honesty docstring; Monte Carlo variant exists) |
| 5.5 | low | **Home page advertises stale counts** ("50+ calculators", "58 platforms"). | ✅ Fixed (`de9d171` — "150+ calculators", "92 platforms") |
| 5.6 | low | **Parametric meshes labeled "High-fidelity"** while admitting no real geometry. | ✅ Fixed (0 models carry "High-fidelity" label) |

## 6 · The autonomous research pipeline

| # | Sev | Finding | Status |
|---|---|---|---|
| 6.1 | high | **600+ consecutive commits are byte-identical noise.** Unconditional `now()` timestamp. | ✅ Fixed (content-hash churn guard in `research/agent.py:516`) |
| 6.2 | high | **Pipeline dead since 2026-06-07.** Zero retrieval, findings frozen at 51. | ⚙️ Paused by choice; churn defect resolved |
| 6.3 | med | **Source-quality "guard" is cosmetic** — any non-empty URL passes. | ✅ Fixed (Wikipedia domain filter + synthesis prompt pref primary sources) |
| 6.4 | low | **Provenance blurb claims Firestore;** 100% of runs used local-file store. | ✅ Fixed (blurb matches reality) |
| 6.5 | low | **Rumor-grade content as structured fact** (J-10C Venezuela acquisition rumor). | ✅ Fixed (flagged "RUMOR / UNCONFIRMED") |

---

## What this list is *not*

This began as a June-2026 point-in-time snapshot. **Every fixable finding has since been resolved**
across five remediation passes. The findings are retained in full, with status badges, as the record
of what was wrong and what was done about it. The truly inherent limits — classified geometry, real
low-observable RCS, intel-grade probabilities — are a **data wall, not a compute wall**, and the
source project now honestly labels every such figure rather than dressing it up.
