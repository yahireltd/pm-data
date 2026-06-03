---
id: MS-003
slug: v2-wysiwyg-editor-authoring-surfaces-ui-sweep
title: "v2: WYSIWYG editor + authoring surfaces + UI sweep"
project: pm-tool-self
state: in_progress
order: 3072
created: 2026-05-29T13:27:52Z
updated: 2026-05-29T13:27:52Z
acceptance_criteria:
  - Milkdown editor round-trips cleanly to .md (frontmatter preserved)
  - Web authoring for status notes
  - Web authoring for decisions / ADRs
  - Ticket-detail re-skinned onto the primitives
  - Board re-skinned onto the primitives
  - Meetings re-skinned onto the primitives
  - Decisions list re-skinned onto the primitives
  - Overview re-skinned onto the primitives
slip_records: []
target_date: 2026-06-30
---

# v2: WYSIWYG editor + authoring surfaces + UI sweep

## Acceptance criteria

_What must be true for this milestone to count as hit._

- Milkdown editor round-trips cleanly to `.md` with frontmatter preserved (snapshot test bounds drift).
- Web authoring surface for weekly status notes.
- Web authoring surface for decisions / ADRs.
- Ticket-detail, board, meetings, decisions list, and overview re-skinned onto the primitive library.

## Notes

W2 of the v2 push. Builds on the makeover foundation from MS-002.
