---
id: T-0554
title: "Quality: multiple structured failure points per product (name + note + photo + active toggle) with affected in-stock qty"
type: feature
state: done
created: 2026-07-14T05:11:11Z
updated: 2026-07-22T10:11:21Z
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
  channel: kickoff M-001
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - "[x] A product can have multiple failure points (replacing today's single free-text field on StockQualityInfo.failurePoints)."
  - "[x] Each failure point has: name, note, a photo, and an Active on/off status."
  - "[x] Active failure points can be flagged/surfaced where relevant — e.g. on the Quote Builder for sales and other views (confirm exact surfaces on pickup)."
  - "[x] Each failure point shows the 'active in stock' quantity affected (how many units this exists on)."
  - "[x] Existing free-text failurePoints content is preserved / migrated (not silently dropped)."
  - "[x] The Quality section on the product info page lets staff add/edit/remove failure points and toggle each active."
out_of_scope: []
code_anchors:
  - path: ya-hire/common/models/StockQualityInfo.php
    note: current single free-text `failurePoints` field (L12/L32) — to be replaced by a one-to-many
  - path: ya-hire/backend/controllers/StockController.php
    note: failure-points render as textarea in buildQualitySectionContent (~L15278-15291) + save (~L20035) — rework to structured list
  - path: ya-hire/backend/views/stock/view-product-info.php
    note: product info Quality section UI
  - path: ya-hire/common/models
    note: "NEW model + table for failure points (one-to-many per product): name, note, photo, active, + link to derive affected in-stock qty"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - quality-management
  - stock
attention: null
version: 24
---

