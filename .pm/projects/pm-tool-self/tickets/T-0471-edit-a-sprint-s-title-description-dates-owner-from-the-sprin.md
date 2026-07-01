---
id: T-0471
title: Edit a sprint's title, description, dates & owner from the Sprints view
type: feature
state: done
created: 2026-06-23T12:06:44Z
updated: 2026-07-01T11:58:21Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
  channel: web
  contact: austin@yahire.com
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A sprint card on the Sprints view has an Edit affordance that opens a form for title, goal/description, start date, end date, capacity and owner."
  - "[x] Editing is offered only for planned/in_progress sprints (completed/cancelled stay frozen, matching the server guard)."
  - "[x] Saving persists all changed fields; only changed fields are sent; concurrent-edit (stale) is surfaced, not silently overwritten."
  - "[x] An owner (dev lead) can be set from the Sprints view, with a roster type-ahead — parity with the Phases planner."
  - "[x] The sprint card shows the owner when one is set."
out_of_scope: []
code_anchors:
  - path: web/app/_components/SprintsList.tsx
    symbol: EditSprintForm
    note: edit form (title/goal/dates/capacity/owner) + Edit pencil affordance on the card
  - path: web/app/projects/[slug]/sprints/page.tsx
    note: passes roster people[] for the owner type-ahead
  - path: web/app/_actions/sprints.ts
    symbol: updateSprintFields / setSprintOwner
    note: existing actions the form calls
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260623-1207
    model: claude-opus-4-8
    started: 2026-06-23T12:07:48Z
    status: completed
    ended: 2026-06-23T12:08:08Z
    summary: "You can now edit a sprint's details directly from the Sprints view. Each active or planned sprint card has a small pencil \"Edit\" button that opens a short form where you can change the sprint's title, its goal/description, its start and end dates, its capacity, and who owns it (the dev lead) — all in one place and saved together. The owner field offers your team's names as you type, the same way the Phases planner does, so setting an owner no longer requires going to the Phases page. Finished and cancelled sprints stay locked (they're a historical record), so the Edit button only appears on sprints you can still change. If two people edit the same sprint at once, the second save is caught and flagged rather than silently overwriting. Before this, the title and dates couldn't be edited at all on this screen (only the goal, via a hidden click-to-edit), and an owner could only be set on the Phases planner. Benefit: sprint housekeeping — renaming, re-dating, adding a goal, assigning an owner — is now quick and obvious from the place you actually manage sprints."
    test_plan: |-
      1. Go to a project → Sprints. On a planned or active sprint card, click the pencil (Edit) next to the sprint id.
      2. The card shows a form: Title, Goal/description, Start date, End date, Owner (dev lead), Capacity.
      3. Change the title and save — the card updates. Repeat for goal, dates, and capacity.
      4. Set an owner: start typing a name — the team roster appears as suggestions (type-ahead). Pick one and save; the card shows "Owner: <name>".
      5. Clear a field (e.g. blank the goal or a date) and save — it's removed from the card.
      6. Set end date earlier than start date — it should refuse with a clear message.
      7. Confirm completed and cancelled sprints have NO pencil (they're frozen).
      8. Cross-check: the Phases planner can still set the sprint owner too, and both reflect the same value after refresh.
      Edge: if someone else edited the same sprint meanwhile, saving shows a "someone else changed this" message instead of overwriting.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 12
branch: master
---

## Problem

Dogfood asks on the Sprints view:
- Couldn't **edit a sprint's title, description (goal) or dates** — title/dates were display-only; only the goal had an inline editor.
- Couldn't **set a sprint owner** here — that was only possible on the Phases planner.

## Done

- Added an **Edit** (pencil) affordance on each editable sprint card → an inline form for **title, goal/description, start date, end date, capacity, and owner**. Saves changed fields via `updateSprintFields` (carries version for stale-detection) + `setSprintOwner` for the owner. Owner uses a roster type-ahead (`loadRoster`), matching the Phases planner.
- Gated to planned/in_progress (completed/cancelled are frozen records — server already rejects edits).
- Sprint card now shows the owner when set.
- Replaced the old inline goal-only editor with this fuller form (one edit path).

## Conversation

**2026-06-23 12:08 claude-code:** Run run-20260623-1207 completed — You can now edit a sprint's details directly from the Sprints view. Each active or planned sprint card has a small pencil "Edit" button that opens a short form where you can change the sprint's title, its goal/description, its start and end dates, its capacity, and who owns it (the dev lead) — all in one place and saved together. The owner field offers your team's names as you type, the same way the Phases planner does, so setting an owner no longer requires going to the Phases page. Finished and cancelled sprints stay locked (they're a historical record), so the Edit button only appears on sprints you can still change. If two people edit the same sprint at once, the second save is caught and flagged rather than silently overwriting. Before this, the title and dates couldn't be edited at all on this screen (only the goal, via a hidden click-to-edit), and an owner could only be set on the Phases planner. Benefit: sprint housekeeping — renaming, re-dating, adding a goal, assigning an owner — is now quick and obvious from the place you actually manage sprints.

---

**2026-07-01 11:58 — you**

working as described
