---
id: T-0534
title: "Make Xero posting self-healing: recover lost GUID write-backs instead of minting duplicates/orphans"
type: bug
state: in_progress
created: 2026-07-09T13:58:22Z
updated: 2026-07-09T14:52:41Z
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
assignee:
  kind: agent
  name: claude-code
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
agent_runs:
  - id: run-20260709-1413
    model: claude-opus-4-8
    started: 2026-07-09T14:13:20Z
    status: in_progress
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-09T14:13:20Z
    progress:
      - at: 2026-07-09T14:14:02Z
        note: "All four guards implemented in the working tree on master (uncommitted — project policy blocks agent commits; Austin reviews and commits). Changes in common/models/XeroFunctionsNew.php: (1) postItemisedInvoices and postItemisedCreditNotes now post in chunks of 50 with GUID write-back immediately after each chunk (a killed process strands ≤1 chunk instead of a whole day/week batch); (2) a thrown batch create no longer aborts with `return []` — it runs recoverInvoiceGuids/recoverCreditNoteGuids on that chunk's numbers (Xero may have committed despite the throw — the 2026-06-19 500) and continues with remaining chunks; (3) \"must be unique\" validation errors now trigger the same recovery (self-heal: reads the existing GUID back by number, fills only blank local rows, never writes to Xero) instead of error-looping forever; (4) postDepositRefunds now saves xeroPaymentID BEFORE logging success, and logs an error if the local save fails. Recovery lookups are bulk (1 API call per 50 invoices / 20 credit notes) to respect the daily quota. Root-cause context: php-fpm request_terminate_timeout=600s (introduced with the PHP8 migration ~2026-04-30) SIGKILLs long posting runs silently — confirmed on the test box; awaiting Austin's confirmation of live's value. Also still uncommitted in the same tree: the earlier /xero/heal-invoice-numbers bulk rewrite (backend/controllers/XeroController.php)."
      - at: 2026-07-09T14:52:41Z
        note: "Phase 2 (money objects) implemented in the working tree on master, all lint-clean. (1) XeroLogger::soleSuccessGuids — heal-from-log source: returns a GUID per record ONLY when the log holds exactly one distinct success GUID (records with 2+ are known duplicates, never auto-healed). (2) postOverpayments + postDepositOverpayments: heal-from-log pre-check (each heal VERIFIED against Xero by fetching the overpayment and matching our echoed JSON paymentID/depositID before writing xeroOverpaymentID; linked-deposit write-back mirrored), then per-record posting with DETERMINISTIC idempotency keys (yh-op-{paymentId} / yh-dep-{depositId}) and per-record try/catch — a failed create logs and continues, and a same-key retry within Xero's ~24h window replays the original response (no duplicate, write-back completes on retry). (3) postDepositRefunds: same heal (verified via payment reference allocationID) + per-payment posting with key yh-dra-{allocationId}. Remaining for the same pattern: credit-note refund payments (7 historical dupes), deposit charges (1), payment refund overpayments (1 triple) — same recipe, smaller paths. Cron design agreed with Austin: daily console posting at T-7 with T-8 re-sweep first (retries land inside the idempotency window), plus a second intra-day retry pass; BLOCKER for cron: Xero token lives in $_SESSION (StorageClass) — needs DB-backed token store with refresh lock (Xero refresh tokens are single-use; racing them is the likely cause of the invalid_grant errors in the log). Live fpm config confirmed identical to test: request_terminate_timeout=600s, Apache Timeout 300, memory 128M."
    test_plan: |-
      On the TEST box (no live risk, but note test box has no Xero token — API-touching paths need live or a token):
      1. `php -l common/models/XeroFunctionsNew.php` after pulling — no syntax errors.
      2. Code review the diff: confirm the chunk loop writes back inside the loop, the catch calls recover* then `continue` (never `return []`), and deposit refunds save before logging.

      On LIVE after deploying (Austin):
      3. Self-heal happy path: pick one already-posted itemised invoice from June, note its xeroInvoiceID, set it to NULL in the DB, re-run posting for that day's window. EXPECT: no repeated "Invoice # must be unique" error; a `self_heal` success row in xero_posting_logs for that invoice; xeroInvoiceID re-filled with the SAME GUID as before (compare!). This proves recovery never mints a new invoice.
      4. Normal posting regression: run a quiet recent day's window end-to-end. EXPECT: identical posted/error counts to what that window produced before, plus no new duplicate GUIDs (census query from T-0509 comment shows zero NEW objects with >1 distinct xero_response).
      5. Deposit refund ordering: after any window that posts deposit refunds, verify every deposit_refund success row has a non-NULL DepositRefundAllocations.xeroPaymentID (SELECT dra.id FROM deposit_refund_allocations dra JOIN xero_posting_logs l ON l.model_type='deposit_refund' AND l.model_id=dra.id AND l.status='success' WHERE dra.xeroPaymentID IS NULL — expect 0 rows for new activity).
      6. Cross-impact: postItemisedInvoices/postItemisedCreditNotes are also called as prereq steps inside postAllInvoicePaymentAllocations, postCreditNoteInvoiceAllocations, postDepositCharges and postDepositRefunds — re-check one window where those prereq posts fire (log shows posted_itemised_invoices_before_payment_allocation) and confirm allocations then succeed.
      7. Ongoing (2 weeks): re-run the duplicate census — zero NEW duplicated objects.
labels:
  - xero
  - posting
  - data-integrity
attention: null
version: 5
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