---
id: T-0642
title: Redeliveries and re-collections are invisible to the auto route planner
type: bug
state: ready
created: 2026-07-22T15:53:46Z
updated: 2026-07-22T18:17:43Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
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
agent_runs: []
labels:
  - sketch-planner
  - solver
  - redelivery
attention: null
version: 2
---

## Problem

The solver's job builder derives jobs purely from contract hire dates (hireStartDate / hireEndDate). A failed delivery rebooked as a redelivery exists only as a ya_run_contracts row (reDelCol=1, its own runDate) — the contract's dates don't change. So the sketch planner neither shows nor plans redeliveries: they consume no solve capacity, don't appear on the board, and must be hand-placed on the run planner after finalising. Risk: a redelivery is forgotten, or the solved vehicle plan has no room for it. Found while investigating C093283 (failed 22 Jul, redelivery row on 23 Jul, invisible to the 23 Jul solve).

## Fix shape

buildJobsForDate should also emit jobs from active reDelCol=1 rows with runDate = the planning date whose contract isn't already due that date — with the row's own window, weight/volume and address. Finalize's stop loop then needs to write these back against the redelivery row (update its runID) rather than creating a duplicate. Staleness guard: skip if the redelivery row was deactivated/completed since the sketch was drawn.