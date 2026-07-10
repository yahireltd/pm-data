---
id: T-0542
title: Stop internal test contracts posting to live Xero
type: chore
state: inbox
created: 2026-07-10T19:13:23Z
updated: 2026-07-10T19:13:23Z
project: null
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee: null
acceptance_criteria:
  - Decision made (flag vs internal-customer exclusion vs process-only) with Zsolt and Austin
  - Posting flow provably excludes test contracts (test on sandbox)
  - Existing live test documents dispositioned (voided/excluded) so backlogs and integrity reports are clean
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - xero
  - accounts
  - t-0538-follow-on
attention: null
version: 1
---

## Problem

Zsolt's quote-builder test contracts are real contracts in the live system (real customer records, e.g. 'Zsolt Turu' / zsolt@yahire.com), so posting runs sweep their invoices/credit notes into the real Xero org. Found live: INV 69663 for test contract C089510 sitting in Xero as an Awaiting-Payment £604.36 receivable (GUID dcc8f76d-9ef5-4883-87d9-61c56cd2c697) — Austin voiding. Austin has noticed several such docs reaching Xero.

Test contracts also pollute the accounts pipeline: they appear in unposted backlogs, repair-docs candidates, and the doc-integrity mismatch list (2 of the 4 current live broken docs are from C089510, a cancelled test contract).

## Options to decide

1. An `isTest` / internal flag on contracts (set at creation for staff-created test data) that the posting flow and accounts reports exclude.
2. Exclusion by internal customer (e.g. customer email domain yahire.com, or a flagged internal customer list) — catches existing data without re-flagging.
3. Process-only: test in the sandbox/test box instead of live (already possible since the T-0534 test-server setup — may just need agreement with Zsolt).

Whichever is picked, also decide the clean-up for existing test contracts/documents already in the live pipeline.

## Context
Found during T-0538 triage (2026-07-10). See T-0538 run notes for the C089510 disposition.