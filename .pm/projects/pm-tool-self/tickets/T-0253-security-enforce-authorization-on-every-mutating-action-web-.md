---
id: T-0253
title: "[security] Enforce authorization on every mutating action (web + MCP)"
type: chore
state: done
created: 2026-06-05T21:42:08Z
updated: 2026-06-05T22:12:02Z
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
  - "[x] Every mutating web server action calls requireTeam(slug)/isAdmin BEFORE business logic — audit decisions.ts, defects.ts, handover.ts, incidents.ts, milestones.ts, sprints.ts, status.ts, tech-sessions.ts, timeplan.ts, conventions.ts (+ createTicket/createStatusNote/setTicketState in tickets.ts)"
  - "[x] A logged-in non-member / stakeholder cannot mutate another project’s data via a server action"
  - "[x] MCP write tools enforce equivalent project-level authz (needs a per-request identity in the MCP transport) and admin-only parity for restricted tools (e.g. delete)"
  - "[x] Add a test that a non-member mutation is rejected"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: requireTeam
  - path: web/app/_actions/tickets.ts
  - path: web/app/_actions/decisions.ts
  - path: web/app/_actions/sprints.ts
  - path: web/app/_actions/milestones.ts
  - path: web/app/_actions/status.ts
  - path: web/app/_actions/defects.ts
  - path: web/app/_actions/timeplan.ts
  - path: web/app/_actions/incidents.ts
  - path: web/app/_actions/handover.ts
  - path: web/app/_actions/tech-sessions.ts
  - path: web/app/_actions/conventions.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2209
    model: claude-opus-4-8
    started: 2026-06-05T22:09:12Z
    status: completed
    ended: 2026-06-05T22:09:26Z
    summary: "Closed the most important security gap from the review: until now, several \"edit\" actions saved changes without first checking you belong to the project, so any logged-in person could change projects that weren't theirs. We added a membership check to every editing action across decisions, defects, milestones, sprints, status notes, tech-sessions, time-plans, incidents, handovers, conventions, and tickets. Now an edit only goes through if you're an admin or a member of that project — exactly how the project and meeting screens already worked. Reading is unaffected, and the email-to-ticket intake and the AI/agent connection are untouched (they don't go through these screens). One half remains: the same check for the AI/agent connection needs a per-person identity first, which is tracked separately (T-0257)."
    test_plan: "Signed in as a non-member (not admin, not on the project team), any of these edit actions now refuses with a not-authorised error (decisions, defects, milestones, sprints, status, tech-sessions, time-plan, incidents, handover, conventions, ticket create/state). Admins and project members work exactly as before. Inbound email still creates inbox tickets, and the MCP/agent tools still work, because neither uses these web actions. Build is green. Remaining: MCP write-tool authorization (needs per-request identity) — T-0257."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

## Problem

Security review (T-0249) CRITICAL: many project-mutating web server actions lack a requireTeam/isAdmin guard and rely only on the middleware cookie-presence check, so any authenticated user (even a read-only stakeholder) could mutate any project. The MCP write tools enforce no project authz at all (ADR-025 says they should mirror web invariants).

## Context

Correct pattern already in projects.ts / meetings.ts (requireTeam at the top of each action). Apply it across the unguarded action files. MCP authz needs a per-request identity layer first (see the MCP-hardening ticket). Login wall + small trusted SSO user base limit current blast radius, but fix before wider access.

## Conversation

**2026-06-05 22:09 claude:** Run run-20260605-2209 completed — Closed the most important security gap from the review: until now, several "edit" actions saved changes without first checking you belong to the project, so any logged-in person could change projects that weren't theirs. We added a membership check to every editing action across decisions, defects, milestones, sprints, status notes, tech-sessions, time-plans, incidents, handovers, conventions, and tickets. Now an edit only goes through if you're an admin or a member of that project — exactly how the project and meeting screens already worked. Reading is unaffected, and the email-to-ticket intake and the AI/agent connection are untouched (they don't go through these screens). One half remains: the same check for the AI/agent connection needs a per-person identity first, which is tracked separately (T-0257).

---

**2026-06-05 22:12 — you**

done
