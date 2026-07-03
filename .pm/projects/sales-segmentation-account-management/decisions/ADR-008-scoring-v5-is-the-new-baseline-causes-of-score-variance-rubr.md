---
id: ADR-008
slug: scoring-v5-is-the-new-baseline-causes-of-score-variance-rubr
title: Scoring v5 is the new baseline; causes of score variance + rubric fixes
state: accepted
decided: 2026-07-03
decided_by:
  - Ben
  - Austin
project: sales-segmentation-account-management
supersedes: null
superseded_by: null
tickets:
  - T-0456
version: 2
---

## Context
We re-ran the TS-001 scoring rubric (fit 0–40 + freq 0–30 + scale 0–30; A≥70/B40–69/C<40; **blind to spend**) on all 528 customers in the same pass as segmentation, and compared to the original 12-Jun (v0) scores.

## Findings
- Exact tier match **352/528 = 67%** (1 in 3 changed tier). But **164 of 176 disagreements are ±1 tier; only 12 are catastrophic A↔C.**
- **Bias fresh−orig = +1.4 pts → no systematic drift** (the rubric is not a different yardstick). Pearson 0.73, mean |Δ| 12.6.
- **Causes, decomposed from the data:**
  1. **STEP-0** — v0 had no personal/shared/proxy rule and scored *people* 77–85/A (e.g. academics on `.ac.uk`); 17 accts. v5 fixes both directions.
  2. **"False-C"** — v0 buried real event businesses at 0–10 on a failed fetch / unfollowed redirect (National Gallery 0→74, CREtech 2→66); 34 accts. v5 follows redirects + uses the one-search fallback.
  3. **v0 over-scored** prestige names & suppliers (a hotel 100→54, an AV supplier 98→52) — v5 is more disciplined (peer/competitor flag, lower fit for in-house venues, retail = not-relevant).
  4. **Borderline judgment noise** — 49 accts flipped on a ≤12-pt swing right at the 40/70 cut.
- **Web-lookup non-determinism is real and measurable** — mean |Δ| rises with uncertainty: confidence high **11.3** / med **16.1** / low **17.6**. (And the two runs are ~6 months apart, so businesses also changed/closed/were acquired.)
- **Two rubric cliffs amplify** the noise: "cap scale at 10 if fit<15" (a ±1 fit swing = ±20 pts; 24 accts sit at fit 13–16) and the hard 40/70 tier cuts.

## Decision
1. **Adopt the v5 run as the scoring baseline** — strictly better-instrumented than v0 (STEP-0 + redirect-follow + fetch-fallback); it corrects ~50+ materially wrong v0 scores (false-A people, false-C venues).
2. **Rank by SCORE, not tier** — or add a review-band / hysteresis around 40 and 70 so a 69↔71 doesn't flip an account's whole treatment.
3. **Smooth the fit<15 scale-cap cliff.**
4. **Keep storing fit/freq/scale + evidence** for auditability (v5 does).
5. **Re-score on a cadence** — the web and the customers genuinely move.
**The rubric maths is sound — do not change the weighting.** Conclusion: ~⅓ of the movement is v5 fixing v0 instrumentation bugs; ⅔ is web-read + judgment variance amplified by threshold cliffs.

## Consequences
- **T-0456** (customer scoring) updates to the v5 method + fixes (2)/(3) → call it scoring v6 once they land.
- Re-scoring the live `customer_sales_scores` will move ~⅓ of tiers — expected and correct.
- State **proposed** pending Ben/team confirm. Method + run mechanics: TS-002. Taxonomy: ADR-007.