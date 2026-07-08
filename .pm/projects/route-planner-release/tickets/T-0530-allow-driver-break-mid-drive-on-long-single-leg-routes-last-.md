---
id: T-0530
title: Allow driver break mid-drive on long single-leg routes (last resort), surfaced as a memo
type: feature
state: in_progress
created: 2026-07-08T17:13:24Z
updated: 2026-07-08T17:13:37Z
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
  - "Probe: dedicated 3.5T route for the Coventry job (C091968 piece) flips from break_no_fit INFEASIBLE to FEASIBLE with a mid-drive break on the return leg"
  - "Replay of run #2495 still plans 0-drops and no existing route changes its break placement (mid-leg only fires when after-stop placement fails)"
  - A sketch route containing a mid-drive break shows the break banner at the right clock time and an automatic memo mentioning the mid-drive break
  - "Break policy gates still hold for mid-leg breaks: start ≥ 10:00, ≥ 3h active before, work remaining after"
out_of_scope: []
code_anchors:
  - path: backend/assets/RoutePlannerPython/solver_td.py
    note: _schedule_break ~line 1000 (fallback scan), _rebuild_route_with_break ~line 1082 (mid-leg walk support), response break-step emission ~line 3760
  - path: common/services/SketchPlanService.php:189
    note: break step parsing — carry mid_drive flag + auto-memo
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260708-1713
    model: claude-fable-5
    started: 2026-07-08T17:13:37Z
    status: in_progress
labels:
  - solver
  - route-planner
attention: null
version: 3
---

## Problem
Break scheduling only places breaks at customer stops. On long single-stop routes the driver realistically breaks at motorway services mid-drive, but the model can't express that, so some configurations stay infeasible: a 3.5T to Coventry has only 2h44 of active time when it leaves the customer (under the 3h minimum-before-break) and nowhere else to put the required break — the van is refused even though the run is fine with a services stop 15 minutes into the drive home. Austin: "can we allow the break mid drive… maybe we need a memo to allow a mid drive break (only on those single drive long distance jobs)".

## Design
Solver (solver_td.py):
- In _schedule_break, ONLY when the normal after-stop scan finds no feasible placement, scan legs with drive ≥ 45min as mid-leg break hosts.
- partA (drive before the break) is chosen minimal to satisfy both gates: break start ≥ 10:00 and cumulative active ≥ 3h. If partA exceeds the leg, the leg can't host.
- Approximation: the break adds its 45/30min to the leg (arrival_next = departure + leg_drive + break_dur); partA positions the break's clock time but doesn't change arrival. Conservative and consistent with the walk.
- Work after break = rest of the leg + everything downstream (return drive counts, per the T-0527 fix). Standard shift/window/limit re-validation via the existing rebuild walk.
- Break step emitted with mid_drive=true so downstream can tell it apart.

PHP/UI:
- SketchPlanService already parses break steps into route['breaks'] — carry the mid_drive flag through.
- Routes with a mid-drive break get an automatic memo: "Driver break mid-drive (~HH:MM, e.g. at services) on the [postcode] leg" — no new UI controls needed; the existing break banner + memo rendering carry it on both sketch and (after finalise) run planner.

## Scope guard
Mid-leg placement is a LAST RESORT only — normal multi-stop London routes keep breaking at stops exactly as today.