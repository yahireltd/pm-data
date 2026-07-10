---
id: T-0541
title: "Review: manual-price survival in the quote builder (LTH crossing + qty/matrix reprice)"
type: spike
state: triaged
created: 2026-07-10T18:31:35Z
updated: 2026-07-10T21:48:08Z
project: accounts-integrity
section: null
parent: null
children: []
order: 5120
priority: p2
reporter:
  kind: agent
  name: Claude (T-0538 session)
  channel: claude-code
assignee: null
acceptance_criteria:
  - "Decision confirmed with Austin AND the colleague who owns the quote builder: survive+flag vs current behaviour, for each of the two cases"
  - "If survive+flag: priceSource provenance design agreed (column, session key, all 7 writers enumerated) and quote-builder regression tests exist before implementation"
  - "If current behaviour kept: document the rule so manual fixes after date/qty changes are re-applied deliberately (and staff know they will be wiped)"
out_of_scope: []
code_anchors:
  - path: backend/controllers/SalesController.php
    symbol: actionCalculateHireDays
    note: LTH overwrite ~6018, wipe ~6110, matrix overwrite ~6094
  - path: backend/controllers/SalesController.php
    symbol: actionUpdateItemQty
    note: priceFixed overwrite ~3781-3825
  - path: backend/controllers/SalesController.php
    symbol: actionManualPriceUpdate
    note: the intended manual-fix flow ~6625
  - path: common/models/YaContractItems.php
    symbol: saveItemsAndAccessories
    note: session->DB persistence, deletes rows absent from session ~587
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quote-builder
  - pricing
  - t-0538-follow-on
attention: null
version: 4
---

## Problem

The quote builder silently overwrites or wipes manually-set prices (`priceFixed`) in two places, which is one of the ways contract pricing diverges from what Austin intended (feeds the T-0538 broken-document class):

1. **Date change crossing the long-term-hire threshold** — `SalesController::actionCalculateHireDays`:
   - LTH branch (~line 6018): `$items[$itemStockID]['priceFixed'] = $longTermHireItemPrice;` — unconditional overwrite with the computed LTH price.
   - Regular branch (~line 6104-6111): when the matrix has no discounted price, the fix is preserved only if BOTH old and new durations are under the threshold; otherwise wiped to `''`. When the matrix HAS a discounted price (~line 6094), it overwrites unconditionally.
2. **Qty change crossing a discount-matrix price break** — `SalesController::actionUpdateItemQty` (~line 3781-3825): when the item has a `priceFixed` and the matrix returns a discounted price for the new qty, the fix is overwritten with the matrix price (and recomputed LTH price on long-term hires).

## Decision so far (Austin, 2026-07-10 — then deferred)

Austin initially chose for both cases: **manual price survives + UI flag** ('manual price — review?' / 'manual price kept — matrix says £X'), then reversed to **keep current behaviour for now** — he does not want to change his colleague's quote-builder functionality without a dedicated review. This ticket is that review.

## Design sketch (from the T-0538 session)

- `priceFixed` today cannot distinguish a manual price from a computed one — LTH totals, matrix prices and customer-% discounts all land in the same field (the retro-classification found ~22k "likely manual" values contaminated by computed ones). Any survival rule needs **provenance**: a `priceSource` column (e.g. `manual` / `matrix` / `lth` / `discount`, NULL = legacy) on `ya_contract_items` AND `ya_contract_items_archive`, set by `actionManualPriceUpdate`, carried through the session payload (`$quoteNo.'.items'`), hydrated in `actionEditQuote`, persisted in `YaContractItems::saveItemsAndAccessories`.
- With NULL treated as legacy/computed, existing data keeps current behaviour — only newly-set manual prices gain protection. Zero behaviour change at rollout until someone sets a manual price.
- All 7 session-items writers need the field carried through: actionEditQuote, actionRemoveItemFromQuote, actionUpdateItemQty, actionAddItemToQuote, actionCalculateHireDays, actionManualPriceUpdate, actionApplyRemoveGoodsDiscount.
- Quote-stage (pre-contract) items have a separate table/flow — decide whether provenance carries through quote→contract conversion or contract-stage only.

## Risks

- The session-based quote builder is fragile (colleague-built); every writer mutates shared session arrays. Needs the quote-builder regression tests (T-0538 remaining item) in place BEFORE touching it.
- Package children and the min-order item (785) have their own price semantics — exclude from any survival rule.

## Conversation

**2026-07-10 19:13 claude-code:** Live evidence for the quote-builder package-pricing review, from INV 69663 (test contract C089510, verified line-for-line identical local vs Xero — so these are contract data defects, not posting or generation defects):

1. **Package double-charge**: 'White Wooden Bar Hire' 2 × £209.99 AND 'White wooden bar package' 2 × £209.99 both fully priced on the same contract — £419.98 charged twice. The other package components (bar top piece, folding bar frame, panel front & sides) are correctly zeroed alongside, so the bar hire line looks like a component that kept its full price.

2. **Package-parent state inconsistency**: 'Party Starter Pack' 3 × £23 is PRICED as a parent line on this contract, while on C089368 the same product was flagged isPackage=1 with NO children — so the pricing engines skipped it and its £92 vanished from both header and lines (doc 72858, the open T-0538 replay exception). Same product, two contradictory data states, two different failure modes.

3. **Duplicate item rows**: two 'Trestle Table Hire (6ft)' lines at different prices (3 × £11.00 and 1 × £10.49) for one product.

These all originate in quote-builder item/package handling and belong in this review's scope alongside the manual-price survival rules. Both contracts are Zsolt's test contracts — worth walking through exactly what he did in the builder to produce each state.

**2026-07-10 21:48 claude-code:** One more quote-side pricing defect for this review's scope, found during the T-0538 code review: **`Quotes.php` `syncDiscountItem` (quote-side, ~line 5002) treats the VAT-rate CODE as a percentage** — `($this->vatRate == 2) ? 0.0 : ($this->vatRate / 100.0)` computes VAT at **1%** for standard-rated quotes (code 1 ÷ 100) when sizing the 962 discount item. The contract-side equivalent was reviewed and is correct; this is quote-stage pricing only (feeds quoted discounts before conversion), but it's exactly the kind of silent mispricing this review exists to catch. Verify the blast radius (how discounted quotes convert to contracts) as part of the session with Zsolt.
