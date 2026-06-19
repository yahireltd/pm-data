---
id: ADR-004
slug: t-0430-scope-split-hosted-training-service-in-hardware-passt
title: T-0430 scope split — hosted training service IN, hardware-passthrough aim-assist OUT
state: proposed
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets:
  - T-0430
version: 1
---

## Context
T-0430 ("make your own aim assist site") requests BOTH a model-training service AND hardware that bridges monitor->machine to intercept video and inject input. The latter is the canonical online-cheat architecture (it exists only to be invisible to anti-cheat) and contradicts the guardrail.

## Decision (PROPOSED — owner to confirm)
The buildable, in-scope half is a hosted training service: capture/upload -> qualify -> held-out train -> deliver an offline ONNX + single-player SDK the user owns (MS-010). The hardware video-interception / monitor-passthrough half is permanently OUT. A downloaded detector + offline relative-mouse control is mechanically a single-player aimbot — defensible only within the offline/accessibility/QA framing; the service ships no online inference and no passthrough.

## Consequences
A cited decision prevents the prohibited reading creeping back when "aim assist site" is built. T-0430 lives outside P-0017 (cross-project link). Owner confirmation requested that the hardware passthrough is permanently excluded.