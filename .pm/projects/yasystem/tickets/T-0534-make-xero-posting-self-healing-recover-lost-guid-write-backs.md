---
id: T-0534
title: "Make Xero posting self-healing: recover lost GUID write-backs instead of minting duplicates/orphans"
type: bug
state: review
created: 2026-07-09T13:58:22Z
updated: 2026-07-09T16:19:48Z
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
  - "Sandbox (Demo Company) e2e: a prepared day-window posts cleanly end-to-end from the test box via `php yii xero-post/daily`; immediately re-running the same window creates ZERO new objects in the demo org"
  - "Sandbox kill-test: `kill -9` the posting process mid-run, re-run — no duplicates in the demo org; heal_from_log/self_heal rows appear and write-backs complete (this simulates the fpm request_terminate_timeout SIGKILL)"
  - "Heal safety: a record given a synthetic SECOND success log row (2 distinct GUIDs) is REFUSED auto-heal with a 'left for review' log row; a synthetic success row pointing at a WRONG GUID is refused with a reference-mismatch log row — nothing written locally in either case"
  - "Invoice self-heal: NULLing a posted invoice's xeroInvoiceID and re-running its window restores the SAME GUID (compare before/after) with no 'must be unique' loop and no new invoice in Xero"
  - "Money-object heal: NULLing a posted deposit refund's xeroPaymentID and re-running restores the SAME GUID via a verified heal_from_log success — zero Xero writes"
  - "Token store: after /xero/login, web pages and the console command authenticate off the same DB row; no new invalid_grant errors in xero_posting_logs over the following week; concurrent browser run during the cron is rejected by the mutex"
  - "Live steady-state: weekly duplicate census (objects with >1 distinct xero_response GUID) shows zero NEW objects after deploy, measured over 2+ weeks"
  - "Test-box safety: post-restore job blanks xero_oauth_tokens on the sandbox DB (test box must never hold a live-org token)"
