---
id: M-003
slug: accounts-integrity-programme-release-approval-posting-handov
title: Accounts Integrity Programme — release approval & posting handover
state: scheduled
created: 2026-07-10T22:41:30Z
updated: 2026-07-10T22:41:30Z
scheduled_at: 2026-07-10T23:45:00Z
duration_minutes: 50
location: TBD (Austin to schedule with accounts + director; Fran needed for item 7)
project: accounts-integrity
pre_project: null
milestone: MS-002
organizer:
  kind: human
  name: Austin
stakeholders: []
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
outcomes: []
attachments: []
calendar:
  graph_event_id: null
  ics_url: null
kind: progress
version: 1
---

Pre-read: docs/accounts-integrity-meeting-pack.md (final, commit 41d308c2) — plain-English narrative incl. the along-the-way findings table and the website dependency on decision 1. Depth: project P-0020 charter, ADR-001..006, TS-001/TS-002, T-0538 deep dive. Suggested live demo: Demo Company INV #78416 next to the old smeared CR 75664 PDF. Ticket map for the agenda: T-0534/T-0543 (items 1-2), T-0538 (3-4), T-0542 (5), T-0541 (6), T-0545 (7); T-0540 = the website dependency; T-0548 = PDF storage housekeeping (no decision needed).