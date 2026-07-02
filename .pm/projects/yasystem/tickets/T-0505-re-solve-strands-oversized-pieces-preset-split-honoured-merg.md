---
id: T-0505
title: "Re-solve strands oversized pieces: preset-split honoured merged run-contract rows verbatim (C091282)"
type: bug
state: triaged
created: 2026-07-02T12:57:40Z
updated: 2026-07-02T14:16:48Z
project: yasystem
section: null
parent: null
children: []
order: 40960
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: claude-code session with Austin
assignee: null
acceptance_criteria:
  - Re-solve of a date whose run contracts contain merged (oversized) split rows re-splits by capacity instead of stranding pieces
  - Genuine manual splits with fleet-loadable pieces are still honoured verbatim on re-solve
  - Discarded presets emit a [Jobs][PRESET-SPLIT] warning log naming the contract and piece sizes
out_of_scope: []
code_anchors:
  - file: common/components/RoutePlannerNew.php
    symbol: buildJobsForDate / presetPiecesFitFleet
  - file: common/services/CopyPlanToRunService.php
    symbol: mergeSequentialSplits
  - file: common/tests/unit/models/PresetSplitSanityTest.php
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sketch-planner
  - route-planning
attention: null
version: 5
branch: PickingSketchSalesDashFriday
---

## Problem
C091282 (03/07/2026, ~8570kg / ~594m³ event job to Olympia) would not split into Full-Luton chunks on re-solve. Two pieces of 3888kg / 259m³ sat permanently in the unassigned pool — no vehicle in the fleet can carry them, so the solver could never place them.

## Root cause (three-step interaction, verified in code)
1. An earlier solve split the contract into Luton-size pieces; several landed consecutively on the same vehicle.
2. Finalize's `mergeSequentialSplits()` (CopyPlanToRunService, same logic in SketchPlanService) recombined contiguous same-vehicle pieces into ONE `ya_run_contracts` row with SUMMED weight/volume — hence 3888kg/259m³ rows. This merge is intentional (cleaner warehouse view).
3. The preset-split honouring added 2026-04-27 (commit ea6c4288, `RoutePlannerNew::buildJobsForDate`) sees >1 rows for a contract/type/date and feeds those rows to the solver verbatim, explicitly with no capacity capping. Merged sums exceed every vehicle → pieces strand unassigned.

Surfaced only now because the June live-first mirroring made re-solving already-finalised dates the normal workflow.

## Fix
`RoutePlannerNew::buildJobsForDate`: before honouring a preset split, validate every piece fits the largest vehicle in today's fleet (new pure helper `presetPiecesFitFleet()`). If any piece is oversized — or all pieces are zero-size on a non-zero contract — the preset is discarded with a warning log (`[Jobs][PRESET-SPLIT] ... discarding`) and the normal capacity splitter runs instead. Genuine manual splits (which are loadable by construction) are still honoured, including pieces above the split-strategy size that target a bigger truck. This also self-heals dates already carrying merged rows: the next re-solve re-splits them correctly.

## Test plan
- Unit: `PresetSplitSanityTest` (11 cases) — green.
- In-app: open the sketch planner for 03/07/2026 and Re-solve with Full Luton selected. C091282 should now appear as ~8 pieces of ≤1140kg/≤98m³ and get planned instead of stranding 2 oversized pieces in the pool. Check `sketch-planner.log`/app log for the `[Jobs][PRESET-SPLIT] ... discarding` line.
- Cross-impact: a date with a genuine manual split whose pieces all fit a real vehicle must still keep that split on re-solve (preset honouring unchanged for valid presets).

## Conversation

**2026-07-02 13:39 claude-code:** **Follow-up (commit 07edc907): Austin confirmed the preset feature "landed wrong" — it was built to keep MANUAL splits made by logistics on the run planner, but fired on bare row-count.** Reworked so honouring requires human intent, and the intent markers now survive re-solves.

**What counts as intent now (either one):**
- A manual run-planner split — `actionSaveSplit` stamps `ya_contracts.splitFlag` + `deliverySplits`/`collectionSplits` for the piece type. This is the original use case.
- A preserved piece — "Keep this split on finalise" tick or a stand label typed on the sketch (`ya_run_contracts.piecePreserved` / `pieceNote`).

Multiple rows alone no longer trigger honouring: every finalized solver split leaves multiple rows, so the old rule froze the solver's own past output into all future re-solves (and, merged, produced the C091282 stranding).

**Round-trip fix (the deeper "landed wrong"):** the preserve markers previously survived exactly one finalize. Solver-built sketch stops and run-mirrored stops both dropped `preserve_split`/`piece_note`, so the next finalize rewrote rows without `piecePreserved` and the split silently stopped being honoured. Now honoured preset jobs carry the markers, all three stop-builder paths keep them (solver routes, unassigned pool, mirror-from-runs), and finalize rewrites them. Manual splits self-mark as preserved, which also stops `mergeSequentialSplits` recombining their pieces.

The fleet-capacity gate from the original fix stays as a safety net for already-corrupted rows.

**Test plan additions:** `PresetSplitSanityTest` now 18 cases (intent detection covered). In-app: (1) split a contract manually on the run planner, re-solve the date on the sketch — pieces keep the manual shape and show "Keep this split on finalise" ticked; finalize + re-solve again — shape still kept. (2) A solver-split contract with nothing ticked re-splits freely by capacity each re-solve. (3) Tick "keep this split" on a solver split, finalize, re-solve — shape now kept.

**2026-07-02 14:00 claude-code:** **Third commit (b-branch `PickingSketchSalesDashFriday`): item-level preservation.** Austin confirmed logistics splits are sometimes item-deliberate ("chairs in van 1, staging in van 2"), so preserving weights/volumes alone wasn't enough — finalize was re-dealing the items with the weight-greedy `distributeItemsToPieces` even when the split shape was honoured.

**How it works now:** before finalize's cleanup deletes the old rows, it snapshots the per-piece `ya_run_contract_items` for every split whose stops carry preserve markers (manual run-planner splits self-mark via the preset). The old rows (ordered `typeCount, id`) map 1:1 onto the new stops' `piece_sequence` because the re-solve honoured them in that same order, so the human's item assignment is replayed piece-for-piece. Guards: replay only happens when the snapshot's piece count matches the stop's `piece_count` (a capacity re-split means the snapshot no longer describes the plan → weight distribution as before), and empty/zero-qty piece maps fall back too.

**Test plan additions:** `DeliberateSplitItemsTest` (5 cases) — green; full planner suites green (104 tests). In-app: split a contract on the run planner putting a distinctive item entirely in piece 2; re-solve the date on the sketch; finalize; open the run planner and check piece 2 still holds exactly that item (not a weight-balanced mix). Edge: change the split type so the solver re-splits by capacity — items should redistribute by weight (old behaviour) with no errors.

**Known remaining limitation:** if the contract's items are amended AFTER the manual split (qty increased), the replayed assignment reflects the split-time quantities — same staleness the run planner itself has for manual splits. Not addressed.
