---
id: T-0645
title: "Phantom finalized plans and phantom lock stamps: sketch state must be backed by real runs"
type: bug
state: done
created: 2026-07-22T16:19:32Z
updated: 2026-07-22T19:07:15Z
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
  - "[x] A finalized sketch whose runs were all deleted is demoted to superseded on load, with the purple banner offering it back, and the date loads as a normal draft/mirror"
  - "[x] A date replanned from scratch on the run planner releases the sketch the same way; an edited-in-place plan stays finalized and syncs"
  - "[x] Routes whose runs are gone lose their padlock/ON-ROAD badge within one poll cycle or on reload, and their jobs are replannable by the solver"
  - "[x] Re-solving after reopening a phantom does NOT flip it back to finalized"
  - "[x] The Date Already Finalized dialog only appears when the finalized plan's own runs are active"
out_of_scope: []
code_anchors:
  - path: common/models/SketchPlan.php
    note: isBackedByActiveRuns() — run_id overlap with the date's active runs
  - path: backend/controllers/RoutePlannerController.php
    note: findOrCreateSketchForDate demote branch; solver-run view gate; lock-sync poll clears dead-run stamps
  - path: common/services/SketchPlanService.php
    note: stampRunLockStates clearing branch; createFromSolverRun resolve-restore requires isBackedByActiveRuns
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-1619
    model: claude-fable-5
    started: 2026-07-22T16:19:48Z
    status: completed
    ended: 2026-07-22T16:20:08Z
    summary: |-
      Austin found a date that claimed to be finalised, showed a vehicle "on the road", and blocked every attempt to replan it — while the run planner showed a completely empty day. The cause: a week-old test plan whose runs had since been deleted kept its "finalised" label and its frozen lock badges, and the system trusted the label over reality. Every fresh solve got hidden behind the ghost, so the date was effectively unplannable.

      Now the sketch's claims must be backed by reality: a finalised plan whose own runs are gone (deleted, or replaced by a manual replan on the run planner) steps aside automatically — it becomes restorable history and the date opens up for normal planning. Padlock and "on road" badges disappear within seconds when the run behind them no longer exists, making those jobs editable and replannable again. Plans merely edited on the run planner keep working exactly as before. Without this, any date whose runs were deleted after finalising would have become a trap: looking planned to the office while being empty for the drivers.
    test_plan: |-
      On the routing test box (deployed; re-check item 1 on live after its git pull):

      1. Phantom demote: finalise a future date → on the run planner, DELETE all its runs → reload the sketch date → loads as DRAFT with the purple banner offering the old plan; log shows "none of its runs are active"; run planner unchanged (empty).
      2. Manual replan release: finalise a date → delete all runs AND build 2 manual runs → reload sketch → DRAFT mirroring the manual runs, old plan restorable from the banner.
      3. Edited-in-place still fine: finalise → move a job between runs on the run planner → reload sketch → still FINALIZED, showing the edited layout.
      4. Phantom lock clears: with a sketch open showing a locked/ON-ROAD route, delete that run on the run planner → within ~10s (poll) or on reload the padlock and ON ROAD badge disappear and the route is editable; Re-solve now replans those jobs.
      5. No flip-back: reopen a phantom-finalised plan (Edit Plan) → re-solve → it stays a draft and the solver output is the working candidate (not buried).
      6. Split collapse regression: finalise a plan containing a same-run split (pieces recombine) → reload → still FINALIZED (no false demotion — the check is link-level).
      7. Cross-impact: normal supersede/restore (T-0613 flow) unchanged — finalise A, re-solve, finalise B, restore A all behave as before.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: written
labels:
  - sketch-planner
  - state-machine
  - T-0613-family
attention: null
version: 11
---

## Problem (found by Austin's 28 Jul testing, 22 Jul)

Sketch 626 was finalized on 16 Jul in sandbox testing; its runs were later deleted, but the sketch kept status 'finalized'. Loading the 28th rendered the week-old plan (including a locked "ON ROAD" route for a vehicle not on any live plan), the "Date Already Finalized" dialog hijacked solver-run views, and the reopen protection kept flipping the phantom back to finalized — burying every fresh solve as an archived candidate while the run planner showed an empty day. Separately, the stale route kept its locked/ON-ROAD stamps because every lock-state reader skipped routes whose run no longer exists, and the 10s poll echoed the sketch's own stale flag back as truth.

## Fixes (three commits, deployed to routing test box)

1. Phantom guard: a finalized sketch is demoted to SUPERSEDED (restorable via the banner) when it is no longer backed by runs; the date falls through to the normal draft/live-mirror flow.
2. Backing = OVERLAP with the sketch's OWN runs (SketchPlan::isBackedByActiveRuns), not mere run existence — so a manual run-planner replan-from-scratch also releases the date. Link-level on purpose: in-place run edits keep run ids (still backed, synced over as before) and finalize's same-run split collapsing never changes route/run linkage, so neither falsely demotes.
3. Phantom lock stamps: stampRunLockStates, the draft date-load, and the lock-sync poll now CLEAR locked/auto_locked/pushed/run_status when the backing run is deleted/deactivated (dropping the dangling run link on hard delete) — the route becomes editable and the solver may replan its jobs.

## Conversation

**2026-07-22 16:20 claude-code:** Run run-20260722-1619 completed — Austin found a date that claimed to be finalised, showed a vehicle "on the road", and blocked every attempt to replan it — while the run planner showed a completely empty day. The cause: a week-old test plan whose runs had since been deleted kept its "finalised" label and its frozen lock badges, and the system trusted the label over reality. Every fresh solve got hidden behind the ghost, so the date was effectively unplannable.

Now the sketch's claims must be backed by reality: a finalised plan whose own runs are gone (deleted, or replaced by a manual replan on the run planner) steps aside automatically — it becomes restorable history and the date opens up for normal planning. Padlock and "on road" badges disappear within seconds when the run behind them no longer exists, making those jobs editable and replannable again. Plans merely edited on the run planner keep working exactly as before. Without this, any date whose runs were deleted after finalising would have become a trap: looking planned to the office while being empty for the drivers.

---

**2026-07-22 19:07 — you**

done
