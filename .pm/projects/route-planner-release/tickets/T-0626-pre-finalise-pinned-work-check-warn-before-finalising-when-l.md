---
id: T-0626
title: "Pre-finalise pinned-work check: warn before finalising when locked/loading jobs won't move as drawn"
type: feature
state: review
created: 2026-07-20T16:52:11Z
updated: 2026-07-20T18:29:22Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Finalising a sketch that places a locked-run job on a different vehicle is refused with needs_lock_confirm and a named list (contract no, movement, run it stays on, sketch placement, reason)
  - Amber dialog shows the list; Cancel leaves everything untouched (no supersede, no runs written); 'Finalise around them' completes the finalise with the pinned jobs left as they are
  - No dialog when every pinned job's sketch placement matches its live run/vehicle (zero added friction in the normal mirrored case)
  - Warehouse-touched jobs moved to a different vehicle in the sketch trigger the dialog with reason 'warehouse loading has started'; same-vehicle touched jobs do not
  - Same-day sketches show the typed-TODAY dialog first, then the lock dialog; confirming both finalises exactly once
  - Locked skips appear as named warnings in the post-finalise summary
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: "finalize(): sketchPlacement recording in the contract-ids loop; lock divergence pre-check (returns needs_lock_confirm unless options[confirm_locks]); Gap-1 supersede moved BELOW the check so a refused finalise has no side effects; named locked-skip warnings in the write loop"
  - path: backend/controllers/RoutePlannerController.php
    note: actionSketchFinalize passes confirm_locks POST flag into service options
  - path: backend/web/js/SketchPlanner.js
    note: _doFinalize(confirmToday, confirmLocks); needs_lock_confirm handler; confirmLockFinalize/cancelLockFinalize; lockConfirmDialog data prop
  - path: backend/views/route-planner/sketch-planner.php
    note: amber pinned-jobs modal (table of contract/stays-on/sketch/reason)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260720-1652
    model: claude-fable-5
    started: 2026-07-20T16:52:21Z
    status: completed
    ended: 2026-07-20T16:52:42Z
    summary: |-
      When a planner finalises a sketch, jobs that are already committed on the live plan (run locked, pushed to the driver, or loading started in the warehouse) stay exactly where they are — the system has always protected them. The problem was that nobody was told until AFTER the button was pressed, and even then only as a count, not names. So the finalised runs could quietly differ from the picture the planner had just drawn, with no chance to unlock first.

      Now, before anything is written, finalise checks every pinned job against where the sketch puts it. If any would not move as drawn, the planner gets a clear warning dialog naming each job, the run it will stay on, what the sketch shows instead, and why it's pinned — with the choice to cancel and unlock those runs first, or knowingly finalise around them. If the sketch already matches the live position of every pinned job (the normal case), there is no dialog and no extra friction. Without this, a planner could believe the sketch layout had been applied when part of it hadn't — the exact overwrite-surprise risk Austin flagged for Monday's go-live.
    test_plan: |-
      All on the sandbox (13.43.184.10), branch tip 496057f3, using a FUTURE date with a solved sketch. Run `php yii planner-audit/date <date>` after each finalise.

      1. Baseline (no friction): finalise a sketch on a date with no locked/loading runs → NO amber dialog, finalise completes as before.
      2. Locked divergence: finalise once so runs exist → on the run planner, LOCK one run → in the sketch, drag a job OFF that locked run onto another vehicle → Finalise. EXPECT: amber "will NOT move as drawn" dialog naming the contract, the run+reg it stays on, "sketch shows it on <other vehicle>", reason "run locked by a planner".
      3. Cancel path: press "Cancel — I'll unlock first" → check nothing changed: no new runs, previous finalized sketch NOT superseded (check sketch-planner.log has finalize-blocked "Pinned jobs diverge" and NO finalize-supersede after it), audit unchanged.
      4. Confirm path: Finalise again → "Finalise around them" → completes; the pinned job is still on its locked run; post-finalise alert includes the named "on a locked run — left where it is" warning; audit PASS.
      5. No-divergence pinned: leave the locked run's jobs where the sketch mirrors them → Finalise → no dialog.
      6. Touched case: unlock runs; add an itemcountlog count against one job (mark loading in warehouse view) → move that job to a different vehicle in the sketch → Finalise → dialog reason "warehouse loading has started". Same-vehicle → no dialog.
      7. Same-day stacking: on a TODAY sketch with a locked divergence → typed-TODAY dialog first, then amber lock dialog; confirming both finalises exactly once (check log: one finalize-request … Finalize complete).
      8. Cross-impact: re-run S7 supersede/restore (T-0613) once — the Gap-1 supersede was MOVED below the new check inside finalize(); confirm finalize B still supersedes A and the restore banner appears. Also re-test one normal re-solve + finalise (createFromSolverRun path untouched but shares the service).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: "RELEASE-PLAN-2026-07-20.md: new 'Pinned-work warning' checklist block added to the weekend test plan"
