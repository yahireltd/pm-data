---
id: ADR-006
slug: deploy-to-the-pm-tool-server-via-systemd-caddy-smashthegrann
title: Deploy to the pm-tool server via systemd + Caddy (smashthegranny.yahire.com)
state: accepted
decided: 2026-06-06
decided_by:
  - Austin
  - Claude
project: demo-neon-smash
supersedes: null
superseded_by: null
tickets: []
kind: deployment
---

> Drafter: Claude · Accepted by: Austin · 2026-06-06.

## Context
The first playable version is built, and we chose to put it on the internet to demo the deploy flow. It runs on the **same server as pm-tool**, on its own domain and port, without disturbing pm-tool. (Hosting at all is a scope change from "local only" — see the change-control ADR.)

## Decision — how it ships
- **Host:** the existing pm-tool server, alongside pm-tool — none of pm-tool's config touched.
- **Code:** `/home/ubuntu/smashthegranny` (copied up; becomes a git checkout once the deploy key is added).
- **Service:** `smashthegranny.service` (systemd) runs `bun server/src/index.js` on **port 3002** (3000 = pm-tool web, 3001 = pm-mcp), `Restart=on-failure`.
- **Runtime:** Bun 1.3 runs the Node-standard server unchanged — zero install, no Node needed.
- **Routing / TLS:** an appended Caddy block `smashthegranny.yahire.com → 127.0.0.1:3002`, **validated before reload**; Caddy auto-obtains the Let's Encrypt cert (tls-alpn-01).
- **Deploy loop (the pm-deploy mirror):** a read-only GitHub deploy key on the server + `/usr/local/bin/smashthegranny-deploy` = `git pull --ff-only && systemctl restart smashthegranny`.

## Forward steps
`git push` locally → on the server `sudo smashthegranny-deploy` → confirm `https://smashthegranny.yahire.com` returns 200.

## Smoke test (post-deploy)
`systemctl is-active smashthegranny` = active; `curl localhost:3002` = 200; the public URL returns 200; play a game and submit a score; it appears on the board.

## Rollback (mandatory)
- **Bad release:** `git -C /home/ubuntu/smashthegranny reset --hard <previous-sha> && sudo systemctl restart smashthegranny`.
- **Take it down entirely:** `sudo systemctl disable --now smashthegranny`; remove the `smashthegranny.yahire.com` block from `/etc/caddy/Caddyfile`; `sudo caddy validate` then `sudo systemctl reload caddy`.
- pm-tool is unaffected throughout (separate service, separate Caddy block). The leaderboard data (`server/data/scores.json`) is git-ignored, so rollback never touches live scores.

## Consequences
- One more isolated service + domain to watch; fully separate from pm-tool.
- Proves the same push-then-deploy flow as pm-tool, on a second app.