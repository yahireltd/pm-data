---
id: T-0268
title: Richer review handoff — surface what changed + what to test inline at review
type: feature
state: triaged
created: 2026-06-05T22:53:16Z
updated: 2026-06-22T14:04:33Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - On the /review LIST, each review-state ticket shows a small marker that a change summary + test plan are available, so it's discoverable without clicking in blind.
  - The marker links through to the ticket detail page, where ReviewBanner already surfaces the summary, files changed, acceptance-criteria checklist, and test_plan (no rebuild of that).
  - Confirm the detail-page ReviewBanner remains the review surface and still shows all of the above.
out_of_scope:
  - Inline /review list-row expansion or a click-to-open drawer (decided out — the detail page is the review surface).
  - Auto-posting the test_plan as a conversation comment.
code_anchors:
  - path: web/app/review/page.tsx
    symbol: list marker for review-state rows
  - path: web/app/_components/TicketRow.tsx
    symbol: review-state indicator
  - path: web/app/_components/ReviewBanner.tsx
    symbol: existing detail surface (no change)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Re-scoped 2026-06-08 — mostly already built

The review hand-off is **already surfaced on the ticket detail page**: `ReviewBanner` shows the run summary, files changed, the acceptance-criteria checklist, and the test_plan. So the original "it's buried" concern is largely solved.

## What's left (decided: detail page is enough — shrink)

The only gap: the **/review LIST** doesn't indicate that this rich change-info exists, so you click in blind. Add a small marker on each review-state row that a summary + test plan are available, linking through to the detail page. That's the whole remaining job.

## Out of scope

Inline list-row expansion or a click-to-open drawer (the detail page is the review surface); auto-posting the test_plan as a comment.

## Conversation

**2026-06-22 14:04 claude-code:** **Backlog triage 2026-06-22 — largely already built; close or trim.**

The richer review handoff is mostly live: the ticket detail page already shows a `ReviewBanner` with the run summary, files changed, the acceptance-criteria checklist and the test_plan at review time. The only residual is surfacing a "what changed / what to test" marker on the **list row** (not just the detail page). Recommend either closing as done, or trimming the scope down to just that list-row marker.
