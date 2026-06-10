---
id: ADR-005
slug: portfolio-of-portfolios-is-the-meta-architecture-joint-panel
title: Portfolio-of-portfolios is the meta architecture; joint-panel metas are dead
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Opus 4.7 (1M)
  - Claude Fable 5
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
Meta v2/v3 (joint-panel: merge specialist predictions into one cross-sectional rank) failed because specialists have different native universes — S5 scored +0.13 on its 257-name universe but −6.59 when forced into the 1,347-name joint decile panel (NaN dilution). Discovered on the server line 2026-04-26; the laptop's rerun_meta_clean.py independently converged on the same fix.

## Decision
Specialists run as capital sleeves on their native universes; the meta allocates across sleeve RETURN series (trailing-only weights). Prediction-merging across mismatched universes is forbidden.

## Consequences
The only G2-passing allocation in the project is this structure: trailing-sharpe over {S2,S5,S3} = +0.370 realistic, shuffle null +2.21σ, Dirichlet +1.72σ (scripts/v4_nulls.py). Any future meta must beat best-standalone by ≥0.10 AND both nulls (charter G2).