---
id: P-0020
slug: accounts-integrity
name: Accounts Integrity Programme — Xero posting & document generation
state: active
phase: build
created: 2026-07-10T21:16:32Z
updated: 2026-07-15T16:05:10Z
owner:
  kind: human
  name: Austin
goal: "Reduce accounts' reliance on IT: eliminate the generation/posting problems that force Austin to step in with manual data fixes, and make any remaining issue self-announcing and self-serviceable by accounts."
success_criteria:
  - "30 days post-release: zero entries in the invoice-integrity log channel from newly generated documents (or every entry triaged same-day)"
  - "30 days post-release: /doc-integrity mismatch list frozen at the historical set; validations page 100% PASS on new documents"
  - Posting runs complete with zero orphans/duplicates; daily digest received; run-issues page actioned within a day
  - All historical cleanup items dispositioned (voids done, 251316 fixed, C089676 reviewed)
  - No IT data-fix requests from accounts attributable to generation/posting defects
parent_project: null
related_projects: []
parent_ticket: T-0509
parent_pre_project: PP-011
kind: initiative
key_decisions: []
labels: []
order: 1024
problems:
  - Financial documents (invoices/credit notes) were generated with headers disagreeing with their line items — locally-rendered and Xero-rendered amounts diverged (the adjuster smear), credit notes doubled (itemRef collisions), VAT mis-stated (string codes, no-VAT contracts) — each one becoming a manual DB fix by IT after accounts flagged it
  - The Xero posting pipeline could duplicate transactions (double-click on post-all, no mutex), orphan documents (created in Xero but GUID never written back — 729 healed), and hang for 22h on rate limits; failures were silent until accounts noticed
  - IT time is consumed doing accounts data repairs instead of development; there is no systematic visibility of document/posting health — problems surface via accounts complaints
  - Internal test contracts leak into live accounts and Xero (real receivables for test data)
  - "Rollout of the fixes is high-risk: two stacked branches, data cleanup, config/RBAC steps, and behaviour changes to money documents"
goals:
  - "PRIMARY: reduce accounts' reliance on IT — no more Austin stepping in for manual DB fixes caused by generation/posting defects"
  - Every document header equals what its lines render on Xero, by construction; residuals visible on labeled lines
  - Every document posts to Xero exactly once; failures logged, digested, and self-healing on the next run
  - "Issues self-announce (daily digest, invoice-integrity log, /doc-integrity pages) and are triageable by accounts without IT: every flagged item links to the document, the Xero record, and the manual-refunds page"
  - IT retains fast investigation tooling (doc-integrity deep dive, replay harness, E2E harness) for the rare cases that do need engineering
cost_of_inaction: "Continued weekly IT time on manual accounts data fixes; recurring double/orphaned postings in Xero requiring hand-voiding (264 pairs one incident); customer-facing documents that disagree with Xero by up to thousands of pounds (C089676 pair: £3,412 headers vs ±£99 lines); VAT misstatement risk on adjustments; accounts trust erosion — every close takes longer; and the fixes already built (T-0534 merge-ready, T-0538 proven) rot on branches while live keeps generating the old failure classes."
scope_in:
  - "T-0534 posting pipeline: self-heal, heal-from-log, backstops, deterministic idempotency keys, mutexes, DB token store, daily cron + digest, run-issues/repair pages"
  - "T-0538 generation integrity: diff-engine fixes (composite keys, VAT, nominals), Xero-valid labeled adjustment lines, shadow invariant, writer guards (sign, VAT variant, contractType), investigation tooling (doc-integrity, replay, E2E), Xero deep-link helper"
  - "Release engineering: sequencing, params (xeroOrgShortCode), RBAC route grants, canary, hypercare monitoring"
  - "Historical data cleanup: test-doc voids, item 251316 (+£180), C089676 accounts review, live-since-snapshot check, 6 legacy positive credit items"
  - "Prevention follow-ons as project tickets: T-0542 (test contracts out of Xero), T-0541 (quote-builder pricing review), T-0539 (proformas), T-0540 (yahirenew/chl audit)"
scope_out:
  - Quote-builder session-writer behaviour changes (manual-price survival) — explicitly deferred by Austin; T-0541 review gates any change
  - Sister-project (yahirenew/chl) code changes — T-0540 audits first
  - Header-engine (recalculatePrice) semantics changes — deliberately untouched for risk
  - Xero chart-of-accounts or accounting-policy changes on live
  - Legacy (pre-go-live) document regeneration
