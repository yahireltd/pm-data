---
id: T-0642
title: Redeliveries and re-collections are invisible to the auto route planner
type: bug
state: review
created: 2026-07-22T15:53:46Z
updated: 2026-07-22T18:50:12Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A rebooked redelivery whose runDate = the planning date appears as a job in the sketch solve with its window and weight
  - Finalising assigns the existing redelivery row to the chosen run (no duplicate rows; planner-audit PASS)
  - Contracts due normally on the date are unaffected
  - A redelivery completed or deactivated after the sketch was drawn is skipped with a named warning at finalize
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerNew.php
    note: buildJobsForDate — contract-date-driven job emission; add reDelCol=1 row sourcing
  - path: common/services/SketchPlanService.php
    note: finalize stop loop — write-back path must target the existing redelivery row
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1817
    model: claude-fable-5
    started: 2026-07-22T18:17:49Z
    status: completed
    ended: 2026-07-22T18:18:09Z
    summary: |-
      Rebooked redeliveries and re-collections were invisible to the automatic route planner: a failed job moved to another day only exists as a special row on its new date, and the planner only looked at contract hire dates. So the solver reserved no vehicle time for redeliveries, they never appeared on the planning board, and logistics had to remember to hand-place each one after finalising — a forgotten-redelivery and overloaded-vehicle risk.

      Now every unplanned rebooked job appears in the solve for its new date, clearly labelled "(REDEL)" or "(RECOL)", with its own weight and time window (partial rebookings carry just the remaining portion). When the plan is finalised, the job's existing row is assigned to the chosen vehicle — nothing duplicated, its item list untouched — and if the rebooking was completed or moved while the plan was being drawn, finalise skips it with a named warning instead of guessing. Verified end-to-end on the test box with a real solve and a conservation-audit PASS. The build also flushed out and fixed three unrelated defects the audit caught: split pieces being written with full contract weight after a same-run collapse, dead runs surviving with stale weight totals, and a false "solve already running" refusal from a stale lock file.
    test_plan: |-
      On the routing test box (see the finished example: 30 Jul sketch, stop "C093261 (REDEL)" on the vehicle-76 run; audit PASS):

      1. Create a real rebooking: mark a delivery failed on the failed-jobs page, rebook it to a quiet future date.
      2. Open that date's sketch and solve → the job appears labelled (REDEL) with the ROW's weight and window; check the unassigned pool shows it labelled the same if the solver drops it.
      3. Finalise → run planner shows the redelivery on its chosen run; in the DB the ORIGINAL rebooked row now carries that runID (no second row for the contract); its items are unchanged.
      4. `php yii planner-audit/date <date>` → PASS (checks 3/4 must not flag the partial weight/items — exclusions added).
      5. Staleness: rebook another job, solve, then complete/move the rebooked row BEFORE finalising → finalise warns "Rebooked job ... SKIPPED" and everything else lands; audit PASS.
      6. Same-day rebooking (fail + rebook to the SAME date) → solver does NOT double-emit; the contract appears once via the normal path.
      7. Regression sweep: solve + finalise a date containing a same-run split collapse → audit check 4 passes (piece-weight fix); confirm no active empty runs left with stale totals.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - sketch-planner
  - solver
  - redelivery
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-22T18:18:09Z
version: 5
---

## Problem

The solver's job builder derives jobs purely from contract hire dates (hireStartDate / hireEndDate). A failed delivery rebooked as a redelivery exists only as a ya_run_contracts row (reDelCol=1, its own runDate) — the contract's dates don't change. So the sketch planner neither shows nor plans redeliveries: they consume no solve capacity, don't appear on the board, and must be hand-placed on the run planner after finalising. Risk: a redelivery is forgotten, or the solved vehicle plan has no room for it. Found while investigating C093283 (failed 22 Jul, redelivery row on 23 Jul, invisible to the 23 Jul solve).

## Fix shape

buildJobsForDate should also emit jobs from active reDelCol=1 rows with runDate = the planning date whose contract isn't already due that date — with the row's own window, weight/volume and address. Finalize's stop loop then needs to write these back against the redelivery row (update its runID) rather than creating a duplicate. Staleness guard: skip if the redelivery row was deactivated/completed since the sketch was drawn.

## Conversation

**2026-07-22 18:18 claude-code:** Run run-20260722-1817 completed — Rebooked redeliveries and re-collections were invisible to the automatic route planner: a failed job moved to another day only exists as a special row on its new date, and the planner only looked at contract hire dates. So the solver reserved no vehicle time for redeliveries, they never appeared on the planning board, and logistics had to remember to hand-place each one after finalising — a forgotten-redelivery and overloaded-vehicle risk.

Now every unplanned rebooked job appears in the solve for its new date, clearly labelled "(REDEL)" or "(RECOL)", with its own weight and time window (partial rebookings carry just the remaining portion). When the plan is finalised, the job's existing row is assigned to the chosen vehicle — nothing duplicated, its item list untouched — and if the rebooking was completed or moved while the plan was being drawn, finalise skips it with a named warning instead of guessing. Verified end-to-end on the test box with a real solve and a conservation-audit PASS. The build also flushed out and fixed three unrelated defects the audit caught: split pieces being written with full contract weight after a same-run collapse, dead runs surviving with stale weight totals, and a false "solve already running" refusal from a stale lock file.

**2026-07-22 18:50 claude-code:** **Two follow-on fixes from Austin's review of the test-box fixture (22 Jul evening):**

1. **Delta refresh flagged rebooked stops REMOVED** (d739017e): the refresh compares stops against the date's due-contract list — a rebooked job's contract is never due on its rebooked date, so every refresh struck the stop through. Rebooked stops now stay out of the contract delta and validate against their own row (removed only when the row is deactivated, moved off the date, or completed).

2. **syncPlanFromRuns stripped the marker** (c080848d): loading a finalized sketch rebuilds stops from run rows with sync's own field list, which dropped redelivery_rc_id and the (REDEL) label — so the REMOVED flag came back on every load (hard refresh couldn't help) and a later finalize would have lost the row link. Sync now stamps the marker and label from the row itself.

Also noted during review: the simulated fixture's dates were unrealistic (redelivery a week after the hire ended) — an artifact of test-data selection, not product behaviour; the rebooking UI owns date choice. Fixture retired via the death-path (row deactivated → finalize skipped it loudly → audit PASS). The remaining review step is unchanged: one REAL rebooking through the failed-jobs UI on a sensible date, solve → finalise → run planner.
