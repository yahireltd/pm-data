---
id: T-0211
title: Move dashboard and tickets menu links
type: feature
state: review
priority: p2
created: 2026-06-04T02:04:38Z
updated: 2026-06-04T03:15:40Z
project: null
section: null
parent: null
children: []
order: 13312
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "Admin-only top menu: move Dashboard + Tickets (and the workflow links Inbox/Review/Ready/In-progress) out of the left sidebar into a top bar shown only to admins, so members/stakeholders get a decluttered sidebar"
  - Resolve 'Projects' vs 'Your projects' — consolidate to one entry, or clearly differentiate their purpose so the nav isn't redundant
  - A non-admin's sidebar shows only what's relevant to their role
out_of_scope: []
code_anchors:
  - path: web/app/_components/Sidebar.tsx
    note: current nav source
  - path: web/app/_components/SidebarNav.tsx
  - path: web/app/layout.tsx
    note: top-bar placement + admin gate
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260604-0205
    model: claude
    started: 2026-06-04T02:05:05Z
    status: completed
    summary: Admin-only top bar for the global workflow links (Dashboard/Tickets/Inbox/Review/Ready/In-progress); sidebar decluttered for members/stakeholders; 'Your projects' renamed 'My work' (distinct from 'Projects'/all).
    ended: 2026-06-04T03:15:40Z
    test_plan: "As admin: the workflow links are in the top bar, not the sidebar. As a member/stakeholder: those links aren't shown. 'My work' (/me) and 'Projects' (/projects) are now distinct entries."
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-04T03:15:40Z
---

we should add these to the top menu only only for admin users to not take up space in the side menu also your projects / meetings /conventions (is there a difference between prijects / your prijects can we consolodate

## Conversation

**2026-06-04 03:15 claude:** Run run-20260604-0205 completed — Admin-only top bar for the global workflow links (Dashboard/Tickets/Inbox/Review/Ready/In-progress); sidebar decluttered for members/stakeholders; 'Your projects' renamed 'My work' (distinct from 'Projects'/all).
