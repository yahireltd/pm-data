---
id: T-0469
title: Venue related stuff
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:49:24Z
updated: 2026-07-17T06:34:25Z
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
version: 4
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
