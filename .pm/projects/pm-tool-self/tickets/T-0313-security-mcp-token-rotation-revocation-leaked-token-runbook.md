---
id: T-0313
title: "[security] MCP token rotation / revocation + leaked-token runbook"
type: chore
state: triaged
created: 2026-06-09T16:09:34Z
updated: 2026-06-09T16:09:34Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - Documented procedure to issue / rotate / revoke a SINGLE dev's MCP token (edit the pmMcpTokens secret + restart pm-mcp) without affecting the others
  - Retire the legacy shared mcpToken once all three admins are on per-user tokens (remove the fallback so only per-user tokens are accepted)
  - "Leaked-token runbook: revoke that dev's token, grep the pm-mcp audit log for its activity, re-issue"
  - Rotation needs only a secret update + pm-mcp restart - no rebuild/redeploy
out_of_scope: []
code_anchors:
  - path: mcp-server/src/http.ts
    symbol: loadTokens
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - security
attention: null
---

## Problem

Per-user MCP tokens (ADR-037 / T-0257) are long-lived shared secrets held in the `pmMcpTokens` AWS secret. We need the operational controls around them: rotate or revoke ONE dev's token without disrupting the others, a documented leaked-token procedure, and retirement of the legacy shared token once everyone has migrated.

## Context

Follow-on to **T-0257** (which shipped per-user tokens + the write audit log). The audit log makes a leaked token traceable; this ticket adds the rotation/revocation lifecycle + runbook around it. Files: `mcp-server/src/http.ts` (loadTokens). The shared token currently still resolves to a "shared" principal for backward-compatible migration — that fallback is what gets retired here.