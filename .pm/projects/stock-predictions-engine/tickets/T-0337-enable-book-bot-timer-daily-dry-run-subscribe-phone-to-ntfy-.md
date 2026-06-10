---
id: T-0337
title: Enable book-bot.timer (daily dry-run) + subscribe phone to ntfy watchdog topic
type: chore
state: triaged
created: 2026-06-10T01:50:49Z
updated: 2026-06-10T01:50:49Z
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
version: 1
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