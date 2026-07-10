---
id: ADR-005
slug: charge-compensation-vat-follows-the-type-of-charge-never-the
title: Charge/compensation VAT follows the TYPE of charge, never the product involved
state: accepted
decided: 2026-07-10
decided_by:
  - Austin
project: accounts-integrity
supersedes: null
superseded_by: null
tickets:
  - T-0538
version: 1
---

## Context
UK VAT treatment: a charge for a damaged/missing item is compensation for loss (0%), while a dirty charge is a cleaning SERVICE (20%) — even when the item involved is zero-rated. The system encodes this via the issue-type mapping (issue types 10/12 → stock 787/789 at 0%; otherwise 786/788 at 20%), and staff select the 0% stocks manually where appropriate (751 zero-rated credits since go-live, zero mispairs). An earlier fix attempt inferred VAT from the target product's rate — wrong, and withdrawn on Austin's correction.

## Decision
The vatable/zero-rated choice belongs to the CALLER (issue-type mapping / staff selection), reflecting the charge's legal nature. The only writer-side inference permitted is contract-level no-VAT (vatRate=2 forces the 0% stock variant). Manual credits strip VAT at the contract's rate, not a hardcoded /1.2.

## Consequences
resolveVatVariant is deliberately narrow; the E2E asserts a zero-rated target does NOT change the caller's stock choice. Any future automation of the 0% selection must key on issue/charge type, not product VAT profiles.