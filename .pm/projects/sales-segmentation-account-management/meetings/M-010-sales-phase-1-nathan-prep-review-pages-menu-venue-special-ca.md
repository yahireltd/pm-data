---
id: M-010
slug: sales-phase-1-nathan-prep-review-pages-menu-venue-special-ca
title: Sales Phase 1 — Nathan prep review (pages, menu, venue, special captures, email templates)
state: held
created: 2026-07-15T07:41:07Z
updated: 2026-07-17T05:25:38Z
scheduled_at: 2026-07-16T09:00:00
duration_minutes: 60
location: TBD
project: sales-segmentation-account-management
pre_project: null
milestone: MS-001
organizer:
  kind: human
  name: Zsolt
stakeholders:
  - name: Nathan
    role: Sales Manager SME
    channel: email
    contact: nathan@yahire.com
    internal: true
  - name: Ben
    role: Business SME & Systems Architect
    channel: email
    contact: ben@yahire.com
    internal: true
  - name: Zsolt
    role: Developer & System SME
    channel: email
    contact: zsolt@yahire.com
    internal: true
agenda:
  - topic: Remove redundant pages — usage review + what can merge
    ticket: T-0467
  - topic: Menu / activities tidy — top-level pages, submenu, activity logging
    ticket: T-0468
  - topic: Venue — assignment/finding/editing, map view, commission calcs, booking-system flag
    ticket: T-0469
  - topic: Main pages review — Ledger/CRM/Profile/QB/Pending Qs bug-bears & overhauls
    ticket: T-0470
  - topic: Special captures — define the 3 tiers (Simple/Manual/Expert)
    ticket: T-0574
  - topic: Email templates — template manager overhaul + personalisation + ownership
    ticket: T-0575
outcomes:
  - description: "[T-0467 Redundant pages] Build a dedicated Contract-Checking page, then retire the Website Quotes page; add a select-all and optimise the page. Also: release the search page to all users."
    recorded_at: 2026-07-17T05:24:42Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0468 Menu/activities tidy] Not covered in this session — carry forward to a follow-up with Nathan."
    recorded_at: 2026-07-17T05:24:47Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0469 Venue — data & cart] Flow venue info through the system; explore linking the Venue DB to the cart. Fix: the cart postcode DB is missing the venue Olympia. Flag certain venues as needing control/action. Capture lift-size vs large-item data (does the venue have a lift; item size vs lift size). Per-venue pricing (e.g. ExCel dearer) is already tracked in T-0551."
    recorded_at: 2026-07-17T05:24:55Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0469 Venue — booking-system integration] Booking systems differ by venue and have requirements (e.g. Voyage Control): booking codes collected from the customer, QR codes from the venue. System should guide the process and notify logistics of changes after the point of booking. Nathan advocates early booking → a bookings-required list (required / done / changes-detected) that saves drivers, vehicles and times at the point of action. Likely a candidate for the logistics project (P-0019) rather than P-0018 — to decide when ticketing."
    recorded_at: 2026-07-17T05:25:00Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0469 Venue — commission calcs] Venue commission workflow: go to Venue DB > orders > check for unassigned, over a month range, and email the client each order with goods total + grand-total goods total for the set. Caveat: charges are deducted from commissionable value, but credits stay included (so the customer doesn't benefit). Nathan dislikes the extensions process — check with Austin for clear logic: may need to take invoice values related to the contract to give total commissionable value, and assess the logic/ethics of charges vs not paying commission. Needs Austin logic/ethics review before build (likely a spike)."
    recorded_at: 2026-07-17T05:25:07Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0470 Main pages] Ledger/CRM/Profile/QB/Pending: add Leads and Key-account requests (plus the icon on the QB). Resolve the possible overlap between Smartest View and the Ledger, specifically around contracts. Ledger fix: it currently shows only quote value (which can be cancelled or change), not contract value — should show contract value."
    recorded_at: 2026-07-17T05:25:14Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0470 My Accounts — deferred] The My Accounts page rework is parked until the relationship-management process is clearer (flagged for Austin/Zsolt, for later)."
    recorded_at: 2026-07-17T05:25:17Z
    owner:
      kind: human
      name: Zsolt
  - description: "[Commission bug — investigate] A split contract cancelled to value £0 is still showing £666 on Paula's Q2 results (commission thresholds). Zsolt to look into cancelled value vs commission paid. Candidate for a bug ticket after review."
    recorded_at: 2026-07-17T05:25:22Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0574 Special captures] The 3-tier (Simple / Manual / Expert) definition was not covered in this session — carry forward to a follow-up with Nathan."
    recorded_at: 2026-07-17T05:25:26Z
    owner:
      kind: human
      name: Zsolt
  - description: "[T-0575 Email templates] Template-manager overhaul + personalisation + ownership — not worked through in detail this session; carry forward. (Related: Nathan mentioned payment chasers, which are already homed in T-0418; and an action to ask inbound what they send — Frank/Helio/Sam.)"
    recorded_at: 2026-07-17T05:25:30Z
    owner:
      kind: human
      name: Zsolt & Austin
  - description: "[Cross-cutting] Additional points raised: (1) IDEA — use ML on outcomes of pricing vs time vs actual, with feedback loops (note: new-ML build is currently a project scope-out, so treat as a future spike/idea). (2) Roles & responsibilities — Nathan wants these defined (governance / Prototype 2). (3) Reference material to review: Nathan's document sent 16/7, the customer-expectation doc, and Zac's spreadsheets."
    recorded_at: 2026-07-17T05:25:38Z
    owner:
      kind: human
      name: Zsolt
