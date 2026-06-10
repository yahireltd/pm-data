---
id: T-0345
title: S11 quality-trend-hold (Buffett × Simons) — run the pre-registered test after 2026-07-15
type: spike
state: triaged
created: 2026-06-10T02:30:15Z
updated: 2026-06-10T02:30:15Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - Test executed once with the unchanged registration; PASS/FAIL/BLOCKED reported with provenance
  - Adversarial audit upholds the verdict
  - "If PASS: G4 admission to the paper book; if N1-FAIL: reverts to T-0343 plain skew sleeve"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - research
  - pre-registered
  - charter
attention: null
version: 1
---

## Problem
The project's founding vision — Buffett-style quality screening married to Simons-style systematic machinery — was never tested in the one configuration the evidence hasn't killed: quality RANKS for slow portfolio formation (not ML features), breakout entry, multi-year no-time-stop trend exit, judged on terminal wealth.

## Spec
Frozen in `docs/preregistration_s11_quality_trend_hold.md` (commit `7e61502`, 2026-06-10 — pre-dates any test by design). Zero free parameters. Three nulls (unscreened universe ≥1.15×, junk quintile, 200 random pools at 2σ), coverage gate → BLOCKED not bent, N1 failure kills the Buffett half with no re-sweep.

## Schedule
On/after 2026-07-15 (charter moratorium), after T-0338 panel merge verified. Reuses `exit_policy_study.py` machinery verbatim. Relates: T-0343 (skew sleeve — S11's fallback if N1 fails).