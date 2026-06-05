---
id: T-0235
title: Pre Project Inputs / Project Inputs
type: feature
state: review
priority: p2
created: 2026-06-05T15:58:23Z
updated: 2026-06-05T18:21:48Z
project: pm-tool-self
section: null
parent: null
children: []
order: 65536
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Should be able to input these the using a wysiwyg editor like the body that displays the md when not being editied
out_of_scope: []
code_anchors:
  - path: web/app/_components/EditableMarkdownField.tsx
  - path: web/app/_components/CostOfInactionField.tsx
  - path: web/app/_components/WorkflowImpactCard.tsx
  - path: web/app/_components/PreProjectEditor.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1559
    model: claude
    started: 2026-06-05T15:59:48Z
    status: completed
    summary: The free-text boxes on projects and pre-projects are now proper rich-text editors instead of plain text areas — the same editor used for the main body, so you format with a toolbar and never see raw markdown. On a project's charter that's the "Cost of inaction" answer and the whole "Workflow & impact" section (current/new workflow, sources of truth, training, rollback); on a pre-project it's the summary and cost-of-inaction. Each renders its formatted text when you're not editing (on the charter) and saves the same way it did before. List-style fields (problems, goals, scope, who's affected) keep their simple one-per-line entry, since a rich editor doesn't suit a list.
    ended: 2026-06-05T18:21:48Z
    test_plan: "On a project charter: click the Cost of inaction or any Workflow & impact field — it opens the WYSIWYG editor (bold/lists/etc.); save and it shows the rendered text. On a pre-project: the Summary and Cost of inaction are rich editors in the edit form, saved by the form's Save button. Confirm list fields (problems/goals/scope) are unchanged. Existing plain-text values still display correctly."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-05T18:21:48Z
---

Some of the text areas for the pre-projects and projects are just plain text areas. Can we make these like the body with the MD wysiwyg editors

## Conversation

**2026-06-05 18:21 claude:** Run run-20260605-1559 completed — The free-text boxes on projects and pre-projects are now proper rich-text editors instead of plain text areas — the same editor used for the main body, so you format with a toolbar and never see raw markdown. On a project's charter that's the "Cost of inaction" answer and the whole "Workflow & impact" section (current/new workflow, sources of truth, training, rollback); on a pre-project it's the summary and cost-of-inaction. Each renders its formatted text when you're not editing (on the charter) and saves the same way it did before. List-style fields (problems, goals, scope, who's affected) keep their simple one-per-line entry, since a rich editor doesn't suit a list.
