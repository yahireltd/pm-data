---
id: T-0612
title: Conservation audit command (plan vs finalised runs) + finalise decision logging
type: feature
state: in_progress
created: 2026-07-17T13:09:55Z
updated: 2026-07-17T13:14:20Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - php yii planner-audit/date <date> runs on the sandbox and reports PASS on a healthy finalised date
  - Deliberately breaking a date (delete one run-contract item row in a transaction) flips the relevant check to FAIL naming the contract
  - Item totals check validates split contracts piece-by-piece
  - Run start/end/max W+V and per-stop accrued figures verified against recomputation
  - finalise writes one log line per contract-level decision (distribution source, preserved/abandoned splits, skipped stops) to sketch-planner.log
out_of_scope: []
code_anchors:
  - path: console/controllers/PlannerAuditController.php
    note: new command
  - path: common/services/SketchPlanService.php
    note: finalize — add decision logging; recalculateRunTotals maths to mirror in audit
  - path: common/components/RoutePlannerNew.php:1133
    note: buildJobsForDate eligibility filters to mirror for the due-contract check
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1314
    model: claude-fable-5
    started: 2026-07-17T13:14:20Z
    status: in_progress
labels:
  - route-planner
  - release-blocker
  - testing
attention: null
version: 3
---

## Problem
Pre-release, Austin's core worry is silent loss: jobs or items missing from finalised run contracts, or weights/volumes wrong. Manual spot-checks don't scale to "test to destruction", and when something does look wrong we need logs to reconstruct what finalise decided.

## Build
1. Console command `php yii planner-audit/date YYYY-MM-DD` that verifies, for a date, the conservation invariants between the plan and the live runs:
   - every due contract (same eligibility as the solver's job builder) appears on active run contracts, or is explicitly listed as unplanned
   - sketch (finalized) stops ↔ active run-contract rows 1:1 per piece — nothing lost, nothing duplicated
   - item conservation per contract+type: per-piece item quantities sum to the contract's items
   - weight/volume conservation: piece weights sum to contract weight; run startWeight/endWeight/maxWeight/Vol equal recomputation from rows; accrued sequence equals recomputation
   - orphans: active run-contracts on inactive runs, duplicate rows beyond declared splits
   PASS/FAIL per check with the offending contract numbers. Exit code non-zero on FAIL (scriptable).
2. Extra SketchPlanLogger coverage at finalise decision points: per-contract item distribution chosen (deliberate replay vs greedy), preserved splits honoured/abandoned + why, any stop skipped and why, run timing/distance fields written.

Run the audit against every sandbox test date and (after release) against each live day in week 1.