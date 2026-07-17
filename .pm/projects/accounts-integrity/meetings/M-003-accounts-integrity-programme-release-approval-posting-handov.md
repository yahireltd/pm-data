---
id: M-003
slug: accounts-integrity-programme-release-approval-posting-handov
title: Accounts Integrity Programme — release approval & posting handover
state: held
created: 2026-07-10T22:41:30Z
updated: 2026-07-17T19:56:32Z
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
  - description: DECIDED — The invoice adjustment line will be labelled "VAT adjustment" (not "Rounding adjustment") since VAT percentage rounding is the only genuine cause; labelling it VAT heads off customer queries. Effie proposed the "VAT adjustment" wording (speaker-attributed transcript); Austin to implement.
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
  - key: meetings/M-003/1784318189520-meeting-recording-2026-07-16-1341-transcript.txt
    filename: meeting-recording-2026-07-16-1341-transcript.txt
    content_type: text/plain
    size: 22877
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-17T19:56:29Z
calendar:
  graph_event_id: null
  ics_url: null
kind: progress
version: 27
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

_Transcribed 2026-07-17T19:56:29Z on-device._

Austin: this time it should work it was a file size limit before um yeah so did first of all can you see the link uh does it work um no No?

Effie: No. The link didn't work, but then I saw that later on you had sent the details. I just thought, okay, cool, I've got the detail here.

Austin: I sent you a new link a minute ago. Just test that one for me. I just want to see what happens when you click it.

Effie: A minute ago?

Austin: So your account's inbox.

Effie: Oh.

Jhuztine: Steve.

Austin: Can you actually see the meeting notes on there? Yeah, that's fine. You can sign in with that.

Jhuztine: Where to go?

Austin: No, you don't have meetings on there.

Effie: Yeah, see, that's the same thing that happened to me yesterday. It says nothing on your playlist.

Austin: There you go.

Effie: Nothing in there.

Austin: Pick that link again. This will see what you're signed in. Does it work? Yeah, no, it's not that right. All right, that's fine. I've got the notes on here, and yeah, you've got them in your email. So, yeah, so basically, this was all done on the back of... double transaction post uh and it was caused by a double click on the button simple thing to do but of course the massive issue um and then when i was going through uh the fix for that there was loads of other possibilities where if something went wrong during posting we'd end up with stuff that we have that is on zero but we don't know about it and it will get posted again leading to double And then I just made it a lot more safe. So basically before it was doing, like, batches of 1,000 invoices, right? So if something went wrong... Yeah. could have up to a thousand things to correct yeah okay so it's gonna work in smaller batches so if there is an issue with that small batch yeah be able to identify that correct yeah so now now it's doing it one at a time so it's logging the attempt to zero and then it's logging the response and the uh thing so we'll be able to tell if we attempted something but it didn't actually get a response and that that one went wrong. So everything at most will probably have, I think the largest batch I do is 50, but most things are done individually. But everything's done a lot more safely now. And then the reason I decided to do daily posting was because if you're doing it in a week batch, it'll be really slow to do the one at a time challenge. type thing okay so so with this automatic posting it's gonna occur every morning yes it goes every morning 6 15 and does minus eight days and minus nine days um and how long does it take to complete is it it takes it says it it said there's a summary in the email i think it's a few hundred seconds or something like that okay cool yeah so it's not not much maybe five minutes

Effie: 120 seconds.

Austin: Yeah, about five-ish minutes.

Effie: But that hasn't actually occurred yet.

Austin: That last one I've done there, that's exactly what will happen tomorrow now. It will do 8th and 9th tomorrow when it goes.

Effie: So when that happens, when it does automatically post, is there anything we need to be checking?

Austin: So you get that summary email, gives you a list of things that went wrong because nothing's gone wrong yet. There's nothing in there. And then it also gives you a link. And the link has a lot more detail than also of what went wrong. So it will give you the contract number, what it was trying to post. And then if you do clear it up on the day, then tomorrow's post will pick it up if you fix the data that needs fixing.

Effie: Oh, because he's going to repost the previous date, I mean.

Austin: Yeah.

Effie: Okay, right.

Austin: There may be things that show up here that offer you to fix the data. If you do see that, don't run it straight away, let me check that. Because it's not, that's not been fully, fully tested, the fix. I'm not sure if I still left the button there or not. So if you do see that pop up, don't press it. Just let me know.

Jhuztine: Fix the data.

Austin: Yeah, any issues that you see initially on the list, just let me know about them. I'll be looking at the email as well. But don't attempt to fix anything until I've had a look at it. So the other part of the project that's not yet released is what we really are talking about now. Ben mentioned you had a list of some common things that go wrong.

Effie: The thing is, anything that needs to be responded to with urgency, I feel like we send to you on a, whether it's a daily or weekly basis.

