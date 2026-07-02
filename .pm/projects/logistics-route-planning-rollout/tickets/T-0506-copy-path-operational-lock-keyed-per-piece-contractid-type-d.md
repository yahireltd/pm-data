---
id: T-0506
title: Copy-path operational lock keyed per piece ({contractID}|{type}) — deferred asymmetry closed
type: bug
state: triaged
created: 2026-07-02T12:57:55Z
updated: 2026-07-02T12:57:55Z
project: logistics-route-planning-rollout
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
  channel: claude-code session with Austin
assignee: null
acceptance_criteria:
  - A locked delivery piece no longer skips the same contract's unlocked collection piece on the copy path
  - A warehouse-touched delivery row can never be reassigned while processing the contract's collection stop
  - deleteExistingRuns(skipLocked) preserves pushed and in-progress (status>=30) runs, not just manually locked ones
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
agent_runs: []
labels: []
attention: null
version: 1
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