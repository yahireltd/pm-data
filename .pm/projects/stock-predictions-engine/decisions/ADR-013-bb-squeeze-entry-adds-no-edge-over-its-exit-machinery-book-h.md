---
id: ADR-013
slug: bb-squeeze-entry-adds-no-edge-over-its-exit-machinery-book-h
title: BB_Squeeze entry adds no edge over its exit machinery; book has no null-beating sleeve; EXECUTE=1 on hold
state: accepted
decided: 2026-06-13
decided_by:
  - Austin
  - claude-code
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets:
  - T-0341
version: 1
---

## Context
With PEAD permanently closed (ADR-011) and MR dropped (ADR-007), BB_Squeeze was the book's SOLE live sleeve. T-0341 restated it honestly on the repaired data (2026-06-13, austin-ubuntu):

- **Provenance ladder (close-based):** 1.726 (current-name + touch-fill) → 0.801 (PIT survivor + close) → **0.718 (PIT delisted-inclusive + close)**. Config frozen identical to the 2026-06-10 run; no re-tuning.
- **Data fix:** `adv_monthly` was rebuilt over the merged panel first (90 → 4,263 delisted names) — the derived selection layer had silently re-introduced survivorship after the T-0338 bar merge, so the earlier 0.80 was itself survivor-biased.
- **Decisive entry-permutation null:** REAL 0.718 vs null 0.708 ± 0.413 (n=200), **z = +0.02**. Random same-size entries from the same universe through the identical close-based TP10/SL5/hold-10 machinery score the same. (Survivor-universe null agreed: 0.801 vs 0.726±0.415, z=+0.18.)
- **G1 (run for the first time as specified, +50bps borrow):** S2 h20 = +0.28 flat → +0.14 per-ticker slip → **+0.12 + borrow** (+0.06–0.08 discounted). Passes the +0.05 freeze threshold — the only sleeve that clears its gate.

## Decision
1. **BB_Squeeze's entry signal carries no demonstrable edge.** Its 0.72 Sharpe is universe beta + asymmetric exit machinery in a 2023–2026 bull window, not alpha — it does not beat its null.
2. **The paper book has NO sleeve that beats its own null at a meaningful Sharpe.** S2 (+0.06–0.08 discounted) is a thin diversifier, not a carrier.
3. **EXECUTE=1 (MS-015) is on HOLD.** Do not allocate real capital behind a BB-only book whose sole sleeve is statistically indistinguishable from random entry.
4. The dry-run paper loop continues (zero cost; it validates ops + the G3 reconciliation clock), explicitly understood as ops-validation, not edge-validation.
5. Retire the 1.73 and 0.80 BB figures; the standing number is **0.72-fails-its-null**.

## Consequences
- The charter's 2026-09-07 ≥1.0 honest-Sharpe target now requires discovering a genuinely null-beating sleeve essentially from scratch. Probability is low.
- Research focus shifts to the only credible candidates: S11 quality-trend-hold (post-2026-07-15, judged on its own pre-registered nulls), S10 Form-4 insider clusters (needs the full EDGAR backfill), and net-issuance/buyback long-leg (post-moratorium, needs clean PIT shares data) — plus S2 as a small diversifier IF a real carrier emerges.
- No book change is made on this ADR beyond holding EXECUTE=1; sleeves are not added or removed, the dry-run continues.