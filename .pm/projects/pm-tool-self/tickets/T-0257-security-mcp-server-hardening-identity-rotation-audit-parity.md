---
id: T-0257
title: "[security] MCP server hardening (identity, rotation, audit, parity)"
type: chore
state: in_progress
created: 2026-06-05T21:43:06Z
updated: 2026-06-09T16:08:38Z
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
  - "Per-user MCP tokens (pmMcpTokens secret: email->token) resolve to the dev at the transport; unknown token -> 401"
  - Legacy shared token still accepted during migration (resolves to 'shared'); retired once all admins migrate (-> T-0257b)
  - "Every authenticated MCP write logged to the pm-mcp journal: who (dev) / when / what (tool + target)"
  - All three admins provisioned with their own tokens and migrated off the shared token
  - Attribution only - no per-project authz (all MCP users are admins); ADR-010 transport invariants preserved
out_of_scope:
  - Per-project RBAC (all MCP users are admins - nothing to restrict)
  - Token rotation/revocation + leaked-token runbook (T-0257b)
  - GitHub token least-privilege (T-0257c)
  - OAuth/OIDC per-user auth (future, larger lift)
  - Per-record in-data attribution + read auditing (would need the principal threaded through every tool)
code_anchors:
  - path: mcp-server/src/http.ts
    symbol: resolvePrincipal
  - path: mcp-server/src/http.ts
    symbol: auditWrite
relates: []
blocks: []
blocked_by: []
duplicates:
  - T-0175
duplicate_of: null
agent_runs:
  - id: run-20260609-1520
    model: claude
    started: 2026-06-09T15:20:18Z
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
---

## Problem

Security review (T-0249) HIGH/MEDIUM: the remote MCP used ONE shared bearer token with no per-request identity, no rotation/revocation, and no audit logging. A leaked token = full, untraceable data access — and you can't tell which dev's agent did what.

## Scope (decided 2026-06-09 — see the "Per-user MCP tokens + write audit log" ADR)

Every MCP user is a project admin, so per-project authorization buys nothing. This ticket is **attribution, not RBAC**: per-user tokens that resolve to the dev, plus a write audit log. Token rotation/revocation moved to **T-0257b**; GitHub least-privilege to **T-0257c**.

## Approach (this ticket)

`mcp-server/src/http.ts` resolves each bearer token to a principal — per-user tokens from the `pmMcpTokens` secret (email -> token) resolve to that dev; the legacy shared token still works (resolves to "shared") so migration breaks nothing. Every authenticated write is logged to the pm-mcp journal `{ ts, dev, agent, tool, target }`. Constant-time compare + all ADR-010 transport invariants preserved.

## Context

Files: `mcp-server/src/http.ts`. Decisions: ADR-010 (remote transport), ADR-012 (deferred per-user MCP auth — closed by this), ADR-025 (write parity).