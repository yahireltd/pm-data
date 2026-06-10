---
id: ADR-008
slug: s9-sector-momentum-rejected-as-regime-timed-beta-g4-admissio
title: S9 sector momentum rejected as regime-timed beta (G4 admission test)
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Fable 5
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
Pre-registered G4 test (2017–2026Q1, lagged gate, gate-churn costs, same dates/costs for all series): gated S9 best variant +0.537 vs gated SPY +0.600 — fails its own null by −0.06 when it needed ≥+0.30. Ungated S9 +0.738 vs ungated SPY +0.704: monthly-rebalanced sector beta. Bonus finding: the gate's HY leg was dead data (non-null 677/2,513 days, NaN→full exposure) — effectively VIX-only all along.

## Decision
S9 does not enter the book in any form. The frozen "S9 + smooth gate" paper strategy (PAPER_TRADE_SETUP.md) is void.

## Consequences
Closes the last live claim of the April server line. scripts/s9_g4_admission.py + memory/s9_g4_admission_results.md are the reusable G4 test pattern (strategy vs its natural null on identical dates/costs).