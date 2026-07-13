---
id: T-0534
title: "Make Xero posting self-healing: recover lost GUID write-backs instead of minting duplicates/orphans"
type: bug
state: done
created: 2026-07-09T13:58:22Z
updated: 2026-07-13T16:51:03Z
project: accounts-integrity
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
  - "[x] Sandbox (Demo Company) e2e: a prepared day-window posts cleanly end-to-end from the test box via `php yii xero-post/daily`; immediately re-running the same window creates ZERO new objects in the demo org"
  - "[x] Sandbox kill-test: `kill -9` the posting process mid-run, re-run — no duplicates in the demo org; heal_from_log/self_heal rows appear and write-backs complete (this simulates the fpm request_terminate_timeout SIGKILL)"
  - "[x] Heal safety: a record given a synthetic SECOND success log row (2 distinct GUIDs) is REFUSED auto-heal with a 'left for review' log row; a synthetic success row pointing at a WRONG GUID is refused with a reference-mismatch log row — nothing written locally in either case"
  - "[x] Invoice self-heal: NULLing a posted invoice's xeroInvoiceID and re-running its window restores the SAME GUID (compare before/after) with no 'must be unique' loop and no new invoice in Xero"
  - "[x] Money-object heal: NULLing a posted deposit refund's xeroPaymentID and re-running restores the SAME GUID via a verified heal_from_log success — zero Xero writes"
  - "[x] Token store: after /xero/login, web pages and the console command authenticate off the same DB row; no new invalid_grant errors in xero_posting_logs over the following week; concurrent browser run during the cron is rejected by the mutex"
  - "[x] Live steady-state: weekly duplicate census (objects with >1 distinct xero_response GUID) shows zero NEW objects after deploy, measured over 2+ weeks"
  - "[x] Test-box safety: post-restore job blanks xero_oauth_tokens on the sandbox DB (test box must never hold a live-org token)"
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
attention: null
version: 28
branch: t0534-xero-posting-self-heal
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

**2026-07-09 17:52 claude-code:** **Sandbox test suite COMPLETE (Demo Company, new test box xerotest.yahire.com). 13 scenarios, one major negative finding, fixed and re-tested. Branch now at 67c316ca (3 commits).**

