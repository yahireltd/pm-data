---
id: T-0449
title: 'Run planner: contracts can be assigned to a soft-deleted run, leaving "deleted" runs full of live jobs'
type: bug
state: triaged
created: 2026-06-22T11:28:33Z
updated: 2026-06-22T11:28:33Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - actionPlanContract rejects (or reactivates) when the target run is missing or active = 0, for both the existing-run-contract move branch and the new-contract branch
  - RunPlanner.js surfaces a clear message when a plan-onto-inactive-run is rejected, matching the existing containsContracts UX
  - YaRuns::afterSave() writes an 'active' change to run_changes so run deletions/restores are auditable
  - No regression to normal drag-to/from the unassigned (-1) pool or normal run-to-run moves
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - logistics
  - run-planner
  - data-integrity
attention: null
version: 1
---

## Problem
Runs that still contain contracts are ending up soft-deleted (`ya_runs.active = 0`). The delete guard is supposed to make this impossible. Observed live on runs **24176** and **24177** (week of 2026-06-19): both were inactive yet, once reactivated, immediately showed the jobs that were on them. This silently removes runs from the planner while live jobs remain attached — a missed-delivery / data-integrity risk.

## Root cause
It's a sequence-of-actions hole, not a single broken check:

1. **Delete guard is a point-in-time snapshot.** `LogisticsController::actionDeleteRun()` (≈line 4672) blocks deletion only when the run currently has run-contracts with `runID = <thisRun>` AND `active = 1` (via the `getRunContracts()` relation in `YaRuns.php:134`, which filters `active = 1`). If the run momentarily has zero such rows, it soft-deletes (`$run->active = 0`).
2. **"Remove job from run" parks it, doesn't delete it.** In `actionPlanContract()` (≈line 415/427) dragging a job off a run sets its `runID = -1` (unassigned pool). The run-contract row stays `active = 1`.
3. **Drag all jobs off → run looks empty → user deletes it** (manual confirm dialog in `RunPlanner.js:1034`, "Run must be empty before deleting"). Guard passes legitimately.
4. **Drag jobs back on.** `actionPlanContract()` takes `runID` straight from POST and assigns it with **no check that the target run exists or is active** (the run lookup at `LogisticsController.php:380` is commented out). The jobs re-attach to a now-inactive run.

Result: an `active = 0` run full of `active = 1` contracts. Because the contracts kept their `runID`, reactivating the run brings the jobs straight back.

The `contract_run_log` shows the `→ -1` then `→ <runID>` churn that fingerprints this. The `active` flip itself is **not** audit-logged — `YaRuns::afterSave()` logs vehicle/driver/mate/lock/start-time/status/pushed to `run_changes`, but not `active` — which is why the deletion is invisible in the run history.

## Fix
1. **Primary — reject planning onto a dead run.** In `actionPlanContract()`, before assigning `$runContract->runID = $targetRunID` (both the move branch ~415 and the new-plan branch ~461), load the target run when `$targetRunID !== -1` and bail if it's missing or `active !== 1`. Surface a message in `RunPlanner.js` like the existing `containsContracts` handling.
2. **Audit — log `active` changes** to `run_changes` by adding an `active` case to `YaRuns::afterSave()`, so future run deletions are traceable (who/when).

## Remediation query (find other affected runs)
```sql
SELECT r.id AS runID, r.runDate, r.vehicleID, COUNT(rc.id) AS liveContracts,
       GROUP_CONCAT(rc.contNo ORDER BY rc.runOrder) AS contracts
FROM ya_runs r
JOIN ya_run_contracts rc ON rc.runID = r.id AND rc.active = 1
WHERE r.active = 0 AND r.runDate >= CURDATE()
GROUP BY r.id ORDER BY r.runDate, r.runID;
```