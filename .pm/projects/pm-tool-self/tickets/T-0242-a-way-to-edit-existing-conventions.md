---
id: T-0242
title: A way to edit existing conventions
type: feature
state: review
priority: p2
created: 2026-06-05T17:49:55Z
updated: 2026-06-08T16:11:41Z
project: pm-tool-self
section: null
parent: null
children: []
order: 74752
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] we can amemnd conventions via the ui"
out_of_scope: []
code_anchors:
  - path: cli/src/lib/conventions.ts
    symbol: writeConventionsHand
  - path: web/app/_actions/conventions.ts
    symbol: setConventionsAction
  - path: web/app/_components/ConventionsControls.tsx
  - path: web/app/conventions/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1444
    model: claude-opus-4-8
    started: 2026-06-08T14:44:41Z
    status: completed
    ended: 2026-06-08T14:45:02Z
    summary: You can now edit the team conventions, not just add to them. Before, the Conventions page only let you tack a new rule onto the end — if a convention was worded badly, out of date, or needed removing, there was no way to change it from the app. Now there's an "Edit" button that opens the whole hand-written conventions section as editable text; you can reword, reorder, or delete rules and save. The automatically-gathered "Lessons from closed projects" section underneath is left completely alone, so editing your rules can never accidentally wipe the lessons rolled up from finished projects. This keeps the "how we work" document accurate and tidy over time instead of only ever growing.
    test_plan: |-
      1. Open the Conventions page. Add a couple of conventions if the list is empty.
      2. Click "Edit" → the hand-written section opens as an editable markdown box pre-filled with the current conventions (the generated lessons block is NOT shown).
      3. Reword/reorder/remove a line, then "Save conventions" → the page re-renders with your changes.
      4. Confirm the "Lessons from closed projects" section beneath is unchanged after saving.
      5. Click Edit then Cancel → no changes are made.
      6. Try saving an empty box → it's refused with a clear message.
      7. Open /help → the Conventions section now mentions the Edit action.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: Updated /help conventions content to document the new Edit action.
labels: []
attention:
  needed_by: human
  reason: WYSIWYG editing added per your note (commit 574561b) — editing conventions now uses the visual editor, not raw markdown. Needs a deploy to see live, then confirm + close.
  since: 2026-06-08T15:41:30Z
---

no way to edit conventions

## Conversation

**2026-06-08 14:45 claude:** Run run-20260608-1444 completed — You can now edit the team conventions, not just add to them. Before, the Conventions page only let you tack a new rule onto the end — if a convention was worded badly, out of date, or needed removing, there was no way to change it from the app. Now there's an "Edit" button that opens the whole hand-written conventions section as editable text; you can reword, reorder, or delete rules and save. The automatically-gathered "Lessons from closed projects" section underneath is left completely alone, so editing your rules can never accidentally wipe the lessons rolled up from finished projects. This keeps the "how we work" document accurate and tidy over time instead of only ever growing.

**2026-06-08 15:41 claude:** Done — editing conventions now uses the visual (WYSIWYG) editor instead of a raw-markdown box. It's the same editor you get on project and ticket descriptions, so you format conventions visually (headings, bold, bullets) without seeing any markdown syntax; ⌘/Ctrl+Enter saves. Behind the scenes it still saves to the same place, and the automatically-gathered "Lessons from closed projects" section underneath is still left untouched.

Note: this is a fresh code change (commit 574561b) — it needs a deploy before it shows up on the live site. Sending back to review.

**2026-06-08 16:11 claude:** **Closing summary — follow-up change (WYSIWYG editing)**

**What we did:** Editing the team conventions now opens the same visual (WYSIWYG) editor used for project and ticket descriptions, instead of a raw-markdown text box.

**Why we did it:** You asked for a WYSIWYG when editing. Editing raw markdown means knowing markdown syntax, which isn't friendly for a non-developer PM.

**What would have happened if we did nothing:** Anyone amending the conventions would have to hand-write markdown by eye — fiddly, easy to get wrong, and off-putting.

**The benefit:** You can format conventions visually (headings, bold, bullet lists) with no markdown knowledge. It still saves to the same place, and the automatically-gathered "Lessons from closed projects" section underneath is still left untouched.
