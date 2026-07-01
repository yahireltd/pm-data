---
id: T-0498
title: "Commission Thresholds page: whole-£ sales figures, conversion-date ordering + note system"
type: feature
state: triaged
created: 2026-07-01T05:57:29Z
updated: 2026-07-01T05:57:29Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
assignee: null
acceptance_criteria:
  - Sales results table (summary tiles, Goods Value row/total, Tier rows, Total Commission) show whole pounds with no decimals; figures no longer overflow cells on narrow screens.
  - Contract breakdown (drill-down) lists contracts in conversion-date order, newest first, using ya_contracts.createdDate.
  - Breakdown shows a 'Converted' date column; clicking the column header sorts it correctly by date.
  - "Breakdown has a Note column with a pencil icon: grey when no note, green when a note exists."
  - Clicking the pencil opens a modal to type/edit a note; saving persists it (contractnotes type 13) and the icon turns green on refresh.
  - Clearing a note's text and saving removes the stored note (icon returns to grey).
  - Note editing is admin/manager only; salespeople see a read-only indicator and cannot save.
  - Drill-down goods values retain 2 decimals (decimal removal is scoped to the sales results table only).
out_of_scope: []
code_anchors:
  - path: backend/views/management/commission-thresholds.php
    note: Sales-results decimals (tiles/Goods Value ~L580-640), formatNumber0 helper + tier/commission calls, drill-down sort/Converted+Note columns, note modal + showNoteModal/saveNote JS
  - path: backend/controllers/ManagementController.php
    note: "actionCommissionThresholdsContracts: type-13 note map + createdDate/commissionNote/canNote row fields; new actionCommissionNoteSave endpoint"
  - path: common/models/Contractnotes.php
    note: "type docblock: added 13 = Commission Split Note"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - commission-thresholds
  - management
  - sales
attention: null
version: 1
---

## Problem
Nathan (AM) raised three asks on the **management/commission-thresholds** page:
1. Sales results figures show 2 decimals — on narrow screens they spill over the cell.
2. When drilling into a person's **Contract breakdown** table, contracts aren't in any useful order. He wants them in **conversion-date order (newest first)** so when he reviews sporadically he can focus on the top rows rather than re-checking everything.
3. He needs a **note system** to log *why* a contract hasn't been split (usually: an AM was assigned to the client after the job was booked, so commission stays with the booker).

Zsolt confirmed with Nathan: show the conversion date in the table too; use a pencil/note icon that opens a modal; icon changes colour when a note exists.

## Context / decisions
- **"Conversion date" has no dedicated column.** Confirmed with Zsolt to use `ya_contracts.createdDate` (the contract row is created at quote→contract conversion) as the conversion date — both for sorting and the displayed column.
- **Decimals scope:** sales results table + summary tiles + tier/commission cells only. The drill-down breakdown goods values keep 2 decimals (deliberate).
- **Note storage:** existing `contractnotes` table under a **new type 13 = Commission Split Note**. Admin/manager only (matches the split-toggle permissions); salespeople see a read-only indicator.
- Sort is now purely conversion-date desc (goods value as tiebreak). The previous "split contracts grouped at top" ordering was dropped as it conflicts with a clean top-to-bottom date scan — flagged back to Nathan in case he wants splits pinned.

## What was implemented
- Whole-£ formatting: server-side `number_format(..., 0)` for tiles + Goods Value row/total; new JS `formatNumber0()` for Tier / Total Commission cells; seeds `0.00 → 0`.
- Controller returns `createdDate`, `commissionNote`, `canNote` per breakdown row; loads type-13 notes into a map.
- New `Converted` column (with `data-order` for correct header sorting) + newest-first default sort.
- New `Note` column: grey pencil when empty, green when a note exists; modal to add/edit; clearing text deletes the note.
- New endpoint `management/commission-note-save` (`actionCommissionNoteSave`), admin/manager only.
- Documented type 13 in the `Contractnotes` model docblock.

## Verification note
Implemented and syntax-checked (`php -l` clean on all three files). Not yet exercised in a browser — needs manual verification (see test plan).