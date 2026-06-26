# PLA Systems Lab — Reality Check

An **honest, independently-verified assessment** of a private engineering-analysis project: the
"PLA Offensive Systems Engineering & Analysis Lab" (an `osint_cad` Python physics/CAD toolkit plus
an Angular site, built around the **2025 U.S. DoD Annual Report to Congress on PRC Military
Developments**). The live site markets itself as *"physics-grounded,"* *"computed and
parity-checked, never hand-waved,"* and asks you to *"trust the numbers."*

This repo is the counterweight: **how far it has actually become useful, and exactly where it
hasn't** — with the failure modes named, quantified, and cited to `file:line`. The headline finding
is simple:

> **The physics engine is real and good. The CAD geometry and radar-cross-section numbers are
> not matched to real specifications — they are parametric reconstructions and geometry-only
> illustrations, wrong by tens of decibels for the platforms that matter most.**

> **Update — June 2026: most of this has since been remediated** in the source repo (commits
> `1f4da59`, `c9e9c3f`, `fbd2357`; the project's `make check` guards + 251 tests pass on the fixes).
> The pinwheel-mesh defect is gone, the catalog duplicates are deduped, the fabricated-hardware docs
> are retracted, and the **realistic OSINT RCS estimates are now the authoritative numbers** with the
> geometry-only Physical-Optics figures explicitly flagged shaping-only. What *cannot* be fixed —
> classified mold-line geometry and real low-observable signatures — is now **honestly labelled**
> rather than dressed up. See [Remediation status](#remediation-status--june-2026) below and the
> per-finding status badges in [LIMITATIONS.md](LIMITATIONS.md).

Every claim below was produced by a multi-agent audit of the source repo and **adversarially
re-verified** before publication (see [How this was produced](#how-this-was-produced)). The
full, categorized findings with evidence — and their current remediation status — live in
**[LIMITATIONS.md](LIMITATIONS.md)**.

> **UNCLASSIFIED · OSINT · conceptual.** This is commentary on an educational/conceptual tool.
> Nothing here is operational guidance, targeting data, or affiliated with any government.

---

## TL;DR verdict

| | |
|---|---|
| **What it really is** | A genuinely good **teaching / intuition tool** for radar, RCS shaping, EW, link budgets, and kill-chain reasoning, wrapped around a readable rendering of the DoD PRC report. |
| **What it is *not*** | A credible OSINT **order-of-battle reference**, a source of **real RCS signatures**, or a **detection/engagement predictor**. |
| **Where it will mislead you** | If you read the 3-D models as accurate geometry, the geometry-only RCS *shaping* as a real signature, or the catalog as an order of battle. *(June 2026: the headline RCS is now the realistic OSINT estimate, the catalog is deduped + provenance-flagged, and the meshes are labelled parametric — so the data no longer pretends otherwise.)* |
| **Trust the numbers?** | Trust the **closed-form physics** (parity-checked, textbook-correct). After the June-2026 fixes the **headline RCS is the realistic OSINT estimate** and the catalog is **deduped + provenance-tagged**; the meshes remain **parametric reconstructions** (now labelled as such, not "validated CAD"). |

---

## Remediation status — June 2026

After this audit, the source project fixed most of what it could. Per-category status (detail and
per-finding badges in **[LIMITATIONS.md](LIMITATIONS.md)**):

| Category | Status | What changed |
|---|---|---|
| 1 · Geometry not matched to specs | 🟡 **Mitigated** | Pinwheel-fin defect **fixed** (5 sites, 12 meshes); **DF-41 mesh rescaled to the published ~21 × 2.25 m**; "validated STL"/"high-fidelity" overclaims corrected to admit J-20/Type-055 are parametric; **ship "height" now flagged a keel-to-masthead bounding box, not a draft**. Meshes are *still* parametric reconstructions — classified geometry is unrecoverable — but now defect-free and honestly labelled. |
| 2 · RCS off by tens of dB | ✅ **Fixed (honestly)** | `rcs_absolute` makes the realistic OSINT estimate authoritative (J-20 now −13 dBsm, not +19); detection re-anchored (∝ σ^¼); bare Physical-Optics flagged shaping-only; Type-055 override (+83 → +37 dBsm). **RAM is now frequency-dependent** (full at X-band, ~15% at VHF) and **PO carries a validity flag** (`po_valid`/`po_regime`). PO now also adds **edge diffraction (PTD)** + a **Salisbury-screen layered-RAM model** — the unclassified RCS-prediction method; absolute LO still needs the classified coating. |
| 3 · Catalog unciteable | ✅ **Fixed** | Deduped 110 → 102; every system tagged `provenance`; AJX-002 mis-ID corrected (UAV → XLUUV); duplicate-id guard. **New rule #7 guards numeric cross-contradictions** — and the **3 spec-vs-mesh drifts it surfaced are now reconciled** (PL-17/KJ-700/HQ-26, sourced). Fabrication denylist → **shared module + heuristic detector**. **40 web-researched OSINT citations: sourced 30 → 70 / 102.** |
| 4 · Overclaim / fabricated docs | ✅ **Fixed** | Nonexistent `hardware_compatibility_test.py` harness + fabricated "measured" results + "OPERATIONAL" CPU matrix removed; broken issue URL fixed. **Doc-sprawl now navigable** via `DOCS_INDEX.md` + superseded banners on the near-duplicate hardware docs. |
| 5 · "Parity ≠ correctness" | ✅ **Documented + coverage raised** | `export_parity` docstring corrected; methodology `caveats` block states parity proves *site = toolkit*, not *toolkit = reality*, and that models omit clutter/multipath/ECM. **Parity coverage raised so every function now has ≥2 test points (343 → 412 cases)**; no Python↔TS divergence found. |
| 6 · Dead research pipeline | 🟡 **Mostly fixed** | Content-hash churn guard (no more byte-identical commits); Firestore-lie corrected; real source-tier gate; J-10C rumor flagged UNCONFIRMED. The pipeline itself stays paused by choice (does no new retrieval). |

Legend: ✅ fixed/documented · 🟡 mitigated/labelled. Where open-data physics *can* go further it now does
(PO **+ edge-diffraction + Salisbury-screen RAM**, **CEP-derived** hit probability + Monte-Carlo, and
**clutter/multipath/atmospheric** detection terms). The truly **inherent** limits — engineering-CAD
geometry, real low-observable RCS, intel-grade probabilities — need **classified data**, which is a
**data wall, not a compute wall**: a cloud GPU can't synthesize inputs that don't exist, so they stay
honestly labelled. See LIMITATIONS.md for the
inherent vs by-choice split.

---

## What it genuinely does well

These hold up under scrutiny and are the real contribution:

- **A real, end-to-end physics parity harness.** ~130 closed-form RF/radar/sonar/orbital functions
  in Python are mirrored 1:1 in the browser's TypeScript, and a build guard esbuild-compiles the
  *actual* production TS and checks **349/349 cases** against the Python at 1e-6 tolerance. We ran
  it: `parity: 349/349 match — ALL MATCH ✓`. A number shown on the site really is what the engine
  computes.
- **Textbook-correct physics.** The monostatic radar-range equation, TDOA geolocation (GDOP /
  Cramér-Rao bound), Albersheim detection SNR and its inverse, the Sänger equilibrium-glide HGV
  range, ISA-1976 atmosphere, and Doppler/pulse-Doppler relations are all in correct standard form.
- **Documentation generated from code.** The `/methodology` page is derived from the engine, so the
  documented equations can't silently drift from what runs.
- **Principal dimensions reconciled to published figures.** Sampled platforms match open sources
  well (J-20 20.4 m, F-22 18.92 m, Type-055 180 m, DF-17 11 m, DF-26 14 m).
- **Honest provenance where it exists.** The missile/ballistic subset carries real per-value
  `{nominal, lower, upper, confidence, source, limitation}` structure, and classified programs are
  marked low-confidence (e.g. DF-41 CEP at confidence 0.35). One source doc,
  [`ACCURACY_AND_LIMITATIONS_CAD.md`](ACCURACY_AND_LIMITATIONS_CAD.md), is genuinely candid and
  worth reading.
- **Reproducible & contained.** The whole site rebuilds from a clean checkout through a local
  pipeline with ~12 integrity guards and a pytest suite, and the autonomous research feed is
  architecturally walled off from the authoritative catalog specs.

## Where it falls short — verified limitations

Ordered by how badly they can mislead. Each is cited in **[LIMITATIONS.md](LIMITATIONS.md)**.

### 1. CAD geometry is not matched to real specifications

- **No mesh is from engineering CAD.** All **92** shipped meshes are parametric primitives
  (ogives, cylinders, boxes, lofted stations, flat fins). "Validated" in the code means only
  *mathematically valid triangles* — **not** fidelity to the real platform. Two of the four
  "validated" featured meshes (J-20, Type-055) don't even load their vendored STL — they're
  regenerated parametrically.
- **A geometry defect still ships.** A rotate-before-translate bug splays cruciform fins into a
  "pinwheel" on **17 served registry missile meshes**.
- **Some dimensions overshoot the real article** (e.g. the DF-41 mesh at 23.0 m × 2.475 m sits
  above commonly published ~21 m × ~2.25 m), and ship "height" is a keel-to-masthead bounding box,
  not a meaningful spec.
- Meshes carrying a *"High-fidelity OSINT parametric reconstruction"* note are, by the note's own
  admission, outer-envelope shapes "matched to published figures" with no real internal/edge
  geometry.

### 2. Absolute RCS is physically meaningless for the platforms that matter

- The headline "computed RCS" is bare Physical Optics over coarse aluminium meshes with **no
  radar-absorbent material, edge, or cavity modeling** — RAM is a single flat scalar knockdown.
- For low-observable aircraft this is **off by tens of dB**: the J-20's reported clean frontal RCS
  is **+19.14 dBsm (~82 m²)** versus the repo's *own* open estimate of **0.05 m² (−13 dBsm)** — a
  **~32 dB (1600×) gap**; the F-22 gap is **~57 dB**. The Type-055 ships its raw +83 dBsm
  (~2.2×10⁸ m²) with no realistic override at all.
- Physical Optics is even applied at VHF (λ ≈ 1 m), the resonance regime where the code's *own*
  validity test says it's invalid — and that test is never called.
- **Detection-range envelopes** inherit this wrong RCS and a single hard-coded notional radar
  (1 MW, 40 dBi, −100 dBm) held constant across bands, so the "detection rings" are illustrative,
  not real ranges.

### 3. The catalog's provenance discipline is real but not universal

- The README claims *"nothing ships as fact unless computed or sourced-with-confidence,"* but
  **51 of 110 systems (46%)** ship bare scalars with no confidence or source.
- **8 duplicate entries carry contradictory dimensions** (e.g. PL-17 at 6.0 m vs 4.5 m) that the
  consistency guard can't see; **range / CEP / span / diameter / EIRP are entirely unguarded** (only
  a narrow RCS rule exists, and it doesn't even key on the main catalog file).
- Fabrication defense is a hard-coded 4-string denylist (`YJ-15/17/19/20`), not a detector, and at
  least one entry is mis-identified (AJX-002 filed as a "Tactical UAV"; it's an underwater XLUUV).

### 4. A cluster of documentation overclaims — some fabricated

- A self-graded **"100% accuracy / MAXIMALLY ACCURATE / SELF-VERIFIED COMPLETE"** report (since
  retracted in the source repo at our prompting).
- A test harness `hardware_compatibility_test.py` cited as passing in **10+ places does not exist**;
  docs print fabricated "expected output."
- "Hardware-in-the-Loop / RTL-SDR / PPS" capability marked **COMPLETE** with no such code;
  **"measured (Our Tests)"** hardware numbers (1PPS jitter, a 24-hour U-blox test) with **no data or
  test code behind them**; an **"OPERATIONAL"** multi-CPU verification matrix that was never run.
- **47 root-level `.md` files** with overlapping, near-duplicate hardware-spec documents, plus a
  broken placeholder issue-tracker URL in three of them.

### 5. "Parity-checked" proves consistency, not correctness

- The parity guard proves the **website matches the toolkit** — it says **nothing about whether
  either matches reality.** 43 of 114 functions are exercised at only one input point, and the
  closed-form radar/RF/kill-chain models omit clutter, multipath, atmospheric loss, ECM, sensor
  latency, and human factors. Kill-chain `total_pk` is a precise-looking product of ~6 hand-assigned
  probabilities.

### 6. The "autonomous research" pipeline is effectively dead noise

- It produced a respectable **one-shot** artifact of 51 sourced findings — but as a *running*
  pipeline it has done **zero retrieval since 2026-06-07**. An unconditional timestamp means
  **600+ consecutive commits are byte-identical** ("autonomous findings update" changing only a
  clock value). Its source-quality "guard" is cosmetic (any non-empty URL passes; ~34% are
  Wikipedia), it advertises a Firestore backend it never uses, and rumor-grade content
  (a J-10C acquisition rumor) is published as a structured fact.

## Bottom line

| If you want to… | …should you use the source project? |
|---|---|
| Build intuition for radar/RCS/link-budget/kill-chain math | **Yes** — the physics engine is its real strength. |
| Read the 2025 DoD PRC report in a navigable form | **Yes** — a decent reading/reference shell. |
| Get real RCS signatures or detection ranges | **No** — still geometry-derived; but (June 2026) the site now surfaces the realistic OSINT estimate as the authoritative number and flags the Physical-Optics figure as shaping-only. |
| Use the 3-D models as accurate geometry | **No** — still parametric reconstructions (now labelled as such, and defect-free after the pinwheel-fin fix), not engineering CAD. |
| Cite the catalog as an order-of-battle reference | **With care** — duplicates/contradictions gone, a numeric cross-contradiction guard now runs, **70 of 102 entries carry a researched source** and the rest are flagged unsourced; treat unsourced entries as open-source estimates. |

## How this was produced

A multi-agent audit fanned out across six dimensions of the source repo (CAD geometry, data
integrity, documentation overclaim, physics rigor, the research pipeline, and real-world
usefulness). **Every claimed limitation was then handed to an independent verifier** instructed to
re-open the cited evidence and confirm or refute it before it could be published here — so this
document would not itself become a new layer of unverified assertion. Of the findings produced,
nearly all verified as real; **at least one was refuted and dropped** (a claim that a "3,500+ lines
of code" figure was inflated turned out to be a miscount on the auditor's part, so it was removed
rather than published). Findings cite `file:line` in the source repo as of June 2026.

This began as a **June-2026 point-in-time snapshot**; **most findings have since been remediated**
(see [Remediation status](#remediation-status--june-2026)), and the private source repo runs an
automated loop that keeps fixing residual items. The findings are retained in full, with status
badges, as the record of what was wrong and what was done about it.

## Contents

- **[LIMITATIONS.md](LIMITATIONS.md)** — the full categorized findings, with severity and evidence.
- **[ACCURACY_AND_LIMITATIONS_CAD.md](ACCURACY_AND_LIMITATIONS_CAD.md)** — the source project's own
  candid, parameter-by-parameter limitations doc (reproduced as supporting context).
- **[ARCHITECTURE.md](ARCHITECTURE.md)** — the source project's architecture and parity-discipline
  rationale (reproduced so the critique stands on its own).

## License & framing

Commentary and assessment, reproduced supporting docs © 2026 Bo Shang, under
**GNU AGPL-3.0-only** (matching the source project). Not an official DoD product; not affiliated
with any government or military organization. The point of this repo is **deflationary honesty** —
to make clear what the source tool is and is not, so no one mistakes a conceptual engineering-and-
education aid for real intelligence.
