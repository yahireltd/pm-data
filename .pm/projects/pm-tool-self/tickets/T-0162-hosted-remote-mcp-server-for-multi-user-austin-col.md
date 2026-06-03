---
id: T-0162
title: Hosted / remote MCP server for multi-user (Austin + colleague + Claude Code)
type: feature
state: triaged
priority: p2
created: 2026-06-02T15:38:13Z
updated: 2026-06-02T15:38:13Z
project: pm-tool-self
section: null
parent: null
children: []
order: 33792
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
backlog_status: in_review
source: discovered
---
# T-0162: Hosted / remote MCP server

## Problem

Today the MCP server runs **locally via stdio** — single machine, single user, tools read once at client connect. For real-world use (Austin + a dev colleague + Claude Code all working the same projects) we need a **shared server-of-record over the network**: HTTP/SSE transport instead of stdio; hosting where the .pm store lives (shared git checkout or a backing store); auth; and concurrency handling for simultaneous writes (the markdown files are currently last-write-wins). This is the 'remote MCP server-of-record' step of the deployment path (local-now -> online dashboard -> remote MCP).

## Acceptance criteria

_To define at sprint promotion._