(overheard): Yeah.

Effie: The stuff we've been making note of are things that, little things that we've noticed on the system that we think could be improved. It's more system developments then.

Austin: stuff that's affecting current contracts because okay well i'll go through uh what some of the things we did fix um that were a problem so you who does the posting on this justine are you Yeah, so you'll be aware of the type of errors that happen when you go to place a zero, right? So you have, first of all, no items on the invoice. That normally happens when it's a one penny VAT invoice rounding issues. So that one has been fixed and tested. It will create the line item correctly now. And also the second thing we had an issue with was document totals don't match. That one's normally when customer services get involved in applied charges and the charges... that could lead to mismatches in totals that wouldn't go to zero. That has also been fixed. What was the other issue? Yeah, it mainly came from compensations, I think, that particular problem.

Effie: What's the situation with compensations being entered as a negative?

Austin: Yeah, so somebody done a compo.

Effie: Are there any examples of that?

Austin: It was just one contract and it had a really weird set of line items on it. I think someone just put it in the wrong way. It may have even been me, potentially in the database, doing it manually.

Effie: Because customer service, they can't put in a negative value in that box, can they?

Austin: I think I remember working on that contract. It was something that Paula was dealing with and it was a customer that... was I think we were really late or even a day late or something like that and she put some compo I wanted me to put some compo on urgently and I think I've done it in the database manually okay so that was probably my my issue that caused that to double check whether customer service can can do that because um I think that was already fixed and checked in the I've got I've got a load of work done that's fixed all of these things I just haven't released it yet because I didn't want to in the same week. If something went wrong, it could go really wrong then. So the other thing I looked at was... Our test contract is getting posted to zero. There are quite a lot on there. And some of them are showing their debts and stuff like that.

Effie: Yeah, because this is the thing. Like, with the test contracts, they only get picked up at end of hire. And then at that point in time...

Austin: It's been posted already.

Effie: Yeah, all you can really do is credit it. And then the credit will go into...

Austin: Yeah, but we're paid real VAT. I had an order there that's like £4,000 or something like that.

Effie: Really, we'd prefer if they didn't go in at all.

Austin: Yeah, so that's another thing that's been fixed. I don't know if I've done the fix for that yet, but it's either been fixed or it will be fixed.

Effie: You mentioned something about there's going to be a separate testing environment, so people won't be able to create test contracts on your system. Is that...

Austin: Well, I do have a test environment now. Schulte may still create tests, but what I'm going to do is prevent anything from mining Schulte's test accounts to get posted. So if it's got Schulte at your hire, I'll send it at your hire.

Effie: What if another user has decided to create a test account just for training or something and that ends up going through? Because we've also seen examples of that. Like Fran might create something for some kind of customer service training. You know what?

Austin: I think it's as simple as putting a test contract checkbox on the test contract and then don't post them.

(overheard): Yeah.

Austin: Yeah, it'd be as easy as that. But also though, there's the, if we do have that, They're on our system with credit notes and stuff like that, but not in Xero.

Effie: But then could we just, could we get them excluded from any kind of your system?

Austin: I see what you mean, like exclude it from the totals that you use to compare. Yeah.

Effie: Financial overview, like it should be excluded from really everything.

Austin: What do you use to compare totals normally, financial overview? So if test contracts are excluded from there and don't get posted to zero, then they'll be matched. Yeah. Yeah, that's fine. We can do that. Can I have a look at this? So we can go through. So, yeah, the timing of daily posting, is that OK? That question here is the line that I'm using for the VAT adjustments.

Jhuztine: Rounding adjustment as a line item is okay?

Austin: For when we do have to do a rounding adjustment line, is that okay for a line?

Effie: Oh, what, to appear?

Austin: Yeah, to appear on the invoice.

Effie: What is causing the roundings? It's going to be VAT, isn't it? Why? Because I haven't seen anything recently, but that's because I don't really look at invoices like that. It would be the VAT. when you're doing the percentage on a because if it would only be because of the vat then maybe the line just says vat adjustment so that it's clear to the customer exactly why that is there it domain there may be branding adjustments needed there's always a chance you'll need a random adjustment on an invoice because of uh I can't even think why what we call it the reason the reason why it occurs on zero I can say when we're entering invoices would be if you've like entered something as VAT inclusive yeah that's that's the only time again it's to do with the VAT calculation sometimes it ends up a penny off but that's really the only scenario that would call genuine rounding otherwise if it's not VAT it shouldn't it shouldn't be occurring

Austin: It would be due to that. It would have to be because it's a percentage on a number. And when it's a very small number, that's why it is normally 1 or 2p.

(overheard): Yeah.

Austin: Because if you're trying to do that on a very small number, it's 20% of, you know, it's 20% of one penny, for example.

