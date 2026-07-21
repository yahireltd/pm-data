---
id: T-0632
title: Contract date-move deactivates run rows without recalculating run totals (audit noise)
type: bug
state: triaged
created: 2026-07-21T11:22:27Z
updated: 2026-07-21T11:22:27Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - Moving a contract's hire date off a planned date leaves the affected runs' stored totals equal to recomputation (planner-audit check 4 PASS immediately after the move)
  - The new holding row on the destination date is unaffected
  - No regression to the finalize/run-planner recalculation paths
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: recalculateRunTotals — the recalculation to reuse
  - path: console/controllers/PlannerAuditController.php
    note: check 4 (weight/volume conservation) — the detector that surfaces the gap
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - run-planner
  - legacy
  - post-release
attention: null
version: 1
---

## Problem

When sales move a contract's hire dates, the legacy process deactivates the contract's ya_run_contracts row on the old date (and parks a new runID=-1 row on the new date) but does NOT recalculate the affected run's stored totals (startWeight/endWeight/maxWeight/maxVol/accWeight chain). The stored figures then include a job that is no longer on the run until logistics next edits or re-finalises the date.

Observed on the sandbox morning of 21 Jul: C088271's collection moved 21→28 Jul overnight; `planner-audit/date 2026-07-21` correctly FAILed check 4 on two runs whose stored totals were ~96kg over recomputation. Pre-existing legacy behaviour — not introduced by the release — but now VISIBLE because the audit checks stored vs recomputed totals daily.

## Fix shape

Wherever the date-move deactivates run rows, call the same run-totals recalculation the finalize/run-planner paths use (SketchPlanService::recalculateRunTotals or its run-planner equivalent) for each affected run. Small, contained.

## Interim

Week-1 ops note added to RELEASE-PLAN: an audit FAIL of this shape (stored totals over recompute + a sketch stop with no active row, right after a sales date change) is drift detection working — replan/finalise the date and it clears.