---
id: T-0534
title: "Make Xero posting self-healing: recover lost GUID write-backs instead of minting duplicates/orphans"
type: bug
state: triaged
created: 2026-07-09T13:58:22Z
updated: 2026-07-09T13:58:22Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
  channel: investigation with Austin 2026-07-09
assignee: null
acceptance_criteria:
  - Re-posting an invoice that already exists in Xero (same InvoiceNumber) results in the local xeroInvoiceID being back-filled from Xero and NO error loop — verified on test box by nulling a posted invoice's xeroInvoiceID and re-running the window
  - A simulated 5xx/exception during a batch create (e.g. throw after the API call in a test double) leaves no permanently-NULL rows for objects Xero committed — the same run or the next run writes back their ids
  - "Batch posting writes ids back per chunk: killing the process mid-run strands at most one chunk (verify by inspecting write-back timestamps interleaved with chunk calls in the log)"
  - No posting path logs status='success' before the corresponding local id column is saved (code audit of XeroFunctionsNew + XeroPostingService write-back sites)
  - Duplicate-mint census query (objects with >1 distinct xero_response GUID) shows zero NEW objects after the fix is deployed, measured over 2+ weeks of normal posting
out_of_scope: []
code_anchors:
  - path: common/models/XeroFunctionsNew.php
    note: postItemisedInvoices ~L190-335 (validation-error branch ~L310-315 = self-heal site; catch ~L294-298 = reconcile-on-5xx site); postItemisedCreditNotes ~L337+; postDepositRefunds ~L1427-1533 (success logged L1516 BEFORE save L1518-20)
  - path: common/services/XeroPostingService.php
    note: "postAllForPeriod step table ~L60-135; prereq-post sites that re-hit orphans: postAllInvoicePaymentAllocations ~L503-633, postCreditNoteInvoiceAllocations ~L760+, postDepositCharges ~L1347+"
  - path: backend/controllers/XeroController.php
    note: actionRunPost ~L445 (mutex, ignore_user_abort); actionHealInvoiceNumbers ~L1008+ (the backlog heal, log-driven bulk lookup — pattern to reuse for self-heal); actionDedupeChecklist ~L646 (static HTML checklist, ticks not persisted)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - xero
  - posting
  - data-integrity
attention: null
version: 1
---

## Problem

Every Xero posting path shares one shape: *select rows WHERE xero-id column IS NULL → create in Xero → write the returned id back afterwards*. Anything that separates the create from the write-back leaves the local column NULL while Xero holds the object, and the next run re-posts it:

- **Objects without a Xero uniqueness key** (overpayments, payments, refund payments, allocations) → **silent duplicates in Xero**. Census on live `xero_posting_logs` (2026-07-09): ~401 objects posted ≥2× with different GUIDs, leaking since 2025-07. Biggest burst = T-0509 (2026-07-02 concurrency, 264 pairs, manually voided). Slow leak continues (~monthly singletons through 2026).
- **Invoices/credit notes** (unique number) → Xero rejects the retry with "Invoice # must be unique" → **permanent orphan** (local id stays NULL forever, no recovery path), and since the July hardening (7a230d48) orphans also **block payment/credit/deposit allocations**. Two events: 2026-06-19 (Xero 500 mid-createInvoices, 289 invoices) and 2026-07-02 (concurrency, 440). All 729 healed 2026-07-09 via `/xero/heal-invoice-numbers`.

Proven triggers: Xero 5xx mid-batch-create (06-19); concurrent runs pre-mutex (07-02); silent process death mid-write-back (php-fpm request_terminate_timeout — ignore_user_abort doesn't stop SIGKILL); historical deploy lag. The GET_LOCK mutex (added 07-02) covers concurrency only.

## Fix (four guards)

1. **Self-heal on "Invoice # must be unique"** — in the validation-error branch of `postItemisedInvoices` / `postItemisedCreditNotes`, look up the existing invoice/credit-note in Xero by number and save its GUID locally instead of only logging. Universal net: converts any orphan into a one-run self-repair.
2. **Reconcile-on-5xx** — when a batch `createInvoices`/`createPayments`/etc. throws, treat it as *maybe committed*: re-fetch those numbers/references from Xero and write back any ids found, before giving up.
3. **Chunked create-with-immediate-write-back** — post in ~50-object chunks and write ids back immediately after each chunk response, so a killed process loses at most one chunk (currently one batch of up to 1000 writes back only at the end).
4. **Save-before-log ordering** — never log `success` before the local write-back is saved (e.g. XeroFunctionsNew::postDepositRefunds logs success at ~L1516 then saves at ~L1518-20; a death between produces a success-logged row with NULL id → guaranteed future duplicate).

Nice-to-have: persist dedupe-checklist ticks in DB (current checklist is static HTML; "did we finish the manual voiding?" is unanswerable from data).

## Out of scope

Repair of remaining historical duplicates (tracked in T-0509 conversation: overpayment 47708 open-period 4×-post + closed-period accounts-call items).