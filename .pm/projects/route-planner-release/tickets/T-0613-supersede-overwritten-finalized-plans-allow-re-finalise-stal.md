---
id: T-0613
title: Supersede overwritten finalized plans; allow re-finalise; staleness guard on finalize
type: feature
state: review
created: 2026-07-17T14:00:59Z
updated: 2026-07-20T15:37:22Z
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
  - "[x] Finalizing a candidate on an already-finalized date flips the old sketch to superseded (log entry with both ids); only one finalized sketch per date exists afterwards"
  - "[x] A superseded sketch can be opened and re-finalised; doing so supersedes the newer one and is logged as a restore"
  - "[x] Re-finalising a plan containing a contract moved to another date / cancelled: that stop is SKIPPED with a clear warning naming contract + reason; remaining stops finalize normally; audit passes on the result"
  - "[x] A contract whose weight changed >10% since the sketch produces a warning but still finalises"
  - "[x] Same-day typed confirmation (T-0611) still applies to restores"
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: finalize() — supersede flip + staleness guard in the stop loop
  - path: backend/web/js/SketchPlanner.js
    note: status handling / finalize button visibility for superseded
  - path: backend/views/route-planner/sketch-planner.php
    note: status pill + button
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1401
    model: claude-fable-5
    started: 2026-07-17T14:01:14Z
    status: completed
    ended: 2026-07-17T14:07:20Z
    summary: "Built Austin's decisions on overwritten finalized plans, plus the staleness protection his follow-up questions identified. (1) When a plan is finalized over an already-finalized date, the old plan is now marked SUPERSEDED — a distinct, findable state — with a loud log entry naming both plans and the user; there is only ever one finalized plan per date. (2) Overwritten plans are recoverable: a purple banner on the sketch page lists them with Restore buttons. Restoring deliberately brings the old plan back as the working DRAFT rather than re-finalising it directly — it then passes through the normal load flow, which flags every contract that changed since, and the normal finalize with all its safety checks. (3) The new staleness guard runs on EVERY finalize: stops whose contract has moved date, become self-collection/self-return, or been cancelled are skipped with named warnings; contracts whose weight drifted more than 10%, whose address/postcode changed, whose time window changed, or whose service time increased still finalise — with current details written fresh — but warn explicitly that the route was planned around the old assumptions and suggest a re-solve. Every skip and warning also lands in the sketch-planner log."
    test_plan: |-
      1. Pull branch + hard refresh. Finalize date X with sketch A; re-solve to a candidate and finalize that: sketch A flips to superseded (log: finalize-supersede), purple banner appears on the date with "Restore #A".
      2. Click Restore: confirm dialog → plan A becomes the working draft (previous draft archived), page reloads, auto-refresh delta banner reports any contract changes since A was drawn.
      3. Staleness skips: move one of A's contracts to another date in sales, then finalize A: that stop is SKIPPED with a warning naming the contract and the new date; audit passes on the result; run planner shows the rest.
      4. Staleness warnings: change a contract's weight >10% (or its delivery window/postcode/duration), finalize: warning lists exactly what changed ("planned X, now Y ... consider a re-solve") but the stop IS finalised with current details.
      5. Same-day dialog still fires when restoring+finalising a TODAY plan.
      6. planner-audit/date after each of the above: PASS every time (the audit now always compares against the single current finalized sketch).
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
  since: 2026-07-17T14:07:20Z
version: 12
---