Effie: Yeah, exactly.

Austin: So that's why you get them. So... If it is just from that, I can say that rounding adjustment.

Effie: Yeah, because I think if we label as rounding, it always just opens the door for customers to ask questions. Even if it is a penny, oh, what's that about?

Austin: Yeah.

Effie: Why is that a code? Why am I seeing this on my invoice?

Austin: On the customer facing invoices, if it does appear, I can put an explainer on it. to say you know occasionally when we're doing that you know a rounding is a penny off it's a known it's a known issue of what's accounting so people who know about it won't really have a problem i'm sure um all right so so here's my question how how did we have a scenario where the where a rounding caused like a difference of 600 and something That was a Jolt Test contract, I believe. That was a test. Where was the test?

Effie: So it said, in the worst case, a cancelled contract's credit note said £604 on the summary, but £18 in the lines.

Austin: Yeah, I think that one was a Jolt Test contract to run them broadly.

Effie: Okay, so... leads to a massive difference so would so the system would the system be able to pick those up or do we just have it would pick those up and prevent them from going in the first place uh to zero okay yeah and when it picks them up is is it flagging them for you or it would get flagged in the uh post in there

Austin: so much error on zero genuine one is there is there any way it could pick them up before because by the time we post we've done we've actioned what we need to action for that customer already and it's like if the thing now is these things shouldn't happen in the first place that's the whole point of this fix so we shouldn't get to these situations anymore um in the first place so I'm quite confident that most of the things that happen have been solved. So I really think you should need a lot less than my time to do the data fixes that I do quite often. That's the main point of it really because it's been a few times where when something goes wrong it goes really wrong and then there's just loads. yeah um so really and truly i just need to test because because the changes are made involve uh issue report resolutions uh price calculations and things like that i just really need to test uh properly so a bit more detail before i release that so it may be

Effie: And what if anything occurs in that window, like now and then?

Austin: So we could still get something in between that need data fixes, and I'll fix those before going live. Because obviously we still may have existing problems that are not yet posted to Xero from the period up until we go live with the prevention fix.

Effie: Because you said you checked everything from what? Is it everything from...

Austin: Between June... Everything since the new style invoicing. On the posting, I posted thousands of times. I've done the whole year, again, six times. So I kept resetting the demo company, going through the whole process. Everything was fine. And then, again, I've been testing things that did cause a problem, like... no line titles don't match or no line items on the invoice. And then I went through them all again, basically rewound the system to before that invoice got generated, then done it again and it generated properly. So anything that was a problem initially was no longer a problem when I retested it. And I'm going to do that sort of testing again.

Effie: So the contracts that you did find issues with, do they need to be corrected or they've been corrected?

Austin: There is some that do need correcting still. I think I've done a few of them on the live system when I found them.

Effie: Have any of those gone into zero or were these all things that were prevented from going in?

Austin: Yeah, so these would be things that get prevented from going into zero anyway because of zero rejected them.

Effie: Yeah. to fix the course so then if these are if these are invoices what does that mean because you've checked you've checked the invoice totals in zero versus your system and haven't we got to the bottom of all the differences we still have me and something So some of the stuff you found probably relates to those remaining differences?

Austin: Yeah, that would be pre-existing issues of this nature that caused it, yeah. Yeah, and the credit note probably got rejected for no line items or mismatched document total. Okay. Yeah, so these are the type of things that I'm trying to prevent and also what I'm going to try and do... it and change it but obviously if it's strike we can't we can't change it but for the back stuff I'll let you guys just so it's just trying to make you guys less reliant on my time and and not having to wait for me to do stuff you know while you need to do it

Effie: what was the point about how customer service had charges on and like the duplicate charges being added or there was a point about when they reverse in some cases they're doing charges but in other cases they're reversing yeah I realise if you re-resolve an issue

Austin: It doesn't do the reversal. It deletes the initial one. So then the invoice line looks a bit weird because it doesn't show the reversal. Yeah, yeah, yeah. So that was fixed, but it's not released yet.

Effie: So will it be that customer service can no longer delete charges?

Austin: They can still go into the quote folder and do it, but I don't think that's what they were doing. delete of the item from the contract instead of reversing it then having the option to go into the quote builder and delete can that be can that be removed uh yeah because when they go back into the quote builder it changes delivery pricing

Effie: Even though they might not do it, it's just that not knowing and having that option there is kind of a bit risky.

Austin: What I can do is prevent them removing, deleting charge and compo items from the contract on the quote builder. I'll just take off the button. Alright, so I think... covered most of the things i really wanted to let you know about yeah there's a lot there this is more for your uh sanity so you know what i've tested and how and just to make sure you know to see this had a full uh proper look into it um yeah so there's a little few things to what it should be. I need to have a chat with Joel about those things. I didn't want to touch his clothes there. Test contracts out of the book, so it's like, what's that? Yeah, so that's what we just spoke about as well, the issues getting deleted. Point A. Yeah, so that's just basically it. probably is. So yeah, I will be doing the test of next week then on the other changes, the prevention changes.

