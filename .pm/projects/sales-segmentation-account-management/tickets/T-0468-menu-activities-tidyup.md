---
id: T-0468
title: Menu / Activities Tidyup
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:48:35Z
updated: 2026-07-15T10:18:45Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 3072
reporter: null
assignee: null
acceptance_criteria:
  - Daily-driver pages confirmed (from the page-usage reports + Nathan) and placed on the Top Level menu.
  - Remaining pages categorised into a submenu or top-level alphabetically; menu links removed for pages not used / very rarely used.
  - The menu is built from the usage CSV we compile (top-used → top-level; rarely/never used → drop or submenu).
  - Menu search is released to all users.
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
version: 3
---

## Source
Sales Phase 1 — "Question prep for Nathan" doc (Menu / activities tidy).

## Scope
- Confirm the main daily-driver pages and put them on the **Top Level** menu — driven by the page-usage reports (sales + management, last 60 days).
- **Build the menu from the usage CSV** we compile (top-used = top-level; rarely/never-used = drop or submenu).
- Categorise the remaining pages — collapse into a submenu or top-level, alphabetically.
- Remove menu links for pages not used / very rarely used.
- **Release the menu search to all users** (currently limited) — folded in here.

## Skippable deep-dive
- Activities can be edited now, but that will change (it affects process management — they can't be adjusted). Is there a quicker way? e.g. Call / Email / Meeting / Task, then pick the exact activity, with call/email/meeting shown top-level via an icon. Better as its own deep-dive; gated on the activities-process direction.

## Needs (Nathan meeting)
- Confirm the top-level set with Nathan; agree what merges / gets dropped.
- Activities-process direction (if the process changes, activities change).

## Inputs
- Page-usage reports: `sales-page-usage-report` + `management-page-usage-report` (last 60 days) — the ranked usage that drives the top-level vs drop/submenu decisions.