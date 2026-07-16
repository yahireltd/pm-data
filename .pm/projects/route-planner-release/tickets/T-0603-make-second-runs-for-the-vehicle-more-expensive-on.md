---
id: T-0603
title: Solver prefers a second shift on the same vehicle over an idle same-class vehicle — price second runs properly
type: bug
state: review
priority: p1
created: 2026-07-16T17:04:10Z
updated: 2026-07-16T17:57:44Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 10240
reporter: null
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260716-1743
    model: claude-fable-5
    started: 2026-07-16T17:43:48Z
    status: completed
    ended: 2026-07-16T17:57:44Z
    summary: 'Fixed the solver double-running vehicles while identical ones sat idle. Root cause: a second run on the same physical vehicle cost the solver exactly the same as using an idle vehicle (staff and fuel are identical either way), so it picked arbitrarily — and logistics watched a 3.5T go out twice with its twin parked. Two changes on the solver: a £100 "turnaround" charge on every second/third run per vehicle (pricing the rushed warehouse unload-reload a double-run forces), and a deterministic tidy-up pass after the optimiser that moves any remaining second-run onto the cheapest idle vehicle that can take it. Verified on the 10 July replay: second-runs went from 5 to ZERO (16 routes on 16 distinct vehicles), no jobs dropped, and the plan actually got £190 cheaper. The charge is far below the drop penalty, so days genuinely needing a vehicle out twice still solve. Deployed to the solver box and synced to the repo.'
    test_plan: |-
      1. Re-solve a busy day on the sandbox sketch: check the board for the same registration appearing on two routes — should now only happen when every vehicle of every usable class is already out.
      2. Reproduce logistics' case: a day with spare 3.5Ts — the spare must be used before any 3.5T goes out twice.
      3. Force the multi-shift case: mark enough vehicles off-road that a day needs more runs than vehicles — second runs must still appear (jobs must NOT drop instead). 
      4. Locked routes: lock a route, re-solve — the polish pass never moves locked routes or steals locked vehicles.
      5. Watch dispatch times on any remaining second-runs: slot-2 runs still respect the turnaround gap.
      Dial: SECOND_RUN_TURNAROUND_PENCE (£100) in solver_td.py — raise if logistics still see unwanted double-runs, lower if too many big vehicles get used for small second loads. Deploy = scp repo copy + restart vrp-solver (backup solver_td.py.bak-20260716-T0603 on the box).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - solver
  - route-planner
  - release-blocker
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T17:57:44Z
version: 6
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
Logistics saw the solver give one 3.5T a second run while an identical 3.5T sat unused. A second run on the same vehicle forces the warehouse into a fast turnaround (unload, reload, re-dispatch) — an idle same-class vehicle should always be preferred.

## Likely mechanism (to confirm)
The SA cost (_compute_route_cost) charges staff + fuel per ROUTE — a second shift on the same physical vehicle costs the same as opening a fresh vehicle, and the slot-level fixed_cost / extra_shift_penalty fields carried on the virtual vehicles (slot 1 fixed_cost 220000, slot 2 506000, extra_shift_penalty 286000 in the request meta) appear NOT to be charged in the TD solver's cost function at all (they were OR-tools-era knobs). So slot 2 is literally free vs slot 1 of a spare vehicle. Fix: in the TD cost, when assigning to slot_index ≥ 2, add a turnaround cost — large enough that a same-class idle vehicle always wins, small enough that a genuine second run (all vehicles of that class used) still happens rather than dropping the job.

## Guard
Do NOT make second runs infeasible — days genuinely needing 2 shifts per vehicle must still solve. Verify with the 10 Jul replay request (uses 51 virtual slots) that drops stay 0.

## Conversation

**2026-07-16 17:57 claude-code:** Run run-20260716-1743 completed — Fixed the solver double-running vehicles while identical ones sat idle. Root cause: a second run on the same physical vehicle cost the solver exactly the same as using an idle vehicle (staff and fuel are identical either way), so it picked arbitrarily — and logistics watched a 3.5T go out twice with its twin parked. Two changes on the solver: a £100 "turnaround" charge on every second/third run per vehicle (pricing the rushed warehouse unload-reload a double-run forces), and a deterministic tidy-up pass after the optimiser that moves any remaining second-run onto the cheapest idle vehicle that can take it. Verified on the 10 July replay: second-runs went from 5 to ZERO (16 routes on 16 distinct vehicles), no jobs dropped, and the plan actually got £190 cheaper. The charge is far below the drop penalty, so days genuinely needing a vehicle out twice still solve. Deployed to the solver box and synced to the repo.
