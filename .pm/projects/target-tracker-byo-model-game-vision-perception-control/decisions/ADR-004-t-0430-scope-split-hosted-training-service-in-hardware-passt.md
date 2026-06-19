---
id: ADR-004
slug: t-0430-scope-split-hosted-training-service-in-hardware-passt
title: "T-0430 scope: training service IN; hardware passthrough DEFERRED + purpose-scoped (single-player/offline)"
state: proposed
decided: 2026-06-19
decided_by:
  - austin
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets:
  - T-0430
version: 2
---

## Context
T-0430 requests a model-training service AND a monitor->machine video-interception + hardware-input device. The owner's earlier interest in passthrough was CONSOLE automation (xim2-style: HDMI capture + emulate the controller) for platforms where software injection is impossible. Owner elected (2026-06-19) to keep the hardware option OPEN rather than permanently exclude it.

## Decision
The hosted/released training service (an offline ONNX the user owns) is IN scope (MS-010). The hardware-passthrough capability is NOT built now and is not a current milestone, but is NOT permanently excluded either — it is DEFERRED. The guardrail constrains PURPOSE, not modality: if ever pursued, hardware passthrough is permitted ONLY for single-player/offline targets (e.g., console QA / accessibility where software injection is impossible) and is PROHIBITED for gaining advantage in online multiplayer or evading anti-cheat. The dividing line is single-player-offline vs online-anti-cheat-evasion — not hardware vs software.

## Consequences
Keeps the owner's console-automation option alive without weakening the core guardrail. If pursued, it becomes a separate scoped milestone with its own single-player/offline acceptance gate and threat model. A passthrough device is harder to scope-enforce than software, so any future build needs explicit single-player/offline guardrails. State remains proposed (revisit). Cross-links T-0430.