---
id: T-0540
title: Bring website invoice/credit generation (yahirenew + chl) up to date — Release 2 dependency
type: spike
state: triaged
created: 2026-07-10T16:48:50Z
updated: 2026-07-10T22:38:41Z
project: accounts-integrity
section: null
parent: null
children: []
order: 4096
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: deep dive with Austin 2026-07-10
assignee: null
acceptance_criteria:
  - "Both website repos audited: generation/adjuster code located and diffed against the fixed engine"
  - Strategy decided and recorded (port vs shared code path)
  - "Fixes applied to both sites and validated: website-generated documents pass the same header==lines checks (visible on /doc-integrity/validations)"
  - MS-003 not declared hit until website-generated documents are proven current-engine
out_of_scope: []
code_anchors: []
relates: []
blocks:
  - T-0538
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - websites
  - yahirenew
  - chl
  - release-dependency
  - t-0538-follow-on
attention: null
version: 3
milestone: MS-003
---

## Problem

The customer-facing websites (yahirenew and chl/chairhirelondon) **generate invoices and credit notes themselves when a customer makes a payment** — through their own copies of the generation logic, in separate repos. The T-0538 fixes (labeled Xero-valid rounding lines, composite item keys, VAT handling, writer guards) live only in the management system.

**Per Austin (2026-07-10): the T-0538 rollout depends on bringing the websites up to date.** If Release 2 ships while the websites still run the old logic, website-generated documents keep flowing in with the old defect classes (hidden rounding smears, VAT slips) — the mismatch list starts growing again from a different door, and the "documents born right" claim only covers management-side generation.

## Work

1. **Audit** both website repos: locate their invoice/credit generation and adjuster code; diff against the fixed engine (compareContractVersions / adjustInvoiceLineTotals / getFullContractAsLineItems equivalents).
2. **Decide the strategy**: port the fixes into each site's copy, OR (better long-term) make the sites call the shared fixed code path — record as an ADR either way.
3. **Apply + test**: replay-style validation on website-generated documents (the doc-integrity page already covers them once they're in the DB — website docs land in the same invoices/invoice_items tables and are visible to the same checks).
4. **Gate**: MS-003 ("Document generation integrity on live") carries a criterion for this — it cannot be declared hit while websites generate old-engine documents.

## Sequencing options for the meeting
(a) Block Release 2 until websites are ready, or (b) release the management system first and fast-follow the websites before declaring the milestone hit — the validations page will show any website-generated FAILs in the interim, so (b) is monitorable. Austin to choose.