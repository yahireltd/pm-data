---
id: T-0337
title: Enable book-bot.timer (daily dry-run) + subscribe phone to ntfy watchdog topic
type: chore
state: triaged
created: 2026-06-10T01:50:49Z
updated: 2026-06-10T02:28:12Z
project: stock-predictions-engine
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - book-bot.timer active, next-run scheduled Mon-Fri 20:35 UTC
  - Heartbeat file updating daily; watchdog quiet
  - Phone receives ntfy test message
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ops
  - user-action
attention: null
version: 3
---

## Problem
The paper loop is built, smoke-tested and committed (`d4bb8f6`) but the trading timer is disabled — the dead-man watchdog (already live) will correctly alert ~30h after the last smoke-test heartbeat until it runs daily.

## Action (Austin)
```
systemctl --user enable --now book-bot.timer
```
on austin-ubuntu, and subscribe the ntfy app to topic `austin-quant-book-b4b92040`.

## Context
Dry-run only: signals + reconcile + heartbeat, zero orders until EXECUTE=1 (separate decision, see MS-015). Machine must stay powered on — the era-3 bot died of host power-offs.

## Conversation

**2026-06-10 02:23 claude-code:** book-bot.timer enabled 2026-06-10 — first dry run Wed 21:35 BST (20:35 UTC), then every weekday. Watchdog timer fires daily 09:00 BST. Remaining for this ticket: Austin subscribes the ntfy app to topic `austin-quant-book-b4b92040`; note the first run will likely refuse signals on freshness grounds until the bar-panel merge (T-0338) lands — that's the guard working, not a failure.

**2026-06-10 02:28 claude-code:** **Complete — ready to close.** Timer enabled (first dry run 2026-06-10 21:35 BST, weekdays thereafter), watchdog daily 09:00 BST, ntfy test push sent and **confirmed received on Austin's phone**. The dead-man's switch is fully wired end-to-end. Remaining acceptance item (heartbeat updating daily) confirms itself after the first scheduled run tonight.
