---
id: T-0253
title: "[security] Enforce authorization on every mutating action (web + MCP)"
type: chore
state: triaged
created: 2026-06-05T21:42:08Z
updated: 2026-06-05T21:42:08Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - Every mutating web server action calls requireTeam(slug)/isAdmin BEFORE business logic — audit decisions.ts, defects.ts, handover.ts, incidents.ts, milestones.ts, sprints.ts, status.ts, tech-sessions.ts, timeplan.ts, conventions.ts (+ createTicket/createStatusNote/setTicketState in tickets.ts)
  - A logged-in non-member / stakeholder cannot mutate another project’s data via a server action
  - MCP write tools enforce equivalent project-level authz (needs a per-request identity in the MCP transport) and admin-only parity for restricted tools (e.g. delete)
  - Add a test that a non-member mutation is rejected
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

Security review (T-0249) CRITICAL: many project-mutating web server actions lack a requireTeam/isAdmin guard and rely only on the middleware cookie-presence check, so any authenticated user (even a read-only stakeholder) could mutate any project. The MCP write tools enforce no project authz at all (ADR-025 says they should mirror web invariants).

## Context

Correct pattern already in projects.ts / meetings.ts (requireTeam at the top of each action). Apply it across the unguarded action files. MCP authz needs a per-request identity layer first (see the MCP-hardening ticket). Login wall + small trusted SSO user base limit current blast radius, but fix before wider access.