## Problem (Austin's decision on the T-0612 drift finding)
When a re-solved candidate is finalized over an already-finalized date, the old finalized sketch silently stays status=finalized and diverges from the runs (sketch #615 case). Decision: mark the overwritten plan **superseded** with proper logging; keep it possible to re-finalise a superseded plan. New concern raised: re-finalising an OLD superseded plan whose contracts have since changed (moved date, cancelled, amended weights) must not create phantom or stale runs.

## Build
1. On successful finalize, any OTHER finalized sketch for the same date flips to status='superseded' — logged (finalize-supersede: old id, new id, user). Exactly one finalized sketch per date from then on.
2. Superseded sketches can be re-finalised: the finalize action accepts them (logged as finalize-restore); the flip in (1) then supersedes the newer plan symmetrically. UI: superseded plans show a "Re-finalise (restore this plan)" button and a distinct status pill.
3. **Staleness guard in finalize (applies to every finalize)**: before creating a run contract, verify the stop's contract still belongs to the date (D: hireStartDate == planDate and delMethod != 5; C: hireEndDate == planDate and colMethod != 11; unconverted = 0; contract exists). Violations are SKIPPED with a warning naming the contract + reason (returned in the response the UI already alerts, and logged finalize-skip). Also warn (not skip) when the contract's current weight differs >10% from the sketch pieces' sum — stale amounts signal.

## Conversation

**2026-07-17 14:07 claude-code:** Run run-20260717-1401 completed — Built Austin's decisions on overwritten finalized plans, plus the staleness protection his follow-up questions identified. (1) When a plan is finalized over an already-finalized date, the old plan is now marked SUPERSEDED — a distinct, findable state — with a loud log entry naming both plans and the user; there is only ever one finalized plan per date. (2) Overwritten plans are recoverable: a purple banner on the sketch page lists them with Restore buttons. Restoring deliberately brings the old plan back as the working DRAFT rather than re-finalising it directly — it then passes through the normal load flow, which flags every contract that changed since, and the normal finalize with all its safety checks. (3) The new staleness guard runs on EVERY finalize: stops whose contract has moved date, become self-collection/self-return, or been cancelled are skipped with named warnings; contracts whose weight drifted more than 10%, whose address/postcode changed, whose time window changed, or whose service time increased still finalise — with current details written fresh — but warn explicitly that the route was planned around the old assumptions and suggest a re-solve. Every skip and warning also lands in the sketch-planner log.

**2026-07-20 15:21 claude-code:** **Sandbox test failure (20 Jul, plan 2026-07-21, sketches 686/689): restore didn't work. Root cause found and fixed — three stacked bugs.**

What Austin saw: finalise plan A → Edit Plan → re-solve → finalise plan B. No purple "Restore #A" banner ever appeared.

**Bug 1 — missing migration (the fatal one).** `sketch_plans.status` was `enum('draft','finalized','archived')` — no `superseded` member. The T-0613 code was writing a value the column can't hold (non-strict MySQL coerces it to `''`), so no plan could ever become superseded and the restore banner query always came back empty. Fixed by new migration `m260720_160000_t0613_sketch_status_superseded` (adds the enum member + a `finalized_at` column). **This migration is now part of the Monday live deploy — without it T-0613 is inert.** Release plan updated (9 migrations, was 8).

**Bug 2 — client auto-save wipes the server's plan markers.** Sketch auto-save round-trips only the browser-side plan keys (routes/unassigned/solver_params), and `updatePlan()` replaced `plan_json` wholesale. That destroyed `finalize_baseline_snapshot` — which was both the post-finalise drift baseline AND the "this draft was previously finalised" marker the re-solve protection relied on. Confirmed in the data: sketch 686's plan_json had lost the snapshot entirely. Fix: `updatePlan()` now merge-preserves server-owned keys, and the reopen detection uses the new `finalized_at` DB column, which a plan-json write can't touch.

**Bug 3 — the Edit-Plan detour bypassed supersede.** Reopen flips finalized→draft; the next solve then archived that draft as an ordinary scratch draft, so at finalise time there was no 'finalized' plan left to supersede. Fix: anything with `finalized_at` set is treated as plan history everywhere — the finalise guard supersedes it even if it's sitting as draft/archived, and the draft-archiving sweeps mark ex-finalised drafts superseded (restorable) instead of archived.

Also fixed: restore now redirects to `?id=<plan>` (the `?date=` load prefers the still-finalised newer plan and would have displaced the restored draft straight back), and superseded plans are edit-blocked until restored.

**Sandbox state:** fix deployed (commits c03d7868 + fa6bee33), migration applied, and sketches 686/689 flipped to superseded with their true finalise times from the logs — the 21 Jul sketch page should now show the restore banner offering both. Test plan updated with the Edit-Plan detour scenario and a baseline-survives-autosave check.
