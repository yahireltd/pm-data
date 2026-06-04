---
id: ADR-010
slug: remote-mcp-over-http-bearer-token-secured-loopback-bound-beh
title: Remote MCP over HTTP, bearer-token secured, loopback-bound behind Caddy TLS
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0162
---

## Context
The web RBAC lets humans drive pm-tool, but Claude Code agents needed to read/write the SAME live `.pm/` store remotely (e.g. drive the `pm-tool-self` board from a laptop). The existing MCP was stdio-only — local-process, no network reach. Options: (a) keep stdio and require everyone to run on the server — defeats the purpose; (b) expose the MCP publicly with OAuth/per-user auth — heavyweight for what is initially one trusted operator; (c) reuse the web app's SSO session for MCP calls — MCP clients are not browsers and don't carry that cookie flow.

## Decision
Ship a second service, an HTTP MCP transport (`mcp-server/src/http.ts`), exposing the same tools over `POST /mcp` against the live store. Defence in depth: it binds to `127.0.0.1:3001` only (never public), is reachable solely through Caddy TLS at `https://<host>/mcp`, and requires `Authorization: Bearer <token>` on every request. The token is fetched at startup from AWS Secrets Manager (`pmMcpToken`, or the `mcpToken` field on `pmToolAuth`) via the instance role — never on disk — and compared with a constant-time, length-safe SHA-256 hash so it can't be timing-guessed. Transport is stateless (fresh server+transport per request). A no-auth `/healthz` probe reveals nothing. It runs as its own systemd unit (`pm-mcp`) and must be restarted to pick up a rotated token.

## Consequences
Remote agents can operate the live board with a single header, and the secret never touches disk. We accept that this is a coarse, all-or-nothing surface: the bearer token grants full tool access with NO per-user RBAC and NO project scoping (unlike the web app) — every holder is effectively an admin. That makes the token a high-value secret (rotation requires a `pm-mcp` restart) and means it must only be given to trusted devs/PMs; stakeholders must use the web UI, never the MCP. This security gap is explicitly accepted as interim, with per-user MCP auth deferred (see the shared-token ADR).