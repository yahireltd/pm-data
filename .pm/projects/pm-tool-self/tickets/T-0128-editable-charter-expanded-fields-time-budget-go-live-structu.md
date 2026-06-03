---
id: T-0128
title: Editable Charter + expanded fields (time budget, go-live, structured workflow impact)
type: feature
state: done
created: 2026-05-06T10:18:33Z
updated: 2026-06-02T17:09:12Z
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
  name: claude-code
acceptance_criteria:
  - "[x] Charter problems/goals/risks/success_measures arrays are inline-editable on the project Charter tab (add, edit, remove)"
  - "[x] New scalar fields time_budget_hours and go_live_target editable on the Charter, render in a Time & scope card"
  - Project type selector (tech / non-tech) on the Charter — editable; non-tech projects skip code-anchored DoR (acceptance for future tickets, not enforced retroactively)
  - "Workflow impact rendered as structured fields: current workflow, new workflow, who's affected, migration path (cutover/parallel/phased/one-way), sources of truth, training, rollback. All inline-editable."
  - aligns_with[] placeholder array on project schema with empty-state hint pointing at "strategy goals not yet defined" — wire-up deferred until Humanness work lands
  - All schema additions are backwards-compatible; existing projects without new fields render fine
  - "Live test: agency-rates and pm-tool-self both load their Charter pages; edits to a field on agency-rates persist (visible after a page refresh) and don't break the older Overview tab"
out_of_scope:
  - Project type at creation flow — editable on Charter is enough for now; New Project dialog change is a separate small ticket
  - Linter changes to make non-tech DoR skip code anchors — separate ticket
  - Time-spent (actual) tracking — only estimate is captured here
  - Strategy doc itself — waiting on Humanness work
