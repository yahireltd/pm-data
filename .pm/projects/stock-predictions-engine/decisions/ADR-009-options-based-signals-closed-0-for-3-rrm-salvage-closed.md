---
id: ADR-009
slug: options-based-signals-closed-0-for-3-rrm-salvage-closed
title: Options-based signals closed 0-for-3; rrm salvage closed
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
Three independent, audited attempts: (1) S6 standalone sleeve — v1 30-ticker −1.71, v2 50-ticker negative at every horizon despite positive IC (Apr 2026, Opus 4.7); (2) options as S2 features — Sharpe lift −0.46 to −1.03 (Apr 2026); (3) options as lagged market-state meta gating — +0.239/+0.236 vs baseline +0.370, BELOW their own shift nulls (−1.21σ/−2.37σ); even a deliberate lookahead gate missed the bar (Jun 2026, Fable 5, audit-reproduced to the digit). Data caveats: panel is 50 large names from 2022-04; P/C OI field is a duplicate of P/C volume (builder bug, build_options_features.py:372).

Separately, rrm_h1 (IC ~8σ, net −0.77 on turnover) failed its third salvage: as S2-h20 overlay, best variant +0.054 ΔSharpe vs +0.10 required, turnover cap breached; lagged rrm weekly IC among S2 candidates +0.011 (t≈1.1).

## Decision
Options-derived signals are closed in all three forms (kill list). rrm overlays/salvages are closed. Caveat recorded: the options-gating window (2023-04→2026-03) contains no true crisis; this is a portfolio-process closure, not a metaphysical one.

## Consequences
No further effort on the existing options panel or rrm beyond their roles as falsified baselines. The $199/mo Polygon options upgrade is permanently off the table.