---
id: T-0413
title: TDF prices
type: feature
state: triaged
priority: p2
created: 2026-06-18T07:00:03Z
updated: 2026-06-18T07:12:04Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 35840
reporter: null
assignee:
  kind: human
  name: zsolt@yahire.com
acceptance_criteria:
  - "Customer Profile: user can open a list of all products grouped by category, each with a checkbox."
  - "Customer Profile: user can tick multiple lines and apply a single % in one action; it saves to every selected item."
  - An item discount can be entered as either a % or a fixed price.
  - "Category %: can be set and applies to all items in that category that have no item-level discount."
  - "Overall %: still applies only where no category or item discount exists."
  - "Discount precedence is enforced: item > category > overall. (Confirm with Nathan/Taran.)"
  - Existing discount document / expiry behaviour is unchanged.
  - "Quote Builder: adding an item pulls its effective profile discount (item -> category -> overall) automatically."
  - "Quote Builder: discounted lines show an indicator with the %, plus the original price where it is below standard."
  - Discounted prices are stored via the existing manual-price field; PDF, totals, and contract conversion render unchanged.
  - "Option B: after each of the three recalc paths (date change crossing LTH, qty hitting a quantity-break tier, whole-quote goods discount) the system re-applies the % to the new base price, so the discounted price is not wiped."
  - Discount is calculated on the recalculated base price (incl. LTH), with the % applied after. (Depends on the base-vs-shown-price decision.)
  - If a base catalogue price later changes, the quote/profile reflects the new price with the % re-applied automatically (no manual rework).
  - Removing a discount restores the item's normal pricing (blanket or none).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## Problem

&#x20; Special-pricing customers (e.g. Tobacco Dock Food) get a blanket discount — \~32% off many, but not all, items. Today Nath sets these manually per item: slow, easy to miss items

&#x20; (especially accessories), and wiped whenever a base price, qty, or date changes.

&#x20; Request

&#x20; Select many items at once and apply a single % discount — set on the customer profile and reflected automatically on quotes.

&#x20; Scope — revamp Customer Profile → "Customer Special Prices" (Nathan, 29 May)

&#x20; \- A) Discount doc / expiry — keep as is

&#x20; \- B) Overall % — keep (applies where no specific discount exists; category/item overrides it)

&#x20; \- C) Category % — revamp + streamline the category list (e.g. Catering / Standard Chairs / Armchairs) for targeting client types

&#x20; \- D) Item discount (% or fixed price) — the core ask: list all products by category, tick multiple lines, apply one % (e.g. 30%)

  

&#x20; Quote Builder behaviour

&#x20; \- Pulls the profile's overall/category/item discounts through automatically

&#x20; \- Small icon on discounted lines (e.g. "30% — Black Bistro Bar Stool"); show original price where it's below standard

&#x20; \- Stored via the existing manual-price field (PDFs, totals, contract conversion keep working)

&#x20; \- Option B (recommended): recalc the base price first (incl. LTH), then apply the % after — so the discount survives the three recalc paths that otherwise wipe it: date change crossing

&#x20; LTH, qty hitting a quantity-break tier, and the whole-quote goods discount

&#x20; Open decisions (the "needs defining" bit)

&#x20; 1\. Does the % apply to the catalogue base price (replaces the blanket customer discount — Nathan's "this is their discount", recommended) or to the currently shown price (compounds with

  the blanket discount)? 

&#x20; 2\. Precedence between item / category / overall, plus LTH and the whole-quote goods discount.

&#x20; 3\. Category taxonomy needs a tidy-up before (C) is workable.

  

&#x20; Priority: Not urgent (Nathan), but will recur often — agree timeline with Ben.

&#x20; Note: Not a small QB change — touches the three existing recalc paths.

## Conversation

**2026-06-18 07:12 Zsolt:** Before we build the "select items, apply a %" tool, a few pricing rules need agreeing — each one changes the final price a customer sees, so I'd rather lock them now than guess. A quick answer/pick on each:

**1. What does the % come off — the base price, or the already-discounted price?**
If an item is normally £100 and the customer already has 10% off, and we apply a 32% special:
- Option A — off the base price (replaces the 10%): **£68.** One clear number; matches "this is their discount."
- Option B — on top of the 10% (stacks): **£61.20.**
Recommendation: **Option A.**

**2. Does a specific discount replace the overall one, or add to it?**
Proposed: an item or category discount overrides the overall blanket % (strength order: item → category → overall). Please confirm.

**3. Long-term hire (LTH).** Long hires already lower the unit price. For a special-priced item on a long hire, does the customer get **(a) both** — the 32% on top of the reduced long-term price — or **(b) whichever is lower**? Recommendation: (a), but flag if that's too generous.

**4. Whole-quote discount.** If a blanket discount is applied to a whole quote, should special-priced lines be **excluded** (they already have their deal) or **included** (discounted again)? Recommendation: excluded.

**5. Category cleanup.** Discounting by category only works if the product categories are tidy — currently they're not. OK to invest some time tidying them as part of this?

Q1 and Q2 are the blockers — the build can't start without them. Once settled I'll firm up the ticket and we'll agree timing with Ben.
