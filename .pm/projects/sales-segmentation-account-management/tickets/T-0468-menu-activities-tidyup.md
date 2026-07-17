---
id: T-0468
title: Menu / Activities Tidyup
type: chore
state: triaged
priority: p2
created: 2026-06-23T11:48:35Z
updated: 2026-07-17T08:05:07Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 3072
reporter: null
assignee:
  kind: human
  name: Zsolt
acceptance_criteria:
  - "[x] Menu rebuilt from the CSV: constantly-used pages on Top Level, the rest in submenus / alphabetical."
  - "[x] Menu links for unused/rarely-used pages removed."
  - Menu search released to all users.
out_of_scope: []
code_anchors: []
relates:
  - T-0467
  - T-0608
  - T-0609
  - T-0610
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 12
---

## Problem
The menu/navigation needs tidying: constantly-used pages should be top-level, the rest organised into submenus, and dead links removed. Menu search should be available to everyone.

## Design notes (from M-010)
- Build the menu structure from the CSV Zsolt prepared (rbac-menu-tree.csv).
- Put the main, constantly-used pages on the **Top Level**.
- Categorise the remaining pages: collapse into a submenu or top-level, alphabetically.
- Remove menu links for pages not in use or very rarely used.
- **Decision:** release the menu search to **everyone**.
