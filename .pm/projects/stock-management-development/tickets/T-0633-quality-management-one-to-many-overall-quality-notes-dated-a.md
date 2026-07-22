---
id: T-0633
title: "Quality Management: one-to-many overall-quality notes (dated + attributed)"
type: feature
state: done
created: 2026-07-21T11:43:33Z
updated: 2026-07-22T05:54:42Z
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
  - "[x] Staff can add multiple overall-quality notes to a product (replacing the single overwritten otherNotes field)."
  - "[x] Each note records who added it and when (attributed + dated)."
  - "[x] Notes display in the Other Notes area of the product info Quality section, newest first."
  - "[x] Existing otherNotes text is preserved / migrated in as the first note (no data loss)."
  - "[x] Kept simple for now: add + list is enough; edit/delete can come later."
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
version: 20
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

## Conversation

**2026-07-21 12:49 claude-code:** **Placement decided (Zsolt):** the one-to-many notes move to a **full-width "Overall Quality Notes" section directly under the grade-photo row, above the Quality Checks list** — not in the current left column.

Knock-on: **Other Notes leaves the left column**, so Failure Points takes that whole column. The new section reads as a dated/attributed list (`• note — user, date`) with an **Add note** control, sitting naturally next to the Quality Checks history.

Plan when built: new `stock_quality_notes` table (`stockID, note, createdBy, createdAt`) + model; render the full-width section (newest-first list + add); migrate the existing single `StockQualityInfo.otherNotes` in as the first note; add/list actions (edit/delete can come later per AC). Keep the old `otherNotes` column as a dormant safety-net.

**2026-07-21 12:56 claude-code:** **Built (working tree, not committed).** Add + list only, per decision.

**New / changed**
- Table `stock_quality_notes` + model `StockQualityNote` — DDL **and** migration of the existing `otherNotes` (as the first note) in `sql/stock_quality_notes.sql` (run once).
- New full-width **Overall Quality Notes** panel (`renderQualityNotes`) inserted under the grade-photo row, **above** the Quality Checks list: header + add box (textarea + Add) + notes **newest-first** as `note — user · date`.
- **Failure Points now fills the whole left column** — Other Notes removed, the old `flex:7/flex:3` split + 300px sub-cap gone; list is `flex:1`, scrolls, bounded to the row height (stays level with the grade cards).
- `actionAddQualityNote` (`stock/add-quality-note`), gated by the `quality` edit perm, re-renders the section. JS `addQualityNote` in `view-product-info.php`.
- Legacy single-note path (`actionSaveQualityInfo`/Save Notes/`saveQualityInfo`) now dormant; old `otherNotes` column kept as safety-net.
- Docs: feature doc + action doc.

**RBAC:** `stock/add-quality-note` — covered by `stock/*` + quality-edit perm.

**Test plan (human review):**
1. Run `sql/stock_quality_notes.sql`. Products that had an Other Notes value show it as the first note in the new panel.
2. Quality tab: Failure Points now fills the left column; the **Overall Quality Notes** panel sits full-width under the grade photos, above Quality Checks.
3. Type a note → **Add** → it appears at the top with your name + timestamp; the box clears.
4. Empty add is blocked (client + server).
5. Non-quality-edit user: sees the notes list read-only (no add box); the endpoint 403s.
6. Cross-impact: grade photos, failure points, scoring tiles, and Quality Checks all still work.

**2026-07-22 05:15 claude-code:** **Layout refinement (feedback):** full-width Notes + Checks looked sparse. Reworked to **side-by-side** — **Overall Quality Notes (flex 5) | Quality Checks (flex 7)** in one row under the grade photos. Both are **fixed 400px panels with internal scroll** so they always sit level; the Quality Checks list gained a matching "Quality Checks" header/panel. Lint clean; docs updated.

**2026-07-22 05:20 claude-code:** **Notes panel polish (feedback):** each note now renders in a **subtle bubble** (light fill, soft rounded border) instead of a plain divider line; the add-note **textarea is fixed size** (`resize:none`). Lint clean.

**2026-07-22 05:42 claude-code:** **Final notes-panel styling:** each note is a light-blue bubble — `background:#f0f6ff; border:1px solid #b3d1ff; border-radius:8px` — chosen to tie in with the Quality Checks score tiles (same `#f0f6ff`) beside it. Name · date is **right-aligned** inside the bubble; the add-note textarea is fixed size (`resize:none`). Lint clean; working-tree only.

**2026-07-22 05:49 claude-code:** **Add-note textarea:** height now controlled by `rows="3"` (dropped the `min-height:44px`), giving a clean 3-line box; still `resize:none`. Lint clean.

---

**2026-07-22 05:54 — you**

done

---

**2026-07-22 05:54 — you**

Records: docs updated (actionAddQualityNote.md, stock-quality-notes.md); tech-session none-needed; status-note none-needed.
