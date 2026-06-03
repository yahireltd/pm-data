---
id: TS-002
slug: production-deployment-hosting-remote-mcp-and-the-c
title: "Production deployment: hosting, remote MCP, and the code/data split"
project: pm-tool-self
created: 2026-06-03T17:40:24Z
updated: 2026-06-03T17:40:24Z
problem: "Stand up pm-tool as a real hosted product: online behind login, drivable by Claude remotely, with the app's code separated from its project data so the two evolve without colliding."
success_criterion: Live at https://support.yahire.com with Microsoft SSO + AWS-managed secrets; a bearer-secured remote MCP (pm-tool-live) Claude can read/write; one source of project data (pm-data) with off-box backup; and a one-command redeploy.
decisions:
  - "Two-repo split: code in yahireltd/pm-tool (edit locally -> push -> deploy), project data in yahireltd/pm-data on the server (PM_ROOT -> data). A code pull never touches data and vice versa."
  - Remote MCP over HTTP behind Caddy TLS, bound to 127.0.0.1, gated by a bearer token fetched at runtime from Secrets Manager (mcpToken on pmToolAuth).
  - "The live server is the single source of project data: no local .pm; project management happens only via the web UI or the pm-tool-live MCP."
  - Secrets stay in AWS Secrets Manager (eu-west-2), fetched at runtime via the WEB_SERVER instance role; never on disk.
  - Deploy via `sudo pm-deploy` = fetch + reset --hard origin/master + build + restart (build THEN restart, always).
actions:
  - text: Restrict sign-in to @yahire.com via a signIn callback in web/auth.ts (tenant already limits; enforce the domain explicitly).
    ticket: T-0174
  - text: Build per-user MCP auth so each person authenticates as themselves, replacing the single shared token.
  - text: Push the 2 outstanding code commits (local MCP removal + dev guide) from the Mac.
  - text: "Optional: harden pm-data-sync with fetch+rebase before push to self-heal if a second writer ever appears."
side_quests:
  - "Stale-process bug: rebuilding .next without restarting next start served mismatched chunks -> 'client-side exception'. Fixed by restart; pm-deploy now always builds-then-restarts."
  - bun install rewrites bun.lock and blocks git pull on the server; pm-deploy uses reset --hard origin/master to sidestep it.
  - GitHub forbids one deploy key on two repos -> two keys (read code / write data) with SSH host aliases; they bypass org SAML SSO.
  - Local DNS negative-cached the new A record while the rest of the world resolved fine.
---

# TS-002: Production deployment: hosting, remote MCP, and the code/data split

## Problem

_Stand up pm-tool as a real hosted product: online behind login, drivable by Claude remotely, with the app's code separated from its project data so the two evolve without colliding._

## Success criterion

_Live at https://support.yahire.com with Microsoft SSO + AWS-managed secrets; a bearer-secured remote MCP (pm-tool-live) Claude can read/write; one source of project data (pm-data) with off-box backup; and a one-command redeploy._

## Decisions

- Two-repo split: code in yahireltd/pm-tool (edit locally -> push -> deploy), project data in yahireltd/pm-data on the server (PM_ROOT -> data). A code pull never touches data and vice versa.
- Remote MCP over HTTP behind Caddy TLS, bound to 127.0.0.1, gated by a bearer token fetched at runtime from Secrets Manager (mcpToken on pmToolAuth).
- The live server is the single source of project data: no local .pm; project management happens only via the web UI or the pm-tool-live MCP.
- Secrets stay in AWS Secrets Manager (eu-west-2), fetched at runtime via the WEB_SERVER instance role; never on disk.
- Deploy via `sudo pm-deploy` = fetch + reset --hard origin/master + build + restart (build THEN restart, always).

## Actions

- Restrict sign-in to @yahire.com via a signIn callback in web/auth.ts (tenant already limits; enforce the domain explicitly). (T-0174)
- Build per-user MCP auth so each person authenticates as themselves, replacing the single shared token.
- Push the 2 outstanding code commits (local MCP removal + dev guide) from the Mac.
- Optional: harden pm-data-sync with fetch+rebase before push to self-heal if a second writer ever appears.

## Side-quests

- Stale-process bug: rebuilding .next without restarting next start served mismatched chunks -> 'client-side exception'. Fixed by restart; pm-deploy now always builds-then-restarts.
- bun install rewrites bun.lock and blocks git pull on the server; pm-deploy uses reset --hard origin/master to sidestep it.
- GitHub forbids one deploy key on two repos -> two keys (read code / write data) with SSH host aliases; they bypass org SAML SSO.
- Local DNS negative-cached the new A record while the rest of the world resolved fine.
