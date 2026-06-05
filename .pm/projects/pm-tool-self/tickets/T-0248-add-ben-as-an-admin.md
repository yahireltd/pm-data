---
id: T-0248
title: Add Ben as an admin
type: chore
state: triaged
created: 2026-06-05T21:24:36Z
updated: 2026-06-05T21:24:36Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - Ben's email added to access.admins in .pm/config.yml so he has full admin access
  - Confirm via the app that Ben sees admin-only controls (delete, members, preview-as)
  - Admin access implications noted / cross-checked against the security review (T- security)
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

Ben needs admin access to pm-tool. Admins (config.yml `access.admins`, ADR-008) have full access everywhere, so this expands the trust surface — pair it with the security review.

## Context

The change is adding Ben's email to `.pm/config.yml` `access.admins`. **Need Ben's email address to do it.** RBAC model: admin / member / stakeholder (ADR-008/009).