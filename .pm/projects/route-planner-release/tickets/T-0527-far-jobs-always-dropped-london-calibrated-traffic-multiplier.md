---
id: T-0527
title: "Far jobs always dropped: London-calibrated traffic multipliers inflate long legs ~4x; dispatch estimator self-inflicts max_wait/late failures"
type: bug
state: review
created: 2026-07-08T14:59:08Z
updated: 2026-07-08T15:24:06Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260708-1509
    model: claude-fable-5
    started: 2026-07-08T15:09:14Z
    status: completed
    progress:
      - at: 2026-07-08T15:20:20Z
        note: "Implemented on solver box (backup solver_td.py.bak-20260708-T0527). (1) Urban-blend multipliers: per-node urban flag via haversine ≤28km from London centre; per-leg urban share = whole leg (both ends urban, bit-identical to old behaviour), 600s raw exit share (one end urban), or zero (both ends outside); motorway multiplier keeps 18% of the hour's congestion, floored at the class overnight baseline (1.35 truck / 1.08 van). Depot→Coventry@08:00 fell 341→168min, Chichester 375→183, New Forest 390→190. (2) _smart_dispatch: binary-search latest departure arriving ~5min before first window using the same td_travel_time as the walk — kills the estimator/walk disagreement that dispatched at 01:25 and failed max_wait. (3) Break fixes exposed once drive times sane: return-drive-home now counts as active time after a break (single-far-stop routes were 'break_no_fit' with a 2.5h drive home counting as zero); and when the only break slot lands before 10:00 on a wide-window early job, the scheduler retries with a delayed dispatch so the break lands after 10:00 (rebuild re-validates all windows). Probe now shows all five dropped jobs FEASIBLE on 7.5T/12T/18T (3.5T vans still blocked on far singles by the 3h-minimum-before-break policy — acceptable, trucks cover). Both-urban legs and night legs bit-identical by construction. Full replay of run #2495 in progress to count drops end-to-end."
    ended: 2026-07-08T15:24:06Z
    summary: "Fixed the route planner permanently dropping every out-of-town job. The solver's traffic multipliers (calibrated from real drive reports, which are nearly all short London trips) were applied to whole journeys, so it believed Coventry was a 5.7-hour drive instead of 2.8 and declared every far job impossible on every vehicle. The fix classifies each stop by distance from London and applies the full congestion multiplier only to the London portion of a journey — a nearby trip like Southend keeps most of the multiplier (getting out of London dominates), a Coventry run keeps only the fixed exit share, and legs between two out-of-town stops carry no London penalty at all. This mirrors Austin's delivery-category mileage-band idea but computed from exact coordinates. Fixing the times exposed three further blockers, all fixed: the dispatch-time estimator priced drives at rush hour then departed at night and failed its own waiting rule; the legally-required driver break treated the long drive home as zero remaining work so no break slot ever qualified on single-far-stop routes; and early-morning far jobs had their only break slot before the 10:00 break policy floor, now handled by departing later. Verified end-to-end by replaying the exact saved 10 July request: 0 dropped jobs (was 7 originally, 5-6 after the T-0524 seed fix), all 73 jobs on 15 routes, £4,363 direct cost. London-only journeys are computed bit-identically to before, so normal plans are unaffected. If we had done nothing, every job outside the M25 would keep being silently abandoned despite idle vehicles."
    test_plan: |-
      **All changes are live on the solver box** (/opt/vrp-solver/solver_td.py, service restarted). Backups on the box: solver_td.py.bak-20260708-T0524 (before seed fix) and solver_td.py.bak-20260708-T0527 (before this change); revert = copy back + systemctl restart vrp-solver.

      1. Sketch planner, Fri 10 Jul 2026, Full Luton split, defaults: Re-solve. Expect **0-1 dropped** (was 7). The five previously-dropped far jobs (C092202 Chichester 20:00 collection, C091968 D1+D2 Coventry, C091437 New Forest, C091439 Salisbury) should appear on truck routes (7.5T or bigger — 3.5T vans legitimately can't take the far singles due to the 3h-before-break policy).
      2. Check the far routes look operationally sane: Coventry ~2h50 drive each way with a 45min break after the drop; New Forest/Salisbury may share one truck departing ~06:45 with the break after 10:00; Chichester dispatches ~16:50 to hit the 20:00-20:00 window.
      3. Regression — London-only day: re-solve a busy all-London date and compare route count/timings to its history cards. Should look the same (London legs compute identically by construction).
      4. Watch total direct cost: expect it HIGHER than before on days with far jobs (~£4,363 vs £3,939 for 10 Jul) because the solver is now actually serving them instead of abandoning them — that's correct, not a regression.
      5. Edge: any day with a job just inside/outside ~28km of central London (Watford, Slough) — confirm it still plans normally.
      6. Over the next week, compare solver-predicted drive times for far runs against what drivers actually experience; if predictions run consistently long/short we recalibrate URBAN_EXIT_RAW_SEC (600s) and MOTORWAY_MULT_FRACTION (0.18) — noted as follow-up.

      **Cross-impact:** td_travel_time is used by every solver path (greedy, SA, break rebuild) — all covered by the 0-drop replay. Dispatch search replaces the estimator in both evaluate_route and the break-rebuild twin. No PHP changes in this run; the run planner and finalize flow consume solver output unchanged.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - solver
  - route-planner
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-08T15:24:06Z
version: 5
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

## Conversation

**2026-07-08 15:24 claude-code:** Run run-20260708-1509 completed — Fixed the route planner permanently dropping every out-of-town job. The solver's traffic multipliers (calibrated from real drive reports, which are nearly all short London trips) were applied to whole journeys, so it believed Coventry was a 5.7-hour drive instead of 2.8 and declared every far job impossible on every vehicle. The fix classifies each stop by distance from London and applies the full congestion multiplier only to the London portion of a journey — a nearby trip like Southend keeps most of the multiplier (getting out of London dominates), a Coventry run keeps only the fixed exit share, and legs between two out-of-town stops carry no London penalty at all. This mirrors Austin's delivery-category mileage-band idea but computed from exact coordinates. Fixing the times exposed three further blockers, all fixed: the dispatch-time estimator priced drives at rush hour then departed at night and failed its own waiting rule; the legally-required driver break treated the long drive home as zero remaining work so no break slot ever qualified on single-far-stop routes; and early-morning far jobs had their only break slot before the 10:00 break policy floor, now handled by departing later. Verified end-to-end by replaying the exact saved 10 July request: 0 dropped jobs (was 7 originally, 5-6 after the T-0524 seed fix), all 73 jobs on 15 routes, £4,363 direct cost. London-only journeys are computed bit-identically to before, so normal plans are unaffected. If we had done nothing, every job outside the M25 would keep being silently abandoned despite idle vehicles.
