---
id: T-0232
title: No way for agents to write a project status note (missing MCP tool)
type: feature
state: triaged
created: 2026-06-05T15:42:56Z
updated: 2026-06-05T15:42:56Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee: null
acceptance_criteria:
  - An agent can create a project status note through an MCP tool (overall RAG, date, author, and the shipped/at-risk/next body)
  - The tool writes the same status-note shape the web and CLI produce (one validation/source path)
  - It appears in the project's status history like a web/CLI-written note
out_of_scope: []
code_anchors:
  - path: mcp-server/src/server.ts
  - path: mcp-server/src/tools/
  - path: cli/src/commands/new.ts
    symbol: runNewStatusNote
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - dogfood
  - meta
attention: null
---

## Problem

Status notes are the project's heartbeat — at phase boundaries and at least weekly, someone writes "what shipped / what's at risk / what's next" so anyone can pick the project up cold (AGENTS.md §6). But there is **no agent tool to write one**: the MCP surface has no create-status-note tool, so an agent closing out work can record everything else (runs, decisions, tech-sessions, milestones) yet cannot leave the heartbeat. The only ways to write a status note today are the web UI and the command line — neither of which an agent driving the remote tools can reach.

This was hit live while closing the loop on the pre-project-parity work (TS-007): the runs, ADRs and tech-session went in cleanly, but the status note could not be written through the agent tools.

## What we want

A `pm_create_status_note` tool (mirroring the web/CLI), so an agent can write the heartbeat the same way it records everything else.

## Why it matters

If the heartbeat is the one record an agent can't write, it silently rots — the very "we shipped work nobody recorded" failure the status note exists to prevent.

## Out of scope
- Reworking how status notes are stored or displayed — this is purely the missing agent write-path, at parity with the web/CLI.