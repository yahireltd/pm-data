---
id: T-0527
title: "Far jobs always dropped: London-calibrated traffic multipliers inflate long legs ~4x; dispatch estimator self-inflicts max_wait/late failures"
type: bug
state: triaged
created: 2026-07-08T14:59:08Z
updated: 2026-07-08T14:59:08Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: session
assignee: null
acceptance_criteria:
  - "Re-solve 2026-07-10 from the sketch planner: the five far jobs (C092202, C091968 D1+D2, C091437, C091439) are planned onto routes, drops ≤ 1 for the day"
  - Probe script verdicts flip to FEASIBLE for dedicated routes on at least the 7.5T class for all five jobs
  - London-only legs (raw ≤30min) produce identical predicted durations before/after the change (multiplier taper does not touch short legs)
  - A busy all-London day re-solved before/after shows no route-duration regressions (spot-check 3 routes’ predicted vs previous timings)
  - "Dispatch fixed-point: no route in the re-solved plan has first-stop wait > max_wait or a self-inflicted late arrival caused by dispatch-estimate mismatch"
  - vrp-solver.service restarted cleanly; backup file exists on the box
out_of_scope: []
code_anchors:
  - path: /opt/vrp-solver/solver_td.py (solver box 18.169.52.174, no git)
    note: HOURLY_MULTIPLIERS_VAN/TRUCK ~lines 53-80; td_travel_time ~line 556 (fast path + multi-hour walk both need the taper); dispatch estimate ~line 697 and ~line 1035; max_wait check ~line 797 and ~1111
  - path: backend/controllers/RoutePlannerController.php:1148
    note: sketch solver defaults — note max_gap_between_jobs_sec / speed_factor / speed_profile are ignored by solver_td (dead options)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - solver
  - route-planner
attention: null
version: 1
---

## Problem
Even after the T-0524 seed-selection fix, the 2026-07-10 plan drops 5 jobs while 2+ physical vehicles sit idle. Austin: "We could easily dispatch a vehicle containing one of those jobs in each — I can't see any reason why we haven't used the remaining 2 vehicles."

The 5 drops are ALL the out-of-London jobs: C092202 PO18 (Chichester), C091968 D1+D2 CV4 (Coventry), C091437 SO42 (New Forest), C091439 SP3 (Salisbury).

## Root cause (proven by probe, not conjecture)
Probe method: rebuilt the exact problem from solver run #2495's saved request on the solver box (`/tmp/probe_unassigned.py`, `/tmp/probe_matrix.py` on 18.169.52.174) and asked `evaluate_route()` to judge a dedicated single-job route per dropped job on every vehicle class. Verdicts:

**Bug A — traffic multipliers are leg-length blind.** Valhalla raw times are accurate (depot→CV4 94min, →PO18 104min, →SO42 109min, →SP3 108min, →SS0 57min; matches Google). But `HOURLY_MULTIPLIERS_TRUCK` (4.0x at 08h, 4.25x at 09h, 4.59x at 17h — calibrated 2026-05-27 from n=289 drive_time_reports, which are almost all short London hops) is applied to the WHOLE leg. Result: solver believes depot→Coventry at 08:00 = **341min (5.7h)** vs ~2h15 real. So:
- CV4 jobs: dedicated route = 10.6h > 9.5h max_route_dur → infeasible on EVERY vehicle
- PO18 job (20:00-20:00 window): predicted arrival 21:22 → late_arrival on every vehicle

**Bug B — dispatch estimator disagrees with the actual route walk.** `evaluate_route` picks dispatch = tw_start − drive_est − 300 where drive_est is computed at a single "rough departure hour" (near the window). The actual walk then departs at that early time under DIFFERENT (night) multipliers and arrives hours early:
- SO42/SP3 jobs (08:00 window): estimator uses hour-7 traffic (2.81x), dispatches 01:25; actual walk at 1.35x arrives ~05:36 → wait 234min > max_wait 3600 → infeasible on every vehicle. These two jobs are killed purely by this estimator mismatch — they'd fit fine dispatching at 05:45.

So all 5 drops are constraint-model artefacts, not real infeasibility and not the SA search.

## Evidence
- Probe verdicts: every vehicle class returns `max_route_dur 38043s > 34200s` (CV4), `late_arrival 21:22 > 20:00` (PO18), `max_wait 14070s > 3600s` (SO42/SP3).
- Matrix comparison (raw Valhalla vs TD@08h): PO18 104→375min, CV4 94→342min, SO42 109→390min, SP3 108→388min, SS0 57→219min.
- Multipliers: solver_td.py ~line 53 (VAN) / 70 (TRUCK), calibration note at lines 41-52.
- Also noted: request fields `max_gap_between_jobs_sec`, `speed_factor`, `speed_profile` sent by PHP are NOT read by solver_td (dead options for the TD solver).

## Proposed fix (needs Austin sign-off — changes predicted timings globally)
1. **Leg-length-aware multiplier taper**: legs with raw ≤30min keep the full calibrated multiplier (London behaviour unchanged — that's what the calibration data actually measured). For longer legs, linearly blend the effective multiplier down to a motorway cap (e.g. truck 1.7x, van 1.5x) by raw ≥60min. Sanity: CV4 94min→~160min (2h40, real 2h15-2h45); PO18 104→~177min; SS0 57→~110min. Off-peak multipliers (1.35x) sit below the cap → unchanged.
2. **Fixed-point dispatch**: after walking the route, if the first stop has wait>0 (or is late), shift dispatch by that delta and re-walk (2-3 iterations, both evaluate_route copies ~line 697 and ~1035). Kills Bug B independently of multiplier choice.
3. Follow-up (separate): recalibrate multipliers from drive_time_reports with an affine or piecewise model (fixed per-stop overhead vs proportional congestion), including long-leg samples if any exist.

## Context
Solver box 18.169.52.174 `/opt/vrp-solver/solver_td.py` — NO git repo; back up before editing (pattern: solver_td.py.bak-YYYYMMDD-TNNNN). Probe scripts left in /tmp on the box and locally. Related: T-0524 (seed selection by direct £ — fixed 2026-07-08).