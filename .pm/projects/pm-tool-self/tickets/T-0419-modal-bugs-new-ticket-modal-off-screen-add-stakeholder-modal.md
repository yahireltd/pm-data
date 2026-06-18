---
id: T-0419
title: "Modal bugs: new-ticket modal off-screen + add-stakeholder modal closes on name select"
type: bug
state: review
created: 2026-06-18T08:58:32Z
updated: 2026-06-18T13:17:56Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: email
  contact: zsolt@yahire.com
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "New-ticket modal: with long content the modal stays within the viewport and its body scrolls; Add/Cancel remain reachable (sticky footer)."
  - A long new ticket can be created end to end.
  - "Add-stakeholder: searching and clicking a suggested name fills the fields and the modal stays open."
  - A stakeholder can be added to a ticket end to end.
  - Outside-click still closes both modals when clicking genuinely outside them.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1251
    model: claude
    started: 2026-06-18T12:51:24Z
    status: completed
    summary: "Fixed two bugs that made pop-up windows (modals) hard or impossible to use. First, when creating a ticket with a long description, the \"New ticket\" window grew past the bottom of the screen and you couldn't scroll down to reach the Create button — so long tickets couldn't be submitted. The window now stays within the screen, its middle section scrolls, and the Create/Cancel buttons are pinned to the bottom so they're always reachable. Second, when adding a stakeholder, typing a name and clicking one of the suggested people closed the whole window instead of filling in their details — so you couldn't add a stakeholder that way. Clicking a suggestion now fills the fields and keeps the window open. If we'd done nothing, people couldn't create long tickets or add stakeholders from the suggestion list. Benefit: both common actions now work end to end."
    ended: 2026-06-18T13:17:56Z
    test_plan: |-
      New-ticket modal (the blue "New ticket" button, top nav):
      1. Open it and paste a very long description (enough to exceed the screen height).
      2. Confirm the window stays inside the screen and its body scrolls.
      3. Confirm the Create and Cancel buttons stay visible at the bottom (sticky footer) and Create works — a long ticket can be created end to end.
      4. Confirm clicking the dimmed area outside the window still closes it; Escape still closes it.

      Add-stakeholder modal (a ticket → Stakeholders → Add):
      5. Open it, type part of a name, and click a suggested person — the name/channel/contact fields should fill in and the window should STAY open.
      6. Finish and add the stakeholder end to end.
      7. Confirm clicking genuinely outside the window still closes it; Escape still closes it.

      Cross-impact: AddStakeholderDialog is also used for EDITING a stakeholder and on the project (not just ticket) stakeholder list — quickly confirm those still open/close normally. The new-ticket modal lives in the top nav, shown on every page.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - dogfood
  - for-austin
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-18T13:17:56Z
version: 7
defect_status: confirmed
---

## Problem

Two modal UX defects found while using the tool. (For Austin.)

### A. New-ticket modal overflows off-screen
Creating a ticket via the blue "New ticket" button: a long ticket makes the modal extend past the viewport, and you can't scroll to reach the **Add** button — so long tickets can't be submitted. Needs a `max-height` with internal scroll, and ideally a **sticky footer** so the action buttons stay reachable.

### B. Add-stakeholder modal closes when you pick a name
On a ticket, opening "Add stakeholder", searching a name, and clicking a suggestion **closes the whole modal** instead of filling the field — so you can't add a stakeholder.

**Diagnosed cause** (`AddStakeholderDialog.tsx`): the click-outside handler closes the dialog when a `mousedown` target isn't inside it. Picking a suggestion calls `setSuggestOpen(false)`, which unmounts the suggestion `<ul>` (and the clicked `<button>`); React 18 flushes that at the root before the event bubbles to the `document` listener, so the clicked node is **detached** and `dialogRef.contains(target)` returns false → `onClose()` fires.

**Fix** — ignore the outside-click when its target is no longer in the DOM:
```js
function onClick(e) {
  const target = e.target;
  if (!target.isConnected) return; // removed mid-interaction (suggestion pick) = inside click
  if (dialogRef.current && !dialogRef.current.contains(target)) onClose();
}
```

## Conversation

**2026-06-18 13:17 claude:** Run run-20260618-1251 completed — Fixed two bugs that made pop-up windows (modals) hard or impossible to use. First, when creating a ticket with a long description, the "New ticket" window grew past the bottom of the screen and you couldn't scroll down to reach the Create button — so long tickets couldn't be submitted. The window now stays within the screen, its middle section scrolls, and the Create/Cancel buttons are pinned to the bottom so they're always reachable. Second, when adding a stakeholder, typing a name and clicking one of the suggested people closed the whole window instead of filling in their details — so you couldn't add a stakeholder that way. Clicking a suggestion now fills the fields and keeps the window open. If we'd done nothing, people couldn't create long tickets or add stakeholders from the suggestion list. Benefit: both common actions now work end to end.
