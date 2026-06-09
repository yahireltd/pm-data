---
id: T-0317
title: Optimistic version checks — no silent lost edits (all entities)
type: feature
state: triaged
created: 2026-06-09T16:51:53Z
updated: 2026-06-09T17:04:23Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Each entity carries a version (hash or counter); a write includes the version it was based on, and the shared write path re-reads + rejects a stale write so a concurrent change is never silently overwritten -- across ALL entity types (tickets, meetings, decisions, sprints, milestones, status notes, etc.)
  - The read-modify-write is serialized per entity so two simultaneous saves can't interleave (web / CLI / MCP)
  - Web edit forms capture the version on load and send it; on a stale reject the form PRESERVES the user's unsaved text and shows 'this changed since you opened it' so they re-apply over the latest without retyping
  - Agent (MCP) writes are covered by the server-side serialization + version check; verified by a concurrent-edit test (two saves from the same stale base -> the second is rejected, nothing lost)
out_of_scope:
  - Auto-merge of conflicting same-field edits / a merge UI (the deferred 'conflict UX' polish, option d)
  - Real-time collaborative editing (CRDT / OT)
code_anchors:
  - path: cli/src/lib/io.ts
    symbol: writeMarkdown
  - path: mcp-server/src/lib/io.ts
    symbol: writeMarkdownAtomic
  - path: web/app/_components/EditableField.tsx
  - path: schemas
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - concurrency
attention: null
---

## Problem

The data is files with no version check. You open a record; someone else (or an agent) saves a change to it; you save — and your save writes the whole record back from the version *you* loaded, silently wiping theirs. Last-write-wins, no warning. This is the "I have it open, then Zsolt opens, then an agent updates" case exactly.

## Approach

Optimistic concurrency: a version per entity + a serialized read-modify-write in the shared data layer + reject-on-stale. Thread the expected version through the web / CLI / MCP write APIs. On the web, the edit forms keep the user's unsaved text when a save is rejected, so they re-apply over the latest rather than retyping.

## Context

The "a" of the a+b approach (T-0270 / SPR-029). Scope = all entity types; conflict behaviour = reject-but-keep-your-text (decided with the user). Allocator/write-path confirmed by the spike (T-0315).