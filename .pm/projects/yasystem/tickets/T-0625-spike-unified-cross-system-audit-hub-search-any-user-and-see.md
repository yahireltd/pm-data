---
id: T-0625
title: "Spike: unified cross-system audit hub — search any user and see everything they did/changed, system-wide"
type: spike
state: triaged
created: 2026-07-20T12:53:30Z
updated: 2026-07-20T12:53:30Z
project: yasystem
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: backlog idea
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - audit
  - reporting
  - spike
  - cross-system
attention: null
version: 1
---

## Problem
Audit trails are scattered across subsystems, each with its own schema and its own (or no) viewer. There is **no single place** to see activity across the system, and — crucially — **no way to pick a user and see everything they did/changed everywhere**.

Known audit sources today (non-exhaustive):
- Sale-stock adjustments — `stock_sale_adjustments` (T-0555).
- Customer merges — `customer_merge_log` + `ya_customers_linked_emails` (who merged which account, when).
- Price changes — `DiscountmatrixLog`.
- Stock movements — `stock_transactions`.
- RBAC/permission changes — `auth_item` / `auth_item_child` (mdm).
- Ad-hoc `addedBy`/`updatedBy`/`createdBy`/`raisedBy` stamps scattered across many models.

## What's wanted (to investigate)
A **unified audit hub**: one filterable/searchable view over all audit sources.
- Filter/search by **type**, **date range**, **entity/item**, and **user**.
- **User-centric view (headline requirement):** search for a user and get **all of their actions across the entire system** — what they created / changed / deleted / merged / adjusted / re-priced etc., in one timeline, regardless of which subsystem recorded it.

## Spike questions to answer
1. **Inventory** every audit source + its schema (and where "who did it" lives — many are just `*By` columns, not true logs).
2. Define a **common normalised model** (e.g. *when / who / entity type + id / action / detail / before→after*) that each source can map into.
3. **Approach:** adapters that project each source into the common model on read, vs. a central write-time audit log, vs. just cross-linking the individual reports. Trade-offs + rough effort for each.
4. **Coverage gaps:** which meaningful actions aren't logged at all today (so a user's history would be incomplete) — and whether/how to backfill logging.
5. **Feasibility of the user-centric timeline** specifically (can we reliably resolve "userID → all actions" across sources?).
6. Scale / performance / retention considerations.

## Out of scope
- Building the hub — this is research only.
- The sale-stock-only adjustments report (that's the concrete Tier-1 piece, tracked separately).

## Deliverable
A short written recommendation: is a unified hub worth building, which approach, the logging gaps to close first, and a rough sizing — enough to decide whether to spin up a real project/ticket.