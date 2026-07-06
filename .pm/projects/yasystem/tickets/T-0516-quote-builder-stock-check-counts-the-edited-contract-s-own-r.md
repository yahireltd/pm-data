---
id: T-0516
title: Quote-builder stock check counts the edited contract's own reservation against itself
type: bug
state: review
created: 2026-07-06T05:54:36Z
updated: 2026-07-06T05:58:07Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - Increasing an item's qty on a saved contract no longer reports a false shortage caused by the contract's own existing reservation.
  - On contract 47890 (C091789), changing the poseur qty shows the frames and top as available (green) when enough genuinely exist.
  - A normal single-item qty edit on another contract still checks stock correctly (green when room, red when genuinely short).
  - All other callers of stockLevelsBetweenDatesAP are unchanged (param defaults to 0 → no exclusion).
  - Quotes (Q…) are unaffected (pass contractID 0).
  - StockLevels.php and SalesController.php pass php -l.
out_of_scope: []
code_anchors:
  - path: common/models/StockLevels.php
    note: "stockLevelsBetweenDatesAP() line 561: added optional $excludeContractID param; committed-stock query ~630-655 now drops that contract's rows when set. Batch variant (line 873) left unchanged."
  - path: backend/controllers/SalesController.php
    note: "actionUpdateItemQty: passes $contractID (resolved ~3632) into the three stock checks at 3674 (parent), 3865 (package parts), 3956 (accessories)"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260706-0557
    model: claude-opus-4-8
    started: 2026-07-06T05:57:47Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-06T05:57:47Z
    ended: 2026-07-06T05:58:07Z
    summary: "Fixed a false \"not enough stock\" warning on the quote-builder. When someone increased the quantity of an item on an existing contract, the availability check was counting that contract's OWN existing booking as if it were unavailable stock held by someone else — so the contract was effectively competing with itself and looked short by exactly the amount being added. This surfaced on the poseur-table bundle (contract 47730 / C091789): raising the frames to the correct 24 showed a shortage of 6 that didn't really exist. Cause: the shared availability function counts every contract's committed stock in the date range with no way to say \"ignore the one I'm editing\"; it only had that exclusion on the initial page load, not when changing a quantity. The fix adds an opt-in way to exclude the contract being edited, and the quote-builder now uses it. It's an advisory check only (the green/red indicator) — no bookings, prices, or picking data change. Left alone, staff would keep seeing phantom shortages when increasing quantities on live contracts and either override blindly or chase non-issues. The change is backward-compatible: every other place that uses this availability function behaves exactly as before; only the quantity-edit screen opts in. Tested and committed."
    test_plan: |-
      Already smoke-tested by Zsolt on 47730; full checklist for the reviewer:

      1. On contract 47730 (C091789), open quote-builder and change the Black Element Poseur Table qty. Confirm the frames + top now show GREEN (available), where before they showed a false shortage; going to 24 frames is allowed when the 6 extra genuinely exist.
      2. Regression — normal item: on a different contract, increase a plain (non-bundle) item's qty and confirm the stock check still works — green when there's room.
      3. Regression — genuine shortage: pick an item that really is fully committed by OTHER contracts in the window; confirm it still shows RED (we only excluded the edited contract, not everyone).
      4. Quote (not contract): open a Q-number quote, change an item qty, confirm stock checks behave as before (quotes pass contractID 0, so no change — their items aren't in ya_contract_items anyway).
      5. Cross-impact — other users of the shared function: spot-check the Stock Planner and a fresh quote-builder page load still show the same availability numbers as before this change (they don't pass the new param, so should be identical).
      6. Initial load unchanged: reopen 47730 fresh (not editing) and confirm the contract's own items still show correctly (the older edit-quote add-back path is untouched).
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Added docs/models/StockLevels/stockLevelsBetweenDatesAP.md and a stock-check section in docs/controllers/backend/SalesController/actionUpdateItemQty.md (working tree, need committing).
labels:
  - quote-builder
  - stock
  - sales
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-06T05:58:07Z
version: 5
---

## Symptom
On the quote-builder, increasing an item's quantity (surfaced via the bundle editor on contract 47890/C091789 — poseur frames + top) reported "not enough stock" even though there was enough. The contract's OWN existing reservation was being counted as unavailable against itself, so it was short by exactly the extra requested.

## Root cause — `StockLevels::stockLevelsBetweenDatesAP()`
The availability calc sums committed qty from every overlapping `ya_contract_items` row for that stock, with **no contract filter** (`StockLevels.php:630-647`, summed `:667`, availability `:683`). The only "don't count the current contract" logic is a hack gated on the controller action being `edit-quote` AND the passed object being a `YaContractItems` (`StockLevels.php:685-691`).

- **Initial page load** = action `edit-quote`, passes a `YaContractItems` → hack fires → correct.
- **Qty change** = action `update-item-qty`, passes a `YaProducts` → neither condition holds → the contract's own reservation is double-counted → false shortage.

Only surfaced now because the package-cascade fix (T-0515) made the frame qty correctly rise to 24 (needing 6 more); before, the qty was capped so the check never asked for the extra.

## Fix
Added an optional `$excludeContractID = 0` param to `stockLevelsBetweenDatesAP()`; when set, its committed-stock query drops that contract's rows (`->andWhere(['<>','contractID',$excludeContractID])`, guarded). Wired the current contract id into the three quote-builder qty-edit calls (`SalesController.php:3674` parent, `:3865` package parts, `:3956` accessories).

**Backward-compatible:** the param defaults to `0`, so all ~25 other callers are unchanged (the filter is skipped). Only the qty-edit path opts in. Quotes (Q…) pass `0` automatically — correct, since their items aren't in `ya_contract_items`. The initial-load/batch path is untouched (already correct via the existing hack).

## Notes
- This is an advisory check (green/red icon) with an existing overbook override — no data/pricing/reservation change.
- Tested by Zsolt on 47890: frames/top now check green; normal single-item edits still check correctly.

## Conversation

**2026-07-06 05:57 claude-code:** **Correction:** the contract is **47730** (C091789) throughout — I mistyped "47890" in the body/acceptance criteria above. The poseur bundle is contractID 47730, part 1004 (Black Element Poseur Frame).

**Tested + committed:** Zsolt verified on 47730 (frames/top now check green; normal single-item edits still check correctly). Committed as *"Exclude edited contract from stock checks"* (`94fdfc16`).

**Docs added/updated:**
- `docs/models/StockLevels/stockLevelsBetweenDatesAP.md` (new) — how availability is computed, the `edit-quote` add-back hack vs the new `$excludeContractID` param, and the backward-compat/opt-in behaviour.
- `docs/controllers/backend/SalesController/actionUpdateItemQty.md` — added a "Stock check excludes the contract being edited" section. (New doc files, need committing.)

**2026-07-06 05:58 claude-code:** Run run-20260706-0557 completed — Fixed a false "not enough stock" warning on the quote-builder. When someone increased the quantity of an item on an existing contract, the availability check was counting that contract's OWN existing booking as if it were unavailable stock held by someone else — so the contract was effectively competing with itself and looked short by exactly the amount being added. This surfaced on the poseur-table bundle (contract 47730 / C091789): raising the frames to the correct 24 showed a shortage of 6 that didn't really exist. Cause: the shared availability function counts every contract's committed stock in the date range with no way to say "ignore the one I'm editing"; it only had that exclusion on the initial page load, not when changing a quantity. The fix adds an opt-in way to exclude the contract being edited, and the quote-builder now uses it. It's an advisory check only (the green/red indicator) — no bookings, prices, or picking data change. Left alone, staff would keep seeing phantom shortages when increasing quantities on live contracts and either override blindly or chase non-issues. The change is backward-compatible: every other place that uses this availability function behaves exactly as before; only the quantity-edit screen opts in. Tested and committed.
