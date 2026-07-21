---
id: T-0506
title: Copy-path operational lock keyed per piece ({contractID}|{type}) — deferred asymmetry closed
type: bug
state: done
created: 2026-07-02T12:57:55Z
updated: 2026-07-21T12:33:13Z
project: route-planner-release
section: null
parent: null
children: []
order: 6144
priority: p2
reporter:
  kind: agent
  name: claude-code
  channel: claude-code session with Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A locked delivery piece no longer skips the same contract's unlocked collection piece on the copy path"
  - "[x] A warehouse-touched delivery row can never be reassigned while processing the contract's collection stop"
  - "[x] deleteExistingRuns(skipLocked) preserves pushed and in-progress (status>=30) runs, not just manually locked ones"
out_of_scope: []
code_anchors:
  - file: common/services/CopyPlanToRunService.php
    symbol: executeCopy / partitionLockedPieces / deleteExistingRuns
  - file: common/tests/unit/models/CopyPlanPieceLockTest.php
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1524
    model: claude-fable-5
    started: 2026-07-17T15:24:08Z
    status: completed
    ended: 2026-07-17T15:24:26Z
    summary: "Retrospective close-out — fixed 2 July under this ticket (commit 97d9a00a on the release branch), run never recorded at the time. The copy path's operational lock was keyed by contract only, so a locked delivery also froze the same contract's unlocked collection; it now keys per piece ({contractID}|{type}), matching the live finalize path. Two adjacent holes closed in the same pass: warehouse-touched rows also keyed per piece so a collection stop can never reassign the contract's loaded delivery row, and the run-deletion sweep now preserves pushed and in-progress runs (status ≥ 30), not just manually locked ones. Note from the original work stands: all three public copy entry points have no callers (the live path is SketchPlanService::finalize, which only borrows helpers) — fixed anyway so the parallel implementation is safe if ever resurrected, and it remains a candidate for deletion in a future cleanup. Unit coverage: CopyPlanPieceLockTest (7 cases) plus the ReplanSafety/SplitPreservation/OperationallyLocked/FinalizeProtection suites, green at the time."
    test_plan: "No direct in-app test possible — the copy path has no UI callers. The equivalent live-path semantics (per-piece locks on finalize) are exercised by the weekend checklist: lock a route containing a contract's delivery while its collection sits on an unlocked route → re-solve + finalize → the collection moves freely, the delivery stays. If that passes (scenario S1/S5 of ADR-001), this ticket's semantics are verified by proxy. Optional cleanup ticket later: delete the dead copy entry points."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - sketch-planner
  - route-planning
attention: null
version: 12
branch: PickingSketchSalesDashFriday
---

## Problem
`CopyPlanToRunService::executeCopy()` keyed its operational lock (manual lock OR pushed to driver OR status ≥ 30) by `contractID` only, while `SketchPlanService::finalize()` already keys per piece (`{contractID}|{type}`). So on the copy path, a lock on a contract's delivery also skipped its collection when both fell on the same plan date. This was the deferred half of the replan-safety work (the re-solve half was fixed in commit 74077dbd); Austin asked to close it on 2026-07-02.

Two adjacent holes found and fixed in the same pass:
- The protected-piece (warehouse-touched) tier had the same contract-level keying: processing a collection stop could look up — and reassign — the contract's loaded/locked DELIVERY row to the new run.
- `deleteExistingRuns(skipLocked: true)` only skipped manually-locked runs (`locked = 0` filter), so a pushed or in-progress (status ≥ 30) run on a plan vehicle would have been deleted wholesale by the sweep.

## Fix
Mirrors SketchPlanService::finalize() semantics:
- Plan pieces collected as `{cid}|{D|C}` keys; locked run contracts keyed the same way.
- New pure helper `partitionLockedPieces()` computes processable pieces + contracts with ≥1 unlocked piece (drives the reset/delete scope and the all-locked abort).
- Protected (touched) rows now keyed per piece, with locked-overrides-touched, so cross-type reassignment is structurally impossible.
- `deleteExistingRuns` now skips via `YaRuns::operationallyLockedCondition()`.

## Note
All three public copy entry points (`copyPlanToRuns`, `copyCurrentPlanToRuns`, `buildCopyPreview`) currently have NO callers — the live finalize path is `SketchPlanService::finalize()`, which only borrows `getProtectedRunContracts()`/`distributeItemsToPieces()` from this service. The copy path appears to be dead code kept as the parallel implementation; fixed anyway so it is safe if resurrected. Candidate for deletion in a future cleanup.

## Test plan
- Unit: `CopyPlanPieceLockTest` (7 cases) — green; full related suites (ReplanSafety, SplitPreservation, OperationallyLocked, FinalizeProtection) — green.
- In-app: none possible directly (path has no UI callers); the piece-key semantics match the already-verified sketch finalize behaviour.

## Conversation

**2026-07-17 15:24 claude-code:** Run run-20260717-1524 completed — Retrospective close-out — fixed 2 July under this ticket (commit 97d9a00a on the release branch), run never recorded at the time. The copy path's operational lock was keyed by contract only, so a locked delivery also froze the same contract's unlocked collection; it now keys per piece ({contractID}|{type}), matching the live finalize path. Two adjacent holes closed in the same pass: warehouse-touched rows also keyed per piece so a collection stop can never reassign the contract's loaded delivery row, and the run-deletion sweep now preserves pushed and in-progress runs (status ≥ 30), not just manually locked ones. Note from the original work stands: all three public copy entry points have no callers (the live path is SketchPlanService::finalize, which only borrows helpers) — fixed anyway so the parallel implementation is safe if ever resurrected, and it remains a candidate for deletion in a future cleanup. Unit coverage: CopyPlanPieceLockTest (7 cases) plus the ReplanSafety/SplitPreservation/OperationallyLocked/FinalizeProtection suites, green at the time.

---

**2026-07-21 12:33 — you**

done
