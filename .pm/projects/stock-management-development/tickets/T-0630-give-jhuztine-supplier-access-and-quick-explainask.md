---
id: T-0630
title: Give Jhuztine supplier access and quick explain/ask what she needs
type: feature
state: triaged
priority: p2
created: 2026-07-21T06:21:55Z
updated: 2026-07-21T07:19:17Z
project: stock-management-development
section: null
parent: null
milestone: MS-001
children: []
order: 2048
reporter: null
assignee: null
acceptance_criteria:
  - have a talk
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
version: 2
---

## Problem

_What's wrong / what's needed?_

## Context

## Design notes

## Conversation

**2026-07-21 07:19 claude-code:** **Done: supplier access granted + explainer sent (2026-07-21).**

**Access:** created a scoped **`Suppliers Permissions`** in the RBAC manager (bundles the supplier routes only — `/stock/suppliers`, `/stock/supplier-view` + their add/edit/delete item/contact/order sub-actions + `/ajax/all-items-search`) and assigned it to **Jhuztine (user 687)**. Deliberately *not* the broad `/stock/*`, so she only gets Suppliers.
- Menu: Suppliers link not added yet — she reaches it via the page search / `/stock/suppliers`; it'll be added to the nav when the menu tidy-up reaches Stock.

**Explainer email sent to Jhuztine** covering: where to find it, and each section — Details, Contacts, Items (incl. the new inline pencil-edit for reference/price), Orders — plus that everything saves instantly. Also asked her what she needs (day-to-day tasks, most-used info, anything currently in spreadsheets/email, anything missing/awkward). Added: **Sandor currently manages this page** — she should coordinate changes with him; and flagged that a **new orders-overview page (search/filter) is in development**.

**Open:** awaiting Jhuztine's reply on what she needs — will capture any requests here and spin up follow-up tickets if needed. (This satisfies the ticket's "give access + explain"; the "ask what she needs" half is pending her response.)
