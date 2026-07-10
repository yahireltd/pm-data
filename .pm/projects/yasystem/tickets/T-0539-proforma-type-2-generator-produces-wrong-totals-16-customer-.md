---
id: T-0539
title: Proforma (type 2) generator produces wrong totals — 16+ customer-facing documents with header ≠ line items
type: bug
state: triaged
created: 2026-07-10T16:48:41Z
updated: 2026-07-10T16:48:41Z
project: yasystem
section: null
parent: null
children: []
order: 1024
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
  - proforma
attention: null
version: 1
---

## Problem

The header≠lines census (see T-0538) found 34 broken documents since 2025 — and **16 of the top 20 are type-2 proformas**, which are CUSTOMER-FACING payment requests. Worst examples: proforma 68216 header **£0.01 vs £4,890.24 of lines**; 67004 £120 vs £2,498; 70202 £10.80 vs £875.71; plus the SC00127 sales-contract pair (75792/71825: header £310 vs lines £100/£210 — partial item sets).

Proformas do NOT post to Xero, so this is deliberately parked out of T-0538 (Austin, 2026-07-10) — but the type-2 generator is a **separate, untraced code path** from the amendment invoice/credit generator T-0538 fixes, and its output goes straight to customers.

## Scope

1. Trace the type-2 proforma generator (whatever creates `invoices` rows with type=2 and their items).
2. Reproduce at least the £0.01-vs-£4,890 case via acceptedchanges history (same method as T-0538's worked examples).
3. Apply the same save-time invariant as T-0538 (header total == Σ qty×unitPrice) to type-2 documents.
4. Repair/regenerate the existing broken proformas.

## Census (type-2 only, re-runnable)

```sql
SELECT i.id, i.contractID, i.created, i.total, i.VAT,
       ROUND(SUM(ii.quantity*ii.unitPrice),2) lineSum, ROUND(SUM(ii.vatAmount),2) vatSum
FROM invoices i JOIN invoice_items ii ON ii.invoiceID=i.id
WHERE i.voided=0 AND i.type=2 GROUP BY i.id
HAVING ABS(ABS(i.total)-ABS(lineSum)) > 0.02 OR ABS(ABS(i.VAT)-ABS(vatSum)) > 0.02;
```