---
id: T-0356
title: "Sprint fields are write-once — add goal (and dates/capacity) editing: web inline + pm_update_sprint"
type: feature
state: in_progress
created: 2026-06-11T20:36:36Z
updated: 2026-06-11T20:37:32Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
  channel: claude-code session
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "Web: sprint goal is inline-editable on the sprint card (click-to-edit, Enter/blur saves, Esc cancels), with stale-save detection (T-0317 pattern) — the 'No goal set' warning becomes the clickable empty state"
  - "MCP: new pm_update_sprint tool edits goal / title / start_date / end_date / capacity of an existing sprint; only provided fields change; empty-string goal clears it; body '## Goal' section stays in sync with frontmatter"
  - State transitions stay exclusively in pm_update_sprint_state (no fake transition needed to edit fields)
  - Dev wiki (/docs MCP tool surface) and the sprints screen help updated in the same change
  - SPR-030 actually gets its goal, dates, and right-sized capacity set using the new path (proof it works end-to-end)
out_of_scope:
  - Editing committed_items outside pm_commit_ticket/pm_uncommit_ticket
  - "Sprint body sections other than ## Goal (planning notes / demo / retro editing)"
  - Backfilling goals on completed sprints
code_anchors:
  - path: web/app/_actions/sprints.ts
    symbol: add updateSprintFields after createSprint; mirror updateSprintState write recipe + staleCheck
  - path: web/app/_components/SprintsList.tsx
    symbol: "SprintCard goal block (lines ~197-203) → SprintGoalEditor; pattern: EditableProjectField.tsx"
  - path: mcp-server/src/tools/sprint-tools.ts
    symbol: "add updateSprintInput + runUpdateSprint next to runUpdateSprintState; keep body ## Goal coherent"
  - path: mcp-server/src/server.ts
    symbol: register pm_update_sprint after pm_update_sprint_state (~line 455)
  - path: web/app/docs/content.ts
    symbol: MCP tool surface section — document pm_update_sprint
  - path: web/app/help/content
    symbol: sprints screen help — goal is now click-to-edit
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260611-2037
    model: claude-fable-5
    started: 2026-06-11T20:37:32Z
    status: in_progress
labels:
  - dogfood
  - sprint
attention: null
version: 3
---

## Problem

A sprint's goal is write-once at creation, on every surface. The web "New sprint" form, `pm new sprint --goal`, and `pm_create_sprint` can all set it — but no web action, CLI subcommand, or MCP tool can edit it afterwards (verified: the only post-creation sprint writers touch state/velocity/committed_items). The in-progress sprint card then nags "No goal set — a one-sentence focus keeps the sprint from sprawling." with no way to act on it. Dates and capacity are equally immutable, even though capacity auto-anchoring can produce misleading numbers (SPR-030 got 13 from a mean skewed by one 31-ticket sprint).

## Design

Two surfaces, both following existing patterns:

- **Web**: `updateSprintFields(project, id, patch, basedOnVersion)` server action (mirrors `setProjectField` + `updateSprintState` write recipe, with `staleCheck`); the sprint card's goal line becomes an inline click-to-edit (`EditableProjectText` pattern). Empty goal renders the existing warning text as the clickable placeholder.
- **MCP**: `pm_update_sprint` (modeled on `pm_update_ticket`: patch only supplied fields, `changed[]`, reject empty patch). Rewrites the body `## Goal` section so `pm_get_sprint`'s body matches frontmatter; clearing the goal restores the placeholder. Optimistic concurrency comes free via `writeMarkdownAtomic`/`applyVersion`. State transitions deliberately stay in `pm_update_sprint_state`.

Found while kicking off SPR-030 (this sprint has no goal and cannot be given one). AGENTS.md §10: missing a tool to satisfy a gate the right way → add the tool.
