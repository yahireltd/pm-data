---
id: ADR-003
slug: base-per-game-specialist-architecture-and-the-accuracy-curat
title: Base + per-game-specialist architecture and the 'accuracy = curation quality, not source' thesis
state: accepted
decided: 2026-06-19
decided_by: []
project: target-tracker-byo-model-game-vision-perception-control
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The product direction and roadmap sequencing rest on a two-tier model architecture that is currently undocumented, and whose value claim is unmeasured.

## Decision
One diverse multi-game BASE model (generic 'character/enemy') + per-user SPECIALIST fine-tunes. The qualify.py funnel (dedup -> CLIP domain gate -> ensemble-agreement labels) is the curation-quality enforcement. Thesis: model accuracy is determined by curation quality, not data source — so the funnel is source-agnostic, while the *preferred* source is gameplay-capture / licensed / synthetic (domain match + licensing).

## Consequences
The base value claim (base reaches the reliability bar with materially less customer data) is CONDITIONAL on the measured transfer curve on a held-out GAME (MS-009). If the base doesn't materially cut data needed, the two-tier economics need revisiting. Depends on diverse multi-game data (MS-008) and the leaky-metric discipline (MS-005/007).