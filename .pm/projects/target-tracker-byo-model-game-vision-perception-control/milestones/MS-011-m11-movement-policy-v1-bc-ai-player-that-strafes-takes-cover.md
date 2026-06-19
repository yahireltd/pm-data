---
id: MS-011
slug: m11-movement-policy-v1-bc-ai-player-that-strafes-takes-cover
title: "M11 — Movement policy v1: BC AI player that strafes, takes cover, opens doors & navigates (single-player/offline)"
project: target-tracker-byo-model-game-vision-perception-control
state: planned
order: 11264
created: 2026-06-19T04:46:50Z
updated: 2026-06-19T04:46:50Z
acceptance_criteria:
  - "Frame↔action logger records synchronized (frame, 9-dim action) pairs from human Half-Life: Uplink play, stored as a versioned dataset under provenance.py + MS-005 QA gates (wall-clock alignment between capture frames.jsonl and the input log)."
  - control.py UinputController extended with stateful held-key support for KEY_W/S/A/D/E/SPACE/LEFTCTRL — verified that +forward/+moveleft/+use/+jump/+duck actually fire in GoldSrc via uinput (we already proved uinput keyboard works for console cheats).
  - "BC trainer produces a movement.onnx (hybrid encoder: 3-conv CNN on 84×84 thumbnail + MLP on padded detector-box features → multi-head) exportable like the detectors and runnable inside live.py's per-frame budget."
  - "live.py refactored to ONE perception step feeding an arbiter (ADR-009): analytic detector+aim owns look/attack when an enemy is present; the learned policy owns WASD/use/jump/duck; merged through the extended controller."
  - "movement_eval harness reports a reproducible scorecard (extends MS-007): level progress/checkpoints, SURVIVAL WITH GOD MODE OFF (deaths, time-alive), zero/near-zero stuck events, door-open success, and human-likeness vs held-out play."
  - Movement runner + logger + trainer all enforce the [scope] single_player_offline attestation per-entrypoint; god-mode data-bias (no-cover behavior) and the 'more complete autonomous player' misuse risk are logged in RISK_REGISTER + RESPONSIBLE_USE.
slip_records: []
phase: build
version: 1
---

# M11 — Movement policy v1: BC AI player that strafes, takes cover, opens doors & navigates (single-player/offline)

## Acceptance criteria

_What must be true for this milestone to count as hit._

## Notes
