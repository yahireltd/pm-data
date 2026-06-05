---
id: T-0257
title: "[security] MCP server hardening (identity, rotation, audit, parity)"
type: chore
state: triaged
created: 2026-06-05T21:43:06Z
updated: 2026-06-05T21:43:06Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - Per-request identity in the MCP transport (so write-tool authz from the authz ticket can be enforced)
  - Token rotation/revocation without a full rebuild + a documented incident procedure for a leaked token
  - Audit logging of MCP write requests (who/when/what), correlatable with data changes
  - "Documented policy: which MCP tools are admin-only / restricted vs universal, kept in parity with the web (e.g. delete is admin-only on web)"
  - GitHub read-only token least-privilege documented (Contents + Metadata read only)
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

Security review (T-0249) HIGH/MEDIUM: the remote MCP uses one shared bearer token with no per-request identity, no rotation/revocation, no audit logging, and write tools that diverge from the web threat model (e.g. delete needs no admin). A leaked token = full, untraceable data access.

## Context

Files: mcp-server/src/http.ts (token, no identity/logging), mcp-server/src/tools/* (no authz — see the authz ticket), delete-ticket.ts (confirm-only vs web admin-only). ADR-010/012/025.