---
id: T-0413
title: TDF prices
type: feature
state: triaged
priority: p2
created: 2026-06-18T07:00:03Z
updated: 2026-06-18T07:05:47Z
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
version: 4
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
