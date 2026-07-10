---
id: T-0540
title: "Cross-project audit: do yahirenew (website) and chl (Chair Hire London) share the invoice-generation / priceFixed bug classes found in T-0538?"
type: spike
state: triaged
created: 2026-07-10T16:48:50Z
updated: 2026-07-10T21:17:34Z
project: accounts-integrity
section: null
parent: null
children: []
order: 4096
priority: p2
reporter:
  kind: agent
  name: claude-code
  channel: deep dive with Austin 2026-07-10
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - invoicing
  - data-integrity
  - cross-project
attention: null
version: 2
---

## Problem

T-0538 found systemic document-generation defects in this codebase (yasystem): header totals and line items computed by independent engines with a residual-dumping "adjuster"; item saves overwriting `priceFixed` verbatim from the client payload; no header==Σlines invariant anywhere.

Per Austin (2026-07-10): **the yahirenew project (customer-facing website) has its own invoice generation**, and **chl (Chair Hire London, sister website)** is a related codebase. If either shares copied/parallel generation or item-save logic, the same bug classes are live there too — producing documents this project's fixes will never see.

## Scope

1. Locate invoice/document generation in yahirenew — compare against yasystem's `generateInvoiceWithItems` / `compareContractVersions` / `adjustInvoiceLineTotals` lineage (shared ancestry? copied code?).
2. Same for chl.
3. Check both for the `priceFixed` verbatim-overwrite pattern (`saveItemsAndAccessories` equivalents) and for any header-vs-lines invariant.
4. Run the T-0538 census query against whichever databases those projects write documents into.
5. Outcome: either "clean — different mechanism" per project, or fix tickets mirroring T-0538 in the right repos.

## Relates

T-0538 (root findings + census query), T-0539 (proforma generator — the website is a likely proforma producer too, worth checking while in there).