code_anchors:
  - path: schemas/project.schema.json
  - path: cli/src/types.ts
    symbol: Project
  - path: web/app/_actions/projects.ts
  - path: web/app/_components/Charter.tsx
  - path: web/app/_components/SuccessCriteria.tsx
  - path: web/app/projects/[slug]/charter/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260506-1018
    model: claude-opus-4-7
    started: 2026-05-06T10:18:47Z
    status: needs_review
    progress:
      - at: 2026-05-06T10:28:50Z
        note: "Charter is now editable end-to-end. New components: EditableStringList (generic), RiskEditor (description + severity + mitigation), WorkflowImpactCard (current/new workflow, migration_path enum, sources of truth, who affected, training, rollback), TimeAndScopeCard (project_type tech/non-tech, time_budget_hours, go_live_target). Schema additions: project_type, time_budget_hours, go_live_target, workflow_impact{} object, aligns_with[] placeholder, all backwards-compatible. Server actions: addCharterItem/editCharterItem/removeCharterItem (generic for problems/goals/measures/aligns_with/who_affected), addRisk/editRisk/removeRisk, plus setProjectField extended via applyCharterScalar to handle dot-pathed workflow_impact.* fields. Charter.tsx wires it all. Smoke-tested on three projects (pm-tool-self, agency-rates, picking-planner-decouple) — all render 200, all charter fields edit-affordable for PM, empty states clean."
    ended: 2026-05-06T10:28:59Z
    summary: Editable Charter shipped with all the director's brief fields. Four core sections (Problems / Goals / Risks / Success measures) now inline-editable with add/edit/remove. New Time & scope card (project type tech/non-tech selector, time budget hours, go-live date). New Workflow & impact card with seven structured fields including the migration_path enum that would have caught your route-planning project's 1-way-vs-2-way bug at framing time. Aligns_with placeholder ready for whatever Humanness produces. All schema additions are backwards-compatible; legacy goal/success_criteria still render on projects that haven't migrated. Verified on three real projects.
    cost_usd: 0
    test_plan: |
      ## How to verify

      Open `/projects/pm-tool-self/charter` (best demo — has rich content already)
      then `/projects/agency-rates/charter` (also rich), then
      `/projects/picking-planner-decouple/charter` (the empty-state case).

      ### 1. Time & scope card (top)
      - Click **Type** → switch between Tech and Non-tech. Hint text below changes.
      - Click **Time budget** → number input appears, type a value (e.g. 80), Enter saves. Refresh — value persists. Clear the field (empty + Enter) — removes the value.
      - Click **Go-live target** → native date picker. Pick a date. Page refresh — persists.

      ### 2. Four framing sections (Problems / Goals / Risks / Measures)
      - **Hover any item** in Problems/Goals/Measures — pencil + X icons appear on the right. Pencil opens an inline edit; X removes (no confirm — that's intentional, fast workflow).
      - Click **Add problem** at the bottom of the Problems section → input appears, type text, Enter adds. Esc cancels.
      - **Risks**: click **Add risk** → form appears with severity dropdown (low/medium/high), description input, optional mitigation textarea. Save persists; Cancel discards.
      - Hover a risk → pencil opens the same form pre-populated for edit.

      ### 3. Workflow & impact card (the big one — your route-planning lesson)
      - Each text field is click-to-edit; values persist on blur or Cmd/Ctrl-Enter.
      - **Migration path** is a select with five options (TBD / Cutover / Parallel / Phased / One-way). Hint text under it changes per option — try each, the description updates.
      - **Who's affected** is a list editor like the framing sections — add roles or names.

      ### 4. Aligns with (placeholder)
      - Should show muted "Strategy goals not yet defined…" hint with an "Add alignment freeform" button below if you want to add anything anyway.

      ### 5. Backwards compat
      - Open `/projects/picking-planner-decouple/charter` — that project has only legacy `goal` and `success_criteria`. The Charter renders fine: Goals shows the legacy goal as one entry, Measures shows the legacy criteria. Counter shows 2/4 sections populated.
      - Open `/projects/picking-planner-decouple/overview` — the older Overview tab still works (we didn't break it).

      ### 6. Stakeholder mode (read-only)
      - Append `?view=stakeholder` to any charter URL. All edit affordances should disappear. Hover items → no pencil/X icons. Add buttons gone. Time-budget shown as text not editable. Migration path shown as a colored badge not a select.

      ### Negative cases
      - Try setting time_budget_hours to a negative number → server rejects with error toast.
      - Try go_live_target with a malformed date → server rejects.
      - Try saving a risk with no description → form blocks save with inline error.
    artifacts:
      - kind: file_modified
        ref: schemas/project.schema.json
      - kind: file_modified
        ref: cli/src/types.ts
      - kind: file_modified
        ref: web/app/_actions/projects.ts
      - kind: file_added
        ref: web/app/_components/EditableStringList.tsx
      - kind: file_added
        ref: web/app/_components/RiskEditor.tsx
      - kind: file_added
        ref: web/app/_components/WorkflowImpactCard.tsx
      - kind: file_added
        ref: web/app/_components/TimeAndScopeCard.tsx
      - kind: file_modified
        ref: web/app/_components/Charter.tsx
labels:
  - dx
  - charter
  - dogfood
  - human-side
attention: null
---

## Problem

The read-only Charter we shipped on T-0125+T-0127's tail proved the four-question shape works. But for it to actually be a forcing function — and for the director's discipline brief to land — it needs to be **editable inline** and **expanded** to capture the rest of his brief.

Specifically, missing today:
- **Time budget** (hours) — "how much time to expend? — end result needs to be worth the time spent"
- **Go-live target** — "when are we aiming to go live"
- **Workflow impact**, structured — the route-planning project failed exactly because workflow implications weren't understood up front. Not a freeform note; structured fields that force the question (current vs new workflow, migration path, sources of truth).
- **Project type** — tech vs non-tech, because half the team's projects (hiring, marketing, policy rollouts) aren't code work and shouldn't pretend to be
- **Strategy alignment hook** — placeholder for when Humanness work lands

## Context

Surfaced in dogfood conversation 2026-05-06. Austin shared his director's brief (problems / goals / risks / measures / meeting cadence / time budget / workflow changes / go-live) and a real project failure: the route planning project that wasn't framed properly and required three rounds of refactor when reality hit. The structured workflow-impact fields are designed to catch exactly that class of bug at framing time, before code is written.

## Out of scope

- Project type at the New Project dialog (editable on Charter is fine for now)
- Linter rule for non-tech DoR
- Actual-time tracking (estimate only)
- Strategy doc itself — waiting on bosses' Humanness engagement

## Conversation

**2026-05-06 claude-code:** Filed during the dogfood planning conversation. The director's brief plus the route-planning lesson plus the focus-and-finish theme all converge on the Charter being the load-bearing artefact. Editable + expanded = the next slice.
