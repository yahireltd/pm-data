---
id: T-0257
title: "[security] MCP server hardening (identity, rotation, audit, parity)"
type: chore
state: review
created: 2026-06-05T21:43:06Z
updated: 2026-06-09T16:19:23Z
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
    status: completed
    summary: "We changed how AI assistants connect to the live board so we can tell who's doing what. Before, every developer's assistant used the SAME shared key, so the system only knew \"a valid connection\" — no way to tell which person's assistant created, edited, or deleted something, no record to look back on, and if that one key ever leaked the only fix was changing it for everyone at once. Now each developer gets their OWN key, the system recognises which person it is, and it writes a who/when/what line for every change made through an assistant — a proper trail. Because everyone with MCP access is already a full admin, we deliberately kept this about accountability (knowing who did it), not adding new restrictions. It shipped with no disruption: the old shared key still works, so nobody's connection drops, and people switch to their own key one at a time. The change is live and the audit trail is already recording. The only remaining step is that switch-over — each of the three admins points their assistant at their own key (Austin first); after all three move across, the old shared key is retired (tracked separately)."
    ended: 2026-06-09T16:19:23Z
    test_plan: |-
      ## Verify
      - pm-mcp starts with the tokens loaded: `journalctl -u pm-mcp | grep 'HTTP transport'` → `(N token(s); audit on)`.
      - An unknown bearer token → 401; a known token (a per-user one, or the legacy shared one during migration) is accepted.
      - Every write through the MCP appears in the audit journal: `journalctl -u pm-mcp | grep 'mcp audit'` → `{dev, agent, tool, target}`; a read (`pm_list_*`/`pm_get_*`/`pm_search`) does not.
      - After a dev migrates, their writes show `dev: <their email>` instead of `dev: shared`.

      ## Remaining / linked
      - Migration of all three admins + retiring the legacy shared token → **T-0313**.
      - GitHub read-only token least-privilege → **T-0314**.
    records:
      docs: updated
      tech_session: TS-014
      status_note: none-needed
      docs_note: "ADR-037 records the decision (per-user tokens + write audit, attribution-not-RBAC; closes the per-user-MCP-auth gap deferred in ADR-012). Follow-ons filed: T-0313 (rotation/revocation + retire the legacy token), T-0314 (GitHub least-privilege)."
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-09T16:19:23Z
---

## Problem

Security review (T-0249) HIGH/MEDIUM: the remote MCP used ONE shared bearer token with no per-request identity, no rotation/revocation, and no audit logging. A leaked token = full, untraceable data access — and you can't tell which dev's agent did what.

## Scope (decided 2026-06-09 — see the "Per-user MCP tokens + write audit log" ADR)

Every MCP user is a project admin, so per-project authorization buys nothing. This ticket is **attribution, not RBAC**: per-user tokens that resolve to the dev, plus a write audit log. Token rotation/revocation moved to **T-0257b**; GitHub least-privilege to **T-0257c**.

## Approach (this ticket)

`mcp-server/src/http.ts` resolves each bearer token to a principal — per-user tokens from the `pmMcpTokens` secret (email -> token) resolve to that dev; the legacy shared token still works (resolves to "shared") so migration breaks nothing. Every authenticated write is logged to the pm-mcp journal `{ ts, dev, agent, tool, target }`. Constant-time compare + all ADR-010 transport invariants preserved.

## Context

Files: `mcp-server/src/http.ts`. Decisions: ADR-010 (remote transport), ADR-012 (deferred per-user MCP auth — closed by this), ADR-025 (write parity).

## Conversation

**2026-06-09 16:19 claude:** Run run-20260609-1520 completed — We changed how AI assistants connect to the live board so we can tell who's doing what. Before, every developer's assistant used the SAME shared key, so the system only knew "a valid connection" — no way to tell which person's assistant created, edited, or deleted something, no record to look back on, and if that one key ever leaked the only fix was changing it for everyone at once. Now each developer gets their OWN key, the system recognises which person it is, and it writes a who/when/what line for every change made through an assistant — a proper trail. Because everyone with MCP access is already a full admin, we deliberately kept this about accountability (knowing who did it), not adding new restrictions. It shipped with no disruption: the old shared key still works, so nobody's connection drops, and people switch to their own key one at a time. The change is live and the audit trail is already recording. The only remaining step is that switch-over — each of the three admins points their assistant at their own key (Austin first); after all three move across, the old shared key is retired (tracked separately).
