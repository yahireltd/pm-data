---
id: ADR-007
slug: mr-dropped-from-the-paper-book-charter-amendment-1
title: MR dropped from the paper book (charter amendment 1)
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
Rule pre-registered 2026-06-10 before the post-refresh re-test: MR keeps its seat only if standalone ≥+0.30 @10bps on the PIT universe AND it improves the book. Re-test on refreshed data + rebuilt 5,771-name PIT universe: MR PIT top-30 @10bps = +0.118 over 810 days (5bps +0.41, 15bps −0.17 — breakeven ~12bps; corr to PEAD +0.48, not the −0.05 the old audit carried).

## Decision
MR is out of the book (condition 1 failed; rule is AND). Parked — G4 re-entry only with a changed spec.

## Consequences
The project's original "only validated edge" (0.77) is fully retired: universe selection bias accounted for ~+1.15 of it and the modern window the rest. Book config sleeves.mr=false.