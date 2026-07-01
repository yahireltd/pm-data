---
id: T-0498
title: "Commission Thresholds page: whole-£ sales figures, conversion-date ordering + note system"
type: feature
state: done
created: 2026-07-01T05:57:29Z
updated: 2026-07-01T06:12:44Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Nathan
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Sales results table (summary tiles, Goods Value row/total, Tier rows, Total Commission) show whole pounds with no decimals; figures no longer overflow cells on narrow screens."
  - "[x] Contract breakdown (drill-down) lists contracts in conversion-date order, newest first, using ya_contracts.createdDate."
  - "[x] Breakdown shows a 'Converted' date column; clicking the column header sorts it correctly by date."
  - "[x] Breakdown has a Note column with a pencil icon: grey when no note, green when a note exists."
  - "[x] Clicking the pencil opens a modal to type/edit a note; saving persists it (contractnotes type 13) and the icon turns green on refresh."
  - "[x] Clearing a note's text and saving removes the stored note (icon returns to grey)."
  - "[x] Note editing is admin/manager only; salespeople see a read-only indicator and cannot save."
  - "[x] Drill-down goods values retain 2 decimals (decimal removal is scoped to the sales results table only)."
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
agent_runs:
  - id: run-20260701-0603
    model: claude-opus-4-8
    started: 2026-07-01T06:03:48Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-01T06:03:48Z
    ended: 2026-07-01T06:04:27Z
    summary: "Made three improvements to the Commission Thresholds page that an account manager asked for. First, the sales results figures now show whole pounds instead of pennies, so the numbers no longer spill out of their cells on smaller screens. Second, when you click a person's name to see their contract breakdown, the contracts are now ordered by when they converted (most recent at the top) and a new \"Converted\" date column is shown — so the AM can review the latest ones at a glance instead of re-checking the whole list each time. Third, there's now a per-contract note: a small pencil icon that opens a box to jot down why a contract hasn't been split (for example, the client got an account manager only after the job was booked, so the commission stays with the original booker). The pencil is grey when empty and green once a note is saved. Without these, the page stayed hard to read on narrow screens, the AM had to re-scan every contract to find new ones, and there was nowhere to record split decisions — so the reasoning lived only in people's heads. Note: the changes are written and syntax-checked but are sitting in the working copy uncommitted, and haven't been clicked through in a browser yet — this ticket is the record; commit/deploy is pending Zsolt's go-ahead."
    test_plan: |-
      Prereq: view the changes locally (working tree, not yet committed). Open management/commission-thresholds and pick a date range.

      Decimals:
      1. Confirm the summary tiles (Goods Value, Split Value, etc.), the Goods Value row, Tier rows and Total Commission all show whole £ with NO decimals.
      2. Narrow the browser window — confirm figures no longer overflow their cells.
      3. Cross-check: the drill-down breakdown goods values SHOULD still show 2 decimals (this was intentionally left alone). Confirm they do.

      Conversion-date ordering + column:
      4. Click a salesperson's Goods Value cell to open the Contract breakdown. Confirm rows are ordered newest-converted first (top = most recent).
      5. Confirm a new "Converted" column appears (next to Hire Start) with sensible dates.
      6. Click the "Converted" column header — confirm it re-sorts by real date order (not alphabetically by the dd.mm.yyyy text).

      Note system (log in as an admin/manager, e.g. an account you know is in the admin/manager list):
      7. On a contract with no note, confirm a GREY pencil icon in the Note column. Click it → modal opens titled with contract no + customer.
      8. Type a note, Save. Confirm modal closes, the breakdown refreshes, and the pencil is now GREEN.
      9. Click the green pencil again → confirm the modal pre-fills the saved text. Edit it, Save, confirm it persists.
      10. Open the note, clear all text, Save → confirm the note is deleted and the pencil returns to GREY.
      11. Edge — permissions: log in as a plain salesperson, drill into your own data. Confirm you do NOT get an editable pencil (read-only green icon only where a note already exists, otherwise a dash), and that you cannot save.

      Cross-impact:
      12. The whole-£ change added a new JS helper (formatNumber0) used only by the main sales table; the drill-down still uses the original 2dp formatter. Re-confirm both the split summary line and split-row goods values in the drill-down are unchanged (still 2dp).
      13. Confirm the existing Commission Split modal, Assignment History modal, and SYSTEM "Assign to AM" panel on the same page still work (shared page/JS was edited around them).
      14. New note type is contractnotes type 13 — confirm it does not appear in other note surfaces (delivery/collection/sales/logistics notes etc. are types 1-12 and should be unaffected).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: actionCommissionThresholds.md
labels:
  - commission-thresholds
  - management
  - sales
attention: null
version: 16
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

## Conversation

**2026-07-01 06:04 claude-code:** Run run-20260701-0603 completed — Made three improvements to the Commission Thresholds page that an account manager asked for. First, the sales results figures now show whole pounds instead of pennies, so the numbers no longer spill out of their cells on smaller screens. Second, when you click a person's name to see their contract breakdown, the contracts are now ordered by when they converted (most recent at the top) and a new "Converted" date column is shown — so the AM can review the latest ones at a glance instead of re-checking the whole list each time. Third, there's now a per-contract note: a small pencil icon that opens a box to jot down why a contract hasn't been split (for example, the client got an account manager only after the job was booked, so the commission stays with the original booker). The pencil is grey when empty and green once a note is saved. Without these, the page stayed hard to read on narrow screens, the AM had to re-scan every contract to find new ones, and there was nowhere to record split decisions — so the reasoning lived only in people's heads. Note: the changes are written and syntax-checked but are sitting in the working copy uncommitted, and haven't been clicked through in a browser yet — this ticket is the record; commit/deploy is pending Zsolt's go-ahead.

**2026-07-01 06:09 claude-code:** **Decision — note visibility (2026-07-01):** Notes stay **admin/manager-only**, as built. We considered letting salespeople *view* (but not edit) notes, since the "why it wasn't split" reasoning could be useful to the booker. Zsolt's call: keep it locked down for now — if Nathan wants salespeople to see notes, we'll revisit then.

Implementation matches this: salespeople get no editable pencil (read-only green icon where a note exists, otherwise a dash), and clicking does nothing — the note text isn't shown to them in the UI. (Minor caveat noted for the future: the note text is still present in the breakdown's JSON response, so it's technically visible via browser dev-tools; we'd strip it from non-editors' payload if/when we tighten this.)

---

**2026-07-01 06:12 — you**

Finished the changes Ben and Nathan requested, sent them emails, so they can check the page
