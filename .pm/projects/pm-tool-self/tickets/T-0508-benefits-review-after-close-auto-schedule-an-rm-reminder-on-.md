---
id: T-0508
title: "Benefits review after close: auto-schedule an RM- reminder on project close"
type: feature
state: triaged
created: 2026-07-02T13:38:42Z
updated: 2026-07-02T13:38:42Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - Closing a project creates an active RM- reminder ~90 days out titled 'Benefits review — [project name]' targeting the project (dashboard links to /projects/[slug])
  - Reminder creation is best-effort — failure never blocks the close
  - "No duplicate: closing twice (or re-closing) doesn't create a second active benefits-review reminder for the same project"
out_of_scope: []
code_anchors:
  - path: web/app/_actions/projects.ts
    symbol: closeProject
  - path: web/app/_actions/reminders.ts
    symbol: createReminder (reused)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - closure
  - reminders
attention: null
version: 1
---

## Problem

Nothing ever asks "did the project deliver what its case promised?" after closure — the PRINCE2 benefits-review gap. Projects close and are never reconciled against the value they were justified on.

## Design

Closing a project auto-creates a global RM- reminder (~90 days out, offsets 7d/0d, dashboard channel) titled "Benefits review — [project]", targeting the project so the dashboard links back to it. Best-effort: a reminder failure never blocks the close; skip creation if an active benefits-review reminder already targets the project.