workstream_ownership:
  - workstream: Code (branches, fixes, tooling)
    owner: Claude (agent) with Austin review
  - workstream: Release decisions & live operations
    owner: Austin
  - workstream: Data cleanup on live
    owner: Austin
  - workstream: Accounts liaison & verification
    owner: Austin (with accounts team)
  - workstream: Quote-builder review (T-0541)
    owner: Austin + Zsolt
version: 82
success_measures:
  - "Count of accounts-requested IT data fixes per month attributable to generation/posting — target 0 (baseline: recurring, e.g. 34-doc census + 264-pair void incident)"
  - Hours of Austin's time per month on accounts data repairs — target ~0
  - Documents on the /doc-integrity mismatch list — frozen at historical set, no new entries
  - Posting-run failures needing human action — digest/run-issues items actioned same-day, zero orphans/duplicates
workflow_impact:
  current_workflow: "Accounts manually batch-post to Xero via /xero/post, typically a week at a time (batching driven by Xero API limits). This is the workflow that caused the founding incident: a double-click on post-all duplicated 264 transaction pairs. Failures surface only when accounts notice something wrong in Xero, then escalate to IT/Austin for manual DB fixes."
  new_workflow: "A daily 06:00 cron posts automatically (mutex prevents overlap with any manual run; self-heal recovers interruptions; circuit breaker respects API limits). Accounts' job becomes: read the daily digest email (all-clear or issues), and for flagged items follow the links to /xero/run-issues (each row deep-links the Xero document and the contract's manual-refunds page). Manual /xero/post remains available with identical safeguards as a fallback."
  who_affected:
    - Accounts team (stop manual posting; start digest/triage routine)
    - Austin (hypercare oversight for the first weeks, then out of the loop)
    - IT (only escalations that genuinely need engineering)
  migration_path: phased
---

# Accounts Integrity Programme — Xero posting & document generation

## **Accounts & Xero — what happened, what we fixed, and what we need to decide**

*Meeting pack — prepared 10 July 2026. Audience: accounts team & directors. No technical background needed.*

***

## **In one paragraph**

A double-click on the "post to Xero" button in May uncovered a much bigger problem: for the past year, some of our invoices and credit notes have been quietly generated wrong, and our connection to Xero could duplicate or lose documents when anything went wrong mid-send. Fixing the original incident led us through the whole chain — how documents are created, how they get to Xero, and how mistakes were being found (by accounts, late) and fixed (by IT, by hand). We have now rebuilt both parts, tested them against every document from the last year plus a battery of deliberate abuse, and everything is ready to release. This meeting is to agree the release plan, the switch to automatic daily posting (which takes the posting job off accounts entirely), and a handful of clean-up decisions.

***

## **The story**

### **1. How it started**

In May, posting a batch to Xero was accidentally double-clicked. The system had no protection against two posting runs at once, so **264 transactions went to Xero twice**. Austin had to find and void every duplicate by hand.

### **2. What we found while fixing that**

Each fix uncovered the next layer:

* **Invoices Xero had, but our system didn't know it.** When a send was interrupted (a timeout, a server hiccup), Xero could accept the invoice while our record of that never arrived back. Our system, believing the invoice was never sent, tried to send it again — Xero rejected it as a duplicate, and the invoice showed as unsent forever. We found and repaired **729 invoices** in that state.
* **The duplicate-protection we relied on doesn't work as advertised.** Xero's documentation says a resend within 24 hours is safe. Our testing proved it's actually only safe for about half an hour — after that, a "safe" resend creates a real duplicate. Much of the pipeline's original design leaned on that false promise.
* **Silent failure everywhere.** When posting went wrong, nothing told anyone. Problems surfaced weeks later when accounts noticed something odd in Xero, and every one became an IT job to diagnose and repair by hand.

### **3. The deeper problem: documents born wrong**

Once posting was trustworthy, testing exposed that some documents were **wrong before they ever reached Xero**:

* **Hidden rounding fudge.** When the invoice total and its line items disagreed by a penny or two (normal rounding), the system silently buried the difference inside one of the lines — in a way the customer's PDF showed but Xero ignored. Result: our copy and Xero's copy of the *same document* showed different amounts. In the worst case a cancelled contract's credit note said **£604 on the summary but £18 in the lines**.
* **Double-sized credit notes.** When a charge (e.g. for damaged items) was cancelled, a quirk in how the reversal was recorded could produce a credit note for **twice** the right amount.
* **VAT slips.** In specific situations, VAT could be calculated at the wrong rate — including charging 20% VAT on paperwork for the handful of contracts that are VAT-free.
* **A compensation typed as a negative** turned into a *positive* charge of £180 on a real customer's credit note (the one genuinely broken customer document of the past year — see clean-up decisions).

