---
id: M-003
slug: accounts-integrity-programme-release-approval-posting-handov
title: Accounts Integrity Programme — release approval & posting handover
state: scheduled
created: 2026-07-10T22:41:30Z
updated: 2026-07-16T14:02:28Z
scheduled_at: 2026-07-16T14:30:00Z
duration_minutes: 50
location: TBD (Austin to schedule with accounts + director; Fran needed for item 7)
project: accounts-integrity
pre_project: null
milestone: MS-002
organizer:
  kind: human
  name: Austin
stakeholders:
  - name: Effie
    channel: email
    contact: effie@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-16T13:07:00Z
    role: Accounts SME
  - name: Jhuztine
    channel: email
    contact: jhuztine@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-16T13:07:39Z
    role: Accounts SME
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-07-16T13:07:57Z
    role: DEV
agenda:
  - topic: "Approve the staged release plan (posting first → trial run → safety week → handover) AND decide website sequencing: the customer sites generate invoices/credits on online payment with old logic — hold Release 2 for them, or fast-follow monitored via the health page (rec: fast-follow)"
    ticket: T-0534
    duration_min: 10
  - topic: "Daily posting: confirm the 7-day posting window suits accounts (or shorten); name the digest owner in accounts + escalation rule"
    ticket: T-0543
    duration_min: 10
  - topic: "Customer-visible 'Rounding adjustment' line: approve wording or propose alternative"
    ticket: T-0538
    duration_min: 5
  - topic: "Historical clean-up approvals: void leaked test docs (incl. £604 Xero receivable); correct the £180 ARC compensation + reissue; accounts confirm & void the £3,412 C089676 pair; leave-or-fix the five 2024 credit items (rec: leave, documented)"
    ticket: T-0538
    duration_min: 10
  - topic: "Keeping test contracts out of the books: pick direction (rec: auto-exclude internal emails + test-server policy)"
    ticket: T-0542
    duration_min: 5
  - topic: "Schedule the quote-builder pricing session with Zsolt: manual-price wipes on date/qty changes, bundle double-charging, the 1%-VAT discount bug. Interim staff awareness: manual prices still wiped — re-apply"
    ticket: T-0541
    duration_min: 3
  - topic: "Re-resolving issues: keep the charge history? Today re-resolution DELETES the old charge (numbers right, history gone); cancellation already reverses properly. Rec: make re-resolution match — decision with accounts/Fran"
    ticket: T-0545
    duration_min: 2
  - topic: "Steady state: success measure = accounts→IT data-fix requests to zero; 30-day milestone review"
    duration_min: 2
