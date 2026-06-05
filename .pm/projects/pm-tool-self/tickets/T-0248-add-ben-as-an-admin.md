---
id: T-0248
title: Add Ben as an admin
type: chore
state: review
created: 2026-06-05T21:24:36Z
updated: 2026-06-05T21:39:02Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Ben's email added to access.admins in .pm/config.yml so he has full admin access
  - Confirm via the app that Ben sees admin-only controls (delete, members, preview-as)
  - Admin access implications noted / cross-checked against the security review (T- security)
out_of_scope: []
code_anchors:
  - path: .pm/config.yml
    symbol: access.admins
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2138
    model: claude-opus-4-8
    started: 2026-06-05T21:38:37Z
    status: completed
    ended: 2026-06-05T21:39:02Z
    summary: "Ben now has admin access to the tool — his email was added to the admin list, so he can use the admin-only controls (deleting tickets/meetings, managing project members, and the \"preview as\" role view). The services were restarted so the change is live and can't be accidentally overwritten by the system's own housekeeping. Note: admins have full access everywhere, which is exactly why we've also lined up a proper security review before leaning on that further."
    test_plan: Ben signs in as ben@yahire.com (Microsoft SSO) and sees admin-only controls (ticket/meeting delete, project members editor, preview-as). The live config admins list shows austin@yahire.com + ben@yahire.com. A non-admin still does not see those controls.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T21:39:02Z
---

## Problem

Ben needs admin access to pm-tool. Admins (config.yml `access.admins`, ADR-008) have full access everywhere, so this expands the trust surface — pair it with the security review.

## Context

The change is adding Ben's email to `.pm/config.yml` `access.admins`. **Need Ben's email address to do it.** RBAC model: admin / member / stakeholder (ADR-008/009).

## Conversation

**2026-06-05 21:39 claude:** Run run-20260605-2138 completed — Ben now has admin access to the tool — his email was added to the admin list, so he can use the admin-only controls (deleting tickets/meetings, managing project members, and the "preview as" role view). The services were restarted so the change is live and can't be accidentally overwritten by the system's own housekeeping. Note: admins have full access everywhere, which is exactly why we've also lined up a proper security review before leaning on that further.
