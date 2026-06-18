---
id: T-0414
title: Inventory report (stock by date range) → fixed-asset / stock-reconciliation view
type: feature
state: triaged
created: 2026-06-18T07:34:34Z
updated: 2026-06-18T07:34:34Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Jhuztine Casota
  channel: email
  contact: jhuztine@yahire.com
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
  - stock
  - reporting
  - finance
attention: null
version: 1
---

## Problem

Accounts/Finance (Jhuztine, Effie) need a period stock-reconciliation view — to reconcile stock against the Fixed Asset Register / Xero and surface additions, disposals and losses over a chosen date range. The existing page `/stock/products-in-stock-for-date-range` was built in 2024 as a starting point and should be built out.

## Context

Originated from the Fixed Asset Register thread (May 2024). Ben sponsored it; Jhuztine and Effie reviewed the existing page and proposed the columns below. Ben's steer: "discuss what parts are actionable, not urgent."

## Requested columns (per product, over a date range)

- **Product name** — clickable → product view (`/stock/view-product`)
- **Start count**
- **On hire** — incorporate hire qty
- **Additions** — qty added; clickable → stock transaction additions (`/stock/view-all-transactions`)
- **Purchase price** — should match the invoice
- **Selling price**
- **Repaired** — out for repair; returns to start count once back in use
- **Disposed** — cannot be used
- **Lost / missing** (qty)
- **Replacement cost**
- **End count** = Start count + Additions − Repaired − Disposed − Lost/Missing

## Dependencies / open questions

1. **Transaction sub-types** — transactions don't currently record *why* stock moved (purchase / sale / repair / disposal / adjustment). Splitting Additions / Repaired / Disposed / Lost needs the "closed-loop" categorisation Ben flagged — likely a prerequisite ticket.
2. **Finance fields source** — confirm whether Purchase price / Selling price / Replacement cost already exist on the product record or need adding.
3. **Data accuracy** — figures are only as good as the warehouse maintaining transactions accurately (process dependency, not just code).
4. **"On hire" definition** — current on-hire at the end date, or peak during the period?

## Purpose

Reconcile stock to the Fixed Asset Register / Xero; quantify losses, disposals and additions per period.

## Note

Thread is from May 2024 — confirm it's still wanted and the current page state before sizing.