outcomes:
  - description: "DECIDED — Staged release confirmed: logistics auto-route-planning goes Mon 20 Jul; the accounts prevention fixes get tested through next week and release Mon 27 Jul if clean. Austin confirms go/no-go with accounts by end of next week. Interim posting errors: Austin data-fixes before go-live."
    recorded_at: 2026-07-16T14:01:12Z
    owner:
      kind: human
      name: Austin
    due: 2026-07-24
  - description: "DECIDED — Daily posting confirmed with accounts: runs every morning 06:15 posting day-8 and day-9 (window suits accounts); one-at-a-time posting with attempt+response logging (max batch 50); the error digest email goes to accounts with a per-contract detail link."
    recorded_at: 2026-07-16T14:01:17Z
  - description: 'AGREED RULE — Until the prevention release is verified: accounts do NOT self-fix posting errors and do NOT press the "fix data" button if it appears — report to Austin first; he watches the digest too.'
    recorded_at: 2026-07-16T14:01:31Z
    owner:
      kind: human
      name: Effie & Jhuztine
  - description: DECIDED — The invoice adjustment line will be labelled "VAT adjustment" (not "Rounding adjustment") since VAT percentage rounding is the only genuine cause; labelling it VAT heads off customer queries. Optional short explainer on the invoice face.
    recorded_at: 2026-07-16T14:01:35Z
    owner:
      kind: human
      name: Austin
  - description: "DECIDED — Test contracts stay out of the books: add a test-contract checkbox; test contracts are excluded from Xero posting AND from Financial Overview totals (so comparisons still match); Austin's and Zsolt's test accounts never post. (The £604 case was identified as a Zsolt test contract.)"
    recorded_at: 2026-07-16T14:01:38Z
    owner:
      kind: human
      name: Austin
  - description: "DECIDED — Charge history is kept: re-resolving an issue will REVERSE the original charge (already fixed, ships with the prevention release) instead of deleting it; customer service's delete button for charge/compo items in the quote builder is being removed (going into the quote builder also risks silently changing delivery pricing)."
    recorded_at: 2026-07-16T14:01:40Z
    owner:
      kind: human
      name: Austin
  - description: "DECIDED — Archived contracts can't hide money any more: when customer service adds a charge/compo to an archived contract, it auto-unarchives so it returns to accounts' list (the \"invoice in the shadows\" case Jhuztine found)."
    recorded_at: 2026-07-16T14:01:56Z
    owner:
      kind: human
      name: Austin
  - description: ACTION — Austin data-fixes the 15 leftover mismatched contracts on live (the residue of 4,128 tested; the remaining Xero-vs-system differences trace to these) before/alongside the prevention release.
    recorded_at: 2026-07-16T14:01:59Z
    owner:
      kind: human
      name: Austin
    due: 2026-07-27
  - description: "ACTION — Effie & Jhuztine reorganise the accounts improvement-notes doc into categories/pages; then a follow-up working meeting on the test system to walk through and demo the items. Also agreed direction: raise issues as tickets (flag urgent when urgent) so there's a log, instead of direct asks only."
    recorded_at: 2026-07-16T14:02:02Z
    owner:
      kind: human
      name: Effie & Jhuztine
  - description: "NOT RESOLVED — carried forward: website sequencing for Release 2 (customer sites still generate invoices/credits with old logic on online payment — hold vs fast-follow monitored via the health page) was never discussed. Needs a decision before/at the go/no-go."
    recorded_at: 2026-07-16T14:02:18Z
    owner:
      kind: human
      name: Austin
    due: 2026-07-24
  - description: "NOT RESOLVED — carried forward: the historical clean-up approvals were not walked through item by item (£180 ARC compensation correction + reissue; the £3,412 C089676 pair void; the five 2024 credit items leave-or-fix). Needs explicit accounts sign-off — only the £604 case was settled (Zsolt test contract)."
    recorded_at: 2026-07-16T14:02:22Z
    owner:
      kind: human
      name: Effie & Jhuztine
  - description: 'NOT RESOLVED — carried forward: the quote-builder pricing session with Zsolt is not scheduled (manual-price wipes on date/qty changes, bundle double-charging, the 1%-VAT discount bug — Austin: "I need to have a chat with Zsolt"). Interim staff awareness stands: manual prices still get wiped — re-apply them.'
    recorded_at: 2026-07-16T14:02:26Z
    owner:
      kind: human
      name: Austin
  - description: "NOT RESOLVED — carried forward: no NAMED digest owner in accounts (currently \"both\"), and the 30-day milestone review isn't scheduled. Success measure was informally agreed as \"accounts need less of Austin's time / data-fix requests trend to zero\" — worth pinning at the review."
    recorded_at: 2026-07-16T14:02:28Z
attachments:
  - key: meetings/M-003/1784209290200-meeting-recording-2026-07-16-1341.m4a
    filename: meeting-recording-2026-07-16-1341.m4a
    content_type: audio/mp4
    size: 7600310
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-16T13:41:31Z
  - key: meetings/M-003/1784210309782-meeting-recording-2026-07-16-1341-transcript.txt
    filename: meeting-recording-2026-07-16-1341-transcript.txt
    content_type: text/plain
    size: 25189
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-16T13:58:29Z
calendar:
  graph_event_id: null
  ics_url: null
kind: progress
version: 24
---

