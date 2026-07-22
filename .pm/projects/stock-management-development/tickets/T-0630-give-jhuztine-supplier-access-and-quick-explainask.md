---
id: T-0630
title: Give Jhuztine supplier access and quick explain/ask what she needs
type: feature
state: triaged
priority: p2
created: 2026-07-21T06:21:55Z
updated: 2026-07-22T06:06:34Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 2048
reporter: null
assignee: null
acceptance_criteria:
  - have a talk
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
---

## Problem

_What's wrong / what's needed?_

## Context

## Design notes

## Conversation

**2026-07-21 07:19 claude-code:** **Done: supplier access granted + explainer sent (2026-07-21).**

**Access:** created a scoped **`Suppliers Permissions`** in the RBAC manager (bundles the supplier routes only — `/stock/suppliers`, `/stock/supplier-view` + their add/edit/delete item/contact/order sub-actions + `/ajax/all-items-search`) and assigned it to **Jhuztine (user 687)**. Deliberately *not* the broad `/stock/*`, so she only gets Suppliers.
- Menu: Suppliers link not added yet — she reaches it via the page search / `/stock/suppliers`; it'll be added to the nav when the menu tidy-up reaches Stock.

**Explainer email sent to Jhuztine** covering: where to find it, and each section — Details, Contacts, Items (incl. the new inline pencil-edit for reference/price), Orders — plus that everything saves instantly. Also asked her what she needs (day-to-day tasks, most-used info, anything currently in spreadsheets/email, anything missing/awkward). Added: **Sandor currently manages this page** — she should coordinate changes with him; and flagged that a **new orders-overview page (search/filter) is in development**.

**Open:** awaiting Jhuztine's reply on what she needs — will capture any requests here and spin up follow-up tickets if needed. (This satisfies the ticket's "give access + explain"; the "ask what she needs" half is pending her response.)

---

**2026-07-21 12:35 — Zsolt**

Reply from Jhuztine/Effie

<br />

**Stocks/Suppliers Page:**

1.         Can Extract monthly and yearly summary report

2.         Under suppliers Table

·       Put contact information in one column.

·       Count of Contact not necessary

Suppliers Items

1.         Supplier items first then Suppliers list at the bottom or can be a dropdown

2.         Add field for Order Status

3.         Quantity

4.         Stock Category. This is our Fixed asset categories:

·       Accessory

·       Barrier

·       Barware

·       Catering

·       Chair

·       Gazebo

·       Linen

·       Table

 

**Stock/supplier-view**

1.         Supplier details

·       Supplier details-add button to add contacts. Contact details should reflect under supplier details and remove Contact table

2.         Remove Supplier Items box

3.         Order entry box

·       All orders entry boxes should be after supplier details

·       Can add Invoice number. Order number is only for Sales order.

·       Add discount, total unit price, VAT field

·       Each product should have invoice number next

·       Add function (button) that the delivered product and quantities can be recorded in stock transaction and stock planner.

·       Bundled items should recorded based on how we hire them out. Ex. 50 Coat hangers are 1 stock items.

·       Instead of a box, preferred a add button function to create/add order.

**Questions:**

1.         Final report for how many disposed (unrepairable damage) and missing items and if possible, it will be tagged to the Supplier.

2.         What is the workflow for this process and where does it fall through the whole stock process?

3.         How should we know how many items received, returned and damaged on arrival?

4.         Is it possible to Add the labour cost/hours spent and who are involved for bulk orders, or we can add this under employees report? I need to see the rates as well.

**2026-07-22 06:02 claude-code:** **Moved to the product backlog (out of SPR-001) — 22 Jul.** The original "give Jhuztine access + explain" half is done; Effie's reply (21 Jul) turned the rest into a **substantial batch of supplier-page changes + process questions** (monthly/yearly reports, restructured supplier-view + order-entry, order→stock-transaction recording, bundled-item handling, damaged/missing/returned tracking, labour-cost capture, and workflow questions). That needs a **meeting to scope** before it's sprint-ready — the current acceptance criterion ("have a talk") reflects that.

**Next:** hold the scoping meeting on Effie's list, then break the agreed items into their own tickets (likely several) — this ticket becomes the parent/placeholder for that discussion rather than a single build.

**2026-07-22 06:06 claude-code:** **Scoping meeting created: M-002** ("Supplier pages scoping — Effie/Jhuztine feedback"). Agenda built from Effie's reply (UI tweaks → supplier-view restructure → orders/stock integration → process/workflow decisions → wrap-up/ticket breakdown), all items linked back here. Attendees: Jhuztine, Effie, Zsolt, Ben (+ suggest Sandor, who owns the page). **Date/time TBD — no invites sent yet** (emails not attached); set a date then add emails to send invites.
