---
id: T-0227
title: Revert to / see previous phases
type: feature
state: review
priority: p2
created: 2026-06-05T13:49:51Z
updated: 2026-06-05T18:04:52Z
project: pm-tool-self
section: null
parent: null
children: []
order: 60416
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A way to revert the phase to the pevious
  - "[x] a way to view previous phases (link the phase buttons)"
out_of_scope: []
code_anchors:
  - path: web/app/_components/PhaseCommandCenter.tsx
  - path: web/app/projects/[slug]/phase/page.tsx
  - path: web/app/_actions/projects.ts
    symbol: revertPhase
  - path: cli/src/lib/playbook.ts
    symbol: previousPhase
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1350
    model: claude
    started: 2026-06-05T13:50:39Z
    status: completed
    summary: Projects move through phases (Intake → Planning → Build → …). Until now you could only go forward and couldn't look back. Now the phase buttons along the top are clickable — click any earlier phase to see its checklist read-only, with a clear "you're viewing the X phase — back to current" banner. And there's a "Revert to <previous phase>" button (with a confirm step) that steps the project back a phase if you advanced too soon. Reverting is treated as a deliberate step back, so it doesn't trip the phase gates or leave a "forced" mark on the project.
    ended: 2026-06-05T18:03:23Z
    test_plan: "On a project's Phase view: click an earlier phase button — it shows that phase's checklist read-only, with a \"viewing … / back to current\" banner and no advance/close controls. Click \"Back to current\" to return. On the current phase, click \"Revert to <previous>\" and confirm — the project moves back one phase and you land on the current-phase view. At the first phase (Intake) there's no revert option. A closed project shows no revert."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T18:03:23Z
---

once we progress to the next phase we can not go back, We can also not see the previous phases

## Conversation

**2026-06-05 18:03 claude:** Run run-20260605-1350 completed — Projects move through phases (Intake → Planning → Build → …). Until now you could only go forward and couldn't look back. Now the phase buttons along the top are clickable — click any earlier phase to see its checklist read-only, with a clear "you're viewing the X phase — back to current" banner. And there's a "Revert to <previous phase>" button (with a confirm step) that steps the project back a phase if you advanced too soon. Reverting is treated as a deliberate step back, so it doesn't trip the phase gates or leave a "forced" mark on the project.
