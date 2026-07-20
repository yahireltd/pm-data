---
id: T-0627
title: Per-piece reconciliation for partially-pinned splits at finalise (lift the hard block)
type: feature
state: triaged
created: 2026-07-20T20:42:34Z
updated: 2026-07-20T20:42:34Z
project: route-planner-release
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - "Finalising a sketch where a split movement has one piece on a locked run and one free piece completes WITHOUT the hard block: pinned row untouched, free piece written per the sketch"
  - "Item conservation: the free piece(s) carry exactly the complement of the pinned row's items — planner-audit checks 3 and 4 PASS"
  - Restored plans (stale route run_ids) reconcile via weight-nearest matching without duplicating the pinned piece
  - Fully-pinned splits and single-piece pinned movements behave as today (preserved, named warning, no dialog when placement matches)
  - The T-0626 pinned-divergence dialog still lists the movement with an accurate 'stays on' description
out_of_scope: []
code_anchors:
  - path: common/services/SketchPlanService.php
    note: "finalize(): partial-split hard block (to be replaced by reconciliation); lockedSet/touchedSet per-movement keying; item distribution cache + deliberate replay — the complement subtraction hooks in here; cleanup protection via lockedRunContractIds/protected_ids"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - sketch-planner
  - finalize
  - splits
  - post-release
attention: null
version: 1
---

## Problem

Since c208c127, finalise refuses (hard, not confirmable) when a split movement has SOME pieces on locked/loading runs and some free — because the write loop's pin handling is keyed per (contract,type) while pieces live per run, and finalising would delete the free sibling rows without rewriting them (the audit-caught C091603 loss: 2703kg → 1319kg). The block is safe but means the planner must unlock the run (or keep the piece on its current vehicle) before finalising.

## What "fixed properly" means

Finalise around a partial pin losslessly: keep the pinned piece row exactly as-is, and write the free sibling pieces from the sketch — with the item distribution giving the free pieces exactly the COMPLEMENT of the items already on the preserved pinned row (loaded items stay put; nothing doubled, nothing dropped). Requires matching sketch stops to pinned live rows (route run_id when available, weight-nearest as fallback for restored plans with stale run links), skipping only the matched stops, and subtracting the pinned row's item quantities from the distribution for the remaining pieces. planner-audit checks 3+4 must PASS after finalising a partially-pinned split.

## Context

Post-release follow-up — deliberately NOT built on release eve because item-complement mistakes are exactly the double/missing-items failure class the release hardening is meant to prevent. The hard block plus unlock is the operational workaround meanwhile.