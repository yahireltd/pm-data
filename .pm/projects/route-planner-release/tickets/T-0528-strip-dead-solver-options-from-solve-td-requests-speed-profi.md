---
id: T-0528
title: Strip dead solver options from /solve-td requests (speed profile, speed factor, max gap)
type: chore
state: done
created: 2026-07-08T17:08:20Z
updated: 2026-07-13T16:51:42Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A fresh sketch solve's saved request_json (solver_runs table) contains none of: speed_profile, speed_multipliers, speed_factor, max_gap_between_jobs_sec"
  - "[x] Solve results unchanged (fields were dead): re-solve a day and get an equivalent plan"
  - "[x] compare-solvers page (v1/cost engines) still receives the fields and works"
out_of_scope: []
code_anchors:
  - path: common/components/RoutePlannerPlanService.php:486-494
    note: endpoint selection — strip here when td
  - path: backend/assets/RoutePlannerPython/solver_new.py:525
    note: legacy engine that DOES read max_gap — do not break
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1708
    model: claude-fable-5
    started: 2026-07-08T17:08:42Z
    status: completed
    ended: 2026-07-08T17:10:31Z
    summary: Removed the misleading dead options from the sketch planner's solver requests. The TD solver ignores the speed profile, speed factor and max-gap settings, but they were still sent and saved with every run — anyone tuning them would wrongly conclude the solver was broken. They are now stripped only on the TD path; the legacy comparison engines still receive them because they genuinely use them there. The on-screen legacy ETA tables are unaffected (their multiplier is captured before the strip).
    test_plan: |-
      1. Pull branch on sandbox, run a fresh sketch solve, then check the newest solver_runs row: request_json should contain NO speed_profile / speed_multipliers / speed_factor / max_gap_between_jobs_sec (previous rows still have them — that's fine).
      2. The plan itself should be equivalent to before (the fields were dead).
      3. Open /route-planner/compare-solvers for a date — both v1 and cost engines still run (they still get the fields).
      4. The legacy metrics tables on the sketch view show the same ETAs as before the change.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - solver
attention: null
version: 9
---

## Problem
The sketch/TD solve path sends `speed_profile`, `speed_multipliers`, `speed_factor` and `max_gap_between_jobs_sec` in the request, but solver_td.py reads none of them (proven during T-0527 — grep shows no readers). Anyone "tuning" these against a TD solve concludes the solver is broken. Austin: "we should remove the dead controls".

## Scope constraint
The payload builder in RoutePlannerPlanService is SHARED with the legacy engines — solver_new.py DOES read max_gap_between_jobs_sec, and compare-solvers runs v1/cost through the same service. So the fields must be stripped only when the endpoint is /solve-td, not removed from the shared builder or the legacy view-route-or page.

## Fix
Unset the four fields right after the endpoint choice in RoutePlannerPlanService (solverVersion === 'td'), before the request is sent and before persistSolverRun saves request_json — so saved requests reflect what actually influenced the solve.

## Conversation

**2026-07-08 17:10 claude-code:** Run run-20260708-1708 completed — Removed the misleading dead options from the sketch planner's solver requests. The TD solver ignores the speed profile, speed factor and max-gap settings, but they were still sent and saved with every run — anyone tuning them would wrongly conclude the solver was broken. They are now stripped only on the TD path; the legacy comparison engines still receive them because they genuinely use them there. The on-screen legacy ETA tables are unaffected (their multiplier is captured before the strip).

---

**2026-07-13 16:51 — you**

Done
