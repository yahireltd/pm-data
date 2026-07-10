---
id: ADR-002
slug: generation-never-blocks-residuals-ride-labeled-xero-valid-ad
title: "Generation never blocks: residuals ride labeled, Xero-valid adjustment lines"
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
The old adjuster reconciled header-vs-lines residuals by smearing them into the last line's netAmount — invisible on Xero (which renders qty × unitPrice), producing documents whose local and Xero renderings disagreed. Austin's constraint: documents must generate correctly rather than be refused ("I will have to fix those if they can't be done"), and any adjustment must be labeled meaningfully.

## Decision
Document generation is never blocked. Residuals go on their OWN coherent line (qty 1 × unitPrice == netAmount): 'Rounding adjustment' within the cap (1p per line, 5p floor) or 'Balance adjustment' + error log beyond it (engines disagreeing = upstream bug, made visible not hidden). Residuals that would violate Xero's tax-within-line-amount rule (VAT-only or sign-mismatched) split into a VAT-carrier line (net = 2×VAT) plus a VAT-free net offset — the construction XeroDocRepairService already proved. A log-only shadow invariant checks every generated document.

## Consequences
Header == Xero rendering by construction (proven: Demo Company read-back matches to the penny; the pre-fix shape is empirically rejected by Xero). Customers may see a penny 'Rounding adjustment' line — accepted trade-off for coherent documents. 'Balance adjustment' lines are an investigate-me signal, monitored via the validations page and invoice-integrity log.