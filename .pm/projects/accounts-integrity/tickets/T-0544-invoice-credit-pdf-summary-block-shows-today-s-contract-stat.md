---
id: T-0544
title: Invoice/credit PDF Summary block shows today's contract state, not the document's
type: bug
state: triaged
created: 2026-07-10T21:47:24Z
updated: 2026-07-10T21:47:24Z
project: accounts-integrity
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee: null
acceptance_criteria:
  - Rendering a historical document shows the contract totals AS OF that document's generation (validated against 2+ docs on one contract)
  - Previously Invoiced/Credited exclude documents created after the rendered one
  - Type-3/4 docs without lines render without a 500 (or are proven not to exist)
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - pdf
  - accounts
  - t-0538-follow-on
attention: null
version: 1
---

## Problem

The itemised PDF's Summary block (Contract Total / Previously Invoiced / Previously Credited) is computed **at render time** from the contract's current state, minus the document's own amount — not from the contract as it stood when the document was generated. Rendering two historical documents from the same contract therefore shows different "Contract Total" values, and "Previously Invoiced" includes documents created AFTER the one being rendered. Austin hit this during T-0538 triage: the summaries across two PDFs "didn't appear to match" although both were internally consistent.

Line items are per-generation truth (from invoice_items); only the summary drifts.

## Fix direction
`InvoicePdfService::buildItemised` — source the summary from the archived contract version tied to the document (`getInvoiceContract()` already resolves it and carries versioned totals) and sum only documents created ≤ this document's timestamp.

## Related small hazard (same file, found in review)
A type-3/4 deposit invoice/credit WITHOUT invoice_items lines falls into `buildLegacy`, which only defines its labels for types 0/1/2 → undefined-variable 500. Pre-release check: `SELECT i.id FROM invoices i LEFT JOIN invoice_items ii ON ii.invoiceID=i.id WHERE i.type IN (3,4) GROUP BY i.id HAVING COUNT(ii.id)=0` — guard buildLegacy if any exist.