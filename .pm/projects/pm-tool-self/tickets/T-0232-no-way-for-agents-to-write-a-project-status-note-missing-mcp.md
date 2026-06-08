---
id: T-0232
title: No way for agents to write a project status note (missing MCP tool)
type: feature
state: done
created: 2026-06-05T15:42:56Z
updated: 2026-06-08T15:36:04Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude-code
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] An agent can create a project status note through an MCP tool (overall RAG, date, author, and the shipped/at-risk/next body)"
  - "[x] The tool writes the same status-note shape the web and CLI produce (one validation/source path)"
  - "[x] It appears in the project's status history like a web/CLI-written note"
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
agent_runs:
  - id: run-20260608-1508
    model: claude-opus-4-8
    started: 2026-06-08T15:08:26Z
    status: completed
    ended: 2026-06-08T15:08:39Z
    summary: Reconciliation — no new code. This feature was already built and shipped in an earlier session (the pm_create_status_note tool, commit fde364b, now live), but the ticket was left in "triaged" and never closed — exactly the shipped-but-unclosed gap. Agents driving the tool remotely can now write a project status note (the weekly "what shipped / what's at risk / what's next" heartbeat) through the automation interface, the same way they already record runs, decisions and tech-sessions — using the same validation as the web and command-line, so it shows up in the project's status history identically. This run simply closes the loop on that already-delivered work so it's recorded properly. Routed to review so you can confirm and close it in the sweep.
    test_plan: "This is already live (shipped in fde364b). To confirm: call the pm_create_status_note automation tool against a project with an overall RAG, date, author and body → a status note is created and appears in that project's status history exactly like a web/CLI-written one. (No new code in this run — it's a close-the-loop reconciliation of previously-shipped work.)"
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
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

## Conversation

**2026-06-08 15:08 claude:** Run run-20260608-1508 completed — Reconciliation — no new code. This feature was already built and shipped in an earlier session (the pm_create_status_note tool, commit fde364b, now live), but the ticket was left in "triaged" and never closed — exactly the shipped-but-unclosed gap. Agents driving the tool remotely can now write a project status note (the weekly "what shipped / what's at risk / what's next" heartbeat) through the automation interface, the same way they already record runs, decisions and tech-sessions — using the same validation as the web and command-line, so it shows up in the project's status history identically. This run simply closes the loop on that already-delivered work so it's recorded properly. Routed to review so you can confirm and close it in the sweep.

---

**2026-06-08 15:36 — you**

verified
