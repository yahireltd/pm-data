---
id: T-0256
title: "[security] Server-side access filtering on read paths"
type: chore
state: done
created: 2026-06-05T21:42:52Z
updated: 2026-06-05T22:45:06Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Read paths filter by the signed-in user’s accessible projects (use getEffectiveIdentity + accessibleProjects), not just route guards / client hiding"
  - "[x] Fix: /activity feed, listProjectsForUI + searchTicketsForUI (web/app/_actions/tickets.ts), the projects page, and the dashboard/preview-as data load"
  - "[x] A stakeholder (or an admin previewing-as one) only sees projects/tickets they’re attached to"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: visibleProjectSlugs
  - path: web/app/_actions/tickets.ts
    symbol: listProjectsForUI
  - path: web/app/_actions/tickets.ts
    symbol: searchTicketsForUI
  - path: web/app/activity/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2244
    model: claude-opus-4-8
    started: 2026-06-05T22:44:04Z
    status: completed
    ended: 2026-06-05T22:44:17Z
    summary: "Closed a data-exposure gap from the security review: a few screens loaded everything regardless of who you are and relied on the page being hidden, rather than filtering the data itself. Now the project picker, the ticket search, and the progress/activity feed only return projects and tickets you actually have access to — done on the server, so the data never reaches someone who shouldn't see it. Admins still see everything; members see their projects; stakeholders (and an admin previewing as one) see only theirs; inbox items are admin-only. A smaller, lower-risk follow-up remains: the dashboard's preview-as data filtering (that page is already admin-only, so it's defence-in-depth)."
    test_plan: "As a member or stakeholder (or an admin using \"preview as\"): the project picker dropdown + ticket search only return your projects' items; the activity/progress feed only shows your projects. As an admin: everything still shows. Inbox (project-less) tickets appear only for admins. Build green."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

## Problem

Security review (T-0249) HIGH: several read paths call loadAllTickets/loadAllProjects and return everything, relying on route-level redirects or client-side view-mode to hide data. A stakeholder (or preview-as) can see ticket titles/project names from projects they aren’t on (project picker, search, activity).

## Context

accessibleProjects(email) exists in access.ts but isn’t used in read paths. Principle: filter at the data layer, not the route layer. Files: web/app/activity/page.tsx, web/app/_actions/tickets.ts (listProjectsForUI/searchTicketsForUI), web/app/page.tsx, web/app/projects/page.tsx.

## Conversation

**2026-06-05 22:44 claude:** Run run-20260605-2244 completed — Closed a data-exposure gap from the security review: a few screens loaded everything regardless of who you are and relied on the page being hidden, rather than filtering the data itself. Now the project picker, the ticket search, and the progress/activity feed only return projects and tickets you actually have access to — done on the server, so the data never reaches someone who shouldn't see it. Admins still see everything; members see their projects; stakeholders (and an admin previewing as one) see only theirs; inbox items are admin-only. A smaller, lower-risk follow-up remains: the dashboard's preview-as data filtering (that page is already admin-only, so it's defence-in-depth).

---

**2026-06-05 22:45 — you**

Done
