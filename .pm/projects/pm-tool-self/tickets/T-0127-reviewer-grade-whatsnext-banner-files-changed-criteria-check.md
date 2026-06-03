---
id: T-0127
title: "Reviewer-grade WhatsNext banner: files changed, criteria checklist, test plan"
type: feature
state: done
created: 2026-05-05T17:41:08Z
updated: 2026-05-29T15:23:06Z
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
  - "[x] When ticket.state == 'review' and there's a recent agent_run, the WhatsNext banner shows: (a) files-changed list with paths derived from the latest run's artifacts (file_added/file_modified/file_deleted kinds), (b) acceptance criteria rendered inline as an interactive verify-checklist, (c) the test_plan if present on the run"
  - "[x] Each checklist item persists its tick state in localStorage keyed by ticket id + criterion index"
  - "[x] Approve button is disabled until every acceptance criterion is ticked (override available via 'Approve anyway' secondary control with confirm)"
  - "[x] AgentRun schema gains an optional `test_plan` field (string, markdown). pm_record_run_progress and pm_complete_run accept it. Existing runs without it render a 'no test plan provided' hint"
  - "[x] All four artifact kinds (file_added, file_modified, file_deleted, file_renamed) get a stable rendering in AgentRunsTimeline with monospace path"
out_of_scope:
  - Diff rendering inside the banner — link out to git/IDE instead
  - Per-criterion comment threads — the existing Conversation section is enough for now
  - Auto-running tests — humans verify, tool just shows the plan
code_anchors:
  - path: web/app/_components/WhatsNext.tsx
    lines: 191-201
  - path: web/app/_components/AgentRunsTimeline.tsx
  - path: cli/src/types.ts
    symbol: AgentRun
  - path: mcp-server/src/tools/complete-run.ts
  - path: mcp-server/src/tools/record-run-progress.ts
  - path: schemas/ticket.schema.json
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260505-1747
    model: claude-code
    started: 2026-05-05T17:47:49Z
    status: needs_review
    summary: "Reviewer-grade WhatsNext banner shipped as a dedicated ReviewBanner client component. When ticket.state=review, WhatsNext delegates to ReviewBanner which renders Files changed (file_* artifacts with +/~/−/→ glyphs), Verify (acceptance_criteria as a localStorage-backed checklist), and Test plan (markdown if present, hint otherwise). Approve disabled until all criteria ticked, with secondary Approve anyway override. Schema/types/MCP all gained test_plan (optional string); the artifact enum extended with file_added/_modified/_deleted/_renamed/ticket_filed. Live test: T-0125 (in review) now shows 6 files with correct glyphs, Verify (0/7), and a disabled Approve."
    progress:
      - at: 2026-05-05T17:51:07Z
        note: "Picked up after web-UI claim. Reading existing code: record-run-progress.ts, ticket.schema.json, MarkdownBody, _actions/tickets. Plan: types → MCP tools → AgentRunsTimeline file_* polish → WhatsNext review banner with localStorage-backed checklist. Will treat T-0125 (currently in review) as the live test surface."
      - at: 2026-05-05T17:55:11Z
        note: "All five pieces shipped. Schema: agent-run.schema.json gains test_plan + four file_* artifact kinds + ticket_filed in the enum. Types: AgentRun.test_plan?:string. MCP: record_run_progress and complete_run accept and persist test_plan. Timeline: file_* kinds get +/~/−/→ glyphs with action color. ReviewBanner: new client component with Files changed, Verify checklist (localStorage-backed), Test plan (markdown), gated Approve + Approve-anyway override. WhatsNext delegates to ReviewBanner when state=review. Verified: ReviewBanner renders on T-0125 with 6 files (correct glyphs), Verify (0/7), Approve button disabled, Approve anyway link present, summary surfaced. Closing run with a real test_plan that exercises the new UI itself."
    ended: 2026-05-05T17:56:35Z
    cost_usd: 0
    test_plan: |
      ## How to verify

      1. **Open T-0127 itself** at `/tickets/T-0127` — it's in review state, so the
         new ReviewBanner replaces the old single-paragraph review banner. You
         should see four sections (Review needed → Files changed → Verify →
         Test plan → Actions). This text you're reading IS the Test plan
         section, rendered as markdown.

      2. **Files changed** should list 7 entries with action glyphs:
         - `+` (green) for `web/app/_components/ReviewBanner.tsx` — new file
         - `~` (amber) for the rest (`WhatsNext.tsx`, `AgentRunsTimeline.tsx`,
           `cli/src/types.ts`, the two MCP tool files, `agent-run.schema.json`)

      3. **Verify checklist**: 5 acceptance criteria, all unchecked. The
         **Approve & mark done** button should be **disabled and grayed**, with
         `cursor-not-allowed`. Hover the button — title attribute reads "Tick
         every criterion to enable".

      4. **Tick a few**: counter updates `Verify (0/5)` → `(3/5)`. Approve
         stays disabled.

      5. **Tick all five**: counter reaches `(5/5)` and Approve becomes
         clickable. The "Approve anyway" link disappears.

      6. **Refresh the page** without ticking everything — tick state survives.
         DevTools → Application → Local Storage → keys matching
         `pm:verify:T-0127:0` through `pm:verify:T-0127:4`.

      7. **Approve anyway**: with criteria unchecked, click "Approve anyway" —
         pops a `confirm()`. Cancel keeps state. Confirm transitions ticket to
         done and clears the localStorage entries.

      8. **Send back to agent**: pops a `prompt()` for a reason and transitions
         back to ready with attention=agent. Don't actually do this on T-0127
         unless you want the run reverted.

      9. **Cross-check on T-0125** (also in review): same banner, with 6 files
         and the older 7-criterion list. Shared localStorage namespace
         correctly scoped (keys prefixed with ticket id).

      10. **Negative case**: T-0125's run pre-dates the test_plan field — its
          banner should show "Agent did not provide a test plan for this run."
    artifacts:
      - kind: file_added
        ref: web/app/_components/ReviewBanner.tsx
      - kind: file_modified
        ref: web/app/_components/WhatsNext.tsx
      - kind: file_modified
        ref: web/app/_components/AgentRunsTimeline.tsx
      - kind: file_modified
        ref: cli/src/types.ts
      - kind: file_modified
        ref: mcp-server/src/tools/record-run-progress.ts
      - kind: file_modified
        ref: mcp-server/src/tools/complete-run.ts
      - kind: file_modified
        ref: schemas/agent-run.schema.json