\
\
Pre-read: docs/accounts-integrity-meeting-pack.md (final, commit 41d308c2) — plain-English narrative incl. the along-the-way findings table and the website dependency on decision 1. Depth: project P-0020 charter, ADR-001..006, TS-001/TS-002, T-0538 deep dive. Suggested live demo: Demo Company INV #78416 next to the old smeared CR 75664 PDF. Ticket map for the agenda: T-0534/T-0543 (items 1-2), T-0538 (3-4), T-0542 (5), T-0541 (6), T-0545 (7); T-0540 = the website dependency; T-0548 = PDF storage housekeeping (no decision needed).

<br />

# **Accounts & Xero — what happened, what we fixed, and what we need to decide**

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
* **The customer websites**: they create invoices/credits when a customer pays online, using their own copy of the old logic — bringing them up to date is part of the document-fixes rollout (sequencing decision at item 1).
* A short clean-up list of historical items (see decisions).
* Follow-on tickets: keeping test contracts out of the real Xero; a review of quote-builder pricing behaviour with Zsolt; pro-forma invoices; the issue re-resolution history question; housekeeping (e.g. generated PDFs no longer piling up in storage).

***

## **Meeting agenda — decisions needed**

*Suggested length: 45–50 minutes. Items 1–4 are the substance; 5–8 are quick.*

**1. Approve the release plan** *(10 min — two decisions)* Staged rollout: release the posting system first, then the document fixes. One supervised trial run on live, then a **safety week** where the automatic posting runs every morning *and* accounts keep doing their normal routine — they should find nothing left to post, which is the proof. Then formal handover. **Dependency to decide:** the customer websites also create invoices/credits when a customer pays online, using their own copy of the old logic — they need the same fixes. Either (a) hold the document-fixes release until the websites are updated too, or (b) release the internal system first and update the websites as an immediate fast-follow (the new health page will show any website-created problem documents in the meantime, so it's monitorable). **Recommendation:** approve the staged plan; option (b) for the websites; target the trial run within a week.

**2. Daily posting — timing and ownership** *(10 min — two decisions)* a) The automatic run posts documents dated ~7 days earlier (matching the old weekly rhythm, but now consistent). **Does a 7-day lag suit accounts, or should it be shorter?** b) **Who in accounts owns the daily digest email** — reads it, actions the linked items, and escalates anything unclear (suggested rule: not understood within a day → Austin)?

**3. Customer-visible change: the "Rounding adjustment" line** *(5 min — decision: accept wording / suggest better)* From release day, the occasional invoice/credit note will show a small labelled line (usually ±1–2p) where rounding used to be hidden. It's honest and the totals now always match Xero — but customers can see it. **Approve the wording, or propose alternative.**

**4. Historical clean-up approvals** *(10 min — four quick decisions)* a) **Void the test documents** that leaked into the real Xero (one £604 invoice from a test contract, sitting as an unpaid receivable) plus their local copies. b) **The £180 compensation** on a real customer (ARC): correct the record and reissue the credit note properly. c) **The £3,412 pair** on contract C089676: this was an internal bundle-testing contract — accounts to confirm nothing real hangs off it, then void. d) **Five mispriced credit items from 2024** (finished contracts, pre-new-system): fix or leave as historical? **Recommendation: leave, documented.**

**5. Keeping test contracts out of the books** *(5 min — pick a direction)* All the leaked documents came from staff test contracts using @yahire.com emails. Options: mark test contracts explicitly; automatically exclude internal-email customers from posting; or policy-only (testing happens on the test server now that it exists). **Recommendation: automatic exclusion + policy.** (Full design ticket exists.)

