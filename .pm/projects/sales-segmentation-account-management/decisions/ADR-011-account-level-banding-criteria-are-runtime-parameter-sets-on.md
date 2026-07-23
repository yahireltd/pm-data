---
id: ADR-011
slug: account-level-banding-criteria-are-runtime-parameter-sets-on
title: Account-level banding criteria are runtime parameter sets; ONE pure engine shared by recompute and simulator
state: superseded
decided: 2026-07-03
decided_by:
  - Austin
project: sales-segmentation-account-management
supersedes: null
superseded_by: null
tickets:
  - T-0479
  - T-0457
  - T-0497
version: 2
---

## Context

The account-level thresholds (Strategic/Account floors, Incubation potential, whale share, big-fish triggers) were hardcoded constants in the console recompute, marked "[TUNABLE → T-0479]". The threshold workshop hasn't happened, Ben's team struggles to decide abstract numbers in meetings, and every criteria change would have been a code deploy. Meanwhile the simulator the workshop needs had no guaranteed-identical implementation of the banding rules.

## Decision

1. **One pure engine** — `common/components/AccountLevelEngine.php::computeRow(facts, params)` is the only place banding logic exists. The nightly console recompute and the /account-levels what-if simulator both call it, so live and simulated levels can never drift. Proven by a parity run: 0 mismatches across all 20,044 accounts.
2. **Criteria are data, not code** — named, versioned threshold sets live in `account_level_param_sets`; the `is_live=1` set (newest wins) drives the recompute AND the page. Saving a set is cheap; making it live is explicit; applying it immediately re-bands only changed rows and logs every level move per account (`customer_account_level_log`). Human overrides, confirmations and stages are never touched by a re-band.
3. **The what-if panel is the workshop's decision instrument** — candidate thresholds preview instantly (read-only) with per-level account counts AND £ flows (realised/12m + potential), plus the named biggest movers, so threshold decisions are made against real distributions rather than in the abstract.

## Consequences

Threshold decisions from the workshop become saved parameter sets, not code changes — decision latency is decoupled from build. compute_version stamps which set produced each suggestion (cal-mvp-3:psN). Risk: a bad set made live silently drives the next recompute — mitigated by the per-account reband log, the sets history, and instant re-apply of a previous set. The £ curve (score→potential anchors) remains in code for now; move it into the param set when the calibration decision (design doc §9.2) is taken.
