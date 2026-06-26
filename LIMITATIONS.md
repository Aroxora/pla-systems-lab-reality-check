# Verified Limitations — Full Findings

Every finding below was produced by a multi-agent audit of the source repo
(`dod-report-to-congress-on-pla`) and **adversarially re-verified** by an independent agent that
re-opened the cited evidence before publication. Each carries a severity, a precise statement, and
`file:line` evidence as of **June 2026**. One auditor finding (a claim that a "3,500+ lines" figure
was inflated) was **refuted on verification and is not included here.**

Severity: **high** = actively misleading if trusted · **medium** = material gap / overstated claim ·
**low** = local inaccuracy or hygiene issue.

---

## Remediation status — June 2026

Most findings were fixed in the source repo **after** this audit (commits `1f4da59`, `c9e9c3f`,
`fbd2357`; the project's `make check` guards + 251 tests pass on the fixes). Status per finding ID:

- ✅ **Fixed:** 1.3 (pinwheel fins, 12 meshes regenerated) · 1.4 / 1.6 / 5.1 / 5.2 (RCS: realistic
  OSINT estimate is now the authoritative `rcs_absolute`, detection re-anchored, bare PO flagged
  shaping-only, Type-055 override) · 2.2 (dedup 110→102 + duplicate-id guard) · 2.6 (AJX-002 → XLUUV) ·
  3.1 / 3.2 / 3.3 / 3.4 / 3.5 / 3.6 / 3.8 (overclaim & fabricated-hardware docs retracted, URL fixed) ·
  4.2 (parity docstring) · 5.5 (home-page counts) · 6.1 / 6.3 / 6.4 / 6.5 (research churn guard,
  source-tier gate, Firestore-lie, rumor flagged).
- ✅ **Documented** (honest caveat now shipped in methodology): 4.1, 4.4, 4.5.
- 🟡 **Mitigated / honestly labelled** — the underlying limit is *inherent* (classified geometry and
  real LO signatures can't be computed), so the fix is truthful labelling: 1.1 / 1.2 / 1.9 (meshes are
  parametric reconstructions, now labelled as such rather than "validated CAD") · 2.1 (every system now
  carries a `provenance` flag: sourced vs unsourced) · 4.3 / 5.3 / 5.6 (documented, coverage/wording
  not expanded).
- ⚪ **Still open:** 1.5 (scalar RAM) · 1.7 (ship "height" bounding box) · 1.8 (DF-41 over-size) ·
  2.3 / 2.4 (range/CEP/EIRP cross-checks still unguarded) · 2.5 (hard-coded fabrication denylist) ·
  3.7 (47-file doc-sprawl) · 5.4 (kill-chain hand-assigned probabilities) · 6.2 (pipeline still paused —
  the churn is fixed but it does no new retrieval).

The findings below are kept **verbatim** as the record; each row describes the *original* problem.

---

## 1 · CAD geometry fidelity vs real specifications

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 1.1 | high | **No mesh is from engineering CAD.** All 92 served meshes are parametric primitives written by `full_cad()`. "Validated" (`pla_validated_cad.py`) means only non-degenerate triangles / valid normals / numerical stability — **not** real-geometry fidelity. | `gen_cad.py:520`, `pla_validated_cad.py:5,9-13` |
| 1.2 | high | **2 of 4 "validated" featured meshes ignore their vendored STL.** J-20 and Type-055 carry a `mesh_fn` and are regenerated parametrically (`_type055_hull_mesh → build_ship({length:180,beam:20})`); the vendored `cad_meshes/{j-20,type_055}_validated.stl` are referenced nowhere. | `gen_cad.py:270-272,520`; grep = 0 code refs |
| 1.3 | high | **Pinwheel-fin defect ships in 17 registry missile meshes.** `TriangleMesh.transform()` rotates before translating, and fin loops mount with translate-then-rotate, splaying cruciform fins. (Hi-fi aircraft & platform paths are correctly fixed.) | `cad_geometry.py:265,269`; `pla_systems_cad.py:575-576,1170-1171,1524-1525,1981-1982` |
| 1.4 | high | **Absolute RCS off by 30–60 dB for stealth platforms.** J-20 headline frontal = +19.14 dBsm (~82 m²) after RAM knockdown, vs the repo's own open estimate 0.05 m² (−13 dBsm) → ~32 dB gap; F-22 ~57 dB gap. PO specular-only can't capture edges/intakes/RAM. | `cad_models.json` rcs_aspect vs `hifi_aircraft.py:437,439` |
| 1.5 | med | **RAM is a single flat scalar, not absorber physics.** `reflection = 10^(−absorption_db/10)` applied identically to every facet regardless of aspect/polarization. | `cad_rcs_calculator.py:75,201` |
| 1.6 | med | **Featured Type-055 has no realistic RCS override** (ships raw +83.44 dBsm ≈ 2.2×10⁸ m², `osint_rcs=None`) while comparable combatants (US DDG-51, PLA Type-052D/003) get realistic overrides — inconsistent methodology. | `cad_models.json` Type-055 vs DDG-51/052D |
| 1.7 | med | **Ship "height" is a keel-to-masthead bounding box,** not a spec — conflates sub-deck hull depth + superstructure + masts (Type-055 "35.2 m"). | `gen_cad.py:215` |
| 1.8 | low | **Some missile dimensions exceed published values.** DF-41 mesh 23.0 m × 2.475 m vs commonly published ~21–22 m × ~2.25 m (~10–24% high). | `cad_models.json` DF-41 mesh |
| 1.9 | low | **Generator docstring counts are stale** — advertises 85 systems / 23 mesh-backed; shipped data is 110 systems / 92 meshes. | `gen_cad.py:6-9` vs `cad_systems.json` count |

## 2 · Data fabrication & cross-spec consistency

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 2.1 | high | **Provenance claim false for ~46% of the catalog.** README says nothing ships as fact unless sourced-with-confidence, but **51 of 110 systems** ship bare scalars with no confidence/source string. | `README.md:13,42` vs `cad_systems.json` |
| 2.2 | high | **8 duplicate entries with contradictory dimensions** the consistency check can't see: PL-17 (6.0 vs 4.5 m), H-20, J-36, YJ-12B, KJ-700, SLC-7, WZ-8 (span 6.7 vs 5.0 m), HQ-26 (7.2 vs 8.5 m). 110 entries, 102 unique ids. | `cad_systems.json` systems array |
| 2.3 | high | **range / CEP / length / diameter / span / EIRP are entirely unguarded.** The only cross-source numeric rule is RCS (>10% spread); everything else can silently disagree. | `check_data_consistency.py:78-114` |
| 2.4 | med | **The RCS guard doesn't even cover the main catalog.** Rule #6 keys on `rcs_m2`, a key that appears **0 times** in `cad_systems.json` (which uses `rcs_profile`/`rcs_aspect`). | `check_data_consistency.py:94` |
| 2.5 | med | **Fabrication defense is a hard-coded 4-string denylist** (`YJ-15/17/19/20`), not a detector, and is duplicated in two files that can drift. | `agent.py:464`, `gen_cad.py:1077` |
| 2.6 | med | **Mis-identification in thin entries.** AJX-002 stored as a "Tactical UAV" (drone, span 5.0 m) — it is the extra-large **unmanned underwater** vehicle (XLUUV). | `cad_systems.json` AJX002 |

## 3 · Documentation overclaim / confabulation

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 3.1 | high | **Self-graded "100% accuracy / MAXIMALLY ACCURATE / SELF-VERIFIED COMPLETE"** report signed by "Claude AI Agent SDK" — directly contradicts the candid limitations doc. *(Retracted in the source repo at our prompting.)* | `CAD_ACCURACY_REPORT.md:4-5` (former) |
| 3.2 | high | **`hardware_compatibility_test.py` does not exist** yet is cited as a passing "23+ test" harness in 10+ places, with fabricated "expected output." | `HARDWARE_VERIFICATION.md:148,381,…`; file absent |
| 3.3 | high | **"Hardware-in-the-Loop / RTL-SDR / SoapySDR / PPS" marked COMPLETE** with no such code anywhere in the repo. | `CAD_ACCURACY_REPORT.md:148-173` (former) |
| 3.4 | high | **Fabricated "measured (Our Tests)" hardware results** — 1PPS jitter "15–30 ns RMS", "24-hour continuous test" on a "U-blox NEO-M8T" — with no data or test code behind them. | `REAL_HARDWARE_LIMITATIONS.md:16-65` |
| 3.5 | med | **"VERIFIED SYSTEMS STATUS: OPERATIONAL"** plus a multi-CPU performance matrix (M2 Max 110%, Pi 4 15%, …) that was never run. | `HARDWARE_VERIFICATION.md:62-74,343-352` |
| 3.6 | med | **OSINT/speculative systems presented as confirmed "REAL" hardware** ("NO simulated or theoretical components"; a "Type 1050 Tactical Data Link" matching no attested designation). | `REAL_HARDWARE_ONLY.md:23,816,822` |
| 3.7 | med | **Doc-sprawl:** 47 root-level `.md` files incl. near-duplicate hardware docs (`REAL_HARDWARE_ONLY.md`, `REAL_HARDWARE_SPECIFICATIONS.md`, `HARDWARE_*`). | `ls *.md \| wc -l` = 47 |
| 3.8 | low | **Broken placeholder issue URL** (`github.com/pseudonym-tbd/actual-f35-kill`) — the sole report link in 3 docs; doesn't match the real remote. | `IMPLEMENTATION_README.md:462`, +2 |

## 4 · Physics & parity discipline (limits of the real strength)

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 4.1 | med | **Parity proves site = toolkit, not toolkit = reality.** The guard checks the TS port reproduces the Python to 1e-6 — it validates **no physical model** against measurement. This is the central limitation behind the "trust the numbers" pitch. | `check_parity.mjs:4-5,22` |
| 4.2 | med | **`export_parity.py` docstring overclaims** parity catches "a fabricated number" — it only catches a *port divergence*, not a wrong input. | `export_parity.py:8` |
| 4.3 | low | **Thin regime coverage:** 43 of 114 functions have exactly one parity case — catches wrong-formula ports, not regime-dependent divergence. | `export_parity.py` |
| 4.4 | med | **Closed-form radar/RF omit clutter, multipath, atmospheric loss, ECM** — the workhorse range equation carries one lumped 5 dB loss term. Illustrative, not operational. | `calculators.py:38-55` |
| 4.5 | low | **No human factors, fusion latency, or doctrine** in the kinematic/engagement models — `kill_prob_salvo` is the independent-shot product rule with no correlated failure. | `calculators.py:225-226` |

## 5 · Real-world usefulness (how it's surfaced to users)

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 5.1 | high | **Geometry-only RCS surfaced as a headline "computed RCS pattern."** Non-physical for stealth (J-20 +19 dBsm vs own 0.05 m² estimate). | `cad_models.json` method "geometry-only, no RAM" |
| 5.2 | high | **Detection-range envelopes use the wrong RCS + one fixed notional radar** (1 MW, 40 dBi, −100 dBm) held constant across bands — illustrative, not real ranges. | `gen_cad.py:54-55,390`; `calculators.py:301-309` |
| 5.3 | med | **Physical Optics applied at VHF where the code's own validity test says it's invalid** (λ≈1 m resonance regime); `po_validity_ratio()` exists but is never called. | `cad_rcs_calculator.py:111-116`; `gen_cad.py:390` |
| 5.4 | med | **Kill-chain `total_pk` is a precise-looking product of ~6 hand-assigned probabilities** (0.95×0.90×0.75×0.70×0.90…). | `carrier_strike_kill_chain.py:381-385,901` |
| 5.5 | low | **Home page advertises stale, smaller counts** ("50+ calculators", "58 platforms") than the data/README (92 models, 110 systems) — undercutting the "docs can't drift" selling point. | `home.component.html:70,78` |
| 5.6 | low | **Parametric meshes labeled "High-fidelity"** while the same note admits no real internal/edge geometry and classified coatings. | `cad_models.json` mesh_note |

## 6 · The autonomous research pipeline

| # | Sev | Finding | Evidence |
|---|---|---|---|
| 6.1 | high | **600+ consecutive commits are byte-identical noise.** `export_findings()` writes an unconditional `now()` timestamp, so the file changes every run even when content doesn't — only 8 distinct content versions across ~992 commits. | `research/agent.py:502,52-53` |
| 6.2 | high | **Pipeline is dead since 2026-06-07.** Every scheduled run logs "seeded 0 new tasks", "tavily=0 deepseek=0", findings frozen at 51. | `research/cron.log` |
| 6.3 | med | **Source-quality "guard" is cosmetic** — any non-empty URL passes; the tier classifier "never fails the guard". ~34% of `key_fact` sources are Wikipedia. | `check_research.py:60-62,75-121` |
| 6.4 | low | **Provenance blurb claims Firestore;** 100% of logged runs used the local-file store. | `research/agent.py:503-506` |
| 6.5 | low | **Rumor-grade content published as a structured fact** (a J-10C "Venezuela sought up to 20" acquisition rumor, sourced to Wikipedia). | `research.json` J-10C finding |

---

## What this list is *not*

This is a snapshot, not a verdict on the author's intent or the project's potential. The physics
core is real and good; most of these are fixable, and the private source repo now runs an automated
loop that remediates them one at a time. The reason to publish the list is simple: a tool that asks
you to *"trust the numbers"* owes you an honest account of which numbers have earned it.
