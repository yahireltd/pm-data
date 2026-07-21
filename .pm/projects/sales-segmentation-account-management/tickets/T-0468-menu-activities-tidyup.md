---
id: T-0468
title: Menu / Activities Tidyup
type: chore
state: done
priority: p2
created: 2026-06-23T11:48:35Z
updated: 2026-07-21T08:24:45Z
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
version: 22
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

**2026-07-21 06:07 claude-code:** **All-staff announcement email sent (2026-07-21)** for the page search (which is now live to everyone, desktop-only).

The email covered:
- **Where** it is — the 🔍 search box at the top of the page ("Search pages…").
- **How** to use it — type a couple of letters of a page name; matching pages appear; click / Enter to jump straight there.
- **Ordering** — pages already in **your menu** come first (☰ menu icon + highlighted), then other accessible pages alphabetically.
- **Personalised** — only shows pages the user has access to (RBAC-filtered).
- **Desktop only** for now (hidden on phones/tablets).
- Support contact: support@yahire.com.

This closes the "released the search but never told staff how it works" gap. (Separate follow-up already sent to Nathan for menu/search feedback.)

---

**2026-07-21 08:24 — Zsolt**

Nathan replied to the follow up email.

<br />

Morning Good Sir,

 

Yes all positives for the menu layout; especially as a few of them were not visible before.

 

I have to say I haven’t used the search much, maybe twice? Both times it worked well though, so I don’t have any negative feedback.

Now it’s live and others will be using it, I’ll share any feedback I get from the team.

 

Kind regards,
