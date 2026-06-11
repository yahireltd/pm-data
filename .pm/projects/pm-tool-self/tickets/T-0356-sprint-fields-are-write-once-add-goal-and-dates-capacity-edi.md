---
id: T-0356
title: "Sprint fields are write-once — add goal (and dates/capacity) editing: web inline + pm_update_sprint"
type: feature
state: review
created: 2026-06-11T20:36:36Z
updated: 2026-06-11T21:06:30Z
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
    status: completed
    progress:
      - at: 2026-06-11T20:45:26Z
        note: "Implementation complete on both surfaces: the web sprint card's goal line is now click-to-edit (with the keep-your-text protection if someone else saved first), and agents get a new pm_update_sprint tool that can change a sprint's goal, title, dates and capacity after creation. Docs and the in-app help updated in the same change. Functional tests against a fixture all pass (set/clear/validation/version stamping); web and MCP typechecks pass. An adversarial code review is running before commit; deploy + setting SPR-030's actual goal comes after."
    ended: 2026-06-11T21:06:30Z
    summary: |-
      **What we did**
      Made a sprint's goal, dates and size editable after the sprint exists. On the sprint screen the goal line is now click-to-edit — including the "No goal set" warning, which is now the way in rather than a dead end. AI agents got a matching tool so they can update these fields too. We also added guard rails: a finished or cancelled sprint can't be edited (its record is final), and if two people edit at once the second save is held back with the text kept safe.

      **Why we did it**
      The goal could only be set at the moment a sprint was created — on every surface. If you skipped it, the tool nagged "No goal set" forever while offering no way to set one. We hit this on the current sprint (SPR-030) and confirmed there was genuinely no path. Dates and size were equally frozen, and the auto-suggested size can be misleading (SPR-030 got 13 from one unusually large past sprint).

      **If we did nothing**
      Every sprint created without a goal would carry a permanent, unactionable warning; goals, dates and sizes would stay wrong forever unless someone hand-edited files on the server — which bypasses every safety check the tool has.

      **The benefit**
      Sprints can be tidied up as understanding improves, the nag message now earns its keep, and agents can keep sprint records accurate as part of their normal work. The current sprint gets a proper goal the moment this deploys.
    test_plan: |-
      After `pm-deploy` finishes (needs the SECOND deploy — commit 8e01eee — and hard-refresh the page):

      **Web — happy path**
      1. Open pm-tool-self → Sprints. On the SPR-030 card, click the "No goal set…" line → an input appears → type a goal → press Enter. The goal renders; reload the page — it persisted.
      2. Click the goal again, change it, then click ELSEWHERE on the page (blur-save). It saves with no false "changed by someone else" banner.
      3. Edit again and press Escape — your change is discarded, the old goal stays.

      **Web — edge cases**
      4. Two tabs on the Sprints page: edit the goal in tab 1, then (without refreshing tab 2) edit it in tab 2 — the save must be REJECTED with the "changed by someone else… your text is kept" note, and your typed text must still be in the input.
      5. A completed sprint's card (e.g. SPR-028) shows its goal as plain text — confirm it is NOT clickable.
      6. A planned (not started) sprint also gets the editor — check SPR-023-style planned cards if any exist.

      **MCP (after the pm-mcp service restart in pm-deploy)**
      7. `pm_update_sprint` on SPR-030 with goal + start_date/end_date + capacity → `pm_get_sprint` shows the frontmatter AND the body "## Goal" section matching.
      8. `pm_update_sprint` on a COMPLETED sprint (SPR-028) → must error "cannot edit a completed sprint".
      9. `pm_update_sprint` with no fields → must error "nothing to update".

      **Cross-impact**
      10. Sprint lifecycle still works: create a sprint, commit a ticket, start, complete with a velocity — unchanged behaviour (the new action shares the same write path).
      11. /docs dev wiki MCP table lists pm_update_sprint; /help → Sprints describes click-to-edit; README write-tools list includes it.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Dev wiki MCP table, sprints screen help, README write-tools list — all in the same commits.
labels:
  - dogfood
  - sprint
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-11T21:06:30Z
version: 5
---

## Problem

A sprint's goal is write-once at creation, on every surface. The web "New sprint" form, `pm new sprint --goal`, and `pm_create_sprint` can all set it — but no web action, CLI subcommand, or MCP tool can edit it afterwards (verified: the only post-creation sprint writers touch state/velocity/committed_items). The in-progress sprint card then nags "No goal set — a one-sentence focus keeps the sprint from sprawling." with no way to act on it. Dates and capacity are equally immutable, even though capacity auto-anchoring can produce misleading numbers (SPR-030 got 13 from a mean skewed by one 31-ticket sprint).

## Design

Two surfaces, both following existing patterns:

- **Web**: `updateSprintFields(project, id, patch, basedOnVersion)` server action (mirrors `setProjectField` + `updateSprintState` write recipe, with `staleCheck`); the sprint card's goal line becomes an inline click-to-edit (`EditableProjectText` pattern). Empty goal renders the existing warning text as the clickable placeholder.
- **MCP**: `pm_update_sprint` (modeled on `pm_update_ticket`: patch only supplied fields, `changed[]`, reject empty patch). Rewrites the body `## Goal` section so `pm_get_sprint`'s body matches frontmatter; clearing the goal restores the placeholder. Optimistic concurrency comes free via `writeMarkdownAtomic`/`applyVersion`. State transitions deliberately stay in `pm_update_sprint_state`.

Found while kicking off SPR-030 (this sprint has no goal and cannot be given one). AGENTS.md §10: missing a tool to satisfy a gate the right way → add the tool.

## Conversation

**2026-06-11 21:06 claude-code:** Run run-20260611-2037 completed — **What we did**
Made a sprint's goal, dates and size editable after the sprint exists. On the sprint screen the goal line is now click-to-edit — including the "No goal set" warning, which is now the way in rather than a dead end. AI agents got a matching tool so they can update these fields too. We also added guard rails: a finished or cancelled sprint can't be edited (its record is final), and if two people edit at once the second save is held back with the text kept safe.

**Why we did it**
The goal could only be set at the moment a sprint was created — on every surface. If you skipped it, the tool nagged "No goal set" forever while offering no way to set one. We hit this on the current sprint (SPR-030) and confirmed there was genuinely no path. Dates and size were equally frozen, and the auto-suggested size can be misleading (SPR-030 got 13 from one unusually large past sprint).

**If we did nothing**
Every sprint created without a goal would carry a permanent, unactionable warning; goals, dates and sizes would stay wrong forever unless someone hand-edited files on the server — which bypasses every safety check the tool has.

**The benefit**
Sprints can be tidied up as understanding improves, the nag message now earns its keep, and agents can keep sprint records accurate as part of their normal work. The current sprint gets a proper goal the moment this deploys.
