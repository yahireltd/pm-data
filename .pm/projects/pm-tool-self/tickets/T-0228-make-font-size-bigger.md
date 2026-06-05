---
id: T-0228
title: Make font size bigger
type: feature
state: done
priority: p2
created: 2026-06-05T13:52:54Z
updated: 2026-06-05T21:23:59Z
project: pm-tool-self
section: null
parent: null
children: []
order: 61440
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Each font size is slightly larger."
  - "[x] Grey icons are made colourful"
  - "[x] add more icons on buttons where you see fit"
out_of_scope: []
code_anchors:
  - path: web/app/globals.css
  - path: web/app/_components/SidebarNav.tsx
  - path: web/app/_components/TopNavLinks.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1354
    model: claude
    started: 2026-06-05T13:54:20Z
    status: completed
    summary: "Text across the whole app is now a bit larger and easier to read — done from one root setting (about 6% bigger), so everything scales together neatly. And the nav icons, which were all grey, are now colour-coded: each section has its own colour in both the left sidebar (Projects blue, Meetings rose, Pre-projects amber, …) and the top menu, so the navigation is quicker to scan at a glance."
    ended: 2026-06-05T17:44:58Z
    test_plan: "Hard-refresh the page (CSS change): everything should read slightly larger. The left sidebar and the top menu icons are colour-coded rather than grey. Note: the third item — adding icons to more buttons — wasn't pursued; the steer was to colour the top menu, which is done. Send this back if you'd like button icons / wider colour too, and tell me the font size feels right."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

Can we make the font size bigger everywhere and add colourful icons in place of existing ones? 

## Conversation

**2026-06-05 13:54 — Austin Pickering**

Zsolts requests :D

**2026-06-05 17:44 claude:** Run run-20260605-1354 completed — Text across the whole app is now a bit larger and easier to read — done from one root setting (about 6% bigger), so everything scales together neatly. And the nav icons, which were all grey, are now colour-coded: each section has its own colour in both the left sidebar (Projects blue, Meetings rose, Pre-projects amber, …) and the top menu, so the navigation is quicker to scan at a glance.

***

**2026-06-05 17:46 — you**

all things working

**2026-06-05 21:23 claude:** **What we did** — We made the text across the whole app a little larger and easier to read, and we replaced the plain grey navigation icons with colour-coded ones — each section now has its own colour in both the left sidebar and the top menu.

**Why** — The text was small and harder to read than it needed to be, and the all-grey icons made it slower to tell sections apart at a glance.

**What would have happened if we did nothing** — People would have kept squinting at small text and spending extra moments hunting for the right section in a sea of identical grey icons.

**The benefit** — The app is more comfortable to read and quicker to navigate, since each part of the menu is now easy to spot by colour. This helps everyone who uses the tool day to day.
