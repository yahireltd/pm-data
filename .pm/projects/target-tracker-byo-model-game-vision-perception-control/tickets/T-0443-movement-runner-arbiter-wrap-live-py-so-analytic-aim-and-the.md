---
id: T-0443
title: Movement runner + arbiter — wrap live.py so analytic aim and the learned policy co-drive
type: feature
state: triaged
created: 2026-06-19T04:48:34Z
updated: 2026-06-19T04:48:34Z
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
  - live.py refactored to one perception step feeding aim + movement policy + arbiter; no duplicate detection per frame.
  - "Arbiter precedence verified: aim owns look/attack with an enemy present; policy owns feet; policy owns look-intent when no enemy."
  - Deterministic anti-stuck reflex fires on no-progress and recovers.
  - "[scope] single_player_offline attestation enforced; runs headless on Xash without stuck keys."
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
The trained movement.onnx (T-0443) must drive the game alongside the existing analytic aim, not replace it.

## Context
ADR-009: WRAP live.py. Refactor its per-frame body into ONE perception step (detector.detect → Detection list + 84×84 thumbnail) feeding two consumers + an arbiter. Requires the extended controller (T-0440).

## Approach
- Single perception step shared by aim + policy (no double detection).
- Arbiter: enemy present → analytic aim owns look_dx/dy + attack (suppress policy look); policy owns WASD/use/jump/duck. No enemy → policy owns look-intent for navigation.
- Add a deterministic anti-stuck reflex (per ADR-009 consequence 4): detect no-progress (position/oscillation proxy) → scripted unstick (back+turn) rather than relying on the policy.
- Enforce [scope] single_player_offline at this entrypoint.