attachments:
  - key: meetings/M-010/1784109063870-management-page-usage-report.html
    filename: management-page-usage-report.html
    content_type: text/html;charset=utf-8
    size: 20147
    uploaded_by: zsolt@yahire.com
    uploaded_at: 2026-07-15T09:51:05Z
  - key: meetings/M-010/1784109094975-sales-page-usage-report.html
    filename: sales-page-usage-report.html
    content_type: text/html;charset=utf-8
    size: 15230
    uploaded_by: zsolt@yahire.com
    uploaded_at: 2026-07-15T09:51:36Z
  - key: meetings/M-010/1784110603612-Question_prep_for_Nath_sales_phase_1_-_updated.docx
    filename: Question prep for Nath sales phase 1 - updated.docx
    content_type: application/vnd.openxmlformats-officedocument.wordprocessingml.document
    size: 328037
    uploaded_by: zsolt@yahire.com
    uploaded_at: 2026-07-15T10:16:45Z
  - key: meetings/M-010/1784119812746-rbac-menu-tree.csv
    filename: rbac-menu-tree.csv
    content_type: text/csv
    size: 19622
    uploaded_by: zsolt@yahire.com
    uploaded_at: 2026-07-15T12:50:14Z
calendar:
  graph_event_id: AAMkADg5NzRlZTFjLTdkZGEtNDZlZS05MWIxLTQ5NzJhNWZkNWFjZgBGAAAAAABRaX1b6YusT7RDYf0iEOMlBwCnphZ2BstsS50BbU5pWRaYAALqCQ1nAACnphZ2BstsS50BbU5pWRaYAAdklJRIAAA=
  ics_url: null
  organizer_mailbox: support@yahire.com
kind: scoping
version: 20
---

**Purpose:** work through the "Question prep for Nathan — sales phase 1" doc to gather the info needed to scope Phase 1 (PH-001 / MS-001).

**Attendees:** Nathan, Zsolt, Ben.

**⏰ Time TBC** — placeholder 09:00, morning of 16 Jul. Confirm the exact time.

**Prep:** Zsolt to bring the pages-vs-last-2-months usage summary (for the redundant-pages / main-pages items). Most agenda items are discussion/needs-Nathan — output is detail (acceptance criteria) to flesh out T-0467/0468/0469/0470/0574/0575, plus decisions on the special-capture tiers and email-template ownership.
