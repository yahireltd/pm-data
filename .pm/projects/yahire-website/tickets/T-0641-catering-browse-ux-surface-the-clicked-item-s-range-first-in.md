---
id: T-0641
title: "Catering browse UX: surface the clicked item's range first in the subcategory (+ catering shopping rethink)"
type: feature
state: triaged
created: 2026-07-22T11:54:38Z
updated: 2026-07-22T11:57:28Z
project: yahire-website
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Taran
  channel: email (UX thread, 22 Jul)
assignee:
  kind: human
  name: Zsolt Turu
acceptance_criteria:
  - Clicking a catering item surfaces that item's range at the top of the subcategory listing, with the rest below.
  - The catering browse/order flow is quicker and clearer to use — validated with the people who raised it (Taran / Ben).
  - Exact approach (reorder vs filter/pinned) and any bundles/quick-adds scope agreed in a short design chat before build.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ux
  - catering
attention: null
version: 5
stakeholders:
  - name: taran@yahire.com
    channel: email
    contact: taran@yahire,.com
    internal: true
    added_by:
      kind: human
      name: Zsolt
    added_at: 2026-07-22T11:57:01Z
    notify_on:
      - meeting_scheduled
      - meeting_held
  - name: Thanos
    channel: email
    contact: athanasios@yahire.com
    internal: true
    added_by:
      kind: human
      name: Zsolt
    added_at: 2026-07-22T11:57:17Z
    notify_on:
      - meeting_held
      - meeting_scheduled
  - name: ben
    channel: email
    contact: ben@yahire.com
    internal: true
    added_by:
      kind: human
      name: Zsolt
    added_at: 2026-07-22T11:57:28Z
    notify_on:
      - meeting_held
      - meeting_scheduled
---

## Problem
Browsing catering (and likely most categories) is poor UX. Clicking a specific item — e.g. **Zephyr fork** — loads the subcategory with items **scattered / unordered**; the clicked item's **range (the whole Zephyr range) isn't surfaced at the top**. Raised by Taran, echoed by an industry contact (Orange Events) who found catering "annoying to use when ordering". Ben agrees catering shopping is a friction point and is worth optimising.

## What's wanted
- **Minimum:** when you click into an item, its **range appears first** in the subcategory listing, the rest below.
- **Broader:** rethink the **catering shopping flow** — it's genuinely different from other items. Ideas floated over time: **bundles, quick-adds**, better organisation of the current structure. Must be **elegant, intuitive / idiot-proof, and quick** (Ben).
- **Direction (Zsolt):** full category → subcat → subcat nesting is probably too much; more likely a **filter / click-to-surface-that-range-first** approach — but needs dev + design thinking.

## Notes
- **Needs a quick chat / design pass before build** (Zsolt) — this is a backlog item, not yet build-ready.
- Taran also asked **Athanasios (Thanos)** to review the site for **other UX improvements** — could spin off separate tickets.
- Source: email thread Taran → Zsolt / Thanos / Ben, 22 Jul 2026.

## Open questions (for the chat)
- Exact behaviour: reorder the subcategory to put the range on top, vs a filter/pinned section?
- Which product/category pages does this apply to (catering only, or all)?
- Scope of bundles / quick-adds — in or a follow-up?
- Where in the yasite code the category → subcategory listing + its ordering live (identify on pickup).
