---
id: T-0604
title: Finalised runs missing start/end/max weight & volume on the run planner
type: bug
state: review
priority: p1
created: 2026-07-16T17:18:29Z
updated: 2026-07-16T17:56:05Z
project: route-planner-release
section: null
parent: null
milestone: null
children: []
order: 11264
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - After finalising a sketch, each created run shows start, end and max weight AND volume on the run planner
  - Figures match the sketch board's START/END bars for the same route (same stop order, same maths)
  - Hand-built (non-sketch) runs are unaffected
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: finalise path writing runs/run contracts
  - path: backend/views/logistics/run-planner.php
    note: run header weight/volume display
  - path: backend/web/js/SketchPlanner.js
    note: routeMetrics computed — the start/end/peak maths to mirror
relates:
  - meeting:M-001
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1754
    model: claude-fable-5
    started: 2026-07-16T17:54:49Z
    status: completed
    ended: 2026-07-16T17:56:05Z
    summary: Fixed finalised runs showing no weight/volume figures on the run planner. The run planner header displays the run's peak load (max weight and volume — also used to check the vehicle class is big enough), but the sketch finalise's recalculation only wrote the start and end loads, never the peak — so every run created from a finalised sketch showed 0KG | 0%. The peak is now computed during the same per-stop walk the recalc already does (start fully loaded with deliveries, load rises when collections come aboard mid-route), matching exactly how the run planner's own recalculation works for hand-built runs. Start and end loads were already correct; contracts' accrued at-this-stop figures unchanged.
    test_plan: |-
      1. Pull branch on sandbox. Finalise a sketch for a test date, open the run planner: each created run's header shows non-zero max KG and max volume, and the start/end utilisation bars as before.
      2. Compare a run's figures to the sketch board's START/END bars and peak for the same route — should match (same maths, stop order respected).
      3. A run that's deliveries-only: max = start. A run with collections after deliveries: max > end and can exceed neither-start-nor-end cases correctly.
      4. Vehicle-class check (getVehicleClassRequired) now gets real maxWeight/maxVol on finalised runs — verify no false 'vehicle too small' flags.
      5. Hand-built runs unaffected (their recalc was already correct).
      Note: runs finalised BEFORE this fix still carry 0 — re-finalising the sketch (or any run planner edit that triggers recalculateRun) repairs them; release-week plans will be created fresh so no data migration needed.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - release-blocker
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T17:56:05Z
version: 6
due: 2026-07-20
---

## Problem (M-001 UAT outcome)
When a sketch is finalised, the run's weight/volume figures need calculating: contracts show the accrued weight/volume at their deliver/collect point, but the RUN should show start load, end load and peak (max) weight/volume — logistics report this doesn't currently show on runs created from a finalised sketch.

## Notes
The sketch board already computes exactly this per route (routeMetrics: start/end/peak W+V respecting stop order — deliveries reduce load, collections add). The finalise path needs to compute the same numbers and persist/display them on the run planner run header. Check what the run planner shows for hand-built runs vs sketch-finalised runs — the gap may be that finalise never writes whatever field the header reads, or the header derives it from run contracts in an order the finalised data doesn't satisfy.

## Conversation

**2026-07-16 17:56 claude-code:** Run run-20260716-1754 completed — Fixed finalised runs showing no weight/volume figures on the run planner. The run planner header displays the run's peak load (max weight and volume — also used to check the vehicle class is big enough), but the sketch finalise's recalculation only wrote the start and end loads, never the peak — so every run created from a finalised sketch showed 0KG | 0%. The peak is now computed during the same per-stop walk the recalc already does (start fully loaded with deliveries, load rises when collections come aboard mid-route), matching exactly how the run planner's own recalculation works for hand-built runs. Start and end loads were already correct; contracts' accrued at-this-stop figures unchanged.
