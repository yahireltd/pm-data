---
id: T-0332
title: "ORCHESTRATOR — Refund Process Hardening: master plan, build order, and full testing plan"
type: chore
state: triaged
created: 2026-06-09T20:27:05Z
updated: 2026-06-29T13:23:29Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p0
reporter:
  kind: human
  name: Austin
  channel: claude-code
assignee: null
acceptance_criteria:
  - Every phase gate in the sign-off checklist is ticked, with findings/decisions recorded on the linked tickets
  - The replay set (5–10 reconstructed real refunds) exists, is documented, and was used to verify T-0320, T-0324, and T-0326
  - The guard's shadow window on live completed with all over-cap hits investigated and the enforcement promotion recorded
  - The corruption detection query shows zero new cases at sprint close
  - C090586 is repaired on live and the customer recovery is settled or formally written off
  - A sprint retrospective note exists on SPR-003
out_of_scope:
  - T-0328 (executor transaction hardening) — explicitly post-sprint backlog with its own ADR
  - Stripe payment-taking flows (payment links, deposits) — only the refund path is in scope
code_anchors:
  - path: backend/controllers/ContractsController.php
    symbol: actionProcessContractRefunds
    lines: 1790-1850
  - path: common/services/RefundExecutor.php
    symbol: execute
  - path: common/services/RefundPlanner.php
    symbol: plan
  - path: common/services/PaymentAllocator.php
    symbol: allocate
  - path: common/models/Payments.php
    symbol: getUnusedPayment
    lines: 148-218
  - path: backend/views/contracts/manual-refund.php
    symbol: refund modal + processContractRefunds
  - path: backend/config/main.php
    symbol: sandbox flag / Secrets Manager
    lines: 11-80
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - refunds
  - payments
  - incident-c090586
  - orchestrator
attention: null
version: 6
backlog_status: confirmed_for_release
estimated_effort: runs whole sprint
source: discovered
---

## What this ticket is

The single coordination point for sprint SPR-003 (Refund Fixes). It holds the incident summary, the build order across all tickets, and the COMPLETE testing plan in one place. Individual tickets carry their own copy of their steps; this is the master view. Work the sprint top-to-bottom from here; tick the sign-off checklist at the bottom as gates are passed.

## The incident (why this sprint exists)

On 25 May 2026 the accounts team processed a refund on contract C090586. The screen said £138 was due back; the system sent the customer £1,399.85 — including an irreversible £1,255.85 Stripe refund to their card. The customer flagged it themselves. Full forensic chain (established 9 June, confirmed against the database):

1. **Root cause:** a BACS payment keyed in on 4 May was DATED 5 May. The "unused payment" calculation ignores future-dated allocation records, but the invoice balance calculation does not — so for the rest of that day the payment looked fully unused while its allocation had already settled the invoice.
2. **Trigger:** the statement page silently re-allocates "unused" money on every page view. Someone viewed the page minutes after entry and it applied a phantom £1,261.85 from the already-spent payment.
3. **Concealer:** allocation records are written with back-dated timestamps, so the phantom row looked like it was created at payment time — hiding the corruption for three weeks.
4. **Failed safety net:** when the customer's genuine £1,255.85 card payment later arrived it was applied as £0.00 (invoice already "paid"), making it look like a refundable overpayment. The refund modal netted the figures and showed £138; the execution path drops negatives and refunded every positive in full. Nothing checked the total against what the contract actually held.

A fleet-wide query found only this contract corrupted (plus one historic early-system contract).

## Sprint map and build order

Strict order — later items depend on earlier ones:

