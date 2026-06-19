---
id: T-0436
title: "MS-004: Stand up RISK_REGISTER.md"
type: chore
state: triaged
created: 2026-06-19T00:32:21Z
updated: 2026-06-19T01:33:21Z
project: target-tracker-byo-model-game-vision-perception-control
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - RISK_REGISTER.md seeded with each named unknown, each carrying likelihood/impact/mitigation/trigger/owner
  - the top risk (uinput-on-Xash) has a recorded pass/fail spike slot for MS-006
  - cross-links the godot-headless-injection-gotchas memory
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 2
---

## Scope
A living register seeded with the named unknowns: uinput-on-Xash fragility (+ world-writable /dev/uinput surface); headless bring-up brittleness (vsync->1fps, XTEST-vs-raw, HUD eats clicks, flaky mutter grab); per-game generalization failure; inflated/leaky low-diversity metrics (train.py-flagged); qualify.py/train.py not adversarially verified; data-provenance/licensing exposure; guardrail drift; single-GPU + single-disk capacity. Each: likelihood, impact, mitigation, trigger, owner. Cross-link the godot-headless-injection-gotchas memory.

Milestone: MS-004.

## Conversation

**2026-06-19 01:33 claude-code:** Done (commit f1c7143). Created RISK_REGISTER.md with 10 tracked risks, each with likelihood, impact, mitigation, a trigger signal, and an owner — led by R1 (kernel-input control on the real game is the biggest unproven capability) and R2 (low data diversity inflates accuracy metrics).
