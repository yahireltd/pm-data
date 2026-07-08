---
id: T-0524
title: "Solver drops jobs despite spare fleet: multi-seed winner chosen by direct £ (rewards dropping), ignoring the drop penalty objective"
type: bug
state: in_progress
created: 2026-07-08T12:45:52Z
updated: 2026-07-08T13:43:03Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: recurring unexplained drops, worked example 2026-07-10 plan
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Cross-seed winner is selected by (drops, direct cost) — a solution with fewer drops always beats one with more
  - Re-solve of 2026-07-10 yields at most 5 unassigned under identical settings
  - cost_alt never carries more drops than the primary solution
out_of_scope: []
code_anchors:
  - path: solver server /opt/vrp-solver/solver_td.py
    symbol: multi-seed selection ~:4164-4205
  - path: backend/controllers/RoutePlannerController.php
    symbol: solver defaults :1148
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1343
    model: claude-fable-5
    started: 2026-07-08T13:43:03Z
    status: in_progress
labels:
  - solver
  - sketch-planner
attention: null
version: 3
---

## Problem
Jobs are dropped even with spare fleet. Worked example (plan date 2026-07-10, solved 2026-07-08 12:27 on sandbox): 73 jobs, 17 physical vehicles expanded to 51 virtual shift-vehicles (capacity emphatically not the constraint) — final result 14 routes, **7 drops**, all remote/tight-window jobs (Coventry 16:00-17:00 both split pieces, Salisbury, New Forest, Chichester 20:00-20:00, Southend).

## Root cause (verified in solver logs + source on the solver server)
The payload is correct: sketch planner sends `drop_penalty: 5,000,000` (RoutePlannerController.php:1148, "dropping is last resort") and each SA seed honours it — internal cost ≈ drops×5,000,000 + direct£×~120.

Seed results that day:
- seed 1: cost 35,442,163 | 7 drops | £3,629 direct  ← CHOSEN
- seed 2: cost 35,448,399 | 7 drops | £3,726 direct
- seed 3: cost 25,478,792 | **5 drops** | £3,986 direct  ← best by the solver's own objective

`solver_td.py` ~:4188: the cross-seed winner is picked by `candidate_direct < overall_best_direct` — **direct £ only, penalty objective discarded**. Fewer jobs = fewer miles = "cheaper", so the arbiter systematically prefers seeds that drop MORE. Two placeable jobs were thrown away for £357 of driving. The intra-seed cost_alt logic (line 3058) even documents the correct rule ("same or fewer drops as best") — the cross-seed comparison lacks it. This mechanism biases EVERY solve, which is why drops keep appearing inexplicably.

## Fix (solver server /opt/vrp-solver/solver_td.py — NOT in a git repo, backup before edit)
Compare seeds by `(len(candidate.unassigned), candidate_direct)` tuple — fewest drops wins, direct £ as tiebreaker. Same guard on the cross-seed cost_alt tracking and the final cost_alt acceptance (`drops(alt) <= drops(best)`).

## Remaining question after the fix
Even the best seed left 5 drops in 200s (assign move: only 5 acceptances). Separate follow-up: whether those 5 are genuinely infeasible (e.g. the 20:00-20:00 zero-width window) or need more budget/better assign moves. Fix the arbiter first, re-measure.

## Test plan
1. Re-solve 2026-07-10 on the sandbox sketch planner (same settings: Full Luton, 9.5h, 200s) → unassigned count should be ≤5 (matching or beating the best seed), not 7.
2. Solver log shows the chosen seed is the one with fewest drops; "Multi-seed" summary line reports drops of the winner.
3. Re-solve 2-3 other recent dates that showed drops → drop counts stay same or improve on all (never worse).
4. Cross-impact: cost_alt in the response (used for the £-saving suggestion) never has more drops than the primary solution.