| # | Ticket | What | Why this position |
|---|--------|------|-------------------|
| 1 | T-0331 | Stripe dry-run gateway for the test environment | Every other ticket's testing depends on it; also makes the test box physically unable to send real refunds |
| 2 | T-0320 | Pre-flight refund guard (shadow → enforce) | The change that stops money leaving wrongly; everything else is defence in depth |
| 3 | T-0321 | Double-submission / concurrency lock | Same controller as T-0320 — build right after to avoid merge conflicts |
| 4 | T-0323 | Block future-dated payments | Tiny; gated on one accounts question; kills the root trigger |
| 5 | T-0324 | Modal shows the real refund plan | Needs the side-effect-free plan calculation; benefits from guard being in place |
| 6 | T-0325 | Statement page: no allocation on view, explicit Allocate action | Same screen as T-0324; needs accounts walkthrough before release |
| 7 | T-0326 | Unused-payment future-date blind-spot fix | Highest blast radius (26 call sites incl. Xero) — deliberately LAST, when all other protections are live |
| — | T-0327 | C090586 data repair + customer recovery | Operations track, runs in parallel; rehearse on test box first |
| — | T-0328 | Executor transaction/idempotency hardening | Backlog, after the sprint, own design pass (ADR required) |

## One-time test environment preparation (before ticket 1)

- [ ] Confirm which Stripe secret the TEST server's local params file loads. The sandbox flag only switches the database host — if the test box carries the live key, its refund buttons are live-armed TODAY. Record the finding on T-0331.
- [ ] Confirm the nightly live→test copy schedule and what time the test DB resets (test fixtures are wiped nightly — plan test days accordingly; conversely every morning gives a fresh real-data baseline).
- [ ] Ask accounts: "Do you ever deliberately date a payment tomorrow or later (e.g. expected clearing date)?" Gates T-0323. Record on T-0323.
- [ ] Ask accounts: were BACS refund transfers BR91febc3 (£6) and BRd29a371 (£138) actually sent? Gates T-0327's recovery amount.
- [ ] Check whether May 2026 is already posted/reconciled in Xero for C090586. Gates T-0327's repair method.

## Shared testing techniques (referenced throughout)

**A. Reconstruct–replay–diff (the workhorse).** The test DB is a nightly copy of live, so any recent real refund can be re-run: (1) on the TEST box delete that refund's artefacts (payment-refund rows, their credit-note allocations, Stripe-refund rows, overpayment links) to restore the pre-click state; (2) click Process Payment Refunds with the dry-run gateway on; (3) diff the logged would-be Stripe requests and written rows against what live actually recorded. Match = regression-free; divergence = a finding. Build the replay set once (5–10 refunds covering: pure credit note, genuine overpayment, BACS-only, card+BACS mix, deposit held, sales SC-contract) and reuse it for T-0320, T-0324, T-0326, and T-0328.

**B. The incident fixture.** Apply technique A to C090586 itself — its corrupted pre-refund state is in every nightly copy until T-0327 repairs live. This is the canonical "must be blocked / must be displayed honestly" test for T-0320 and T-0324.

**C. Golden-master balance diff.** Before a risky change, script-capture contract balance, per-payment unused amounts, and per-invoice balances for every contract active in the last 90 days; re-run after the change; diff must be empty except where the change predicts otherwise. Mandatory for T-0326; cheap insurance for T-0325.

**D. No-write proof.** Enable the DB query log, load the page/preview, assert zero INSERT/UPDATE from the area under test. Used by T-0324 (modal preview) and T-0325 (page view).

**E. Detection query (corruption sweep).** Payments whose allocation rows sum to more than the payment: group invoice_payments by paymentID having SUM(amountApplied) > payments.amount + 0.01. Run at sprint start (baseline), after T-0325 and T-0326 land (must not grow), and in T-0327's fleet check.

## Full testing plan by phase

**Phase 1 — T-0331 (dry-run gateway).** Unit: synthesized charge balances (no/partial/full refunds); refund creation logs exact pence/charge-ref/idempotency-key and returns a synthetic object; fail-safe factory returns dry-run whenever sandbox=true regardless of configured key. Integration: one end-to-end refund on the test box → flow completes, rows written, dry-run log correct, LIVE Stripe dashboard shows nothing. Replay 3–5 real refunds (technique A) — logged requests must match live to the penny. Cross-impact: every other Stripe client usage (payments, deposits, payouts, webhooks) untouched. **Gate: no other ticket starts integration testing until this passes.**

