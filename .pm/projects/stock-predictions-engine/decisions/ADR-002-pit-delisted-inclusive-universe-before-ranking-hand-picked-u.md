---
id: ADR-002
slug: pit-delisted-inclusive-universe-before-ranking-hand-picked-u
title: PIT delisted-inclusive universe before ranking; hand-picked universes forbidden
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
The MR strategy's 0.77 Sharpe rested on a hand-picked 20-ticker universe selected in-sample on the same 2023–2025 window (commit 40ceae5: rolling-OOS universe scored 0.48; identifying "good MR stocks" from past MR results is circular). The 2026-06-10 re-test on the PIT top-ADV universe scored +0.118 — the universe was worth ~+1.15 Sharpe of selection bias.

## Decision
All cross-sectional work filters to a point-in-time, delisted-inclusive universe (4,497 delisted names; rebuilt 2026-06-10 at 5,771 tickers) BEFORE ranking or labeling. Curated/hand-picked universes are forbidden for results.

## Consequences
MR lost its anchor status and then (amendment 1) its book seat. Post-2016 IPOs were absent from the panel (audit issue #19) — closed by the 2026-06-10 IPO backfill.