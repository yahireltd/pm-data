---
id: T-0312
title: UI facelift + top-bar declutter (rbac-look) — merged & deployed
type: chore
state: done
created: 2026-06-09T13:27:52Z
updated: 2026-06-09T16:03:08Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - "The design refresh is live: new stat tiles, section dividers, restyled buttons/badges/status pills, and reworked globals.css across the main pages."
  - The top bar shows the username (or SSO name) instead of the raw email.
  - The top bar no longer has the Refresh button or the docs/help links (docs & help remain in the sidebar as Dev wiki / Help).
out_of_scope:
  - The auth-bypass fix that rode in with this merge — tracked on T-0254.
code_anchors:
  - path: web/app/_components/TopNav.tsx
  - path: web/app/_components/ui/StatTile.tsx
  - path: web/app/_components/ui/SectionDivider.tsx
  - path: web/app/globals.css
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ui
  - facelift
attention: null
---

## What shipped
The `facelift/rbac-look` branch merged to `master` and deployed to production today.

**Facelift (design refresh):** new shared UI components — stat tiles, section dividers, restyled buttons / badges / status pills — plus a reworked `globals.css` (spacing + typography), applied across the dashboard, tickets, projects, meetings and more.

**Top-bar declutter:**
- shows your **username** (or your SSO name) instead of the raw email;
- the **Refresh** button is removed (pages already re-read on navigation; a browser reload does the same);
- the **docs** and **help** links are removed from the top bar — they already live in the sidebar (Dev wiki / Help).

## Context
Merged as `0dbe14f`, deployed 2026-06-09 (rode in alongside the auth-bypass fix on T-0254). The facelift itself came from the colleague's `facelift/rbac-look` branch; the top-bar tweaks and the deploy were done in this session.

It's already live — this ticket is the record. Close it once you've eyeballed prod.

## Conversation

**2026-06-09 16:03 — you**

Done after the merge from zsolts design facelift  top menu had scrollbar due to font increase

---

**2026-06-09 16:03 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
