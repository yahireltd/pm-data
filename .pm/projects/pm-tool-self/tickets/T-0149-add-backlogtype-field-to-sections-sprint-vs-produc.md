---
id: T-0149
title: Add backlog_type field to sections (sprint vs product)
type: feature
state: wontfix
priority: p2
created: 2026-05-29T17:57:12Z
updated: 2026-06-22T15:35:26Z
project: pm-tool-self
section: null
parent: null
children: []
order: 20480
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
backlog_status: rejected
source: charter
version: 2
---

# T-0149: Add backlog_type field to sections (sprint vs product)

## Problem

Big-rock #5 (missing). Section-level `backlog_type` enum (sprint|product|other) to complement ticket.backlog_status. Schema + Section type + web editor.

## Acceptance criteria

_To define at sprint promotion._

## Audit note (2026-06-02)

**Closed — delivered differently.** The sprint-vs-product distinction shipped at the ticket level (`ticket.backlog_status` + `Sprint.committed_items`, commit 49b33b1). A section-level `backlog_type` is architecturally redundant — sections are visual groupings, not lifecycle containers. Marked `rejected` to drop it from the active backlog.

## Conversation

**2026-06-22 14:04 claude-code:** **Backlog triage 2026-06-22 — superseded; recommend closing as wontfix.**

The body already records this was delivered differently: a `backlog_type` field on sections was rejected in favour of doing it at the ticket level (`ticket.backlog_status` + `Sprint.committed_items`, commit `49b33b1`). No further work needed.
