---
id: T-0603
title: Solver prefers a second shift on the same vehicle over an idle same-class vehicle — price second runs properly
type: bug
state: triaged
priority: p1
created: 2026-07-16T17:04:10Z
updated: 2026-07-16T17:29:03Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 10240
reporter: null
assignee: null
acceptance_criteria:
  - On a day with a spare same-class vehicle, the solver uses the spare instead of a second run on an already-used vehicle (reproduce logistics' 3.5T case)
  - Days that genuinely need multi-shift (more runs than vehicles of the class) still solve — 10 Jul replay stays 0 drops
  - "The preference holds across classes sensibly: a second run on a 3.5T is still allowed if the only idle vehicles are much larger and the job fits the 3.5T better on cost"
out_of_scope: []
code_anchors:
  - path: backend/assets/RoutePlannerPython/solver_td.py
    note: _compute_route_cost (~line 1300) — no slot/turnaround charge today; VehicleData has slot_index + fixed_cost
  - path: common/components/RoutePlannerPlanService.php
    note: enrichVehiclesForCostSolver — slot fixed_cost / extra_shift_penalty values sent but unread by TD
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - solver
  - route-planner
  - release-blocker
attention: null
version: 3
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Logistics saw the solver give one 3.5T a second run while an identical 3.5T sat unused. A second run on the same vehicle forces the warehouse into a fast turnaround (unload, reload, re-dispatch) — an idle same-class vehicle should always be preferred.

## Likely mechanism (to confirm)
The SA cost (_compute_route_cost) charges staff + fuel per ROUTE — a second shift on the same physical vehicle costs the same as opening a fresh vehicle, and the slot-level fixed_cost / extra_shift_penalty fields carried on the virtual vehicles (slot 1 fixed_cost 220000, slot 2 506000, extra_shift_penalty 286000 in the request meta) appear NOT to be charged in the TD solver's cost function at all (they were OR-tools-era knobs). So slot 2 is literally free vs slot 1 of a spare vehicle. Fix: in the TD cost, when assigning to slot_index ≥ 2, add a turnaround cost — large enough that a same-class idle vehicle always wins, small enough that a genuine second run (all vehicles of that class used) still happens rather than dropping the job.

## Guard
Do NOT make second runs infeasible — days genuinely needing 2 shifts per vehicle must still solve. Verify with the 10 Jul replay request (uses 51 virtual slots) that drops stay 0.