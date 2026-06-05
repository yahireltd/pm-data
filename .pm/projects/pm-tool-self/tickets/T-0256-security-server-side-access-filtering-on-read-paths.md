---
id: T-0256
title: "[security] Server-side access filtering on read paths"
type: chore
state: triaged
created: 2026-06-05T21:42:52Z
updated: 2026-06-05T21:42:52Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - Read paths filter by the signed-in user’s accessible projects (use getEffectiveIdentity + accessibleProjects), not just route guards / client hiding
  - "Fix: /activity feed, listProjectsForUI + searchTicketsForUI (web/app/_actions/tickets.ts), the projects page, and the dashboard/preview-as data load"
  - A stakeholder (or an admin previewing-as one) only sees projects/tickets they’re attached to
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
---

## Problem

Security review (T-0249) HIGH: several read paths call loadAllTickets/loadAllProjects and return everything, relying on route-level redirects or client-side view-mode to hide data. A stakeholder (or preview-as) can see ticket titles/project names from projects they aren’t on (project picker, search, activity).

## Context

accessibleProjects(email) exists in access.ts but isn’t used in read paths. Principle: filter at the data layer, not the route layer. Files: web/app/activity/page.tsx, web/app/_actions/tickets.ts (listProjectsForUI/searchTicketsForUI), web/app/page.tsx, web/app/projects/page.tsx.