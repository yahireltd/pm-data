---
id: T-0249
title: Security review / hardening pass
type: spike
state: triaged
created: 2026-06-05T21:24:45Z
updated: 2026-06-05T21:24:45Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - "Documented review of: Microsoft SSO auth + session handling, the admin/member/stakeholder RBAC (incl. preview-as), and where requireTeam/isAdmin gate mutations"
  - Review the remote MCP bearer-token auth (ADR-010/012) — token scope, rotation, per-user identity gap
  - Review secrets handling (AWS Secrets Manager pmToolAuth incl. the new tokenGithubFine; the read-only GitHub token scope)
  - Review input validation / authz on web server actions + MCP write tools (can a non-member mutate? can preview-as escalate?)
  - Note the agent-policy guardrails (T-0247) + recommend GitHub branch protection on production branches
  - "Output: a prioritised findings list (severity) with concrete fixes, filed as follow-up tickets"
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

As we add admins (Ben) and connect the tool to GitHub + real data, we should do a deliberate security review rather than assume it's fine. Is it possible — yes; this spike scopes + runs it.

## Context

Surfaces to examine: web (Next.js server actions, RBAC in web/app/_lib/access.ts, auth.ts Microsoft Entra), the remote MCP (bearer token, loopback+Caddy, no per-user identity — ADR-010/012), secrets (AWS Secrets Manager), the new read-only GitHub token (T-0243), and the agent-policy gate (T-0247). Deliverable is findings + prioritised fix tickets, not a rewrite.