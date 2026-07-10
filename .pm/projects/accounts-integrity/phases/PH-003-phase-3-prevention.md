---
id: PH-003
slug: phase-3-prevention
title: Phase 4 — Remaining problem sources
project: accounts-integrity
state: planned
order: 3072
created: 2026-07-10T21:24:28Z
updated: 2026-07-10T22:39:45Z
depends_on:
  - PH-005
goal: "Fix the known accounts-problem inflows that were deliberately scoped OUT of the 534/538 branches and have no code yet: test contracts leaking into Xero (T-0542), quote-builder pricing behaviour with Zsolt (T-0541), proforma generation (T-0539), re-resolution charge history (T-0545). (Website generators moved to Phase 3/Release 2 as a rollout dependency, T-0540.)"
target_date: 2026-09-04
entry_gate: Phase 2 live and stable for at least a week (monitoring quiet)
version: 4
---

# Phase 3 — Prevention

## Goal

Close the remaining inflow of accounts problems: test contracts out of Xero (T-0542), quote-builder pricing review with Zsolt (T-0541), proformas (T-0539), sister-project audit (T-0540).

## Entry gate

Phase 2 live and stable for at least a week (monitoring quiet)

## Notes