**6. Quote-builder pricing review** *(3 min — schedule it)* Separate, deliberately-untouched area (Zsolt's), with a review ticket prepared: how manual price fixes behave when dates/quantities change, bundle pricing, and the 1%-VAT discount bug. **Decision: date for a session with Zsolt + Austin.** Interim awareness for staff: a manually-set price is still wiped by date/quantity changes — re-apply it.

**7. Re-resolving issues: keep the charge history?** *(2 min — decision with accounts/Fran)* Today, re-resolving an issue *deletes* the previous charge and creates a new one — the numbers stay right, but the record of what was originally charged disappears. The cancellation flow already does this properly (it keeps the charge and adds a reversal). **Decision: should re-resolution work the same way? Recommendation: yes.** (Ticket ready.)

**8. Steady state** *(2 min — note)* Success measure for the programme: **accounts requests to IT for data fixes → zero**, and Austin's time on accounts repairs → zero. Milestone review in \~30 days; anything still generating hand-fixes gets its own ticket.

***

*Reference for anyone who wants depth: project "Accounts Integrity Programme" in the PM tool (P-0020) — full history, decisions and evidence; technical deep-dive attached to ticket T-0538.*

## Transcript — meeting-recording-2026-07-16-1341.m4a

_Transcribed 2026-07-16T13:58:29Z on-device._

Hopefully this time it should work.
 It was a file size limit before.
 Oh, that's too long.
 Yeah, so did, first of all, can you see the link?
 Does it work?
 No.
 No?
 No.
 The link didn't work, but then I saw that later on you had sent the details.
 I just thought, okay, cool, I've got the detail here.
 I sent you a new link a minute ago.
 Just test that one for me.
 I just want to see what happens when you click it.
 A minute ago?
 It's in the accounts inbox.
 Oh, I see.
 Can you actually see the meeting notes on there?
 Yeah, that's fine.
 You can sign in with that.
 Where to go?
 No, you don't have meetings on there.
 Yeah, see, that's the same thing that happened to me yesterday.
 It says nothing on your plate.
 Nothing in there.
 Take that link again.
 This will see what you've signed in.
 It's not that right.
 All right, that's fine.
 I've got the notes on here.
 And yeah, you've got them in your email.
 so yeah so basically this was all done on the back of um the double transaction post uh and it
 was caused by a double click on the button simple thing to do but it caused a massive issue um and
 then when i was going through uh the fix for that but there was loads of other possibilities where
 if something went wrong during posting we end up with stuff that we have that is on zero but we
 don't know about it and it will get posted again leading to double transactions okay um and then
 there was i just made it a lot more safe um i've slowly so basically before it was doing like
 batches of a thousand invoices right so if something went wrong yeah we could have up to
 a thousand things to correct yeah okay so it's going to work in smaller batches so if there is
 an issue with that small batch yeah be able to identify that correct yeah so now now it's doing
 it one at a time so it's log in the attempt to zero and then it's log in the response and the
 uh thing so we'll be able to tell if we attempted something but it didn't actually get a response
 and we'll know immediately oh that one went wrong and it will come to you in the email um that that
 went wrong um so everything at most would probably have i think the largest batch i do is 50 um but
 most things are done individually uh but everything's done a lot more safely now um and then
 the reason i decided to do daily posting was because uh if you're doing it in a week batch
 it'll be really slow to do the one at a time check and post uh type thing okay so so with this
 automatic posting it's gonna occur every morning yes it goes every morning 6 15 and does minus
 eight days and minus nine days um and how long does it take to complete is it it takes it says
 it said there's a summary in the email i think it's a few hundred seconds or something like that
 okay cool yeah so it's not not much maybe five minutes yeah 120 seconds yeah about about five
 minutes but that hasn't actually occurred yet the daily no that last one i've done there that's
 exactly what will happen tomorrow now and it will do it will do eighth and ninth tomorrow uh when it
 goes so when that happens when it does automatically post is there anything we need to be checking so
 you get that that summary email gives you a list of things that went wrong because nothing's gone
 wrong yet it's uh there's nothing in there and then it also gives you a link and the link has
 a lot more detail than also of what went wrong so it will give you the contract number what it was
 trying to post and why it didn't why it wasn't able to post um so then it'll be easy for you to
 clear up on the day and then if you do clear it up on the day then tomorrow's post will pick it up
 um if you fix the data that needs fixing because he's going to post the repost the previous date
 yeah okay there may be things that show up here that uh offer you to fix the data um if you do
 see that don't run it straight away let me check that um because it's not that's not been fully
 fully tested the fix i'm not sure if i still left the button there or not so if you do see that pop
 up don't press it just let me know fix the data yeah any any issues that you see initially on the
 list just let me know about them i'll be i'll be looking at the email as well yeah but don't
 attempt to fix anything until i've had a look at it um so the other part of the the project is that's
 not yet released is what we really are talking about now ben mentioned you had a list of some
 common things that go wrong um the thing is anything that needs to be responded to with
 urgency i feel like we send to you on a whether it's a daily or weekly basis yeah the stuff we've
 been making note of are things that little things that we've noticed on the system that we think
 could be improved it's more system developments than stuff that's affecting current contracts
 because okay well i'll go through uh what some of the things we did fix um that were a problem
 so you who does the posting on this is justine or you yeah so you'll be aware of the type of
 errors that happen when you go to post to zero right so you have first of all no items on the
 invoice that normally happens when it's a one penny vat invoice rounding issues um so that one
 has been fixed and tested uh it will create the line item correctly now um and also the second
 thing we had an issue with was document totals don't match and that one's normally when uh
 customer services get involved in applied charges and the charges it has a little bit of it had
 around an issue on it and that could lead to mismatches in totals that wouldn't go to zero
 that has also been fixed um what was the other issue yeah it mainly came from
 compensations i think uh that particular problem what's the situation with um compensations being
 entered as a negative yeah so somebody done a compo examples of that it was just one contract
 and it had a really weird set of line items on it um i think someone just put it in the wrong way
 it may even be me um potentially in the database doing it manually um because customer service
 they can't put in and put in a negative value in that box can they i think i remember working on
 that contract it was something that paula was dealing with when it was a customer that was
 i think we were really late or even a day late or something like that and she put some compo
 i wanted me to put some compo on urgently and i think i've done it in a database manually
 okay so that was probably my my issue that caused that to double check whether customer service can
 you can do that because um i think that was already fixed and checked in the i've got i've
 got a load of work done that's fixed all of these things i just haven't released it yet because i
 didn't want to do two big uh things in the same week you know something went wrong it could go
 really wrong then yeah um so the other thing uh i've looked at was our test contracts getting
 posted to zero there are quite a lot on there um and some of them are showing his debts and stuff
 like that yeah because this is the thing like with the test contracts they only get picked up
 at end of hire and then at that point in time it's been posted already we do yeah all you can
 really do is credit it and then the credit will go into yeah but we paid real vat on i've i had
 an order there that's like four thousand pounds or something like that so really we prefer if they
 didn't go in at all yeah so that's another thing that's been fixed uh i don't know if i've done
 the fix for that yet um but it is it's either been fixed or it will be fixed um yeah you mentioned
 something about there's going to be a separate testing environment so people what what won't be
 able to create test contracts on your system is that well i i do have a test test environment now
 um shult may may uh still create tests but what i'm going to do is prevent anything from uh
 mind and shoulders test accounts um to get posted so if it's got a shot at your hire or sitting at
 your hire what if what if another user has decided to create a test account just for training or
 something and that ends up going through because we've also seen examples of that like fran might
 create something for some kind of customer service training and then you know what i think it's as
 simple as putting a test contract check box on the test contract and then don't post them yeah
 yeah yeah it'd be as easy as that um but also though there's the if we do have that then you
 might get balance mismatches you know like where they're on our system with with credit notes and
 stuff like that um not in zero but then could we just could we get them excluded from any kind of
 your system i see what you mean like exclude it from the totals that you you use to compare
 yeah financial overview that it should be excluded from really everything what do you
 use to compare totals normally financial overview so if test contracts are excluded from there and
 don't get posted to zero then they'll be matched yeah yeah that's fine yeah we can do that um can
 i have a look look at this because uh so we can go through uh so yeah the timing of daily posting
 is that okay the minus eight and minus nine days started off at seven but um it's ended up being
 eight and nine is that enough is that okay if i like to be working with yeah okay all right that's
 fine uh that question here is the line that i'm using for the uh you know the bat adjustments
 rounding adjustment as a line line item is okay wait where is oh we're going on here yet um
 Well, when we do have to do a rounding adjustment line, is that okay for a line?
 Oh, what, to appear?
 Yeah, just to appear on the invoice, yeah.
 What is causing the rounding?
 It's going to be VAT, isn't it?
 Why? Because I haven't seen anything recently, but that's because I don't really look at invoices like that.
 It would be the VAT.
 It's when you're doing the percentage on a...
 Because if it would only be because of the VAT,
 then maybe the line just says VAT adjustment
 so that it's clear to the customer exactly why that is there.
 There may be rounding adjustments needed.
 There's always a chance you'll need a rounding adjustment on an invoice
 because of, I can't even think why, what we call it.
 The reason why it occurs on zero, I can say,
 when we're entering invoices would be
 if you've entered something as VAT-inclusive.
 Yeah.
 That's the only time, again, it's to do with the VAT calculation.
 Sometimes it ends up a penny off,
 but that's really the only scenario that would cause genuine rounding.
 Otherwise, if it's not VAT, it shouldn't be occurring.
 It would be due to VAT.
 It would have to be because it's a percentage on a number.
 And then when it's a very small number,
 that's why it is normally like one or two P.
 Yeah.
 Because if you're trying to do VAT on a very small number,
 it's uh 20 percent of you know almost 20 percent of one penny for example yeah exactly um so that's
 why you get them um so because if it if if if i can if it is just from that i can say that rounding
 adjustment that adjustment yeah because i think if we label as rounding it always just opens the
 door for customers to ask questions even if it is a penny oh what's that about yeah yeah why is that
 because why am I seeing this on my invoice?
 Omnibus on the face and invoices, if it does appear,
 I can put an explainer on it to say, you know,
 occasionally when we're doing that, you know,
 a rounding is a penny off.
 It's a known issue of accounting,
 so people who know about it won't really have a problem, I'm sure.
 So here's my question.
 How did we have a scenario where a rounding caused, like,
 a difference of 600 and something that was that was a jolt test coltrane i believe this is a test
 where was it so it said um in the worst case a cancelled contract credit note said 604 pounds
 on the summer race but 18 pounds in the lines yeah i think that one was a jolt test contract
 if i remember rightly um okay so yeah but it still could happen no i mean this when we enjoy
 me and job sometimes make things straight in the database and then if you if that's just us
 calculating it ourselves and if we've calculated it wrong it can lead to a massive difference
 so what so the system would the system be able to pick those up or do we just have to pick those up
 And prevent them from going in the first place to zero.
 Okay.
 Yeah.
 And when it picks them up, is it flagging them for you?
 It would get flagged in the post in there before you go to post it.
 It would say there's an error with this contract.
 That would be a document that would take much error on zero, genuinely one.
 Is there any way it could pick them up before?
 Because by the time we post, we've done, we've actioned
 what we need to action for that customer already.
 And it's like...
 The thing now is these things shouldn't happen in the first place.
 That's the whole point of this fix.
 So we shouldn't get to these situations anymore in the first place.
 And I'm quite confident that most of the things that happen have been solved.
 So I really think you should need a lot less than my time
 to do the data fixes that I do quite often.
 that's the main point of it really because it's been a few times where when something goes wrong
 it goes really wrong and then there's just loads of stuff to fix so the first point was
 yeah when it does go wrong minimize what goes wrong and make it obvious and then the second
 part was prevent the things that are causing me to do manual data fixes on you know daily weekly
 basis yeah um so really and truly i just need to test because because the changes are made involve
 the issue report resolution is price calculation and things like that I just really need to test
 uh properly so a bit more detail before I release that so it may be on Monday this this coming up
 I'm releasing the logistics uh auto route planning so I'll need to be a bit available for them next
 week okay so I'll probably release this one the Monday after so I'll have time to test what
 logistics are you know doing their uh auto route planning uh rollout and then um the following
 monday then i could probably release it once i'm happy uh and what if anything occurs in that
 window like now and then so we could still get we could still get something in between that i need
 data fixes um and i'll fix those before going live because obviously we still we still may
 have existing problems that are not yet posted to zero um from the period up until we go live with
 prevention fix because you said you said you checked everything from what is it everything
 from between june everything since the new style invoicing that that on the posting i posted
 thousands of times i've done the whole year again six times so i kept resetting the demo company
 going through the whole process everything was was fine um and then again i'll be testing
 uh things that did cause a problem like uh no line titles don't match or no line items on the
 invoice yeah and then i went through them all again like basically rerun the system to before
 that invoice got generated yeah then done it again and it generated properly so if anything
 that was a problem initially was no longer a problem when i retested it okay so and i'm going
 to do that sort of testing again so the contracts that you did find issue with do they need to be
 corrected or they've been correct there is some that do need correcting still um i think i've done
 a few of them on the live system when i found them any of those gone into zero or were they
 these all things that were prevented from going in yeah so these these would be things that get
 prevented from going into zero anyway because because of zero rejected them yeah but because
 zero rejected them then i would have to do a manual data fix okay to fix the course so then
 if these are if these are invoices what does that mean because you've checked you've checked the
 invoice totals in zero versus your system and haven't we got to the bottom of all the differences
 we still have me and some you've got some non-matches that's what i need to and that
 That will be sorted once I do those unmatches.
 Some of the stuff you found probably relates to those remaining differences.
 Yeah, that would be pre-existing issues of this nature after that caused it.
 The founding issue and then Paul said credit notes.
 Yeah, and the credit note probably got rejected for no line items
 or mismatched document total.
 Okay.
 Yeah, so these are the type of things that I'm trying to prevent.
 And also what I'm going to try and do is give you guys the ability
 to fix things yourself
 don't need fixing
 you know like
 so you don't
 you know say for example
 Giselle all the time
 she'll miss input
 a deposit
 or payment amount
 and then she needs me
 to delete it
 and change it
 but
 obviously if it's striped
 we can't change it
 but for the back stuff
 I'll let you guys
 just
 yeah
 correct it
 yeah yeah
 so it's just trying
 to make you guys
 less reliant
 on my time
 and
 and not having to wait for me to do stuff you know while you need to do it
 what's the point about um customer service how customer service had very long charges on
 and like the duplicate charges being added or there was there was a point about um oh there was
 yeah when they reverse in some cases they're doing charges but in other cases they're reversing
 yeah i realized if you re-resolve an issue it doesn't do the reversal it deletes deletes the
 initial one so then the invoice line looks a bit weird because it doesn't show the reversal
 yeah yeah so that was uh that was fixed but it's not released yet um yeah so so will it be that
 customer service can no longer delete delete charges uh they can still go into the quote
 builder and do it but i don't think that's what they were doing yeah it actually looks like um
 it was caused by re-resolving the issue which is doing an automatic delete of the item from the
 contract instead of reversing it them having the option to go into the quote builder and delete
 can that be can that be removed uh yeah because when they go back into the quote builder it
 changes delivery pricing sometimes even though they might not do it it's just that not knowing
 not knowing and that option there is kind of a bit risky i think what i can do is prevent them
 removing delete in charge and uh compo items from the contract on the quote builder um
 yeah and that's that's just i'll just take off the button yeah
 all right so i think we've covered most of the things i really wanted to let you know about
 yeah there's a lot there this is more for your uh sanity so you know what i've tested and how
 and just to make sure you know to see this had a full uh proper look into it um
 yeah so the little things with shulk sometimes it's quite horrible price stuff differently to
 what it should be yeah i need to have a chat with shulk about those things i didn't want
 to touch his clothes there and test contracts out of the book so it's like whatever that
 Yeah, so that's what we just spoke about as well, the issues getting deleted, point A.
 Yeah, so that's just basically how we'll know. If you need less than my time, then it probably is.
 So yeah, I will be doing the test of next week then on the other changes, the prevention changes.
 And then I will let you know at the end of the week
 whether we're going to go live on the Monday or not.
 Yeah.
 Oh, this is what I wanted to ask.
 Can you see how you said...
 Yeah, Jeff, I heard what this thing is.
 You said that you tested 4,128 contracts
 and 4,113 were perfect.
 So does that mean there was literally only a few that had issues?
 These were the contracts that did have problems.
 Wait, how many?
 so it would have been but these these were the ones that were not yet fixed by me because
 obviously we were we were fixing as we went along a lot of these things right yeah so the ones that
 were left with us the ones that were left over still for me to action basically those ones still
 need a data fix uh a manual like 15 contracts yeah yeah okay yeah yeah there's things that
 were not yet fixed uh since last june because obviously you guys have been coming to me i've
 been fixing those as we go but these are the things that were left over that we still had
 a problem on them okay so that makes me feel better because in my mind i'm thinking as we go
 like we find things and we raise them with you so surely all that's left should be stuff that
 maybe got missed out or we overlooked and didn't yeah yeah that's exactly what those uh whatever
 how many is our 15 15 yeah yeah yeah so i will be doing the data fix on those as well on the live
 uh system okay cool there's this one thing other thing we wanted to raise with you justine found
 the archive situation what was that once we like archive a contract and then customer service will
 go in and change or add charge or add compo it won't go back to our end of our list and then
 you end up like so if they if they add a it's like a dead cat situation isn't it so so for example
 cousins paid by backs so they're on the non-standard list um say we haven't received the details and
 it's been months you archive them because it's been too long but we know that we need their
 details then somehow i guess customer service have added on the charge and it's stayed in the
 archive because that's where it is and we don't know the charge has been raised so the invoice
 is a shaky it's just a big oh i see yeah so it's an invoice so what i think what should happen is
 as soon as is that enough it's all it's fine it's fine this is just what things are like
 it counts sadly yeah i think then so as soon as uh they raise a charge i'll just double check
 is there is is the contract archived on the higher list and if it is i'll do it um
 yeah yeah and then phrase it as a dead cat it should be a dead cat anyway but you can't see
 it because it's archived yeah basically yeah yeah it's not a dead cat because it was not
 completed you oh okay yeah yeah yeah that's the thing it's not it's not completed because we've
 not actually processed the refund i'll just do a check when i only charge a compo uh is it archived
 and if it is unarchivic um and that should bring it back to your attention yeah so that's that
 part i mean now you guys wanted to tell me some things that you need doing system wise and whatnot
 the thing is we so we over time have been making notes on things that we find and like system
 developments that we think would be helpful it's literally just a shared document where we bullet
 pointed stuff but the format's very messy this is what we was telling you and ben and josh like we
 probably need to get it more organized put into categories so that they're things that you can
 kind of tackle at one time yeah makes sense to do it like that right now they're kind of all over
 the place relating to different pages different functions yeah it would be a the conversation
 would go a bit better if we uh if it was all grouped together because then we can just look
 at the pages and actually demo it um yeah all right so when you get that together we'll plan
 another meeting to go through that that needs to be a whole and then we can actually we'll test it
 on the the test database and test system we can go through some of the stuff and you can show me
 happening or whatever yeah um all right also was having this conversation with josh but um
 he was saying oh when like the system issues come up are we raising tickets and i was basically
 saying that because a lot of the time like we're under time constraints because maybe we've got a
 deposit that needs to go back to a customer so we kind of leave it fixed within the day or a few
 days yeah tend to not raise tickets because they come and speak to me direct normally yeah but if
 we were to say okay we're gonna raise tickets when any issues come up would they be able to be dealt
 with instantly or would that be more of a long-term thing like if it's something that if you raise a
 ticket and raise it as urgent and tell me it's urgent in the ticket and i'll treat it as that
 uh same as sending me an email basically okay because yeah we just want to get into a better
 process of raising issues even for Giselle because it's like I don't know I don't know
 everything that she's raising for you and because we don't have an actual log of all of the issues
 our department has raised it's always mostly just uh correcting mistaken entry yeah so hopefully
 with the changes that are coming there should be less of that yeah but then anything else maybe
 will be a bigger issue that needs a ticket hopefully it's not okay yeah so all right then
 so yeah i'll catch up with you then at the end of next week after i've done the logistics release
 um but what i post in the meantime should should continue um and like i said if you do see any
 errors, don't try and deal with
 them yourself at first because
 I just want to double check all of their features
 so I guess I should.
 Alright, cool. Hopefully
 it's recorded. Thank you.
 Hopefully. Maybe we can just