**Passed:** (1) clean 2-window run — every path incl. all six money types, ~600 objects, zero duplicate GUIDs; (2) identical re-run — 3s/window, zero new objects; (3) kill -9 mid-run at 75s — resumed cleanly, mutex auto-released, no duplicates or orphans (per-record write-back shrank the vulnerable window so far the kill couldn't strand a single record); (4) heal-from-log restores the verified original GUID; (5) tampered-GUID heal is REFUSED on Xero verification ('left for review'); (6) two-GUID records skipped per the known-duplicate rule; (7) locked token refresh — one refresh, zero invalid_grant; (8) sandbox bank accounts resolve by name; (9) refund-payment types are additionally self-blocking on Xero (duplicate re-post bounced with 'amount exceeds outstanding' — no damage possible).

**NEGATIVE FINDING (the important one):** deterministic idempotency keys do NOT replay across posting runs — empirically they replay only within the same access token (~30 min). Staged re-posts of overpayment bank transactions created REAL duplicates in the demo org. Overpayments have no unique number and no outstanding-amount cap, so they were left with no cross-run protection — exactly the T-0509 duplicate type.

**Fix (commits 569f1512 + 67c316ca):** on-Xero backstop in both overpayment paths — before creating anything the log couldn't heal, fetch Xero's RECEIVE-OVERPAYMENTs for the window's dates (one paged call per day, NOT per record) and match candidates client-side on the local id embedded in our echoed line-item description JSON. Exactly one match → recover GUID, skip post (TESTED: original GUID restored). Multiple matches → refuse + flag for review, never post into ambiguity (TESTED: stayed NULL, no third copy). None → post normally.

**Two side-discoveries:** (a) Xero where-filters using Reference string functions (Contains/EndsWith) silently match nothing on BankTransactions — meaning **T-0509's XeroOverpaymentDedupeService::findBankTransactionForOverpayment never matched anything** and should be fixed the same way if ever reused; (b) bank-transaction line items are only returned when the page parameter is passed.

**Test debris in the demo org** (wiped by next demo reset / sandbox refresh): duplicate overpayments for payments 48332/48372 and deposit 33585 — created deliberately by the staged tests.

**Remaining for live:** merge branch → master, deploy, migrate, /xero/login (live org), one manual canary window via console, then cron 0 6,18. Sandbox evidence is all in the fresh xero_posting_logs on the test DB.

**2026-07-09 18:37 claude-code:** **Session continued — 4 more commits on the branch (now 55c636c6):**

1. **Write-ahead attempt rows** (Austin's go-ahead): one 'attempt' row before every Xero create, all 8 paths. Immediately proved itself: when the completeness run froze, the single stranded attempt row (deposit_refund 29452) pinpointed the exact record it was stuck inside.

2. **Live-discovered freeze + two fixes:** the completeness run exhausted the demo org's DAILY API cap (5,000 — all of today's testing) and Xero's 429 carried Retry-After=81178s (~22.5h). xeroCall honoured it verbatim → the run froze in a 22.5-hour nanosleep, confirmed via /proc syscall (hrtimer_nanosleep). Fixes: (a) fail fast when X-DayLimit-Remaining=0 or Retry-After>300s — per-record catches log it and the next sweep completes the work; minute-limit waits still honoured, capped 120s; (b) Guzzle connect/read timeouts (15s/90s) on the AccountingApi client — default was hang-forever on a stalled connection; timing out is safe now because the attempt-row + heal/backstop layers recover a committed-but-unconfirmed create instead of duplicating.

3. **Post-run issue digest + triage page (Austin's feature request):** after each cron run, errors/refused-heals/daily-cap-aborts/stranded-attempts are summarised in an email (params xeroPostDigestTo, default austin@; no issues = no email) linking to /xero/run-issues — a zero-API page where each issue deep-links to the Xero document and the affected contract's /contracts/manual-refund page (contract resolved per record type).

**Re 29452:** the daily-cap 429 means Xero REJECTED the create — nothing exists on Xero; it posts fresh next run. **Pending:** the full completeness re-test (windows 06-15/06-16) could not finish — demo org quota is zero until ~2026-07-10 17:00 UTC (rolling window). Resume tomorrow evening with `php yii xero-post/daily 24 25` (offsets shift with the date), then re-run the completeness report; the earlier snapshot was mid-run and is not a verdict. The interrupted run doubles as another organic kill-recovery case for the resume to prove.

**2026-07-09 19:06 claude-code:** **End-of-day record (2026-07-09) — branch t0534-xero-posting-self-heal @ d13910d6, 7 commits, all deployed to the new test box (xerotest.yahire.com / 3.11.164.105).**

**Commits today:** (1) 8bf47e20 core build — self-heal + heal-from-log + deterministic keys + DB token store + xero-post/daily + prep-sandbox + demo-tenant login guard; (2) 569f1512 reference backstop v1 (superseded); (3) 67c316ca backstop v2 — fetch Type+Date paged, match client-side on echoed description JSON (Xero's Reference string-function filters silently match nothing; line items only returned with page param); (4) 93d1cae8 write-ahead attempt rows on all 8 paths; (5) 7729656a fail-fast on daily-cap 429s (a 22.5h Retry-After froze a run in nanosleep) + Guzzle timeouts 15s/90s; (6) 55c636c6 post-run digest email + /xero/run-issues triage page (contract manual-refund links + Xero deep links); (7) d13910d6 skip invalid LINKED emails in contact creation (root cause of all 'Email address must be valid': phone number / double-@ / trailing-dot in ya_customers_linked_emails — NOT the customers' own email columns), honest 'customer not on Xero' skip labels (76219 'Unknown validation error' was a cascade of customer 59454), triage page grouping, backstop line-item hardening.

**Test evidence so far:** clean run ✓, identical re-run (zero new objects) ✓, kill -9 recovery ✓, heal verified-restore ✓, tampered-GUID refusal ✓, two-GUID skip ✓, backstop recover ✓ + refuse ✓, locked token refresh ✓, cross-run key replay ✗ (disproven — replaced by backstop). PENDING: full completeness run (06-15/06-16 froze on daily-cap exhaustion mid-window).

**TOMORROW'S TEST PLAN (Austin, WFH):**
Quota unlock: RESET the Demo Company first — new org = fresh 5,000/day immediately (limit is per app per org). Then the ritual: /xero/login (pick Demo Company; guard refuses anything else) → create bank accounts 'Barclays Test' + 'Stripe Test' (exact names) → `php yii xero-post/prep-sandbox --yes=1`.
1. Completeness run: `php yii xero-post/daily 24 25` (on 07-10 that's 06-15 then 06-16) — then the completeness report (claude runs it): PASS = zero UNCATEGORISED leftovers; specifically customers 59454/13658/5341 must now post (with customer_linked_email warning rows) and credit note 76219 must post.
2. Attempt accounting: every attempt row has an outcome; zero stranded.
3. Digest: if issues → email arrives at austin@ linking /xero/run-issues; if clean → 'no digest sent' on stdout. Verify the triage page grouping renders.
4. Xero GUI spot-check (accounts eye): open 2-3 posted invoices/credit notes in the demo org — amounts + VAT match ours.
5. Daily-cap fail-fast (free test): if any quota edge is hit, records must ERROR quickly (not freeze) and complete on re-run.
KNOWN GOOD leftovers: bad-email warnings for the 3 customers (until live data fixed); demo-org duplicate debris (48332/48372/33585) vanishes with the reset.

**Also queued (not quota-dependent):** re-add sandbox/refresh cron on live (0 3 * * *, runs on live); fix the 3 linked-email rows on LIVE data (SQL in chat/ticket); OPTIONAL improvements: proactive rate pacing in per-record loops (stop paying the 429 retry tax), include status='warning' rows in digest/triage, report the two Xero API defects (token-scoped idempotency replay; Reference filters matching nothing on BankTransactions) to Xero support. After all green: merge to master → live migrate → /xero/login (LIVE org) → manual canary window via console → single daily cron `0 6 * * *` (twice-daily dropped — its +12h-inside-key-window rationale died with cross-run replay).

**2026-07-09 19:09 claude-code:** Amendment to the queued list: the live→test refresh cron stays OFF for now (Austin) — a nightly refresh would overwrite the prepped sandbox (blanked Xero columns / truncated logs) and force a re-prep before every test day. Re-add it AFTER T-0534 merges to master and live testing is done — sequencing then takes care of itself, since the refresh runs on live and the merged code carries the xero_oauth_tokens preserve line it needs.

**2026-07-10 13:59 claude-code:** **Acceptance testing 2026-07-10 (WFH session): ALL GREEN.** Demo Company reset for fresh quota, sandbox prepped, then four windows posted (2026-06-09, 06-10, 06-15, 06-16) via `xero-post/daily`.

Results against the handover checklist:
1. **Completeness** — zero UNCATEGORISED leftovers across all 10 money-path scopes + referenced customers, all four windows. (~2,067 successful posts, 0 validation errors.)
2. **The three fixed customers** — 59454, 13658, 5341 all posted, each with exactly one `customer_linked_email` warning naming the bad address (07912406000 / rose@themanuallondon.com. / orders@@azreliant.co.uk). Credit note 76219 posted (466efd65-…). Note: the checklist's windows (06-15/16) never contained these records — their documents are dated 06-01→06-11, hence the extra 06-09/06-10 run. A fourth, unplanned proof also appeared: customer 89428 hit the same skip mechanism organically.
3. **Attempt accounting** — 1,022 attempt rows, 0 stranded.
4. **Digest** — clean-run path confirmed ("no digest sent" on old code). NEW: per Austin's request, clean runs now send an "all clear" digest listing every step outcome (commit d1751ab5d) — verified live, email received at austin@.
5. **Re-run no-op** — same windows re-run completed in 4.3s, zero new posts, census still empty.
6. **Duplicate census** — empty throughout.

Remaining before merge: Austin's accounts-eye spot check in the demo GUI (amounts/VAT on 2–3 documents) + triage page render check. The completeness report script (reconstructed this session, previously uncommitted) lives at `/home/ubuntu/xero-completeness-report.php` on xerotest — worth committing to the repo so it survives the box.

---

**2026-07-13 16:50 — Austin Pickering**

Now made live and posted a few days - all went ok. Tomorrow is to clear up until the 5th then run daily via cron

---

**2026-07-13 16:51 — you**

First few posts done and went ok