labels:
  - dx
  - review-ux
  - dogfood
attention: null
---

## Problem

Surfaced during dogfood review of T-0125. The orchestrator (austin) opened the ticket page expecting a reviewer's panel — what changed, what to verify, what to test — and instead got:

- A summary paragraph (good, but prose-only)
- A row of `file_added file_added file_modified ...` with no paths (bug — fixed inline as a follow-up patch on T-0125)
- Acceptance criteria in a small right-side column with decorative checkboxes that didn't gate anything
- A bare "Approve & mark done" button that lets you approve sight-unseen

The data exists (artifacts carry refs, criteria are on the ticket, summaries are full-fidelity). The review banner just doesn't compose them into a decision-support surface.

## Context

The README pitches this tool as the bridge for "humans orchestrate, agents execute." The orchestrator side breaks down at the review gate. Without files-changed + criteria-as-checklist, a human reviewing agent work either rubber-stamps or falls back to `git diff` in another terminal — both of which mean the tool's adding nothing at the most important moment.

## Design notes

**WhatsNext banner — review state expansion.** Today the review-state branch in `WhatsNext.tsx:191-201` returns one paragraph and two buttons. Replace with a multi-section panel:

1. **Files changed** — read `latest_run.artifacts.filter(kind starts with 'file_')`, render as a list of monospace paths grouped by kind (added/modified/deleted/renamed). Counts in the section header.
2. **Verify** — render `ticket.acceptance_criteria` as `<input type="checkbox">` items. Tick state persisted in `localStorage` (key: `pm:verify:T-0125:0`, ...). All-ticked state enables the Approve button.
3. **Test plan** — if `latest_run.test_plan` present, render as markdown. Otherwise a muted "Agent did not provide a test plan" line.
4. **Approve / Send back** — the existing actions, but Approve disabled until checklist complete (or override).

**Schema add.** `AgentRun.test_plan: string` (optional, markdown). Update:
- `cli/src/types.ts:AgentRun`
- `schemas/ticket.schema.json` (the agent_runs subschema)
- `mcp-server/src/tools/record-run-progress.ts` and `complete-run.ts` to accept and persist it

**AgentRunsTimeline.** Already partially fixed on T-0125. Make file_* kinds first-class with stable formatting (icon per kind, monospace path) so they're not just falling through the unknown-kind branch.

## Out of scope

- Inline diff rendering — link out to `vscode://` or git web URL instead
- Per-criterion threaded discussion — Conversation is enough
- Tests run from the tool — humans verify; the tool just shows the plan

## Conversation

**2026-05-05 17:45 claude-code:** Filed during dogfood review of T-0125. Austin: "I cant see what the next steps shoould be for me … should be showing what changed, what to test to prove the changes worked." This ticket is exactly that.

**2026-05-29 15:14 Austin:** ### Testing H1

#### Testing H2

> ## Some quote 

`Testing code`

* list item
* list item

1. another list item
2. more list item
3. check box
4. check

**2026-05-29 15:15 Austin:** ### Heading1 

#### heading 2

**bold**

*italic*

* list item
* list item

1. another type of list item

* checkbox

---

**2026-05-29 15:26 — Austin**

# heading 1

## heading 2

* list item

<br />

1. different list item

<br />

* test
* test2

> a quote

```
some code
```

---

**2026-05-29 15:48 — Austin**

# h1

## h2

**bold**

*italic*

`code`

* ul

<br />

1. ol

* [ ] checkbox
* [ ] <br />
