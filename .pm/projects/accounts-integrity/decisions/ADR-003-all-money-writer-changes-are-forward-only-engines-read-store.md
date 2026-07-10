---
id: ADR-003
slug: all-money-writer-changes-are-forward-only-engines-read-store
title: All money-writer changes are forward-only; engines read stored values (no retroactive drift)
state: accepted
decided: 2026-07-10
decided_by:
  - Austin
  - claude-code
project: accounts-integrity
supersedes: null
superseded_by: null
tickets:
  - T-0538
version: 1
---

## Context
Austin's constraint when the VAT-inheritance fix was proposed: "if we do change that we will expect differences for things we have already posted to xero vs things we re-calculate." Documents already in Xero must not recompute differently under new rules.

## Decision
Rates, prices and stock choices are stamped onto item rows at CREATION by the writers; every engine (header, line diff, replay, doc-integrity) reads the STORED values and never re-derives them for existing rows. Fixes change only what writers stamp on new rows. The single documented exception: the contract-level no-VAT line rule changes how the 28 historic no-VAT amendments would replay (none active; noted in the deep dive).

## Consequences
Replay of historical documents is byte-faithful to what posted (validated: 3,992 docs, 3,984 stable, every exception explained). Historic data defects (e.g. the +£180 compensation) remain data fixes, not code behaviour. Any future engine change must preserve this rule or explicitly document its retroactive surface.