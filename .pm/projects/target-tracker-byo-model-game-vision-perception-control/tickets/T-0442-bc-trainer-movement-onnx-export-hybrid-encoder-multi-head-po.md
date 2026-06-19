---
id: T-0442
title: BC trainer + movement.onnx export (hybrid encoder → multi-head policy)
type: feature
state: triaged
created: 2026-06-19T04:48:26Z
updated: 2026-06-19T04:48:26Z
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
  - Trains a hybrid encoder→multi-head policy and reports per-head metrics on a held-out session.
  - Exports movement.onnx; onnxruntime latency measured and shown to fit live.py's per-frame budget with the detector running.
  - Run is recorded under provenance.py with dataset version + hyperparameters.
  - "Honest README/manifest note: BC v1 is a measured baseline, not finished play quality."
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
Need the v1 behavioral-cloning model that maps the hybrid observation → 9-dim GoldSrc action, exported to ONNX so it runs in live.py's loop alongside the detector.

## Context
Per ADR-009: hybrid encoder = 3-conv CNN on the 84×84 thumbnail + MLP on padded box features → shared trunk → multi-head [softmax move_fwd, softmax move_side, sigmoid attack/use/jump/duck, optional look_dx/dy regression]. Loss = CE + CE + Σ BCE (+ MSE on look when policy-owned), class-weighted.

## Approach
- Train on the T-0442 dataset; report per-head metrics on the held-out session (move accuracy, use/jump/duck F1, look MAE).
- Export `movement.onnx`; verify onnxruntime inference latency fits live.py's per-frame budget on the RTX 5070 Ti alongside the detector.
- Be honest that BC v1 ≠ good play; this is the measured baseline the eval (T-0445) grades, not a finished product.