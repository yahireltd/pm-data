---
id: T-0441
title: BC dataset builder — turn logged sessions into a balanced (obs, action) training set
type: feature
state: triaged
created: 2026-06-19T04:48:17Z
updated: 2026-06-19T04:48:17Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude
assignee: null
acceptance_criteria:
  - Emits (padded box features + 84×84 thumbnail, 9-dim action) examples under provenance.py with a recorded action histogram.
  - Idle-vs-active class balance is reported and corrected (balanced sampling or loss weights).
  - Held-out eval split is by session/time (no cross-split frame leakage), reusing MS-005 QA gate concepts.
  - No pHash-dedup / ensemble-agree gating applied (transition frames preserved).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - movement
  - MS-011
  - ADR-009
attention: null
version: 1
---

## Problem
Logged sessions (T-0441) are raw frame+input streams. The BC trainer (T-0443) needs the hybrid observation defined in ADR-009 and a class-balanced target distribution.

## Context
Observation = k-nearest detector boxes (dx,dy normalized by focal per live.py convention; w,h,score,visible) padded/masked + an 84×84 grayscale thumbnail. Target = 9-dim action vector. Idle frames dominate human play, so naive BC collapses to "do nothing."

## Approach
- For each aligned (frame, action) pair: run the detector to get boxes, build the padded box feature + 84×84 thumbnail, attach the 9-dim action.
- Class-balance: down-weight/under-sample idle frames, up-weight movement/use/jump transitions; report the action histogram.
- Version the output under `provenance.py` + MS-005 QA gates. Keep the train==val bootstrap convention but tag a held-out *session* for honest eval (don't leak frames across the split — split by session/time, not random frame).
- Preserve transition frames (do NOT reuse capture2's dedup/ensemble gates here — they delete the movement signal, ADR-009 consequence 3).