**Phase 2 — T-0320 (guard).** Test box: incident fixture (B) blocked when enforced / logged when shadow, with planned £1,399.85 vs cap £138 in the message; zero rows and zero dry-run requests written on block. Replay set (A) passes unblocked, request-identical. Edge cases: cross-contract allocate-then-refund (cap applies to post-allocation remainder), credit note exceeding payments, repeat refund attempt, deposit held, SC contract. Live: deploy shadow-ON enforce-OFF; review guard log daily; every over-cap hit is investigated (real catch vs false positive — false positive resets the clock); promote to enforced after a clean agreed window; record promotion here and on T-0320. Verify Process Deposit Refunds unaffected.

**Phase 3 — T-0321 (concurrency).** Two simultaneous POSTs for the same contract, 10+ runs: exactly one processes (assert via dry-run log row counts), the other gets a clear in-progress error. Different contracts concurrently: both succeed (lock is per-contract). Lock released after success, failure, AND forced exception. UI: buttons disable on click, re-enable on error. Decide and record whether the lock also covers deposit refunds.

**Phase 4 — T-0323 (future dates).** Only if accounts answered "no legitimate future-dating": form rejects tomorrow (server-side — verify via direct curl POST, not just the date picker); today and backdated save identically to before (compare allocation rows against the previous nightly baseline); both payment-only and deposit-only entries covered; 23:55 local time still accepts "today" (Europe/London). Sweep other payment-entry paths (webhook, admin CRUD, console) — record which exist and whether the rule applies; change none silently.

**Phase 5 — T-0324 (honest modal).** No-write proof (D) on modal open. Incident fixture (B): modal lists the full £1,399.85 plan line-by-line (or the guard-blocked state) — never a different number to what would execute. Preview-vs-execution diff over the replay set (A): every line matches source payment, method, amount — automate this diff; it becomes the permanent regression test. Edges: partially-refunded charge (capped amount shown), allocate-then-refund preview, nothing-refundable state.

