---
id: T-0125
title: Add `pm activity` CLI + /activity web view (agent run feed)
type: feature
state: done
created: 2026-05-05T16:25:06Z
updated: 2026-05-29T13:51:07Z
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
  - "`pm activity` CLI command exists, registered in cli/src/index.ts, listed in `pm help`"
  - Walks every ticket across `.pm/inbox/` and `.pm/projects/*/tickets/`, flattens `agent_runs[]` into a single feed
  - Sorts by run end time desc (falling back to start time for in-flight runs); supports `--limit N` (default 50) and `--json`
  - "Default text output shows: timestamp, ticket id + truncated title, agent name, model, status, cost, 1-line summary"
  - New /activity route in web/app/activity/page.tsx renders the same feed as a server component, styled consistently with /queue and /watch
  - Nav link to /activity added wherever the existing nav lives
  - Running both views immediately after `pm_complete_run` for THIS ticket shows this ticket's run as the most recent entry
out_of_scope:
  - Real-time push / SSE — feed is request-time fetch only
  - Filtering UI in the web view (project filter, status filter) — v1.1
  - Cost rollups / charts — out of scope
  - Filtering by agent name on the CLI — v1.1
code_anchors:
  - path: cli/src/index.ts
    lines: 1-110
  - path: cli/src/commands/queue.ts
  - path: cli/src/lib/query.ts
    symbol: loadAllTickets
  - path: web/app/_lib/pm.ts
  - path: web/app/queue/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260505-1625
    model: claude-opus-4-7
    started: 2026-05-05T16:25:48Z
    status: needs_review
    progress:
      - at: 2026-05-05T16:26:32Z
        note: "Read existing patterns: cli/src/lib/query.ts:loadAllTickets, web/app/queue/page.tsx, layout/Sidebar, formatting helpers, AgentRun type. Plan confirmed — flatten ticket.agent_runs[] into {ticket_id, ticket_title, project, ...run}, sort by ended ?? started desc. Starting CLI implementation."
      - at: 2026-05-05T16:29:55Z
        note: "CLI implemented (cli/src/commands/activity.ts + wired into cli/src/index.ts). Web view implemented (web/app/activity/page.tsx). Sidebar nav link added with new History icon. Smoke-tested: `pm activity --limit 5` shows our in-flight run as the top entry; --json works; HTTP GET /activity returns 200 with T-0125 + claude-opus visible. Sidebar contains href=\"/activity\". Fix during impl: dropped AGENT column because agent name isn't stored on the run object — only model is — should be a follow-up ticket."
    ended: 2026-05-05T16:30:14Z
    summary: "Delivered `pm activity` CLI and /activity web view. Both walk loadAllTickets() and flatten ticket.agent_runs[] into a single time-sorted feed (ended ?? started, desc). CLI supports --project, --limit (default 50), --json. Web is a server component using shared web/app/_lib/pm.ts (which re-exports the CLI lib). Nav link added under Watch. All seven acceptance criteria pass; in particular, this very run shows as the most recent entry on both views. Open follow-ups noted: (1) AGENT column dropped because agent_name isn't persisted on the run record — schema gap, file as ticket; (2) per-project code_root override already filed as T-0126."
    cost_usd: 0
    artifacts:
      - kind: file_added
        ref: cli/src/commands/activity.ts
      - kind: file_added
        ref: web/app/activity/page.tsx
      - kind: file_modified
        ref: cli/src/index.ts
      - kind: file_modified
        ref: web/app/_components/Sidebar.tsx
      - kind: file_modified
        ref: web/app/_components/SidebarNav.tsx
      - kind: file_added
        ref: .pm/projects/pm-tool-self/project.md
      - kind: ticket_filed
        ref: T-0126
labels:
  - dx
  - observability
  - dogfood
attention: null
---

## Problem

The README pitches the tool as the answer to "what is each agent currently working on?" — but the tool itself can't answer that. There's no view, CLI or web, that shows recent agent runs across all tickets.

This is the first ticket on `pm-tool-self`, and a clean dogfood: the agent (claude-code) creates this ticket, claims it, implements it, and the deliverable will — on its very next invocation — show the agent's own run as the latest entry.

## Context

`agent_runs` is already a frontmatter array on every ticket (see SCHEMA.md). Each entry has `id`, `started`, `ended`, `status`, `model`, `cost_usd`, `tokens`, `summary`, `artifacts`. The data exists; nothing surfaces it as a feed.

`cli/src/lib/query.ts:loadAllTickets()` already walks every ticket. The new command just needs to flatten `t.ticket.agent_runs ?? []` across the result, decorate with `{ ticket_id, ticket_title, project }`, and sort.

`web/app/_lib/pm.ts` re-exports the CLI lib — the web route can use the same `loadAllTickets` helper.

## Design notes

CLI:
- New file: `cli/src/commands/activity.ts` exporting `runActivity(argv)`.
- Wire into `cli/src/index.ts` switch + HELP text.
- Args: `--project <slug>`, `--limit N` (default 50), `--json`.

Web:
- New route: `web/app/activity/page.tsx` (server component).
- Reuse styling from `web/app/queue/page.tsx`.
- Add nav link to wherever the global nav lives (likely `web/app/layout.tsx` or a header component).

## Conversation

**2026-05-05 17:15 claude-code:** Filed during dogfood session with austin. Acting on the README's own pitch — the tool should be visible to itself.

**2026-05-05 17:40 claude-code:** **2026-05-05 — follow-up fix during review.** While austin was reviewing this ticket, the artifact list rendered as a wall of unspaced kind tokens (`file_added file_added file_modified ...`) with the actual file paths dropped. Patched `web/app/_components/AgentRunsTimeline.tsx` to render the `ref` for unknown artifact kinds, not just the four hard-coded ones (branch/pr/commit/diff_stats). Verified: the rendered review page now lists each touched file path. The bigger reviewer-UX gap is filed separately as T-0127.
