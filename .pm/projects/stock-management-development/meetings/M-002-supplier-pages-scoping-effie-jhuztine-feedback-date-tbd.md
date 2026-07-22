---
id: M-002
slug: supplier-pages-scoping-effie-jhuztine-feedback-date-tbd
title: Supplier pages scoping — Effie/Jhuztine feedback (date TBD)
state: scheduled
created: 2026-07-22T06:06:21Z
updated: 2026-07-22T06:06:21Z
scheduled_at: 2026-07-22T07:10:00Z
duration_minutes: 60
location: TBD
project: stock-management-development
pre_project: null
milestone: null
organizer:
  kind: human
  name: Zsolt
stakeholders:
  - name: Jhuztine
    kind: human
    internal: true
    role: Requester (Suppliers)
  - name: Effie
    kind: human
    internal: true
    role: Requester (Suppliers)
  - name: Zsolt
    kind: human
    internal: true
    role: IT / Dev
  - name: Ben
    kind: human
    internal: true
    role: Stock process
agenda:
  - topic: Context & goal — access is live + explainer sent; agree the meeting's outcome (what's in, rough size, owner, ticket breakdown)
    ticket: T-0630
    duration_min: 5
  - topic: "Suppliers list page (UI): contact info in one column + drop contact count; Supplier Items first / suppliers list below (or dropdown); add Order Status, Quantity, and Stock Category (Accessory/Barrier/Barware/Catering/Chair/Gazebo/Linen/Table) columns"
    ticket: T-0630
    duration_min: 10
  - topic: "Supplier-view restructure: Add-contact button + contacts shown under Supplier Details (remove Contacts table); remove Supplier Items box; move order entry below details as an 'Add order' button/flow"
    ticket: T-0630
    duration_min: 10
  - topic: "Orders — data + stock integration (the big one): invoice number (per product) vs order number, discount, unit price, VAT; record delivered product + quantities into Stock Transactions + Stock Planner on receipt; bundled-item rule (e.g. 50 coat hangers = 1 stock item); how far an ordering/receiving module goes"
    ticket: T-0630
    duration_min: 20
  - topic: "Process / workflow decisions: capture items received / returned / damaged-on-arrival; disposed (unrepairable) + missing report tagged to supplier; where this sits in the end-to-end stock process; labour cost/hours + rates for bulk orders (on the order vs employees report)"
    ticket: T-0630
    duration_min: 15
  - topic: "Wrap-up: tag each item (quick win / needs build / needs more thought or separate project); assign owners; agree ticket breakdown; note any stock-process decisions (ADRs)"
    ticket: T-0630
    duration_min: 5
outcomes: []
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
version: 1
---

**Date/time: TBD** — to be confirmed. No calendar invites sent yet (emails not attached); once a date is agreed, add attendee emails and send invites.

**Purpose:** turn Effie/Jhuztine's feedback (see T-0630, reply 21 Jul) into agreed, scoped work — decide quick wins vs. changes that touch the core stock process, assign owners, and break it into tickets.

**Pre-read:** Effie's reply captured on **T-0630**.

**Attendees:** Jhuztine, Effie, Zsolt (IT), Ben (stock process). Note: Sandor currently owns the suppliers page — worth having him too if available.