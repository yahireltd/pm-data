---
id: ADR-003
slug: raw-return-p-l-only-residuals-are-train-only-targets-v3b-def
title: Raw-return P&L only; residuals are train-only targets; v3b default
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Opus 4.8 (1M)
  - Claude Opus 4.6
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The specialist rebuild's original backtests scored P&L on forward residual returns — the series the models were trained to rank (circular). Raw P&L turned +1..+2.5 into −1.05..−0.26. The meta (2.51) additionally loaded pre-v3b contaminated OOF and the system silently defaulted to v1 residuals everywhere.

## Decision
P&L is computed on raw returns only; residualization v3b (expanding-window, shift(1), 2-day embargo) is the only permitted version and is the code default (commit 8388cb5); column provenance asserted in code; gating_ensemble.py deprecated with runtime warning.

## Consequences
"Meta Sharpe 2.5" and "S2 2.59" are permanently retired. Honest ML pool: S2 h20 +0.28 flat / +0.12 realistic; everything else ≤+0.32 or negative.