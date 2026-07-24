---
id: T-0652
title: Suppliers area UI modernisation (Suppliers, All Orders, Supplier View)
type: chore
state: done
created: 2026-07-23T12:55:09Z
updated: 2026-07-24T07:52:22Z
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
  channel: chat 23 Jul
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Suppliers, All Orders and Supplier View pages share a consistent modern card design (rounded cards, soft shadow, clean headers)."
  - "[x] Purchasing stat cards on Suppliers + All Orders (+ per-supplier on Supplier View); order cards link through to All Orders with the right filter (status / overdue / supplier)."
  - "[x] Add Contact, Link Product, Create Order and Add Order Item are modals opened from panel-header buttons; the panels themselves just show the lists."
  - "[x] Consistent tables (borderless + hover), pill count badges, pill action buttons, and plain-text empty states across the three pages."
out_of_scope:
  - Batch numbers on order items (separate ticket T-0651).
  - Functional/behavioural changes to orders/contacts/items beyond layout + modal wrapping.
code_anchors:
  - path: backend/views/stock/suppliers.php
    note: "DONE: 6 stat cards (order cards link to all-orders), .sp-* card table, initials avatars, count pills, pill row buttons; blank icons fixed (folder-open, check-circle)"
  - path: backend/views/stock/all-orders.php
    note: "DONE: .ao-* card + header, stat cards that double as status filters (?status/?overdue), quick-search, status-striped expandable rows; Delivered icon fixed (check-circle); FILTERED SUMMARY — when a server-side filter is active, a header (filter label + Clear) over a row of shorter cards mirroring the top 5 (Orders/On The Way/Overdue/Delivered/Total Value) for the filtered subset, computed in-view from loaded $orders (no extra query)"
  - path: backend/views/stock/supplier-view.php
    note: "DONE: .sv-* header + per-supplier stat cards, card panels, modern .svo-* order panels, borderless .sv-table Contacts/Items, right-aligned pill row buttons, blank icons fixed; ALL 8 modals modernised (page-scoped .modal-* + form-control overrides, pencil title icons via span, rounded/aligned Save vs Cancel); Edit Item help-text realigned to Link Product copy"
  - path: backend/views/stock/partials/_supplier-order-panel.php
    note: "DONE: rewritten as .svo-panel card (left status stripe, status pill, borderless items table); JS contracts preserved; also AJAX-rendered by StockController"
  - path: backend/views/stock/partials/_supplier-contact-row.php
    note: "DONE: bare icon actions -> text-only sv-hbtn Edit/Delete pills, right-aligned; edit-*/delete-* classes + data-* preserved"
  - path: backend/views/stock/partials/_supplier-item-row.php
    note: "DONE: bare icon actions -> text-only sv-hbtn Edit/Delete pills, right-aligned; onclick=deleteSupplierItem(this) preserved"
  - path: backend/controllers/StockController.php
    note: shared supplierOrderStats() helper (suppliers + all-orders); actionAllOrders supports ?overdue=1 and ?supplier_id (pre-existing; unchanged by the UI passes)
  - path: docs/features/stock-suppliers-ui-modernisation.md
    note: "DONE: feature doc capturing the whole refresh (pages, order panels, tables, row buttons, blank-icon fix, modals). TODO: add the All Orders filtered-summary strip"
