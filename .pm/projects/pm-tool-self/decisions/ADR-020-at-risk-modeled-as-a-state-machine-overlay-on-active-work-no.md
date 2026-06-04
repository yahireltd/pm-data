---
id: ADR-020
slug: at-risk-modeled-as-a-state-machine-overlay-on-active-work-no
title: at_risk modeled as a state-machine overlay on active work, not a separate flag
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0150
---

## Context
We needed to surface tickets that are in trouble (blocked, slipping, stuck) so they don't silently rot in `in_progress`. Options: (a) a boolean/label `at_risk` flag orthogonal to state, which keeps the linear lifecycle clean but lets a ticket be simultaneously 'in_progress' and 'at risk' with no constraint on what it can do next; or (b) a real state in the ticket lifecycle.

## Decision
Add `at_risk` as a genuine ticket state in the lifecycle (state-machine.ts), positioned as an overlay on active work: reachable from ready/in_progress/review, and able to recover (back to in_progress/ready), still proceed (review/done), or be dropped (wontfix/duplicate). A dedicated lint (ATRISK001) flags stale at_risk tickets. It is a first-class node with explicit legal transitions, not a free-floating boolean.

## Consequences
The ticket state enum and every state-aware surface (board columns, filters, badges) grow by one, and the transition table has a node with unusually broad outflow because 'at risk' work can resolve many ways. The payoff: at-risk work cannot be in an undefined state, every entry and exit is an auditable transition, and the linter can reason about staleness. We accepted a less-linear lifecycle for explicit, enforceable risk handling.