---
id: T-0175
title: Per user MCP auth
type: feature
state: triaged
priority: p2
created: 2026-06-03T17:08:13Z
updated: 2026-06-05T12:48:18Z
project: pm-tool-self
section: null
parent: null
children: []
order: 47104
reporter: null
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

the MCP is secured by a single shared token with full access — it doesn't do per-user permissions yet (the web app does). So only hand that token to trusted 

&#x20; devs/PMs. Stakeholders should use the web UI only, never the MCP. (Per-user MCP auth is a good future ticket — worth putting on the live board.)