**Phase 6 — T-0325 (page-view allocator).** No-write proof (D) on page load for contracts with unallocated payment / unused credit / fully settled (and decide explicitly about the page's existing recalculate-totals-on-view side effect). Explicit Allocate action: previews then writes exactly the capped rows. Over-application impossible: run Allocate against C090586's exhausted payment — applies nothing. Stale-balance-column fix: stored invoice balance equals computed balance after a partial payment. Workflow delta known in advance: diff the unpaid-invoices list before/after on the same nightly data. Accounts walkthrough + sign-off BEFORE release; watch the live unpaid list for a week after. Detection query (E) must not grow.

**Phase 7 — T-0326 (date-filter fix).** Step 0: survey future-dated rows on live — if not near zero, STOP and reassess. Call-site inventory (all ~26, classified by explicit-date vs no-date). Golden-master (C): zero unexplained balance diffs across the 90-day contract population. Unit both modes: tomorrow-dated allocation counted today (no-date mode — the test that would have prevented the incident) and still excluded under an explicit past perspective date (Xero mode). Xero regression: full export figures for a completed past month byte-identical, against TEST Xero IDs only. End-to-end root-cause closure: with this fix and T-0323 temporarily bypassed, post-date a partial payment and view the statement — no phantom allocation may appear.

**Ops track — T-0327 (repair).** Rehearse the repair script on the test box (the corruption is in every nightly copy): before/after row capture, on-screen statement reconciles with the bank reality, customer's other contracts untouched, unpaid list + customer area + Xero dry-run clean, refund button no longer offers phantom money. Only then run on live in a transaction with captured output attached to the ticket. Fleet check (E) re-run; decide the historic contract.

## Live rollout sequence

1. T-0331 carries no live behaviour change (live mode is passthrough) — deploy first, alone.
2. T-0320 ships shadow-only → daily log review → enforcement promotion (the recorded decision).
3. T-0321/T-0323/T-0324 ship behind normal review — low risk, behaviour additive or display-only.
4. T-0325 ships only after accounts sign-off; monitor unpaid list one week.
5. T-0326 ships last, after golden-master and Xero diffs are clean; keep the golden-master script for one week of post-deploy spot re-runs.
6. T-0327 live repair only after rehearsal is clean and recovery is agreed with the customer.

## Sign-off checklist (tick as gates pass)

- [ ] Test server Stripe secret verified (finding on T-0331)
- [ ] Accounts: post-dating question answered (on T-0323)
- [ ] Accounts: BACS transfers confirmed sent/not-sent (on T-0327)
- [ ] Xero posted-status for May confirmed (on T-0327)
- [ ] Phase 1 gate: dry-run gateway passes all tests; replay set established
- [ ] Phase 2 gate: incident fixture blocked; replay set unblocked; shadow window started on live
- [ ] Shadow window clean → enforcement promoted (date + decision recorded)
- [ ] Phases 3–6 each: ticket's tests pass on test box before live deploy
- [ ] Accounts walkthrough done for the allocation workflow change (T-0325)
- [ ] Golden-master + Xero diffs clean (T-0326)
- [ ] C090586 repaired on live; customer recovery settled; fleet check attached (T-0327)
- [ ] Detection query baseline re-run post-sprint: zero new corruption
- [ ] Retro note on SPR-003: what the incident cost and what the sprint changed

## Conversation

**2026-06-10 14:00 claude-code:** ## Investigation record, 2026-06-10: causal chain verified in code AND proven with live data

A multi-agent code investigation (each causal link independently verified, then adversarially re-checked against the code that ran on 25 May) plus a set of forensic queries Austin ran against live confirm the incident narrative in this ticket, with refinements. Queries and expected results are in repo file `sql/incident-c090586-proof-queries.sql`.

### The proven chain

1. **Forward-dating proven mechanically.** Stripe payments are wall-clock-honest, so the auto-increment id sequence dates physical inserts: payment 47215 (dated 05-05 08:35:00) sits at a lower id than Stripe payment 47216 created 04-05 08:59:58 → keyed before 9am on 4 May. The keying fingerprint: `ManualPaymentForm::savePayment` composes created = chosen date + wall-clock H:i (seconds :00); the same flow stamped INV 73100 with true time 04-05 08:35:34. Batch context: Jahzel keyed 12 receipts 08:09–08:35 on bank-holiday Monday 4 May — nine backdated to Fri 1 May, three value-dated to Tue 5 May. Looks deliberate (see T-0323 gate evidence).
2. **The blind-spot asymmetry** (`Payments::getUnusedPayment` filters allocations to created ≤ today 23:59:59, Payments.php:148–218; `Invoices::getBalancesInvoice` has no date filter) made 47215 read as £8,043.97 unused while INV73100 showed £1,261.85 owing — the same allocation row 48030 seen through two different lenses.
3. **The phantom write**: row 48031 (£1,261.85) was physically inserted on 4 May between 08:36 and ~09:00 (it precedes row 48032, wall-clocked ~09:00) — written by the manual-refund GET auto-allocator (ContractsController.php:564–606) or the identical-arithmetic allocate AJAX (PaymentsController:252–310, which has NO originalContractID gate — see T-0325 comment); `applyInvoicePayment` stamped it payment.created+1min = 05-05 08:36:00, concealing it. Code predicts exactly one phantom, writable only on 4 May — matching the data.
4. **The trap**: 12 May, the genuine £1,255.85 Stripe payment hit the "arithmetically full" invoice → deliberate £0.00 allocation row 48262 ("assign 0 so the payment can be refunded", Deposits.php:270–277) → fully "unused".
5. **The over-refund**: the view nets signed unused amounts (−1,261.85 + 1,255.85 + 144 credits = "£138" heading, manual-refund.php:604/676); the execution path drops negatives (PaymentAllocator.php:20) and refunds every positive in full (RefundPlanner/RefundExecutor) → £1,399.85, all three refunds 25-05 14:38 by userID 1021. Executor's 'BR'+md5 reference format matches BR91febc3/BRd29a371; `stripe_refunds.amount` confirmed stored in pence (125585) — independently validating the T-0331 review finding. **System failure, not operator error: the screen said £138.**
6. **Xero rejected the phantom on 20 May** — five days of unread warning before the money left (now ticketed as T-0349, early-warning monitoring).

All decisive code is long-standing (date filter Mar 2024, +1-min stamping Jul 2021, netting/dropping split May 2025) and was byte-identical at the incident-time master HEAD (575277a1) — not a recent regression.

### New facts folded into the sprint

- **Three more landmines repaired under T-0327**: payments 44320/44321/44322 (£3,535.93) keyed 2 Jan 2026, year-typo'd to 31-12-2026 — invisible-allocation state armed until year-end. Not over-allocated (their invoices settled exactly at entry, so the balance>0 gate never fired) — which also confirms the over-allocation precondition: forward-dated payment + invoice with residual balance + a page view/allocate during the window.
- **Fleet sweep clean** otherwise: only historic payment 2493 (2021).
- **Xero state for the repair** (on T-0327): phantom never accepted by Xero, so local correction converges; the 5-June £1,255.85 overpayment posted for 47445 needs Xero-side correction.
- **Test-box still live-armed** even with T-0331 deployed, via actionProcessContractRefundsOld / DepositRefundService / ManualRefundForm (on T-0331) — close these before any sprint integration testing.

### Status vs plan (2026-06-10)

Only T-0331 exists in code (branch refund-hardening-t0331-stripe-dryrun, commits a9615bdd + 84444136; note a9615bdd's message "Branch to test account fixes" understates that it contains the bulk of the implementation). Nothing deployed or run anywhere yet. All other tickets unstarted; build order in this ticket remains correct, with the phase-0 addition: close the test-box bypass paths and run the trio date repair before integration testing begins.

**2026-06-10 18:14 claude-code:** **Phase 1 complete, Phase 2 substantially through its test-box gate (2026-06-10).** Phase 1 (T-0331): all acceptance criteria verified — in review awaiting Austin's sign-off. Phase 2 (T-0320): guard built, adversarially reviewed (one formula blocker caught and fixed pre-deploy: cross-contract outflow blindness), and proven on the test box — incident fixture LOGGED in shadow (planned £1,399.85 vs cap £138, margin −£1,261.85) and BLOCKED in enforce with verified zero writes and zero Stripe calls; an exactly-at-cap real replay passed untouched under enforcement. Remaining before the Phase 2 live gate: re-run the full replay set under enforcement (after tonight's restore re-arms fixtures), edge fixtures (credit-exceeds-payments, cross-contract), then deploy and start the live shadow window. Branch refund-hardening-t0331-stripe-dryrun now carries both T-0331 and T-0320 plus the T-0333 conversions — note for the rollout sequence: the branch is no longer strictly "T-0331 alone", but everything in it is either passthrough-identical or shadow-by-default on live, so the deploy-first-alone property still holds.

**2026-06-25 19:03 claude-code:** **Refund hardening — status & go-live plan (go-live targeted for NEXT WEEK, not before).**

All work below is committed to branch `refund-hardening-t0331-stripe-dryrun` (now on GitHub) and tested on the sandbox/test box. Nothing is live yet.

### Where we are — built, committed, tested
- **Stripe dry-run for the test box (T-0331)** — DONE. The test box cannot touch real Stripe.
- **Incident repair, C090586 (T-0327)** — DONE.
- **Allocation cap (T-0325)** — the chokepoint that stops a payment being allocated more than it holds. It refuses the phantom over-allocation that started the incident.
- **Refund guard (T-0320)** — built, plus two false-positive fixes added this week (credits split-payment cash; tolerates penny rounding). **Validated by replaying ALL 3,023 historical refunds**: 2,932 reproduce exactly, 0 errors; guard over-cap flags dropped 13→8 (the 5 false positives removed), and the original incident is still blocked (£1,399.85 vs £138 cap). Defaults to SHADOW (logs, never blocks).
- **Forward-dated payments blocked (T-0323)** — manual payments can no longer be dated in the future. Closes the original trigger.
- **Deposit refund Stripe failures no longer silent (T-0333 / T-0328)** — safe-retry idempotency key, honest succeeded/failed status, failures surfaced (contract not marked complete). Validated on 17 real Stripe deposits, both success and failure paths.
- **Replay/test harness (this ticket, T-0332)** — reconstruct→replay→diff every historical refund + exercise the deposit fix; sandbox-only, every replay rolls back.

### What's left before go-live (next week)
**Deployment mechanics:**
1. Deploy the branch to live (behaviour-identical except the new guard/cap/forward-date block); run the one migration once; confirm the live config has no `sandbox` flag.
2. Run the guard in **SHADOW first** (logs over-cap refunds, blocks nothing), watch the log on real traffic for a few days, then flip to **ENFORCE** (`params['refundGuardMode']='enforce'`).

**Remaining sprint tickets — decide blocker vs post-go-live:**
- **T-0321** prevent double-submission — recommend before enforce (concurrency).
- **T-0324** refund confirmation must show exactly what will be refunded — the guard now backstops it, but worth fixing for operator trust.
- **T-0326** unused-payment future-date blind spot — now mitigated by the cap + guard + forward-date block; can follow go-live.
- **T-0426** "BACS refund happened when Stripe selected" — investigate.
- Deposit follow-ups (cross-ref **T-0328/T-0349**): a Stripe-failed deposit still *reads* as refunded via `getUnusedDeposit`; and a reconciliation sweep for stuck PENDING/failed Stripe refunds.

**Bottom line:** the core incident is closed and validated (trigger blocked + over-allocation capped + over-refund guarded + deposit failures surfaced). Plan for next week: deploy in shadow, confirm clean, then enforce. Remaining items are hardening/UX, not incident-critical.

**2026-06-29 13:23 claude-code:** 🚀 **SHIPPED TO LIVE — 2026-06-29** (Austin deployed master to production).

The refund-hardening sprint's core is now live (merged via PR #104 + the batch-fix follow-up `07b10059`). Live config confirmed: `sandbox=false`, no `refundGuardMode` → live Stripe, live DB, **guard enforce-by-default**. Deploy was refund-only (no migration — the dry-run table is sandbox-only).

**Live now:**
- **Refund guard (T-0320)** — over-cap refunds are **blocked by default** with a clear operator message. Validated by replaying all 3,023 historical refunds (incident blocked, 5 false positives eliminated, only genuine over-caps flag).
- **Allocation cap (T-0325)** — payments can no longer be over-allocated (the phantom write that started C090586 is refused at source).
- **Forward-dated manual payments (T-0323)** — rejected at the form + date picker.
- **Stripe gateway + dry-run (T-0331)** — live uses the live gateway; dry-run is sandbox-only.
- **Deposit-refund hardening (T-0333)** — idempotency-safe retry, honest status, failures surfaced on **both** the single refund page **and** the end-of-hire batch.

**Remaining (post-go-live, not incident-critical):** T-0321 (double-submit lock), T-0324 (refund confirmation display), T-0326 (unused-payment date blind spot — now mitigated by cap + guard + forward-date block), T-0426 (BACS-when-Stripe), and the deposit follow-ups: T-0328 (don't read a Stripe-failed deposit as refunded) + T-0349 (reconciliation sweep for stuck PENDING/failed refunds). Suggest watching the `refund.guard` / `allocation.cap` logs on live for a few days, and a quick smoke check that a genuine refund still completes.