out_of_scope: []
code_anchors:
  - path: common/models/XeroFunctionsNew.php
    note: healOverpaymentFromLog / healDepositRefundFromLog / healXeroPaymentFromLog / recoverInvoiceGuids / recoverCreditNoteGuids (helpers before postItemisedInvoices); chunked+self-heal postItemisedInvoices + postItemisedCreditNotes; per-record deterministic keys in postOverpayments, postDepositOverpayments, postDepositRefunds, postCreditNoteRefundAllocations, postOverpaymentRefunds, postDepositChargeAllocations
  - path: common/services/XeroLogger.php
    note: soleSuccessGuids — the heal-from-log source with the single-GUID safety rule
  - path: common/models/StorageClass.php
    note: DB-backed token store, method signatures unchanged; reload() used by the refresh lock
  - path: common/services/XeroApiService.php
    note: GET_LOCK('yh_xero_token_refresh') serialised refresh with re-read-after-wait — the invalid_grant fix
  - path: console/controllers/XeroPostController.php
    note: xero-post/daily — T-8 sweep first, then T-7; yh_xero_post_all mutex; cron 0 6,18 * * *
  - path: console/migrations/m260709_160000_create_xero_oauth_tokens_table.php
    note: single-row shared token table
  - path: backend/controllers/XeroController.php
    note: actionHealInvoiceNumbers (bulk backlog heal); xeroSessionMissing now checks the DB store
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260709-1413
    model: claude-opus-4-8
    started: 2026-07-09T14:13:20Z
    status: completed
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
      DEPLOY STEPS (Austin, live):
      1. Review the diff (5 modified + 2 new files), commit, deploy to live.
      2. Run the migration: `php yii migrate` (creates xero_oauth_tokens).
      3. Connect Xero once: open backend /xero/login and complete sign-in — the token now lands in the DB table (check: SELECT * FROM xero_oauth_tokens shows a token + tenant_id).
      4. Add the cron: `0 6,18 * * * cd /var/www/yasystem && php yii xero-post/daily >> runtime/logs/xero-post-cron.log 2>&1`

      VERIFY (happy path):
      5. Manual smoke first: run one recent quiet day's window from the browser as usual (/xero/post). Expect identical results to before; check xero_posting_logs for that window — posted counts normal, no errors.
      6. Console smoke: `php yii xero-post/daily` by hand. Expect two windows (T-8 then T-7) posted, mutex respected, run start/finish rows in the log.
      7. Heal-from-log: pick ONE deposit refund allocation posted in the last weeks (has xeroPaymentID + a success log row), NULL its xeroPaymentID in the DB, re-run that day's window. EXPECT: a heal_from_log SUCCESS row for it, xeroPaymentID restored to the SAME GUID (compare before/after), and NO new payment in Xero on that credit note.
      8. Invoice self-heal: same trick with one itemised invoice's xeroInvoiceID → expect self_heal row, same GUID back, no "must be unique" loop.

      EDGE CASES:
      9. Records with 2+ logged GUIDs (historical duplicates) must NOT be auto-healed — grep the log for heal_from_log errors saying 'left for review'; the closed-period 2025 items should appear here if their windows are ever re-run, never auto-written.
      10. Concurrency: while the cron runs, trigger /xero/run-post — expect the 'already in progress' rejection.
      11. Token sharing: after the cron has run at least once, use a browser Xero page (e.g. /xero/quota alternative or a posting page) — both should work off the same DB token with no re-login; watch the log for absence of new invalid_grant errors over the following week.

      CROSS-IMPACT (shared components touched):
      - StorageClass is also used by XeroOldController, XeroNewController and XeroFunctions (legacy) — open one legacy Xero page to confirm it still authenticates.
      - postItemisedInvoices/postItemisedCreditNotes are called as prereq steps inside payment/credit-note/deposit-charge/deposit-refund allocation steps — step 5's full window run covers these.
      - XeroController::xeroSessionMissing now checks the DB store — verify /xero/dedupe-checklist's not-connected message only appears when genuinely disconnected.

      ONGOING (2 weeks): weekly duplicate census (query on T-0509 comment) must show ZERO new objects with >1 distinct GUID; unposted report (/xero/unposted-report) stays clean apart from genuine validation errors (e.g. credit note 75664's totals bug).
    ended: 2026-07-09T15:07:19Z
    summary: "Made the Xero posting pipeline self-healing so interrupted or failed runs no longer create duplicate money records in Xero or leave invoices stranded — the problem that caused the June/July incidents and has been quietly leaking duplicates since 2025, each needing slow manual accounting repairs. What we did: every document and payment type now (1) checks our own posting history before posting and restores the link if an earlier run already created the record in Xero (verified against Xero echoing back our own identifiers before anything is written — nothing is ever guessed); (2) posts money records one-at-a-time with a fixed retry-safe key, so re-posting the same record within a day returns the original instead of creating a copy; (3) saves its bookkeeping before declaring success, so the records can never claim something was done when it wasn't; (4) posts invoices in small batches with immediate write-back so a killed run loses almost nothing and recovers by itself. We also moved the Xero connection out of the browser session into a shared database store with a refresh lock — this lets the new twice-daily automatic posting command run on a schedule (no human needed, immune to the 10-minute server kill that silently broke runs), and should also stop the recurring \"invalid_grant\" connection errors caused by sessions fighting over Xero's single-use refresh token. If we had done nothing: every posting session remained one interruption away from duplicating hundreds of money records or stranding a week of invoices, with each incident costing days of forensic cleanup. All code is in the working tree on master, uncommitted per project policy — Austin reviews, commits and deploys."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - xero
  - posting
  - data-integrity
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-09T15:07:19Z
version: 10
---

## Problem

Every Xero posting path shared one shape: *select rows WHERE the xero-id column is NULL → create in Xero → write the returned id back afterwards*. Anything separating the create from the write-back (Xero 5xx, php-fpm's request_terminate_timeout=600s SIGKILL — confirmed identical on live and test, introduced with the PHP8 migration ~2026-04-30 — concurrency pre-mutex, deploy lag) minted damage:

- **Non-unique money objects** (overpayments, payments, refund payments, allocations) → silent duplicates in Xero. Census 2026-07-09: ~401 objects posted ≥2× with different GUIDs, leaking since 2025-07. Duplicates need slow manual accounting repair — the costliest failure mode.
- **Invoices/credit notes** (unique numbers) → permanent orphans: "Invoice # must be unique" error-loops forever and (since 7a230d48) blocks payment/credit/deposit allocations. 729 invoices stranded across 2026-06-19 (Xero 500) and 2026-07-02 (T-0509 concurrency); healed 2026-07-09 via /xero/heal-invoice-numbers.

## What was built (final scope — evolved during investigation with Austin)

**Layer 1 — invoices/credit notes (the orphan class):**
- Chunked posting (50/call) with GUID write-back immediately after each chunk (a killed run strands ≤1 chunk, not a day's batch).
- Recover-on-throw: a thrown batch create re-fetches that chunk's numbers from Xero and writes back whatever committed, then continues with remaining chunks (no more `return []` abandonment).
- Self-heal on "must be unique": the duplicate-number rejection triggers a bulk GUID read-back instead of an eternal error loop.

**Layer 2 — money objects (the duplicate class), all six paths (postOverpayments, postDepositOverpayments, postDepositRefunds, postCreditNoteRefundAllocations, postOverpaymentRefunds, postDepositChargeAllocations):**
- **Heal-from-log** (`XeroLogger::soleSuccessGuids`): before posting, check our own posting log for a prior success. Safety rules: only when EXACTLY ONE distinct GUID exists (2+ = known duplicate → refused, logged 'left for review'); fill-only-blank; and each heal is **verified against Xero** by fetching the object and matching the JSON identifiers we embedded at creation (paymentID/depositID/allocationID/paymentRefundOverpaymentID — Xero echoing our own IDs back is the identity proof). Deposit-charge allocations use log-presence only (Xero has no per-allocation GET; write-back value comes from current row data, not the log).
- **Deterministic per-record idempotency keys** (yh-op-/yh-dep-/yh-dra-/yh-cnr-/yh-pro-/yh-dca- + localId), one object per request: any re-post within Xero's ~24h key window replays the original response (no duplicate; the replay carries the GUID so the lost write-back completes). Per-record try/catch — one bad record no longer sinks a batch.
- **Save-before-log ordering** everywhere: the log can never claim success while the local write-back is unsaved.

**Layer 3 — token store + automation (enables the cron, fixes invalid_grant):**
- `xero_oauth_tokens` single-row table (migration m260709_160000); StorageClass rewritten DB-backed with unchanged method signatures — web and console share ONE token.
- XeroApiService serialises refreshes behind GET_LOCK('yh_xero_token_refresh') with re-read-after-wait — Xero refresh tokens are single-use; sessions racing them was the recurring invalid_grant cause.
- New console command **`php yii xero-post/daily`**: re-sweeps T-8 FIRST (failures retry inside the 24h key window), then posts T-7 (accounts' Stripe-lag offset). Cron `0 6,18 * * *` — twice daily so every failure retries at +12h, never at the key boundary. Console execution is immune to fpm/Apache timeouts. Shares the yh_xero_post_all mutex with browser runs.

Also in the same working tree: /xero/heal-invoice-numbers (log-driven bulk backlog heal used for the 729; dry-run default).

## Out of scope
- Legacy (XeroFunctions) invoice/credit paths — all legacy documents already posted (Austin).
- Repair of historical closed-period duplicates (T-0509 conversation holds the worklist).

## Testing constraint (why the test plan is sandbox-first)
Live customer xeroIDs match the live Xero org only — a test org's IDs differ. Sandbox testing works by NULLing xeroIDs (+ doc xero-ids) for a chosen window on the test box: the flow auto-creates contacts in the Demo Company via generateCustomers, so IDs never need to match. SAFETY: the nightly RDS restore copies live's xero_oauth_tokens row to test — it must be blanked post-restore or the test box briefly holds a live-org token.

## Conversation

**2026-07-09 15:50 claude-code:** **Sandbox testing design finalised with Austin (2 additions in the working tree since the run completed):**

1. **Cross-window reference problem (Austin's catch):** allocations in any test window reference parents (invoices/credit notes/payments/deposits) from OTHER windows whose xero-ids are live-org GUIDs — meaningless to a Demo Company, so every such allocation would be rejected. Solution: `php yii xero-post/prep-sandbox --yes=1` (new, hard-guarded: refuses unless params.sandbox=true AND the DB host contains 'restored') blanks EVERY Xero linkage column across the sandbox DB (ya_customers.xeroID; invoices/deposit_charges.xeroInvoiceID; payments/deposits xeroOverpaymentID+xeroPaymentID; invoice_payments.xeroInvoiceID; deposit_refund_allocations/credit_note_allocations.xeroPaymentID; postedToXero flags on credit_note_allocations/payment_refund_overpayments/contract_deposits) and truncates xero_posting_logs (--keepLogs=1 to skip). Every window is then self-contained: the posting steps' existing prerequisite logic selects parents by NULL xero-id with NO date filter, so it creates them in the demo org first, then allocates against fresh GUIDs. The xero_oauth_tokens row is untouched (it's the sandbox's own demo-company token).

2. **Refresh reality + token safety:** the live→test refresh (`yii sandbox/refresh --yes=1`, runs ON LIVE) had its cron line removed — the sandbox has been frozen at a consistent 2026-07-02 pre-incident snapshot; it was never nightly. Recommended re-add on live: `0 3 * * *` (finishes before the test box's 06:00 migrate). SandboxController PRESERVE_TABLES now includes `yahire_db.xero_oauth_tokens` so the sandbox keeps its own demo token and NEVER receives live's (a copied live token would let the test box reach the live Xero org for up to ~30 min). Ordering: migrate on the test box BEFORE the first refresh after deploy so the preserve engages (or blank the row once manually).

Sandbox test sequence: deploy both boxes → migrate test → refresh from live → prep-sandbox → connect Demo Company via sandbox /xero/login → clean post → identical re-run (zero new demo objects) → kill -9 mid-run + re-run (heal proof) → synthetic-log-row refusal tests.

**2026-07-09 16:11 claude-code:** **Sandbox org-configuration gap closed (Austin's catch #2: bank accounts / nominals / VAT don't exist in a demo org).**

Design principle: the sandbox tests prove posting MECHANICS (duplicate prevention, heal, idempotency, kill-recovery) — not chart-of-accounts fidelity. So:

1. **Nominals**: `xero-post/prep-sandbox` now also normalises every `invoice_items.xeroNominal` to **200** (Sales — present in every stock UK demo chart). No chart replication needed.
2. **Bank accounts**: XeroApiService (sandbox branch) no longer uses hardcoded GUIDs (they go stale on every Demo Company reset). It resolves the two bank accounts **by name** at runtime — create `Barclays Test` and `Stripe Test` once per demo reset (Accounting > Bank accounts, any bank/number, ~2 min). Missing accounts fail loudly with setup instructions instead of cryptic per-payment validation errors. Live branch untouched (hardcoded live GUIDs as before).
3. **Account 610** (hardcoded on overpayment lines): create once per demo reset in the chart — the resolve error message reminds you.
4. **VAT**: non-issue — our lines set explicit TaxAmount, and the UK Demo Company carries UK VAT rates. Amounts are asserted against OUR values.

**Per-demo-reset setup (~5 min total)**: reset Demo Company → create 2 bank accounts (exact names above) + account code 610 → connect via sandbox /xero/login → `yii xero-post/prep-sandbox --yes=1` → run the test suite.

**Known-expected sandbox failure**: itemised credit note 75664 has document total ≠ sum of lines in OUR data (live log shows the same rejection) — it will fail in the demo org too. That's a data bug reproducing correctly, not a regression; exclude it from the clean-run assertion.

**2026-07-09 16:19 claude-code:** Correction from Austin: account 610 is a Xero DEFAULT chart account (Accounts Receivable) — it already exists in every demo org, no creation needed (and for RECEIVE-OVERPAYMENT bank transactions Xero fixes the double-entry itself, so the line's account code is effectively moot). The per-demo-reset setup shrinks to just: create the two named bank accounts ('Barclays Test', 'Stripe Test') + connect via sandbox /xero/login + `yii xero-post/prep-sandbox --yes=1`. The resolve-error message has been updated to drop the 610 instruction.