relates:
  - T-0557
  - T-0651
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260724-0427
    model: claude-opus-4-8
    started: 2026-07-24T04:27:38Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-24T04:27:38Z
    progress:
      - at: 2026-07-24T04:46:43Z
        note: |-
          Remaining item 3 (modernise individual order panels) done — working tree, lint clean.

          Rewrote `partials/_supplier-order-panel.php` from the old status-tinted Bootstrap `panel panel-default` into a modern card matching the All Orders aesthetic:
          - White card, rounded, soft shadow, left status-colour stripe (::before) instead of a fully-tinted heading.
          - Clean clickable header: chevron caret + bold order number + status pill (tinted with the status colour); dates shown as small labelled Ordered/Arrival; Edit Order button restyled to the shared `sv-hbtn sv-hbtn-edit` pill (also knocks out part of remaining item 2).
          - Expanded body: labelled summary strip (Order/Arrival/Subtotal/Grand Total), notes, and a borderless + hover Order Items table (`.svo-items`) replacing `table-bordered table-condensed`.

          Preserved every JS contract: root `#order-panel-<id>` + `.supplier-order-panel` class (count + replaceWith), `.edit-order-btn` data-order/data-supplier (+ stopPropagation), `.panel-collapse` with `data-order-id`, and `#order-collapse-icon-<id>` chevron toggle. Same partial is AJAX-injected by StockController (add/update/delete order + delete order-item), so all those paths get the new look with no controller change.

          Replaced the old `.order-panel-heading` / `.order-heading-*` / unused `.panel-heading-purple` CSS in supplier-view.php with the new `.svo-*` styles. Both files `php -l` clean; grep confirms no stray references to the removed classes.

          Still open on this ticket: item 1 (Contacts + Items tables → borderless+hover), item 2 (row Edit/Delete buttons → pill style), item 4 (Edit Item modal help-text copy).
      - at: 2026-07-24T05:15:45Z
        note: |-
          Remaining items 1 & 2 done — working tree, lint clean.

          Item 1 (Contacts + Supplier Items tables → borderless+hover):
          - Both `#supplier-contacts-table` and `#supplier-items-table` changed from `table table-bordered table-striped` to a new `sv-table` class.
          - Extended the existing `.svo-items` CSS selectors to also cover `.sv-table` (one shared borderless+hover definition: uppercase label header row, thin `#f0f2f4` row dividers, subtle `#f7f9fb` hover, no grid lines) — so the two panel tables and the order-items table now read as one system.

          Item 2 (row Edit/Delete buttons → pill style; Edit Order button already done in the panel pass):
          - Converted the bare `fa-pen-to-square`/`fa-trash-xmark` icon actions in `partials/_supplier-contact-row.php` and `partials/_supplier-item-row.php` into text pills: `sv-hbtn sv-hbtn-edit` ("Edit", blue) + new `sv-hbtn-del` ("Delete", red), wrapped in a centered `.sv-row-actions` flex span. Added `.sv-hbtn-del` (red soft pill matching the suppliers page `.sp-btn-del`) and `.sv-row-actions` to supplier-view.php CSS.
          - Preserved every JS hook: classes `edit-supplier-contact`/`delete-supplier-contact`/`edit-supplier-item`/`delete-supplier-item`, all data-* attributes, row ids, and the inline `onclick="deleteSupplierItem(this)"`. Handlers are class-delegated on the table ids and only read data-attributes / remove rows by id, so `<i>`→`<button>` is behaviourally identical. Rows re-rendered via AJAX (add contact/item) inherit the new look with no controller change.

          All 5 touched files `php -l` clean. No schema/behaviour changes.

          Remaining on this ticket: item 4 only (Edit Item modal help-text copy alignment).
      - at: 2026-07-24T05:38:54Z
        note: |-
          Review-feedback tweaks from Zsolt (screenshots) — working tree, lint clean.

          1. Row Edit/Delete pills made compact: added `.sv-row-actions .sv-hbtn { padding:3px 9px; font-size:11px; gap:5px; }` so row actions are smaller than the header pills.
          2. Supplier Items column widths rebalanced (Product was crowding Supplier Reference into a wrap): Product 40%→24%, Supplier Reference 20%→46%, Price 15%→10%, Actions 10%→20%.
          3. Fixed blank stat-card / button icons across the whole suppliers area — root cause: FontAwesome outline (`-o`) glyphs need the regular-weight font, which isn't loaded (only solid is), so they rendered empty. Swapped to solid variants:
             - supplier-view: Orders `fa-file-text-o`→`fa-file-text`, Delivered `fa-check-circle-o`→`fa-check-circle`, Edit Supplier button `fa-pencil-square-o`→`fa-pencil`.
             - all-orders: Delivered `fa-check-circle-o`→`fa-check-circle`.
             - suppliers: Open Orders `fa-folder-open-o`→`fa-folder-open`, Delivered `fa-check-circle-o`→`fa-check-circle`.
             Grep confirms zero `-o` outline icons remain across the three pages + partials.

          Touched: suppliers.php, supplier-view.php, all-orders.php (+ the two row partials earlier). All `php -l` clean. No schema/behaviour changes.

          Ticket status: items 1, 2, 3 done; item 4 (Edit Item modal help-text copy) still open.
      - at: 2026-07-24T05:43:22Z
        note: |-
          Row-button follow-up (Zsolt): match the Suppliers-page .sp-btn exactly + right-align + narrower Actions.

          - Row Edit/Delete buttons: removed the fa icons (the icon width was what made them read bigger than the reference) and set `.sv-row-actions .sv-hbtn { display:inline-block; padding:5px 12px; font-size:12px; }` to mirror `.sp-btn` exactly. Colours already matched (sv-hbtn-edit == sp-btn-view, sv-hbtn-del == sp-btn-del). Now text-only "Edit"/"Delete", identical to the Suppliers page.
          - Right-aligned: `.sv-row-actions` → justify-content:flex-end; both row partials' Actions `<td>` and both tables' Actions `<th>` switched text-center → text-right.
          - Supplier Items columns: Actions 20%→14%, Supplier Reference 46%→52% (Product 24%, Price 10% unchanged) so long SKUs get more room.

          Behaviour unchanged — same edit-*/delete-* classes, data-* attrs, and inline onclick. All touched files php -l clean.

          Ticket: items 1,2,3 done; item 4 (Edit Item modal help-text) still open.
    ended: 2026-07-24T05:51:13Z
    summary: |-
      Finished modernising the suppliers area so the Suppliers, Supplier View and All Orders screens all look like one consistent purchasing dashboard instead of three differently-styled pages. In this run we tackled the last rough edges on the Supplier View screen: the dated per-order boxes were rebuilt as clean cards with a colour-coded status stripe; the Contacts and Supplier Items lists were given the same tidy modern table style as everywhere else; the little Edit/Delete controls on each row became proper buttons that match the rest of the page (same size, shape and right-hand placement); the Supplier Items list was re-proportioned so long supplier product descriptions have room to show; a couple of summary tiles that were showing blank icons (Orders, Delivered, Open Orders) now show their icons again; and the "Edit Item" pop-up help text was reworded to match the "Link Product" pop-up so staff aren't given two slightly different explanations of the same thing.

      Why it matters: the suppliers area is used daily to check orders and manage supplier products. The inconsistent styling and the blank icons made it look unfinished and made a few things (like long product references) harder to read. Left as-is it would have stayed visually disjointed and slightly confusing; now it reads as one coherent, polished area. No data, permissions or behaviour changed — this is presentation only.
    test_plan: |-
      All changes are visual/copy only, working tree, no schema or behaviour changes. Verify on the **Supplier View** page for a supplier that has orders, contacts and linked items (plus a quick cross-check of Suppliers and All Orders).

      1. **Order panels (Supplier View → Orders):**
         - Each order shows as a white rounded card with a left colour stripe matching its status (Pending=amber, Ordered=blue, In Transit=teal, Received=green, Cancelled=red).
         - Header shows chevron + bold order number + a status pill, with Ordered/Arrival dates; an "Edit Order" pill sits on the right.
         - Click the header → it expands to a labelled summary (Order Date / Arrival / Subtotal / Grand Total), Notes (if any), and a clean borderless Order Items table. Click again → collapses; chevron flips.
         - Click "Edit Order" → the edit modal opens (it must NOT also toggle the collapse). Change something and save → the panel re-renders in the new style. Add an order item and delete an order item from the modal → panel re-renders correctly each time.

      2. **Contacts + Supplier Items tables:** both render in the new borderless + hover style (no heavy gridlines/zebra). Headers are the small grey uppercase labels.

      3. **Row Edit/Delete buttons:** text-only "Edit" (blue) / "Delete" (red) pills, right-aligned in the Actions column, and the SAME height as the header buttons (Add Contact, Link Product, Create Order, Add Order Item, Edit Supplier).
         - Contacts: Edit opens the contact modal pre-filled; Delete asks to confirm then removes the row.
         - Items: Edit opens the item modal pre-filled; Delete asks to confirm then removes the row.
         - Add a new contact and a new item → the freshly inserted rows show the same new pill buttons (they're AJAX-rendered from the same partials).

      4. **Supplier Items columns:** Supplier Reference has more width now; long descriptions/SKUs wrap less. Product/Price/Actions still readable.

      5. **Stat-card icons (previously blank):** Supplier View "Orders" and "Delivered" tiles show icons; the "Edit Supplier" button shows a pencil. On the **Suppliers** page, "Open Orders" (folder) and "Delivered" (check) tiles show icons. On **All Orders**, the "Delivered" tile shows an icon. (Root cause was FontAwesome outline `-o` glyphs needing a font weight that isn't loaded; swapped to solid.)

      6. **Edit Item modal copy:** open the pencil on a Supplier Item → the help text under "Link to YA Product (optional)" now reads like the Link Product modal ("Optional — link… Not a YA product? Leave this blank; the Supplier Reference below is what identifies the item."), not the old "rely on the supplier reference below".

      7. **Cross-impact:** the order panel markup is shared (`partials/_supplier-order-panel.php`) and is also re-rendered by StockController over AJAX (add/update/delete order, delete order-item) — all covered in step 1. The row partials (`_supplier-contact-row.php`, `_supplier-item-row.php`) are used both on initial load and after AJAX add — covered in step 3. No controller/model changes.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: stock-suppliers-ui-modernisation.md
labels:
  - suppliers
  - orders
  - stock
  - ui
attention: null
version: 20
---

## Goal
Modernise the whole suppliers area so Suppliers, All Orders and Supplier View read as one consistent purchasing dashboard. Grew out of T-0557 (All Orders) — tracked separately since it's a broad UI refresh, not the "View all orders" feature itself.

## Done so far (2026-07-23, working tree, lint clean)
- **Suppliers page**: 6 stat cards (Suppliers / Open / On The Way / Overdue / Delivered / Total Spent) with £ + items/units/avg sub-lines; order cards link to All Orders. Card-based table, initials avatars, colour count pills, pill action buttons, paginated Supplier Items (25/pg), centered numeric/action columns.
- **All Orders page**: card styling + header; stat cards that double as **status filters** (All / On The Way / Overdue / Delivered / Total Value) via `?status=` + new `?overdue=1`; quick-search; left status-colour stripe per row; expandable detail matching the order panel.
- **Supplier View page**: Bootstrap panels restyled to cards; modern header; per-supplier stat cards (link to `all-orders?supplier_id=`); Supplier Details slimmed to Email/Phone (50/50) + Notes; Products Linked moved to a top card, Contacts count shown in its panel header; **Add Contact, Link Product, Create Order, Add Order Item all converted to modals** with header buttons (counts refresh + modals close on save); plain-text empty states.
- **Shared**: `supplierOrderStats()` controller helper; fixed a blank Product column bug in the order-panel partial.

All read-only / layout only. **No schema changes.**

## Remaining — all DONE (2026-07-24, run run-20260724-0427, committed 07660975)
- ✅ Contacts + Items tables on Supplier View unified to the borderless + hover style (`.sv-table`, shared CSS with the order-items table).
- ✅ Row Edit/Delete + the "Edit Order" button matched to the pill button style (text-only, right-aligned, heights matched via `.sv-hbtn` line-height).
- ✅ Individual order panels modernised — rewritten as `.svo-*` cards with a left status stripe + status pill (the old purple-accented panels are gone).
- ✅ Edit Item modal help text realigned to the corrected Link Product copy.
- ✅ **Plus (same run):** blank stat-card icons fixed across all three pages (FontAwesome outline `-o` → solid), and **all 8 Supplier View modals modernised** (page-scoped chrome/field styling, pencil title icons, rounded Save/Cancel) — see the 2026-07-24 06:52 comment. Feature doc: `docs/features/stock-suppliers-ui-modernisation.md`.

## Source
Chat 23 Jul (Zsolt) — iterative design pass alongside T-0557.

## Conversation

**2026-07-24 05:51 claude-code:** Run run-20260724-0427 completed — Finished modernising the suppliers area so the Suppliers, Supplier View and All Orders screens all look like one consistent purchasing dashboard instead of three differently-styled pages. In this run we tackled the last rough edges on the Supplier View screen: the dated per-order boxes were rebuilt as clean cards with a colour-coded status stripe; the Contacts and Supplier Items lists were given the same tidy modern table style as everywhere else; the little Edit/Delete controls on each row became proper buttons that match the rest of the page (same size, shape and right-hand placement); the Supplier Items list was re-proportioned so long supplier product descriptions have room to show; a couple of summary tiles that were showing blank icons (Orders, Delivered, Open Orders) now show their icons again; and the "Edit Item" pop-up help text was reworded to match the "Link Product" pop-up so staff aren't given two slightly different explanations of the same thing.

Why it matters: the suppliers area is used daily to check orders and manage supplier products. The inconsistent styling and the blank icons made it look unfinished and made a few things (like long product references) harder to read. Left as-is it would have stayed visually disjointed and slightly confusing; now it reads as one coherent, polished area. No data, permissions or behaviour changed — this is presentation only.

**2026-07-24 06:52 claude-code:** **2026-07-24 — Follow-on after review sign-off of the panels/tables/buttons: modernised the Supplier View modals + wrote a feature doc.**

After the four "Remaining" items were done and this ticket moved to review, Zsolt asked to also modernise the pop-up dialogs so they match the refreshed page. Done (working tree, lint clean, still no schema/behaviour changes):

- **All eight Supplier View modals** (Add Contact, Link Product, Create Order, Add Order Item, Edit Contact, Edit Item, Edit Supplier, Edit Order) now share one modern style: borderless rounded card with a soft shadow, a clean white header with a subtle close ✕, a light footer bar, and tidy form fields (small uppercase labels + rounded inputs with the blue focus ring). Styling is **scoped to this page** so no other screens' pop-ups change.
- Edit dialogs gained a pencil header icon to match the Add/Create ones. For the three whose titles are updated live (they show the order number / contact / item name), the dynamic text was moved into its own span so the icon isn't wiped when the name is filled in.
- Footer buttons: Cancel/Close is a soft outlined pill and the green Save buttons were rounded and vertically aligned to sit level with Cancel (a couple of small alignment fixes from Zsolt's eye).

**Docs:** captured the whole refresh (pages, order panels, tables, row buttons, the blank-icon fix, and the modals) in a new feature doc — `docs/features/stock-suppliers-ui-modernisation.md`.

This modal pass was requested directly and not tracked as its own ticket (Zsolt's call). Everything is under this ticket's scope of "one consistent modern suppliers area", so noting it here for the record ahead of final close.

**2026-07-24 (committed):** Zsolt committed the work as `07660975` "Modernize stock suppliers UI views" (7 files incl. the feature doc). Ticket body's Remaining list updated to reflect completion. Ready for final human confirm + close.

---

**2026-07-24 07:33 — you**

design updated

**2026-07-24 07:52 claude-code:** **2026-07-24 — Follow-on (Zsolt): filtered-order summary on the All Orders page.**

When you jump from a supplier into All Orders (or click one of the status cards), the page is now filtered to that subset — but the big cards at the top still showed the company-wide totals, so there was no at-a-glance total for what you were actually looking at.

Added a **filtered summary** that appears only when a filter is active (supplier, status, overdue, date or order-number) and disappears again on the plain All Orders view:
- A small header line showing what's filtered (e.g. "Supplier: Flexfurn") on the left and a **Clear filter** link on the right.
- Below it, a row of shorter cards **mirroring the top cards for just the filtered orders** — Orders (count + £ total), On The Way, Overdue (turns red when there are any), Delivered, and Total Value.

So you immediately see, say, "this supplier: 2 orders, £119,045, all delivered" without doing the maths yourself. It's worked out from the orders already on screen (no extra database work) and is display-only — no data or behaviour changed.

Small caveat: the quick-search box narrows the visible rows on the client side and doesn't recalculate this summary (the summary reflects the main filter, same as the existing "N shown" count). Easy to wire up live if wanted.

This All Orders enhancement relates to T-0557 (the original "View all orders" feature); logging it here since it was built as part of this suppliers-area design pass. Still uncommitted / working tree.
