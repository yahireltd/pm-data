---
id: T-0468
title: Menu / Activities Tidyup
type: chore
state: done
priority: p2
created: 2026-06-23T11:48:35Z
updated: 2026-07-21T05:51:46Z
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
  - "[x] Menu search released to all users."
out_of_scope: []
code_anchors:
  - path: backend/views/layouts/main.php
    lines: 489-496
    note: Global page-search bar — gate removed so it renders for everyone (results stay RBAC-filtered per user; SuperUsers keep unfiltered via data-super). This is 'release search to all'.
  - path: backend/views/rbac/menu.php
    note: RBAC menu manager (/rbac/menu) — used to reorganise top-level/submenu and remove dead menu links; saves via RbacController::actionMenuSaveAll.
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
version: 20
---

## Problem
The menu/navigation needs tidying: constantly-used pages should be top-level, the rest organised into submenus, and dead links removed. Menu search should be available to everyone.

## Design notes (from M-010)
- Build the menu structure from the CSV Zsolt prepared (rbac-menu-tree.csv).
- Put the main, constantly-used pages on the **Top Level**.
- Categorise the remaining pages: collapse into a submenu or top-level, alphabetically.
- Remove menu links for pages not in use or very rarely used.
- **Decision:** release the menu search to **everyone**.

## Conversation

**2026-07-17 08:25 — you**

sales menu tisy up done, will add new pages to it when they are ready, menu search released to everyone

---

**2026-07-17 08:25 — you**

Records: docs updated (actionMenu.md); tech-session none-needed; status-note none-needed.

**2026-07-21 05:51 claude-code:** **Follow-up feedback email sent to Nathan (2026-07-21)** — the follow-up flagged as an M-010 outcome.

Asked how he (and the wider sales team) are finding:
- the **menu layout** — constantly-used pages on top level, rest in submenus, dead links removed; anything mis-placed or that should be promoted back to top level;
- the **page search** now released to everyone — is it surfacing what they expect;
- and how the rest of the team is getting on with both.

Awaiting reply — will capture any feedback here and fold changes into the next round (new pages still to be added to the menu as they're ready).
