---
id: T-0412
title: pm_update_milestone + editable milestone title/date — rename without delete+recreate
type: feature
state: done
created: 2026-06-17T17:43:26Z
updated: 2026-06-22T15:58:05Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
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
labels:
  - dx
  - milestones
attention: null
version: 2
---

## Problem
A milestone's title (and target_date) can't be edited anywhere. MCP has create / state / set_criteria / owner / stakeholders but no general field updater, and the web milestone card renders the title as plain text. So to relabel milestones (e.g. restructuring P-0006's into chapters today) the only option was to DELETE all of them and recreate — losing their history. There's also no MCP delete (only the web removeMilestone). AGENTS.md §10: missing a tool → add the tool.

## Proposal
- `pm_update_milestone`: edit title, target_date, phase. Mirror `pm_update_sprint`.
- Web: make the milestone title + date editable inline on the milestone card.
- (Optional) `pm_delete_milestone` for MCP parity with the web's removeMilestone.

## Acceptance criteria
- A milestone's title and target_date are editable via MCP and the web, without delete+recreate.
- (Stretch) a milestone is deletable via MCP.

## Conversation

**2026-06-22 15:58 — you**

previously done

---

**2026-06-22 15:58 — you**

Records: docs none-needed; tech-session none-needed; status-note none-needed.
