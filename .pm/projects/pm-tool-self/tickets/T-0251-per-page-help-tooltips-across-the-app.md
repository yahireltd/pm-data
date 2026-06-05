---
id: T-0251
title: Per-page help + tooltips across the app
type: feature
state: review
created: 2026-06-05T21:25:01Z
updated: 2026-06-05T21:51:40Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Every main page has a short contextual help blurb (reuse the existing HelpHint pattern in PageHeader)
  - Non-obvious fields/controls get a tooltip explaining what they are and why they matter (plain language)
  - Coverage is consistent — a pass across dashboard, projects (overview/charter/phase/deliver), pre-projects, meetings, dev-tickets, inbox, review, tickets
  - Help text is jargon-free and aimed at a non-technical PM/stakeholder
out_of_scope: []
code_anchors:
  - path: web/app/projects/[slug]/overview/page.tsx
  - path: web/app/_components/PhaseCommandCenter.tsx
  - path: web/app/dev-tickets/page.tsx
  - path: web/app/inbox/page.tsx
  - path: web/app/meetings/page.tsx
  - path: web/app/review/page.tsx
  - path: web/app/tickets/page.tsx
  - path: web/app/pre-projects/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2151
    model: claude-opus-4-8
    started: 2026-06-05T21:51:23Z
    status: completed
    ended: 2026-06-05T21:51:40Z
    summary: 'Added in-app help across the main pages so the tool explains itself, especially for non-technical people. Each page now has a short "what this page is for" note, and the less-obvious fields and buttons have little hover explanations. Covered: the project Overview (state, owner, code repository, and the AI agent permissions, plus a "project details" intro), the Phase view (what phases are, what a required gate is, what "advance anyway" and "revert" do), Dev Tickets, Meetings (scheduled vs held, where reminders live), the Inbox (promote to a project / move to dev tickets), Review (accept or send back), the Tickets filters, and Pre-projects (what "convert" means). All written in plain language. Reused the existing hover-help component so it looks consistent everywhere.'
    test_plan: 'On each page, hover the "?" next to the heading for the page explanation, and hover the "?" by the explained fields/controls for their tooltips: project Overview (state/owner/code repository/agent policy), Phase command center (phase/gate/advance-anyway/revert), Dev Tickets, Meetings, Inbox (promote + move-to-dev-tickets), Review, Tickets (Type/Project filters), Pre-projects (convert). Copy reads in plain, non-technical language. Existing layouts are unaffected.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T21:51:40Z
---

## Problem

Pages and fields aren't always self-explanatory, especially for non-technical PMs/stakeholders. We have a `HelpHint` component (used in some PageHeaders) but coverage is patchy and many controls lack tooltips.

## Context

Reuse the existing `HelpHint` (web/app/_components/ui) for page-level help and add concise tooltips on non-obvious fields/controls. This is a breadth pass — fan out across pages. Keep all copy plain-language (AGENTS.md §11).

## Conversation

**2026-06-05 21:51 claude:** Run run-20260605-2151 completed — Added in-app help across the main pages so the tool explains itself, especially for non-technical people. Each page now has a short "what this page is for" note, and the less-obvious fields and buttons have little hover explanations. Covered: the project Overview (state, owner, code repository, and the AI agent permissions, plus a "project details" intro), the Phase view (what phases are, what a required gate is, what "advance anyway" and "revert" do), Dev Tickets, Meetings (scheduled vs held, where reminders live), the Inbox (promote to a project / move to dev tickets), Review (accept or send back), the Tickets filters, and Pre-projects (what "convert" means). All written in plain language. Reused the existing hover-help component so it looks consistent everywhere.
