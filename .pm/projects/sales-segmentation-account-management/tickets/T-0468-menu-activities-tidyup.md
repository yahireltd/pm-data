---
id: T-0468
title: Menu / Activities Tidyup
type: feature
state: triaged
priority: p2
created: 2026-06-23T11:48:35Z
updated: 2026-07-17T07:55:53Z
project: sales-segmentation-account-management
section: null
parent: null
milestone: MS-001
children: []
order: 3072
reporter: null
assignee: null
acceptance_criteria:
  - "[x] Menu rebuilt from the CSV: constantly-used pages on Top Level, the rest in submenus / alphabetical."
  - "[x] Menu links for unused/rarely-used pages removed."
  - Menu search released to all users.
  - A contract-checking page exists; Website Quotes retired; select-all + page optimisation added.
  - Activity-editing simplification (Call/Email/Meeting/Task + icon) scoped, or explicitly deferred as a deep-dive.
out_of_scope: []
code_anchors: []
relates:
  - T-0467
  - T-0608
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 7
---

## Problem
The menu/navigation needs tidying: constantly-used pages should be top-level, the rest organised into submenus, and dead links removed. Menu search should be available to everyone.

## Design notes (from M-010)
- Build the menu structure from the CSV Zsolt prepared (rbac-menu-tree.csv).
- Put the main, constantly-used pages on the **Top Level**.
- Categorise the remaining pages: collapse into a submenu or top-level, alphabetically.
- Remove menu links for pages not in use or very rarely used.
- **Decision:** release the menu search to **everyone**.
- **Decision:** build a dedicated **contract-checking page** (see previous tickets), then remove the **Website Quotes** page + add a **select-all** and optimise the page. (Pairs with T-0467.)
- [Possible deep-dive] Activities can currently be edited, which affects process management — we shouldn't allow free edits. Explore a simpler/quicker model: Call/Email/Meeting/Task, then select the exact activity, with the type denoted by an icon.