**How widespread was it?** We re-checked **every document generated since the new accounting system went live (June 2025): 4,128 of them**. 4,113 were perfect. The handful that weren't: **1 real customer document** (the £180 one), **3 on internal test contracts** (not real customers), and a small set from the first fortnight of go-live that Austin had already hand-corrected at the time.

### **4. What we built**

Two packages of work:

* **Posting you can trust (ticket 534).** Two runs can't overlap any more (the double-click is now harmless). If a send is interrupted, the system finds the document in Xero on the next run and reconnects it instead of re-sending. Every run ends with a **plain-English email digest** — all-clear, or a list of issues where every item links straight to the document in Xero and the customer's account page. Posting can run automatically every morning.
* **Documents born right (ticket 538).** Rounding differences now appear as their own honestly-labelled line ("Rounding adjustment") so the total, the customer PDF and Xero always agree — and anything bigger than rounding raises an alarm instead of being buried. The double-sized credit note bug, the VAT slips and the negative-compensation trap are all fixed. A new internal page lets us check the health of **every** document — past and future — in seconds instead of an IT investigation.

### **5. How we tested it — and what the tests caught**

Testing was deliberately brutal, and it *did* find problems — which is the point:

* **Every historical document replayed.** We re-ran the new document engine against all 4,128 documents from the last year and compared the results. It changed nothing that was healthy, and correctly explained everything that wasn't.
* **A live-fire rehearsal on a practice copy of Xero.** We ran the real charge → compensation → cancellation → invoice flows end-to-end (24 checks), including a "worst case" designed to force the rounding machinery to work. All green.
* **The testing caught our own mistakes too.** An independent code review found that our first version of the rounding fix could produce a line Xero would refuse. We fixed the construction, and then **proved it against Xero itself**: we posted 34 test documents to the practice company — all 28 correct-format documents were accepted with totals matching ours to the penny, and all 6 deliberately old-format documents were **rejected by Xero with exactly the error we predicted**. The review also caught a case where an early assumption of ours was simply wrong (we'd added duplicate history logging that wasn't needed) — we removed it and said so in the record.
* **Ticket 534 was stress-tested far beyond normal.** We reset the practice copy of Xero and re-ran the whole posting process from scratch **around five times, posting thousands of transactions in total — not one orphaned, not one duplicated**. Along the way it survived: a 22-hour hang scenario (now impossible — it fails fast), sends deliberately interrupted mid-way and recovered on the next run, a full completeness rehearsal (**304 invoices and 109 payments posted with zero missing and zero duplicated**, every allocation reconciled), and — unplanned but valuable — the moment all 34 test documents were rejected at once by the practice company (a missing account setting): it handled the mass failure perfectly — nothing lost, nothing duplicated, every error logged and emailed.

### **6. Other things we found and dealt with along the way**

| What we found                                                                                                                                                                                                                                                                                                            | What we did                                                                                                                                                                                                 |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Charges & compensations (issue resolution) had several traps**: a compensation typed as a negative number flipped into a positive charge; compensations on VAT-free contracts still carried VAT; the manual credit button always assumed 20% VAT; and cancelling a charge could produce a **double-sized credit note** | All fixed. Amounts can't flip sign any more, VAT-free contracts stay VAT-free, manual credits use the contract's real VAT rate, and a cancelled charge now produces exactly one correctly-sized credit line |
| When staff **re-resolve** an issue, the old charge is *deleted* rather than reversed — the books stay correct, but the history of what was charged disappears                                                                                                                                                            | Needs a business decision (queued for this meeting — item 7)                                                                                                                                                |
| Our internal links to Xero documents stopped working when Xero changed its screens                                                                                                                                                                                                                                       | Fixed everywhere, per-company setting so it keeps working after demo resets                                                                                                                                 |
| **Staff test contracts leak into the real books** — every leaked document we found used an @yahire.com email; one test invoice sat in real Xero as a £604 unpaid receivable                                                                                                                                              | Cleanup + prevention decision at this meeting (items 4a and 5)                                                                                                                                              |
| The quote builder can **double-charge bundles** (the bundle *and* its main item both priced — £420 twice on one test contract) and prices bundles inconsistently                                                                                                                                                         | Evidence packaged for the quote-builder review session with Zsolt (item 6)                                                                                                                                  |
| A quote-stage discount bug computes VAT at **1% instead of 20%** when sizing discounts                                                                                                                                                                                                                                   | Added to the Zsolt review (item 6)                                                                                                                                                                          |
| The PDF "summary" box on invoices shows **today's** contract totals, not the totals as they were when the document was issued — confusing when investigating history                                                                                                                                                     | Ticketed as a follow-up improvement                                                                                                                                                                         |
| A "print PDF" function for itemised invoices had been broken since it was built (nobody had ever used it)                                                                                                                                                                                                                | Fixed — and PDF buttons added to the accounts pages so any document is one click away                                                                                                                       |