Effie: Oh, this is what I wanted to ask. You see how you said that you tested 4,128 contracts and 4,113 were perfect. So does that mean there was literally only a few that had issues?

Austin: These were the contracts that did have problems.

Effie: Wait, how many?

Austin: So it would have been... But these were the ones that were not yet fixed by me because obviously we were fixing as we went along a lot of these things, right?

(overheard): Yeah.

Austin: So the ones that were left, the ones that were left over still for me to action, basically, those ones still need a data fix, a manual fix.

Effie: Like 15 contracts? Yeah. Okay.

Austin: Yeah, yeah. There's things that were not yet fixed. It's like...

Effie: left over that was still had a problem on them okay so that makes me feel better because in my mind i'm thinking as we go like we find things and we raise them with you so surely all that's left should be stuff that maybe got missed out or we overlooked and didn't yeah yeah that's exactly what those uh whatever it is our what is it 15 15 yeah yeah yeah so i will be doing the data fix on those as well on the live uh system

Jhuztine: okay cool there's this one thing other thing we wanted to raise with you that justine found the archive situation once we like archive a contract and then customer service will go in and change or add charge or add compo it won't go back to our end of our list And then you end up like...

Austin: So if they add a... That's like a dead cat situation, isn't it?

Effie: So for example, Cussing is paid by BACs. So they're on the non-standard list. Say we haven't received the details and it's been months.

Austin: You archive them.

Effie: archive it because it's been too long but we know that we need their details then somehow I guess customer service have added on a charge and it's stayed in the archive because that's where it is and we don't know the charge has been raised so the invoice is a shaky it's just a big invoice so what I think should happen is as soon as is that enough? It's fine. It's fine. This is just what things are like in accounts, sadly.

Austin: Yeah, I think then, so as soon as they raise a charge, I'll just double check, is the contract archived on the higher list? And if it is, I'll do it. and then phrase it as a dead cat. It should be a dead cat anyway, but you can't see it because it's archived.

Effie: Yeah, basically. It's not a dead cat because it was not completed yet.

Austin: Oh, okay.

Effie: Yeah, that's the thing. It's not completed because we've not actually processed the refund.

Austin: I'll just do a check. When you charge a compo, is it archived? And if it is, unarchive it. And that should bring it back to your attention. Yeah. So that's that part. I mean, you guys wanted to tell me some things that you need to do system-wise and whatnot.

Effie: The thing is, so we, over time, have been making notes on things that we find and, like, system developments that we think would be helpful. It's literally just a shared document where we bullet-pointed stuff, but the format's very messy. This is what we was telling Ben and Josh, that we probably need to get it more organised, put into categories, so that they're things that you can kind of tackle at one time.

Austin: yeah makes sense to do it like that right now they're kind of all over the place relating to different pages different functions yeah it would be a the conversation would go a bit better if we uh if it was all grouped together because then we can just look at the pages and actually demo it um yeah all right so next when you get that together we'll plan another meeting to go through that that needs to be a whole and then we can actually we'll test it on the the test database and test system we can go through some of the stuff and you can show me it happening or whatever yeah um

Effie: Alright. Also, I was having this conversation with Josh, but... He was saying, oh, when, like, these system issues come up, are we raising tickets? And I was basically saying that because a lot of the time, like, we're under time constraints because maybe we've got a deposit that needs to go back to a customer. So we kind of need it fixed within the day or a few days.

Austin: Yeah.

Effie: We tend to not raise tickets because of... Yeah, he comes and speaks to me direct normally. Yeah. But if we were to say, okay, we're going to raise tickets when any issues come up, would they be able to be dealt with instantly or would that be more of a long-term thing like if it's something that if you raise a ticket and phrase it as urgent and tell me it's urgent in the ticket and i'll treat it same as sending me an email basically okay because yeah we just want to get into a better process of raising issues even for Giselle because it's like I don't know everything that she's raising of you and because we don't have an actual log of all of the issues our department has raised Giselle is mostly just correcting mistaken yeah so hopefully with the changes That'll come in. There should be less of that. Yeah. But then anything else maybe will be a bigger issue that needs a ticket.

Austin: Hopefully, isn't the hot part. Yeah.

Effie: Hopefully. Okay, yeah, so.

Austin: All right, then. So, yeah, I'll catch up with you then at the end of next week after I've done... And like I said, if you do see any errors, don't try and deal with them yourself at first because I just want to double check all of the features like I said I should. All right, cool. Hopefully this is recorded.

Jhuztine: Thank you. Hopefully.
