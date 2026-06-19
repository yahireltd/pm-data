---
id: ADR-008
slug: beachhead-market-game-ai-research-hobbyist-tooling-first
title: "Beachhead market: game-AI research / hobbyist tooling first"
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
The product could target accessibility aim-assist, QA/test-automation, or game-AI research/hobbyist tooling. Owner chose (2026-06-19) game-AI research / hobbyist tooling as the first market.

## Decision
First beachhead = game-AI research / hobbyist tooling: technical users who point the pipeline at their own single-player games to build and evaluate detection models and game-playing agents. This reshapes MS-010 from a hosted multi-tenant SaaS toward RELEASED / SELF-HOSTABLE tooling — a documented, reproducible toolkit (capture -> qualify -> train -> deploy) with an optional lightweight hosted convenience — lightening the multi-tenant-security, billing, and funded-labeling burdens.

## Consequences
MS-010 acceptance shifts from "self-serve SaaS + per-account isolation + pricing" toward "released toolkit: clear docs, reproducible runs, a self-hosted path, and guardrail attestation baked in." The reliability bar (ADR-005) is honest-measurement-REPORTED, not a commercial ship guarantee. Accessibility + QA/test-automation remain documented fast-followers. Lower revenue, lower risk, fastest path to real users and feedback. Users typically label their own data (no funded labeling workforce needed at this stage).