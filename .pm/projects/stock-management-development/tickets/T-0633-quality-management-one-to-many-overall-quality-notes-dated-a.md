---
id: T-0633
title: "Quality Management: one-to-many overall-quality notes (dated + attributed)"
type: feature
state: triaged
created: 2026-07-21T11:43:33Z
updated: 2026-07-21T11:44:28Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: split from T-0553
assignee: null
acceptance_criteria:
  - Staff can add multiple overall-quality notes to a product (replacing the single overwritten otherNotes field).
  - Each note records who added it and when (attributed + dated).
  - Notes display in the Other Notes area of the product info Quality section, newest first.
  - Existing otherNotes text is preserved / migrated in as the first note (no data loss).
  - "Kept simple for now: add + list is enough; edit/delete can come later."
out_of_scope: []
code_anchors:
  - path: ya-hire/common/models/StockQualityInfo.php
    note: single otherNotes field being replaced by a one-to-many set
  - path: ya-hire/backend/controllers/StockController.php
    note: buildQualitySectionContent (Other Notes render) + actionSaveQualityInfo (current single-note save)
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: Other Notes UI (textarea + Save Notes) under the Failure Points panel
  - path: ya-hire/common/models
    note: NEW model + table for one-to-many quality notes (note, createdBy, createdAt)
relates:
  - T-0553
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 2
---

## Source
Split out of **T-0553** to keep that ticket focused on the grade cards + photos. Originally AC #6 of T-0553. From kickoff M-001 (Sandor's + Ben's stock docs).

## Problem
Overall-quality notes are a single **overwritten** `StockQualityInfo.otherNotes` textarea on the product Quality tab — one note only, no history, no attribution.

## What's wanted
Turn it into **one-to-many**: staff can add multiple **dated + attributed** notes about a product's overall quality over time, replacing the single overwritten field. Kept simple for now (add + list), expandable later.

## Notes
- Sits in the **same "Other Notes" position** on the Quality tab (product info page), under the Failure Points panel — layout unchanged, just the box becomes an add-note + list.
- **Migrate** the existing `otherNotes` text in as the first note (preserve, don't drop the data).
- Independent of T-0553's grade-card work (shares the Quality section render, so coordinate the edit) — can be built after T-0553.