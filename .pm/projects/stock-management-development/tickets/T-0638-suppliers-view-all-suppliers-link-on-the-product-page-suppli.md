---
id: T-0638
title: 'Suppliers: "View All Suppliers" link on the product page suppliers section'
type: chore
state: done
created: 2026-07-22T07:29:28Z
updated: 2026-07-22T08:35:41Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: build session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] The product info page's Suppliers section header has a 'View All Suppliers' button that opens /stock/suppliers in a new tab."
  - "[x] It's styled distinctly (blue, darkens on hover) so it reads as a page link, not an edit action."
  - "[x] It's always visible — including when no suppliers are linked, and on read-only views."
  - "[x] The existing per-supplier 'View' buttons and 'Add Supplier' edit are unchanged."
out_of_scope: []
code_anchors:
  - path: ya-hire/backend/controllers/StockController.php
    note: renderSection call for the suppliers section (~L12800) — added the header action (8th $actions param); renderSection (~L16499) renders $otherActions before the edit button
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260722-0731
    started: 2026-07-22T07:31:33Z
    status: completed
    policy_ack:
      branch: Stock-Management-Development
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-22T07:31:33Z
    ended: 2026-07-22T07:32:00Z
    summary: Added a "View All Suppliers" button on the product page's Suppliers section that opens the full suppliers list in a new tab. Before, you could only jump to an individual linked supplier — there was no route to the overall suppliers page (which isn't in the main menu yet). This makes the suppliers page easy to reach, including when a product has no suppliers linked.
    test_plan: |-
      1. Open a product → Suppliers section → the header shows a blue "View All Suppliers" button with an external-link icon (to the right, next to "Add Supplier").
      2. Hover → the button darkens.
      3. Click → opens the suppliers list page (/stock/suppliers) in a new tab.
      4. Open a product with no linked suppliers → the button is still visible.
      5. On a read-only view → the button is still visible and works.
      6. Cross-impact: the per-supplier "View" buttons and the "Add Supplier" edit still work as before.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - suppliers
  - stock
attention: null
version: 10
---

## What
Small addition to the **product info page → Suppliers section**: a **"View All Suppliers"** button in the section header that opens the full suppliers list page (`/stock/suppliers`) in a new tab.

## Why
Each linked supplier already has a per-row "View" (→ its own supplier-view), but there was no route to the **overall** suppliers page — handy given Suppliers isn't in the main nav yet, and useful from the empty state ("No suppliers linked").

## Built (working tree, not committed)
- `StockController::renderSection` call for the `suppliers` section (~L12800): added a header **action** — an `<a>` to `/stock/suppliers`, `target="_blank"`, distinct blue (`#5b9bd5`) that darkens to `#0a5eb8` on hover, external-link icon, always-enabled. Lint clean.

## Conversation

**2026-07-22 07:32 claude-code:** Run run-20260722-0731 completed — Added a "View All Suppliers" button on the product page's Suppliers section that opens the full suppliers list in a new tab. Before, you could only jump to an individual linked supplier — there was no route to the overall suppliers page (which isn't in the main menu yet). This makes the suppliers page easy to reach, including when a product has no suppliers linked.

---

**2026-07-22 08:35 — you**

done