## Source
Kickoff M-001 (Ben's document). Second quick-win in Milestone MS-001; same Quality screen as T-0553.

## Problem
Failure points today are a **single free-text textarea** (`StockQualityInfo.failurePoints`). You can't record them individually, attach photos, mark them active/inactive, or know how many units of stock each affects.

## What's wanted
- **Multiple** failure points per product, each with **name, note, photo, and an Active on/off** toggle.
- **Active** ones can be flagged/surfaced elsewhere (Quote Builder for sales, and other views) so people can action them.
- Show the **"active in stock" quantity** affected by each failure point — Ben: "so we know how many of these failure points exist (and can action in various ways/from various views)".

## Open questions (confirm on pickup)
1. New one-to-many table shape (per product; fields: name, note, photo, active, created_by/at).
2. Exactly which views should surface active failure points (Quote Builder + which others?).
3. How "active in stock quantity affected" is derived — per product total, or tied to specific units/batches?
4. Photo storage — reuse the same approach as the quality-grade photos in T-0553.

## Depends on / relates
- Shares the Quality section with **T-0553**; coordinate the two so the section rework is consistent.

## Conversation

**2026-07-21 07:45 claude-code:** **Design finalised (pending Ben/Sandor confirm) — resolves the open questions.**

Decisions with Zsolt:
- **Qty affected = a manual number** per failure point (no per-unit/batch tracking — that'd be a separate big project).
- **Scope** per failure point: **Whole stock** (persists) vs **Specific batch** (one-off; note batch + qty). NB: the system has **no batch/lot tracking** (stock is one running qty via `StockLevels`/`StockTransactions`), so "batch" is a label + manual number, not auto-tracked.
- **Staleness handling:** on **restock** of a product, prompt review of its active *batch* failure points ("still present? update/clear"); whole-stock ones don't nag. Display guard: show `min(qtyAffected, current stock)`.
- **Surfaces:** Quote Builder only for now (Q2).
- Independent of T-0553 (shared Quality *section* / render function, but separate panels — coordinate edits to `buildQualitySectionContent`, and reuse one photo mechanism).

**Plan:**
1. New table `stock_quality_failure_points` (`stockID, name, note, photo, qtyAffected, scope, batchRef, active, lastReviewedAt, createdBy, createdAt`) + model `StockQualityFailurePoint` (manual DDL).
2. Rework the Failure Points panel → add/edit/remove list (name, note, photo, qty, scope, active toggle, needs-review flag).
3. AJAX add/update/delete/toggle (mirror T-0555/56); photo upload reuses the quality-image mechanism.
4. Migrate existing free-text `failurePoints` → one whole-stock point/product (keep old column until verified).
5. Restock review-nudge for batch points + staleness flag.
6. Quote-Builder flag of active points.
7. RBAC + docs.

**Next:** Zsolt to run the two process choices (scope + restock nudge) past Ben/Sandor before build.

**2026-07-21 07:48 claude-code:** **Correction:** the design decisions above are Zsolt's calls — **not pending any external sign-off**. Proceeding to build now; it'll be presented to Ben/Sandor for **review on completion**, not before. (Disregard the earlier "pending Ben/Sandor confirm / run past them before build" line.)

**2026-07-21 08:13 claude-code:** **Built (working tree, not committed).** Structured failure points replace the single free-text textarea.

**New / changed**
- Table `stock_quality_failure_points` + model `StockQualityFailurePoint` — DDL **and** legacy migration in `sql/stock_quality_failure_points.sql` (run once).
- `buildQualitySectionContent` → Failure Points panel is now a list (`renderFailurePointsList` / `renderFailurePointCard`): name, note, photo, qty affected, scope badge (Whole stock / Batch: ref), Active/Inactive, "Needs review" pill. Qty **display-capped to current stock**.
- CRUD: `actionSaveFailurePoint` (create/update + optional photo, reuses `uploadStockAsset` `stock-quality-info` bucket), `actionToggleFailurePoint`, `actionDeleteFailurePoint` — all re-render the section (`qualityDivBodyID`).
- `actionSaveQualityInfo` no longer writes the old `failurePoints` column; "Save Notes" now saves **Other Notes** only. Old column kept.
- Modal + JS in `views/stock/view-product-info.php`.
- **Quote Builder** (`views/sales/quote-builder.php`): read-only warning badge per item with active failure points (edit builder + read-only view), tooltip lists them, amber when stale. One indexed query keyed by the quote's item stock IDs.
- Docs: 3 action docs + `docs/features/stock-quality-failure-points.md`.

**Decision to note — "restock nudge" → time-based staleness.** Hire-stock qty is synced from Insphire; there is **no discrete restock event** in this module to hook. So staleness is time-based: `isStale()` flags active **batch** points not reviewed in 30 days (whole-stock never nag). Any add/edit/toggle stamps `lastReviewedAt` = reviewed. True auto-clear on batch turnover would need real batch/lot tracking (separate project).

**RBAC:** new routes `stock/save-failure-point`, `stock/toggle-failure-point`, `stock/delete-failure-point` — grant to the stock/quality role if it uses per-route grants (covered by `stock/*` / superuser).

**Test plan (human review):**
1. Run `sql/stock_quality_failure_points.sql`. Products with old free text show one "Imported note" whole-stock active card.
2. Quality tab → **+ Add**: name (required), qty, scope=Whole stock → Save → card appears.
3. Add scope=**Specific batch** → batch-ref field reveals; save → red "Batch: <ref>" badge.
4. Photo on add/edit → thumb shows, click opens full; replace works; delete removes asset.
5. **Edit** prefills modal; **Deactivate/Activate** greys/restores; **Delete** (confirm) removes.
6. Set qtyAffected > current stock → card shows "(entered N, capped to stock)".
7. Batch point with `lastReviewedAt` >30d back → amber **Needs review** pill.
8. **Save Notes** saves Other Notes only and does NOT blank the legacy `failurePoints` column.
9. No-quality-edit user: sees list read-only, no buttons; the 3 endpoints 403.
10. **Quote Builder**: quote with an affected product → red badge by item name (builder + read-only), tooltip lists points, amber if stale; unaffected / inactive-only product → no badge; quote loads normally.
11. Cross-impact: good/avg/bad quality images still upload/delete fine (shared re-render + same asset bucket).

**2026-07-21 08:22 claude-code:** **Correction to the earlier "synced from Insphire" note.** That was my inference from the code, not the live process — Zsolt confirms **InspHire is not used**. The codebase does contain a dormant InspHire-shaped `Stock`/`StkTrans` integration (an "Update Stock Qty From Insphire" button on `view-all-products`), which is what I wrongly read as the current stock sync. Disregard that line.

The design conclusion is unchanged and does **not** depend on it: there is **no discrete "stock received / restocked" event** in the app to hook the batch review-nudge onto, so staleness is **time-based** (active batch points not reviewed in 30 days → "Needs review"; any add/edit/toggle counts as a review). Feature doc wording fixed.

**Noted gap:** the app has no clean "stock received" moment. If one is introduced later, the batch nudge could fire on that event instead of the timer. Raising as a possible follow-up.

**2026-07-21 08:24 claude-code:** Follow-up spike raised: **T-0631 — "stock received / restocked" event for hire stock** (to discuss with Ben/Sandor). If that event gets built, the batch failure-point review-nudge here can fire on it instead of the 30-day timer.

**2026-07-21 08:50 claude-code:** **Quote Builder surfacing reworked** (feedback: icon-only, click→details, and must show on AJAX add — not just reload).

- Badge is now an **icon only** (⚠, amber if any point is stale); **click opens a modal** listing the item's active failure points (name, scope, qty, note, photo).
- Root cause of "only shows after reload": quote rows are rebuilt by **server-side string-builder blocks duplicated across add/update/remove** (no shared partial), which didn't include the badge. Fixed by applying the icon **client-side** — `fpDecorate()` fetches active points via new endpoint `SalesController::actionItemFailurePoints` (`sales/item-failure-points`) and re-runs on a `MutationObserver` over `#editItemMainDivID`, so it survives add/update/remove.
- New route `sales/item-failure-points` — sales grant / superuser (add explicitly if per-route). All files lint clean.

**2026-07-21 09:40 claude-code:** **Further Quote Builder refinements (post-review feedback).** All working-tree only, lint clean.

**1. Icon now appears on AJAX add — not just reload (both panels).**
Root cause: quote rows are rebuilt server-side by *several* duplicated builders with no shared partial, and the summary rows carried no stockID. Fixes:
- Added `data-stockid` to the read-only summary rows in **every** render path: full-page view (`quote-builder.php`), the two controller builders (`SalesController.php` ~7688 / ~8498), and — the one that was actually missing — the model builder used by add: `QuoteItems::addItemToQuote()` (`common/models/QuoteItems.php:2361`). Edit rows already had it.
- Client decoration (`fpDecorate`) now targets `data-stockid` directly on both `#editItemMainDivID` and `#qItemsDivID`, with a `MutationObserver` on both so it survives add/update/remove.

**2. Badge is icon-only + click-to-modal.**
Small ⚠ (amber if any point is stale); click opens a modal listing the item's active failure points (name, scope, qty, note, photo). Details fetched via new endpoint `SalesController::actionItemFailurePoints` (`sales/item-failure-points`).

**3. Add/Edit failure-point modal tidy-up** (product info Quality tab): consistent field spacing, clearer Active row ("show on the Quote Builder"), neater photo/replace layout.

**4. Quote Builder "Hire Items" column layout** (per request), applied across all render paths incl. accessories:
- Qty - Item → `col-xs-7 col-md-7 text-left`
- **UnitPrice → "Unit Price"**, `col-xs-2 text-right no-padding`
- Line Total → `col-xs-3` (kept its right padding to align with the inset values)
- Header cells given `no-padding` to match the item rows (Unit Price header floated `pull-right` to match the price values).

**Files touched this round:** `backend/views/sales/quote-builder.php`, `backend/controllers/SalesController.php`, `common/models/QuoteItems.php`, `backend/views/stock/view-product-info.php`.

**Extra test steps:**
- Add an item with an active failure point **without reloading** → ⚠ appears on both the edit builder and the right-hand summary; click either → modal.
- Check "Hire Items" columns line up (header vs values), including a quote with **package/accessories**.

**2026-07-21 09:52 claude-code:** **Failure-point image viewing improved.** Clicking a failure-point photo now opens a **lightbox modal** (image scaled to viewport) with an **"Open in new tab"** button for the original/full-size, instead of jumping straight to a new tab.

- **Quality tab** (`view-product-info.php`): new `#fpImageModalID` + `openFailurePointImage(url)`; card image onclick updated in `renderFailurePointCard` (`StockController.php`).
- **Quote Builder** (`quote-builder.php`): new `#fpImageModal` + `window.openFailurePointImage`; the failure-points modal's image onclick now opens it. Handled the Bootstrap 3 modal-on-modal stacking (z-index bump + restore `modal-open` on close) so the lightbox sits above the failure-points list.

All lint clean; JS syntax-checked. Working-tree only.

**2026-07-21 09:57 claude-code:** **Image-view follow-up (replaces the stacked-modal approach).**
- **Quote Builder:** the stacked/nested image modal caused a stuck backdrop after closing (double-modal bug). Reworked so the image now **swaps inside the existing failure-points modal** (Open in new tab + ← Back) — single modal, no backdrop issue.
- **Quality tab:** standalone image lightbox kept, but **shrink-wrapped to the image** (`display:inline-block; width:auto; max-width:92vw` — same technique as `/stock/stock-for-sale`'s `#imageViewerModalID`) so there's no oversized empty modal. Single modal there, so no stacking concern.

Lint + JS syntax checked. Working-tree only.

**2026-07-21 10:07 claude-code:** **Failure-point card layout redesigned** (Quality tab, `renderFailurePointCard`).

Moved from the stacked layout (tiny 60px thumb near the bottom) to a **media layout**:
- **Image left** — 120×120 framed thumbnail (`object-fit:cover`), click opens the lightbox.
- **Details right** — name + Scope/Active/Needs-review badges on top, then Qty affected and note. Details take full width if there's no photo.
- **Actions row** — Edit / Deactivate / Delete on their own row below a light divider, **right-aligned**.

Cosmetic only (`StockController::renderFailurePointCard`); lint clean, working-tree only.

---

**2026-07-21 10:49 — you**

qb updated with icons for items, click to view, view-product-info page done,

---

**2026-07-21 10:49 — you**

Records: docs updated (actionDeleteFailurePoint.md, actionSaveFailurePoint.md, actionToggleFailurePoint.md, stock-quality-failure-points.md); tech-session none-needed; status-note none-needed.

**2026-07-21 11:25 claude-code:** **Shipped.** Committed and deployed live by Zsolt. Sales team + Ben/Sandor notified by email.

Final tweaks since the last update:
- **Quote Builder modal** now has a short explainer strip under the title: *"Failure points are known quality issues recorded against this item, so the team is aware of them when it's on a quote. Only active ones are highlighted."* (centered).
- **Docs** updated to drop references to the one-off migration SQL (Zsolt applied the table + legacy migration, then removed the script) — docs now note it as "already applied" and record the eventual old-column cleanup.

**Deployment notes:** table + legacy free-text migration already applied on prod; RBAC needs nothing extra (management-tab access implies `stock/*`); old `StockQualityInfo.failurePoints` column retained as a safety-net until verified, then can be dropped (+ remove the two model lines).

Ready for final human sign-off/close.

**2026-07-22 10:11 claude-code:** **Failure-points card polish** (working tree — deploy alongside the current quality batch; cosmetic only, no logic change):
- Scope + status pills now sit **on one line** (`nowrap`).
- **Tighter padding** — card `6px`, panel column `8px`, image/details gap `10px`.
- Removed the **redundant inner box** (border/background/padding) on the list — now one clean panel with cards inside.
- Fixed the list to **scroll** (it was expanding): `max-height:360px` + `scrollbar-gutter:stable`, with the scrollbar pulled **flush to the panel's right edge** (negative margin cancels the panel padding).
