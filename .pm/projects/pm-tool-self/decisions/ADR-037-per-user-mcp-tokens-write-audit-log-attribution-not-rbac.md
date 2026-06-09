---
id: ADR-037
slug: per-user-mcp-tokens-write-audit-log-attribution-not-rbac
title: Per-user MCP tokens + write audit log (attribution, not RBAC)
state: accepted
decided: 2026-06-09
decided_by:
  - austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0257
---

## Context

The remote MCP (ADR-010) authenticated every request with a SINGLE shared bearer token. That token was the entire identity — the server knew "a valid token connected" and nothing more. Consequences:
- **No attribution** — an agent's change recorded only a caller-supplied author string (spoofable, identical for everyone); you couldn't tell whose agent did what.
- **No audit trail** — no record of which connection made which change.
- **Blunt revocation** — one shared secret, so a leak (or a departing dev) forces rotating the single token, dropping everyone at once.

ADR-012 named per-user MCP auth as a KNOWN, ACCEPTED, deferred gap. This closes it.

Scope is deliberately narrow: **every MCP user is a project admin**, so per-project RBAC buys nothing — there is nothing to restrict. The need is **attribution** (who did what) and per-dev revocation, not authorization.

## Decision

Per-user bearer tokens, resolved to an identity at the transport (`mcp-server/src/http.ts`):
- A `pmMcpTokens` secret holds a `{ "<dev-email>": "<token>" }` map (256-bit random tokens; AWS Secrets Manager, never on disk). The presented token resolves to a principal `{ dev }`; an unknown token → 401.
- The **legacy shared token** (`pmToolAuth.mcpToken`) is still accepted during migration and resolves to `{ dev: "shared" }` — no flag day; it is retired (T-0257b) once all admins hold their own tokens.
- Token comparison stays **constant-time** (timingSafeEqual over SHA-256 hashes), checking all tokens with no early return.

Every authenticated **WRITE** (reads — `pm_list_*` / `pm_get_*` / `pm_search` — are skipped) emits one structured line to the pm-mcp journal: `{ ts, dev, agent, tool, target }` — a who/when/what trail correlatable with the data changes. Attribution shows the **agent + the dev** (the token owner).

**Not in scope:** per-project authorization (all admins); changing the in-data author labels (audit-log-only); OAuth/OIDC (the eventual standards-aligned ideal — a much larger lift, a future decision). All ADR-010 transport invariants are preserved (loopback bind, Caddy TLS, secrets-manager sourcing, constant-time compare, stateless-per-request, no-leak healthz); no generic write surface is added (ADR-025).

## Consequences

- You can tell **who did what**, and revoke one dev without disrupting others.
- Tokens are long-lived shared secrets needing per-user provisioning/rotation/revocation — handled in **T-0257b** (rotation + leaked-token runbook).
- Reads are not audited and in-data author strings are unchanged; per-record attribution or read auditing would need the principal threaded through the tool dispatch — a larger change we deliberately skipped.
- GitHub read-only token least-privilege is unrelated and tracked in **T-0257c**.