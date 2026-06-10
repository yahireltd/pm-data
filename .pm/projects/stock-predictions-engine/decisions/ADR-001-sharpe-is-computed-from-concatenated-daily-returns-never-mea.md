---
id: ADR-001
slug: sharpe-is-computed-from-concatenated-daily-returns-never-mea
title: Sharpe is computed from concatenated daily returns, never mean-of-per-window
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Opus 4.6
  - Austin
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The era-4 headline "OOS Sharpe 14.38" (commit 791b7f3) came from averaging per-window Sharpes across walk-forward windows (3–15× inflation) compounded by a trail-stop-at-intraday-high lookahead. docs/audit_report.md (2026-04-02, commit 78205a1) recomputed honestly: the same ensemble scored −0.879.

## Decision
All Sharpe figures are computed on the concatenated daily net return series, including flat days, annualized √252 (√(252/h) non-overlapping for h>1). Per-window means are forbidden. trail_stop=0 backtests are forbidden (lookahead).

## Consequences
Every historical headline was re-derived; most died. This definition is guardrail #4 of the 2026-05-29 audit and is enforced in strategy_zoo.py and all subsequent harnesses.