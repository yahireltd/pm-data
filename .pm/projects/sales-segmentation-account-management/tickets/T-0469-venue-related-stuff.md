---
id: T-0469
title: Venue related stuff
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:49:24Z
updated: 2026-07-21T10:17:19Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 4096
reporter: null
assignee: null
acceptance_criteria:
  - (Parked) Revisit the venue threads with sales + logistics users.
  - Decide which of data/cart, commission calcs, and booking-flag become their own tickets.
  - Decide whether the booking-system thread belongs in P-0018 or logistics (P-0019).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
backlog_status: parked
---

## Problem
Venue-related improvements across the Venue DB, quoting, commission and booking. **Parked** pending further discussion with sales + logistics users.

## Threads (from M-010, Nathan)

### 1. Venue data & cart
- Flow venue info through the system; explore linking the **Venue DB to the cart**.
- **Fix:** the cart postcode DB is missing the venue **Olympia**.
- Nathan's **cart-review** idea: get the customer to select venue info; different pricing per venue (e.g. **ExCel dearer** — see T-0551); data-checking for conflicts in booking systems / opening times.
- Big opportunity: reduce friction for sales + customer, improve venue relationships, and sell as experts.
- A **map view** of venues, filterable by scale (qty/value) and ownership (named + AM assigned) — Nathan likes it generally.
- Venue leads (e.g. cash customers) maximisation → answer via roles & responsibilities.

### 2. Venue commission calcs  *(candidate future ticket / spike — needs Austin logic check)*
- Current manual process: Venue DB > orders > check for unassigned, over a month range, email the client each order with goods total + grand-total goods total for the set.
- Charges are **deducted** from commissionable value; **credits stay included** (so the customer doesn't benefit).
- Nathan dislikes the **extensions** process — check with **Austin** for clear logic: may need invoice values related to the contract for total commissionable value; assess logic/ethics of charges vs not paying commission.
- Aim: automatic/semi-auto, linked with venue invoices + our accounts' payments for a complete picture.

### 3. Booking-system flag  *(candidate future ticket — possibly logistics / P-0019)*
- More than a simple flag: capture which booking system + notes; tick off at job level so logistics can show it's done.
- Certain venues need flagging for control/action.
- Booking systems differ (e.g. **Voyage Control**: booking codes from the customer, QR codes from the venue).
- Guide the process; notify logistics of changes after the point of booking.
- Nathan advocates **early booking** → a bookings-required list (required / done / changes-detected), saving drivers, vehicles and times at the point of action.
- Represent logistics' needs too (to be gathered with them).

## Status
**Parked** — revisit with sales + logistics users before splitting threads 2 (commission) and 3 (booking-system) into their own tickets, and deciding whether booking-system belongs here or in logistics (P-0019).

## Conversation

**2026-07-21 10:17 — Zsolt**

FYI 1 Marylebone wanted us to upload invoices, however this could be a sensitive GDPR issue. \
As such I suggest we manually redact sensitive info that we upload using a free PDF editor. \
I’ve added Zsolt in who will put this on the venue part of the project so you can have a redacted PDF pre-made for you

* *

*Redact a customer name where it is a sole trader, partnership named after individuals, or anything that effectively identifies a private person. A named employee and their direct work email or mobile are also personal data. *

*For your purpose, I’d leave visible:*

* *Company name*
* *Invoice/contract number*
* *Venue and event date*
* *Relevant goods value*
* *VAT and totals, where needed to verify commission*
* *Invoice status*

*Redact personal names, private addresses, direct contact details, payment information and unnecessary notes. You might also redact your item-level pricing if the venue only needs the commissionable total—not for GDPR, but for commercial confidentiality.*

***Best free Windows option***

***PDF24 Creator** is probably the easiest fit. It is free, works offline and says files remain on the PC. Use its actual **Redact PDF** function, rather than simply drawing black rectangles. *

*Alternative: **LibreOffice Draw** has dedicated rectangle and free-form redaction tools and can export the redacted copy back to PDF. *

*Important: do not cover text with a normal shape, highlight or black box. The underlying text can remain selectable or recoverable. Use an actual redaction tool, apply the redactions, save as a new PDF, then test by trying to select/search/copy the hidden text. *

*For repeated venue statements, the better long-term answer is probably a system-generated **commission evidence PDF**, rather than manually redacting every invoice.*
