---
id: T-0611
title: Very strong warning (typed confirmation) when finalising TODAY's plan; server-side guard
type: feature
state: review
created: 2026-07-17T13:09:41Z
updated: 2026-07-17T13:14:00Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Finalising a FUTURE date behaves exactly as before (no new friction)
  - Finalising TODAY shows the red modal; confirm stays disabled until the user types TODAY; cancel backs out cleanly
  - Bypassing the UI (direct POST without confirm_today=1) is rejected server-side with a clear error for today's date
  - The same-day finalise is written to sketch-planner.log with user + sketch id
out_of_scope: []
code_anchors:
  - path: backend/web/js/SketchPlanner.js
    note: finalize() flow + confirm dialog machinery
  - path: backend/controllers/RoutePlannerController.php:2341
    note: actionSketchFinalize — server guard
  - path: backend/views/route-planner/sketch-planner.php
    note: confirm-overlay modal pattern to extend
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1310
    model: claude-fable-5
    started: 2026-07-17T13:10:17Z
    status: completed
    ended: 2026-07-17T13:14:00Z
    summary: Built the strong same-day finalise protection. Finalising a sketch dated TODAY now opens a red warning dialog stating that it replaces the live run plan drivers are working from, showing the run/stop counts and highlighting any unassigned contracts that would be left off — and the confirm button stays disabled until the user literally types the word TODAY. The server independently refuses a same-day finalise that doesn't carry the confirmation flag, so a stale browser tab or direct API call can't slip past, and both refused and confirmed same-day finalises are written to the sketch-planner log with the user id for the audit trail. Finalising future dates is completely unchanged — no new friction on the normal daily workflow.
    test_plan: |-
      1. Pull branch on sandbox + hard refresh. Open a sketch for TODAY, click Finalize: red dialog appears; confirm button disabled; typing "today"/"Today" keeps it disabled; typing TODAY enables it; Cancel closes with nothing changed.
      2. Type TODAY and confirm: finalise proceeds normally; sketch-planner.log gains a 'FINALIZE-TODAY … SAME-DAY finalize CONFIRMED' line with your user id.
      3. Open a sketch for a FUTURE date, Finalize: the ordinary confirm dialog, exactly as before.
      4. Server guard: POST to /route-planner/sketch-finalize for a today-dated sketch without confirm_today (curl or devtools replay): JSON error with needs_today_confirm=true, nothing finalised, log gains a 'finalize-blocked' line.
      5. Edge: leave a today sketch open past midnight, finalize tomorrow-morning — the date is no longer today so the normal dialog shows (correct).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - release-blocker
  - ui
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-17T13:14:00Z
version: 4
---

## Problem
Austin's top release worry: someone overwriting a good live plan — especially ON the delivery day, when vehicles may already be loading or out. Finalising today's date replaces the live run plan; today it's one click.

## Fix
- Client: when the sketch's plan date == today, the finalise flow shows a distinct red warning modal — "You are replacing TODAY'S live plan; vehicles may already be loading or on the road" — requiring the user to TYPE the word TODAY to enable the confirm button. Normal (future-date) finalise flow unchanged.
- Server: sketch-finalize rejects a same-day finalise unless the request carries confirm_today=1 — the guard holds even if the JS is bypassed/stale.
- Log the same-day finalise (who, when, sketch id) via SketchPlanLogger for the audit trail.

## Conversation

**2026-07-17 13:14 claude-code:** Run run-20260717-1310 completed — Built the strong same-day finalise protection. Finalising a sketch dated TODAY now opens a red warning dialog stating that it replaces the live run plan drivers are working from, showing the run/stop counts and highlighting any unassigned contracts that would be left off — and the confirm button stays disabled until the user literally types the word TODAY. The server independently refuses a same-day finalise that doesn't carry the confirmation flag, so a stale browser tab or direct API call can't slip past, and both refused and confirmed same-day finalises are written to the sketch-planner log with the user id for the audit trail. Finalising future dates is completely unchanged — no new friction on the normal daily workflow.
