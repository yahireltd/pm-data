---
id: ADR-006
slug: 90-day-charter-with-pre-registered-gates-g1-g4-governs-all-r
title: 90-day charter with pre-registered gates G1–G4 governs all research and deployment
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
Eight distinct contamination patterns recurred across eras because acceptance criteria were decided after seeing results. The 2026-06-09 session pre-registered docs/CHARTER_90D.md BEFORE executing the foundation runs (commit fec471b — timestamp is the proof).

## Decision
G1: ML kill rule (S2 realistic < +0.05 freezes specialists/meta — evaluated +0.12, not triggered). G2: meta adoption needs ≥+0.10 over best sleeve AND Dirichlet >1σ AND shuffle null. G3: capital gate — ≥60 paper days, slippage ≤1.25× modeled, DD ≤1.5× backtest, then ≤£2k. G4: sleeve admission — walk-forward or zero-tuned-params, lagged gates, PIT universe, real costs, beats natural null. Plus: moratorium on new families before 2026-07-15 (3 pre-listed exceptions), numbers-need-provenance policy, permanent kill list (intraday RL, daily stat arb, short book, binary regime filters, fundamentals-as-ML-features, joint-panel metas, options in all forms, unlagged gates, curated universes).

## Consequences
MR's drop (amendment 1), S9's rejection, and both PEAD kills were mechanical rule applications, not judgment calls made after seeing numbers. Amendments require written reasons in the charter file.