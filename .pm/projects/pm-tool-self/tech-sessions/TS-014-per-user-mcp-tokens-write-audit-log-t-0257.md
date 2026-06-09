---
id: TS-014
slug: per-user-mcp-tokens-write-audit-log-t-0257
title: Per-user MCP tokens + write audit log (T-0257)
project: pm-tool-self
created: 2026-06-09T16:17:56Z
updated: 2026-06-09T16:19:14Z
decisions:
  - 'Scoped to attribution, not RBAC — every MCP user is already a project admin, so per-project checks buy nothing; the need is "who did it", not "can they". Chose audit-log-only at the connection boundary (no need to re-plumb the ~50 tools) and per-user bearer tokens (a pmMcpTokens secret mapping dev-email -> token, 256-bit random). Tokens are read from AWS once at startup and validated in-memory as SHA-256 hashes (constant-time), never per-request and never kept as plaintext. The legacy shared token is retained so the rollout is zero-downtime and staged. All ADR-010 transport invariants preserved. Decided in ADR-037. (A 6-agent parallel mapping pass first surfaced the concrete gaps: an unauthenticated hard-delete and fully-spoofable authorship.)'
actions:
  - text: Migrate the three admins to their own per-user tokens (Austin first), then retire the legacy shared token so only per-user tokens are accepted.
    ticket: T-0313
side_quests: []
problem: The remote MCP authenticated every connection with ONE shared bearer token, so the server only knew "a valid token connected" — not which dev. That meant no attribution (you couldn't tell whose agent made a change), no audit trail, and blunt revocation (a leak forces rotating the single token for everyone at once).
success_criterion: Each dev's agent uses its own token; the server resolves it to that dev and writes a who/when/what audit line for every change; the rollout breaks no existing connection (the shared token keeps working until everyone has migrated).
phase: build
---

# TS-014: Per-user MCP tokens + write audit log (T-0257)

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