labels:
  - sketch-planner
  - finalize
  - safety
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-20T16:52:42Z
version: 5
---

## Problem

Finalise already protects live work — jobs on operationally-locked runs (locked by a planner, pushed to the driver app, or driver started) are never moved, and warehouse-touched jobs are never moved to a different vehicle. But the planner only learned this AFTER pressing Finalise, via a bare count in the success alert ("2 contract(s) on locked runs — left untouched"). Too late to unlock first, and the affected jobs weren't even named. Result: the finalised runs could silently differ from the sketch picture, exactly the "what I drew isn't what I got" surprise Austin flagged (20 Jul).

## What was built

Inside the finalise request — before ANY side effect, including the T-0613 supersede flip — the server compares every pinned (contract, movement) pair against where the sketch places it. If any diverge, finalise refuses with `needs_lock_confirm` plus a named list: contract number, delivery/collection, the run it stays on (run code + vehicle reg), what the sketch shows instead, and the reason it's pinned. The client shows an amber dialog with the list and two choices: cancel (go unlock those runs on the run planner first) or "Finalise around them" (re-posts with `confirm_locks=1`). Pieces whose sketch placement already matches the live pinned run/vehicle don't trigger the dialog — it only appears when the outcome would genuinely differ from the picture. Same server-refuses/dialog/re-post pattern as the T-0611 typed-TODAY gate, and because the check runs inside the finalise call there is no check-then-lock race. Locked skips now also produce named post-finalise warnings (was a bare count).

## Conversation

**2026-07-20 16:52 claude-code:** Run run-20260720-1652 completed — When a planner finalises a sketch, jobs that are already committed on the live plan (run locked, pushed to the driver, or loading started in the warehouse) stay exactly where they are — the system has always protected them. The problem was that nobody was told until AFTER the button was pressed, and even then only as a count, not names. So the finalised runs could quietly differ from the picture the planner had just drawn, with no chance to unlock first.

Now, before anything is written, finalise checks every pinned job against where the sketch puts it. If any would not move as drawn, the planner gets a clear warning dialog naming each job, the run it will stay on, what the sketch shows instead, and why it's pinned — with the choice to cancel and unlock those runs first, or knowingly finalise around them. If the sketch already matches the live position of every pinned job (the normal case), there is no dialog and no extra friction. Without this, a planner could believe the sketch layout had been applied when part of it hadn't — the exact overwrite-surprise risk Austin flagged for Monday's go-live.

**2026-07-20 18:29 claude-code:** **Automated service-level test session on the sandbox (20 Jul evening, plan date 2026-07-21) — full supersede/restore/pinned-work matrix executed against the real DB, with `planner-audit` after every finalise. Two REAL bugs found, fixed, and regression-tested.**

**Bug A — partially-pinned split loses pieces (serious, audit-caught).** C091603's collection is a 2-piece split; with one piece's run locked, "Finalise around them" preserved the locked piece but deleted the unlocked sibling row and the per-movement locked-skip never rewrote it — the 2703kg movement shrank to 1319kg (audit FAILed on both items and weights). Fix `c208c127`: finalise now refuses outright — not confirmable — whenever a pinned (contract,type) also has unpinned live rows, naming the contract and piece counts, until the run is unlocked or the loading piece is drawn back on its current vehicle. Regression-tested: blocked with and without the confirm flag, then unlocked → clean finalise → audit PASS with the lost piece restored.

**Bug B — stale sketch weight written to run rows.** A contract whose weight changed after the sketch was drawn finalised with the old figure on the run row (78kg written vs 156kg actual), understating run loads despite the warning saying "finalised with CURRENT details". Fix `fca769e0`: single-piece stops now write the current contract weight/volume; split pieces keep their sketch share. Re-tested: drift warning still fires, audit PASS.

**Everything else PASSED:** pinned-divergence block fires with full named detail and zero side effects on cancel (no supersede, no writes); confirm path completes with the pinned piece untouched and a named warning; Edit-Plan detour (reopen → re-solve) flips the plan back to finalized with the solver output as a candidate — the durable finalized_at detection working; restore → draft → re-finalise supersedes the newer plan; editing a superseded plan is refused; client-shaped saves preserve the finalize baseline snapshot; weight and service-time drift warnings fire accurately (C091603's service time genuinely grew 82→150m on the sandbox); item/weight/orphan conservation PASS on every audit.

**Not covered (needs a browser, still on Austin's checklist):** the amber/purple/typed-TODAY dialogs actually rendering, concurrency between two users, map/pins, and UI drag-drop split flows. All the server behaviour behind those dialogs is now proven.

**Sandbox end state:** 21 Jul has sketch #689 finalized (audit PASS, 0 warnings), #686 and #698 superseded and restorable via the banner, no locked runs. Note #698 deliberately carries a tampered 78kg stop — restoring and finalising it is a ready-made demo of the weight-drift warning.
