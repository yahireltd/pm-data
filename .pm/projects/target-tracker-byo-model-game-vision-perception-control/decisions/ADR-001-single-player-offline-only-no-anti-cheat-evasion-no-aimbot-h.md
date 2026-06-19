---
id: ADR-001
slug: single-player-offline-only-no-anti-cheat-evasion-no-aimbot-h
title: Single-player/offline only — no anti-cheat evasion, no aimbot hardware passthrough
state: accepted
decided: 2026-06-19
decided_by:
  - austin
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The hard guardrail has lived only as README/scope prose, while control.py docstrings actively marketed uinput in anti-cheat-evasion language — a direct contradiction. The project is becoming a product (T-0430) whose "aim assist site" framing invites a prohibited online-cheat reading.

## Decision
Single-player / offline ONLY. Never online multiplayer. NO anti-cheat-evasion / stealth / detection-avoidance features. NO video-interception or hardware-passthrough aimbot HID. Legitimate framings only: game-AI research, QA/test automation, accessibility aim-assist, CV/dataset tooling. Enforced structurally by GUARDRAIL.md, a required `[scope]` profile attestation that live.py/capture.py check, and a CI denylist guard.

## Consequences
control.py + docs/LIVE_LOOP.md evasion framing is excised; profiles must attest scope. Honest limits: the `[scope]` attestation is an INTENT gate, not network enforcement; a downloaded model + the open-source uinput path is dual-use post-download — this residual is acknowledged, not oversold. Anchors GUARDRAIL.md, the attestation, and the CI guard (MS-004).