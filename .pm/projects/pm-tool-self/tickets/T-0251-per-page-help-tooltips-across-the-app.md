---
id: T-0251
title: Per-page help + tooltips across the app
type: feature
state: triaged
created: 2026-06-05T21:25:01Z
updated: 2026-06-05T21:25:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - Every main page has a short contextual help blurb (reuse the existing HelpHint pattern in PageHeader)
  - Non-obvious fields/controls get a tooltip explaining what they are and why they matter (plain language)
  - Coverage is consistent — a pass across dashboard, projects (overview/charter/phase/deliver), pre-projects, meetings, dev-tickets, inbox, review, tickets
  - Help text is jargon-free and aimed at a non-technical PM/stakeholder
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

Pages and fields aren't always self-explanatory, especially for non-technical PMs/stakeholders. We have a `HelpHint` component (used in some PageHeaders) but coverage is patchy and many controls lack tooltips.

## Context

Reuse the existing `HelpHint` (web/app/_components/ui) for page-level help and add concise tooltips on non-obvious fields/controls. This is a breadth pass — fan out across pages. Keep all copy plain-language (AGENTS.md §11).