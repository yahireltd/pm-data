---
id: T-0146
title: Editable project overview body (inline WYSIWYG)
type: feature
state: done
priority: p2
created: 2026-05-29T15:25:49Z
updated: 2026-05-29T15:28:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 18432
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - The project Overview narrative body is editable inline in the web UI via the WYSIWYG editor (no raw markdown, no CLI needed)
  - Saving persists to the project's .md body through a setProjectBody server action; CLI, linter and agents are unaffected
  - The editor is read-only in the stakeholder view
out_of_scope:
  - The inline project frontmatter field editors (name/state/owner/goal) — already handled
code_anchors:
  - path: web/app/projects/[slug]/overview/page.tsx
  - path: web/app/_actions/projects.ts
  - path: web/app/_components/EditableProjectBody.tsx
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

Austin noticed the project **Overview** narrative body can't be edited in the
web app — it's read-only (`MarkdownBody`), seeded once at project creation and
thereafter only changeable via the CLI, a direct `.md` edit, or an agent. Same
"important content, no web editor" gap the initial audit flagged, now fixable
with the Milkdown editor that already powers ticket/meeting/ADR bodies.

## Design notes

Mirror `EditableMarkdownBody`: a new `EditableProjectBody` component that
renders the read view with an "Edit body" affordance flipping into
`RichBodyEditor`, saving via a new `setProjectBody(slug, markdown)` server
action (load project mutable → `writeMarkdown(path, project, body)`). Read-only
in stakeholder view. `.md` stays the source of truth.
