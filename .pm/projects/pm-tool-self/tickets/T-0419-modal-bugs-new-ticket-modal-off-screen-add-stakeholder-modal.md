---
id: T-0419
title: "Modal bugs: new-ticket modal off-screen + add-stakeholder modal closes on name select"
type: bug
state: triaged
created: 2026-06-18T08:58:32Z
updated: 2026-06-18T08:58:32Z
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
assignee: null
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
agent_runs: []
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 1
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