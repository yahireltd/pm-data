---
id: ADR-004
slug: all-conditioning-signals-gates-regimes-features-lag-1-day
title: All conditioning signals (gates, regimes, features) lag ≥1 day
state: accepted
decided: 2026-06-10
decided_by:
  - Claude Fable 5
  - Austin
project: stock-predictions-engine
supersedes: null
superseded_by: null
tickets: []
version: 1
---

## Context
The server line's two headline numbers — S9 gated "1.86" and meta-v4 "0.957" — both rested on macro_regime_gate applying day-D VIX/HY closes to day-D returns (every other gate in the file lagged; this one didn't). Lagged recompute: meta-v4 0.957 → 0.43 (the gate adds 0.00 Sharpe); the 28 same-day-gated days had mean return −1.11%/day. The S9 sweep's provenance doc was also missing from disk and git.

## Decision
Every conditioning series is shifted ≥1 day before application (gate.shift(1) committed in fec471b); FRED-sourced series get publication lags. Numbers produced with unlagged conditioning are contaminated by definition.

## Consequences
S9 1.86 and meta-v4 0.957 retired in code and docs (docs/server_line_audit_2026-06-09.md, issues #27–#32). The lagged smooth VIX/HY gate proved net-harmful everywhere (11.4×/yr churn ≈ 57bps/yr; gated SPY −2.1 in 2022) — smooth macro gates as exposure scalers are on the kill list.