---
id: T-0612
title: Conservation audit command (plan vs finalised runs) + finalise decision logging
type: feature
state: review
created: 2026-07-17T13:09:55Z
updated: 2026-07-17T13:22:23Z
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
  - php yii planner-audit/date <date> runs on the sandbox and reports PASS on a healthy finalised date
  - Deliberately breaking a date (delete one run-contract item row in a transaction) flips the relevant check to FAIL naming the contract
  - Item totals check validates split contracts piece-by-piece
  - Run start/end/max W+V and per-stop accrued figures verified against recomputation
  - finalise writes one log line per contract-level decision (distribution source, preserved/abandoned splits, skipped stops) to sketch-planner.log
out_of_scope: []
code_anchors:
  - path: console/controllers/PlannerAuditController.php
    note: new command
  - path: common/services/SketchPlanService.php
    note: finalize — add decision logging; recalculateRunTotals maths to mirror in audit
  - path: common/components/RoutePlannerNew.php:1133
    note: buildJobsForDate eligibility filters to mirror for the due-contract check
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1314
    model: claude-fable-5
    started: 2026-07-17T13:14:20Z
    status: completed
    ended: 2026-07-17T13:22:23Z
    summary: "Built the automated conservation audit and the finalise decision logging. One command (planner-audit/date) now verifies for any date that nothing was silently lost between plan and live runs: every due contract is on a run (with expected skips listed separately), every finalized-sketch stop exists as a run row, item quantities are conserved contract-by-contract and piece-by-piece, weights/volumes and the per-stop running-load figures match recomputation, and no rows are stranded on deleted runs. It exits non-zero on any failure so it can be run after every finalise during testing and week one. Finalise itself now logs every decision that could explain a missing item: which item-distribution each split piece got (logistics' deliberate split replayed vs the automatic one), warnings when a split falls back to a full copy, and loud entries when a route or stop is skipped entirely. First real run on the sandbox proved both sides: items, weights and orphan checks all PASS across 13 runs — and the sketch-vs-runs check correctly caught that the test date's finalized sketch had been overwritten by a later re-solve without re-finalizing (its plan shows 10 routes and ~20 stops that the 9-run finalize never created — confirmed by the finalize log showing a clean complete run and those contracts having zero rows ever). That drift-detection is exactly the class of problem the audit exists to surface."
    test_plan: |-
      1. Pull branch on sandbox. Run `php yii planner-audit/date <date>` for a freshly-finalized clean date — expect PASS with warnings only for genuinely unplannable contracts (zero weight, bad postcode).
      2. Break a date deliberately in a transaction (delete one ya_run_contract_items row / one run-contract row) — audit flips to FAIL naming the contract; roll back.
      3. Finalize a plan with splits, then read runtime/logs/sketch-planner.log: one FINALIZE-ITEMS line per piece with source (deliberate_replay for logistics pre-splits, greedy for solver splits, full_copy for unsplit) and qty totals.
      4. OPEN QUESTION for Austin from the 27 Jul run: a finalized sketch's plan can be overwritten by a later re-solve (candidate flow) without re-finalizing — the audit flags it as drift. Decide whether that flow should re-stamp the sketch as draft (probably yes) — currently the finalized record silently diverges from the runs.
      5. Adopt as routine: run the audit after every finalise during weekend testing, and daily against live in week 1.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - route-planner
  - release-blocker
  - testing
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-17T13:22:23Z
version: 4
---

## Problem
Pre-release, Austin's core worry is silent loss: jobs or items missing from finalised run contracts, or weights/volumes wrong. Manual spot-checks don't scale to "test to destruction", and when something does look wrong we need logs to reconstruct what finalise decided.

## Build
1. Console command `php yii planner-audit/date YYYY-MM-DD` that verifies, for a date, the conservation invariants between the plan and the live runs:
   - every due contract (same eligibility as the solver's job builder) appears on active run contracts, or is explicitly listed as unplanned
   - sketch (finalized) stops ↔ active run-contract rows 1:1 per piece — nothing lost, nothing duplicated
   - item conservation per contract+type: per-piece item quantities sum to the contract's items
   - weight/volume conservation: piece weights sum to contract weight; run startWeight/endWeight/maxWeight/Vol equal recomputation from rows; accrued sequence equals recomputation
   - orphans: active run-contracts on inactive runs, duplicate rows beyond declared splits
   PASS/FAIL per check with the offending contract numbers. Exit code non-zero on FAIL (scriptable).
2. Extra SketchPlanLogger coverage at finalise decision points: per-contract item distribution chosen (deliberate replay vs greedy), preserved splits honoured/abandoned + why, any stop skipped and why, run timing/distance fields written.

Run the audit against every sandbox test date and (after release) against each live day in week 1.

## Conversation

**2026-07-17 13:22 claude-code:** Run run-20260717-1314 completed — Built the automated conservation audit and the finalise decision logging. One command (planner-audit/date) now verifies for any date that nothing was silently lost between plan and live runs: every due contract is on a run (with expected skips listed separately), every finalized-sketch stop exists as a run row, item quantities are conserved contract-by-contract and piece-by-piece, weights/volumes and the per-stop running-load figures match recomputation, and no rows are stranded on deleted runs. It exits non-zero on any failure so it can be run after every finalise during testing and week one. Finalise itself now logs every decision that could explain a missing item: which item-distribution each split piece got (logistics' deliberate split replayed vs the automatic one), warnings when a split falls back to a full copy, and loud entries when a route or stop is skipped entirely. First real run on the sandbox proved both sides: items, weights and orphan checks all PASS across 13 runs — and the sketch-vs-runs check correctly caught that the test date's finalized sketch had been overwritten by a later re-solve without re-finalizing (its plan shows 10 routes and ~20 stops that the 9-run finalize never created — confirmed by the finalize log showing a clean complete run and those contracts having zero rows ever). That drift-detection is exactly the class of problem the audit exists to surface.
