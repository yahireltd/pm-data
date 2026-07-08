---
id: T-0530
title: Allow driver break mid-drive on long single-leg routes (last resort), surfaced as a memo
type: feature
state: done
created: 2026-07-08T17:13:24Z
updated: 2026-07-08T19:04:29Z
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
  - "[x] Probe: dedicated 3.5T route for the Coventry job (C091968 piece) flips from break_no_fit INFEASIBLE to FEASIBLE with a mid-drive break on the return leg"
  - "[x] Replay of run #2495 still plans 0-drops and no existing route changes its break placement (mid-leg only fires when after-stop placement fails)"
  - "[x] A sketch route containing a mid-drive break shows the break banner at the right clock time and an automatic memo mentioning the mid-drive break"
  - "[x] Break policy gates still hold for mid-leg breaks: start ≥ 10:00, ≥ 3h active before, work remaining after"
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
    status: completed
    ended: 2026-07-08T17:47:40Z
    summary: "Drivers can now take their legally-required break mid-drive (a services stop) when a long-distance route has nowhere else to put it. Previously breaks could only happen at customer stops, so some vehicle/job combinations were refused outright — a 3.5T van reaching Coventry with under 3 hours of work done couldn't break at the customer (policy requires 3h before a break) and had no other slot, making the run \"impossible\". The solver now, as a last resort only, places the break inside any leg with 45+ minutes of driving, at the earliest point satisfying both the 3-hours-active and after-10:00 rules, and re-validates every time window and shift limit. The break is flagged and the route gets an automatic memo (\"Driver break 30m taken MID-DRIVE (~17:31, e.g. at services)\") so logistics and the driver see it explicitly on the sketch and run planners — no new UI controls, per Austin's suggestion. Normal routes are untouched: the full 10 July replay still plans all 73 jobs with 0 drops and every break at a customer stop, because the fallback only fires when at-stop placement is impossible. Developed in the repo copy and deployed to the solver box (backup solver_td.py.bak-20260708-T0530)."
    test_plan: |-
      1. Regression (already verified, re-checkable): re-solve Fri 10 Jul — 0 dropped, breaks shown at customer stops as before (mid-drive only fires as last resort; no real case on this day triggers it since the calibration lengthened drives past the 3h gate).
      2. To see it live: find/construct a day where a far job (~2-2.5h drive) must go on a 3.5T (e.g. trucks off-road) — the van route should appear WITH the job planned, a break banner mid-way through the return leg at a sensible clock time (>=10:00, >=3h into the shift), and an automatic memo on the route mentioning MID-DRIVE.
      3. Pull branch on sandbox first — the memo + flag plumbing is PHP-side (SketchPlanService).
      4. Finalise a sketch containing a mid-drive break and check the run planner shows the break and the memo survives the copy.
      5. Policy spot-check on any mid-drive break: start >=10:00, >=3h active before it, >=1h work after it.
      Cross-impact: _rebuild_route_with_break gained mid_leg_offset_sec (default 0 — existing at-stop callers unchanged); StepTiming gained is_mid_drive (default False); response break steps/breaks_list gained mid_drive key (additive, existing consumers ignore it).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - solver
  - route-planner
attention: null
version: 10
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

## Conversation

**2026-07-08 17:47 claude-code:** Run run-20260708-1713 completed — Drivers can now take their legally-required break mid-drive (a services stop) when a long-distance route has nowhere else to put it. Previously breaks could only happen at customer stops, so some vehicle/job combinations were refused outright — a 3.5T van reaching Coventry with under 3 hours of work done couldn't break at the customer (policy requires 3h before a break) and had no other slot, making the run "impossible". The solver now, as a last resort only, places the break inside any leg with 45+ minutes of driving, at the earliest point satisfying both the 3-hours-active and after-10:00 rules, and re-validates every time window and shift limit. The break is flagged and the route gets an automatic memo ("Driver break 30m taken MID-DRIVE (~17:31, e.g. at services)") so logistics and the driver see it explicitly on the sketch and run planners — no new UI controls, per Austin's suggestion. Normal routes are untouched: the full 10 July replay still plans all 73 jobs with 0 drops and every break at a customer stop, because the fallback only fires when at-stop placement is impossible. Developed in the repo copy and deployed to the solver box (backup solver_td.py.bak-20260708-T0530).

---

**2026-07-08 19:04 — you**

Done
