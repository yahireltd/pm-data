---
id: T-0235
title: Pre Project Inputs / Project Inputs
type: feature
state: done
priority: p2
created: 2026-06-05T15:58:23Z
updated: 2026-06-05T18:59:56Z
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
  - "[x] Should be able to input these the using a wysiwyg editor like the body that displays the md when not being editied"
out_of_scope: []
code_anchors:
  - path: web/app/_components/EditableMarkdownField.tsx
  - path: web/app/_lib/formatting.ts
    symbol: arrayToMarkdown
  - path: web/app/_actions/projects.ts
    symbol: setCharterText
  - path: web/app/_components/Charter.tsx
  - path: web/app/_components/PreProjectEditor.tsx
  - path: web/app/_components/CostOfInactionField.tsx
  - path: web/app/_components/WorkflowImpactCard.tsx
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
    summary: Updated after your send-back. Now ALL the framing boxes — Problems, Goals, In scope, Out of scope, and the success field — are the same rich editor as the body, on both pre-projects and full-project charters (before it was only Summary + Cost of inaction). You can paste and format freely (headings, bullets, numbered lists), and they show the formatted text when you're not editing. Crucially, both sides now use the same format, so when a pre-project becomes a real project the content carries over and displays identically — which was the worry. Existing content shows as a bullet list and is preserved. Under the hood the data is still stored in the same shape, so nothing else (the phase checks, conversion, etc.) had to change.
    ended: 2026-06-05T18:58:51Z
    test_plan: "Full-project Charter: Problems / Goals / In scope / Out of scope / 'How we'll know we're successful' are rich editors — click to edit, format, save, see it rendered. Pre-project editor: the same five fields are rich; paste a numbered list and it formats. Convert a pre-project → the new project's charter shows the same content rendered the same way. Existing projects: their old list items appear as bullets and read back unchanged after a save. One known leftover: the project Overview page still shows success criteria as plain rows (a secondary read surface) — can follow up if you want it matched too."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

Some of the text areas for the pre-projects and projects are just plain text areas. Can we make these like the body with the MD wysiwyg editors

## Conversation

**2026-06-05 18:21 claude:** Run run-20260605-1559 completed — The free-text boxes on projects and pre-projects are now proper rich-text editors instead of plain text areas — the same editor used for the main body, so you format with a toolbar and never see raw markdown. On a project's charter that's the "Cost of inaction" answer and the whole "Workflow & impact" section (current/new workflow, sources of truth, training, rollback); on a pre-project it's the summary and cost-of-inaction. Each renders its formatted text when you're not editing (on the charter) and saves the same way it did before. List-style fields (problems, goals, scope, who's affected) keep their simple one-per-line entry, since a rich editor doesn't suit a list.

---

**2026-06-05 18:59 — you**

all working now