### **7. What this changes for accounts**

* **Posting stops being a job.** The system posts every morning; accounts read a short daily email instead. Anything needing a human links directly to the right place. Manual posting stays available as a fallback — and even the double-click is now harmless.
* **Problems announce themselves** the day they happen, instead of being discovered in Xero weeks later.
* **IT hand-fixes should drop to zero** for this class of problem — that's the measure we'll track.

### **8. What's left**

* The release itself (staged, with a safety week — see decisions).
* A short clean-up list of historical items (see decisions).
* Follow-on tickets: keeping test contracts out of the real Xero; a review of quote-builder pricing behaviour with Zsolt; pro-forma invoices; the sister websites.

***

## **Meeting agenda — decisions needed**

*Suggested length: 45–50 minutes. Items 1–4 are the substance; 5–8 are quick.*

**1. Approve the release plan** *(10 min — decision: yes / adjust)* Staged rollout: release the posting system first, then the document fixes. One supervised trial run on live, then a **safety week** where the automatic posting runs every morning *and* accounts keep doing their normal routine — they should find nothing left to post, which is the proof. Then formal handover. **Recommendation:** approve; target the trial run within a week.

**2. Daily posting — timing and ownership** *(10 min — two decisions)* a) The automatic run posts documents dated ~7 days earlier (matching the old weekly rhythm, but now consistent). **Does a 7-day lag suit accounts, or should it be shorter?** b) **Who in accounts owns the daily digest email** — reads it, actions the linked items, and escalates anything unclear (suggested rule: not understood within a day → Austin)?

**3. Customer-visible change: the "Rounding adjustment" line** *(5 min — decision: accept wording / suggest better)* From release day, the occasional invoice/credit note will show a small labelled line (usually ±1–2p) where rounding used to be hidden. It's honest and the totals now always match Xero — but customers can see it. **Approve the wording, or propose alternative.**

**4. Historical clean-up approvals** *(10 min — four quick decisions)* a) **Void the test documents** that leaked into the real Xero (one £604 invoice from a test contract, sitting as an unpaid receivable) plus their local copies. b) **The £180 compensation** on a real customer (ARC): correct the record and reissue the credit note properly. c) **The £3,412 pair** on contract C089676: this was an internal bundle-testing contract — accounts to confirm nothing real hangs off it, then void. d) **Five mispriced credit items from 2024** (finished contracts, pre-new-system): fix or leave as historical? **Recommendation: leave, documented.**

**5. Keeping test contracts out of the books** *(5 min — pick a direction)* All the leaked documents came from staff test contracts using @yahire.com emails. Options: mark test contracts explicitly; automatically exclude internal-email customers from posting; or policy-only (testing happens on the test server now that it exists). **Recommendation: automatic exclusion + policy.** (Full design ticket exists.)

**6. Quote-builder pricing review** *(3 min — schedule it)* Separate, deliberately-untouched area (Zsolt's), with a review ticket prepared: how manual price fixes behave when dates/quantities change, bundle pricing, and the 1%-VAT discount bug. **Decision: date for a session with Zsolt + Austin.** Interim awareness for staff: a manually-set price is still wiped by date/quantity changes — re-apply it.

**7. Re-resolving issues: keep the charge history?** *(2 min — decision with accounts/Fran)* Today, re-resolving an issue *deletes* the previous charge and creates a new one — the numbers stay right, but the record of what was originally charged disappears. The cancellation flow already does this properly (it keeps the charge and adds a reversal). **Decision: should re-resolution work the same way? Recommendation: yes.** (Ticket ready.)

**8. Steady state** *(2 min — note)* Success measure for the programme: **accounts requests to IT for data fixes → zero**, and Austin's time on accounts repairs → zero. Milestone review in \~30 days; anything still generating hand-fixes gets its own ticket.

***

*Reference for anyone who wants depth: project "Accounts Integrity Programme" in the PM tool (P-0020) — full history, decisions and evidence; technical deep-dive attached to ticket T-0538.*
