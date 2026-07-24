---
id: T-0660
title: Status notes don't open in the web UI (hit when filing SN-009)
type: bug
state: triaged
created: 2026-07-24T01:40:28Z
updated: 2026-07-24T01:40:28Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code (mac)
  channel: mcp
assignee: null
acceptance_criteria:
  - Opening a status note (e.g. SN-009 on pm-tool-self) from the web UI renders its content instead of failing
  - Whatever the failure mode was (404, blank page, error) is identified and covered by the fix
out_of_scope: []
code_anchors:
  - path: web/app
    symbol: "status-note detail route/view (locate: SN- pages)"
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem

Reported by the Mac agent session on 2026-07-24 (~01:07, in T-0658's Conversation): it filed status note **SN-009** on pm-tool-self via MCP, but status notes were "not opening in the UI right now", so it reposted the content as a ticket comment instead. Exact failure mode (404 / blank / error) not captured — reproduce by opening SN-009 from the project's status notes list.

## Context

SN-009 content is duplicated in T-0658's Conversation (the "Field note — two agents collaborating through the tool" comment), so nothing is lost — but status notes are the weekly-heartbeat surface and need to render.