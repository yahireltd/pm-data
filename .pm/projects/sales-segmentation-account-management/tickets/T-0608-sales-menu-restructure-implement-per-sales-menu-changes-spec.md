---
id: T-0608
title: Sales menu restructure — implement per "sales menu changes" spec (keep/remove/move + new submenus)
type: feature
state: triaged
created: 2026-07-17T06:37:50Z
updated: 2026-07-17T07:23:30Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: sales menu changes.xlsx
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - "Sales menu rebuilt to the target structure: removals, keeps, moves and new submenus (My Accounts, Customer related, Sales Management) applied per the spec."
  - "Removed from menu: Leads, Key Account Requests, Add New Customer, Account & ZC Last Year Contracts, Rating Search, Customer Scoring, Account Segmentation, Sales Person Q/C (after verifying its contract logic is covered elsewhere)."
  - "Moved: Delivery Capacity → Logistics; Payment Related (Unpaid Contracts / Todays Payments / Manual Payment) → Accounts (overlap checked); Pending Quotes → My Accounts; Sold Items and Manage Activities into their new submenus."
  - Website Quotes replaced by the Contract Checking page once built (sequenced with T-0467/T-0468).
  - New Venue links added (Q/C Unassigned Venues + the 3 Venue DB links) to the Venue menu.
  - "RBAC verified: regular sales users cannot see everyone's contract values (Contracts by conversion date, Sales Person Q/C)."
out_of_scope: []
code_anchors: []
relates:
  - T-0468
  - T-0467
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
---

## Problem
Concrete restructure of the **Sales** menu, per the "sales menu changes" spreadsheet — remove dead pages, move some to other areas (logistics/accounts), and introduce new submenus. This is the implementation detail behind the T-0468 menu tidy.

## Target changes (from the spreadsheet)

### Sales (top-level container) — existing items
- **Website Quotes** (`/sales/website-quotes`) — **replace with Contract Checking** once that page is built (see T-0467/T-0468).
- **Leads** (`/sales/leads`) — **remove**.
- **Delivery Capacity** (`/sales/delivery-capacity`) — **move to Logistics**.
- **Smartest View** (`/sales/smartest-view`) — **keep**.
- **Contracts by conversion date** (`/sales/contracts-by-conversion-date`) — **keep**. ⚠️ Check that regular sales **can't see everyone's contract values**.
- **Key Account Requests** (`/sales/key-account-requests`) — **remove**.
- **Ledger** (`/sales/ledger`) — **keep**.
- **Add New Customer** (`/sales/add-customer`) — **not needed** (remove).
- **Account & ZC Last Year Contracts** (`/customers/contract-report`) — **remove**.
- **Sold Items** (`/sales/sold-items`) — **moved from top level** (into a submenu).

### My Accounts — **add to menu** (submenu)
- **Pending Quotes** (`/sales/pending-quotes`) — **move from Misc**.
- **Customer Scoring** (`/sales-scores/index`) — **remove from menu**.
- **Account Segmentation** (`/customers/account-segmentation`) — **remove from menu**.

### Payment Related (container) — **move all to Accounts** (check for overlap)
- Unpaid Contracts (`/contracts/unpaid-contracts`)
- Todays Payments (`/payments/todays-payments`)
- Manual Payment (`/contracts/manual-payment`)

### Misc Sales (container)
- **Rating Search** (`/sales/rating-search`) — **remove**.
- **Long Term Hire Prices** (`/sales/long-term-hire-prices`) — **keep**.
- **Sales Person Q/C** (`/sales/sales-person-qc`) — **remove**, BUT note it does get the contracts right (re Ledger / Smartest View — verify before removing).
- **Manage Activities** (`/sales/manage-activities`) — **move out of Misc** into the new Sales Management submenu.

### Customer related — **new submenu**
- Discounts And Commissions (`/sales/discounts-and-commissions`)
- VIP Customers (`/sales/vip-customer-list`)
- Account List (`/sales/account-list`)

### Venue related
- **Q/C Unassigned Venues** (`/sales/qc-unassigned-venue`) — add to the Venue top menu.
- Add these 3 Venue DB links: `/venue-db/view-venue`, `/venue-db/venue-list`, `/venue-db/delivery-addresses`.

### Sales Management — **new submenu**
- Dashboards etc (TBC)
- Merge Accounts / Merge Cash
- Commission Thresholds
- Manage Activities (moved from Misc, above)

## Notes / cross-impact
- **Contract Checking** page is a prerequisite for retiring Website Quotes (T-0467/T-0468).
- **Delivery Capacity → Logistics** and **Payment Related → Accounts** are cross-area moves — coordinate with those owners and check for overlap/duplication.
- The contract-value visibility check (Contracts by conversion date, and Sales Person Q/C) is an RBAC concern — verify regular sales roles can't see others' contract values.
