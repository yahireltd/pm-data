---
id: T-0246
title: Phase dependent default
type: feature
state: done
priority: p2
created: 2026-06-05T19:35:06Z
updated: 2026-06-05T23:54:22Z
project: pm-tool-self
section: null
parent: null
children: []
order: 78848
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] the default submenu item of the project is selected according to the description"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/project-nav.ts
    symbol: DEFAULT_SECTION_BY_PHASE
  - path: web/app/_lib/project-nav.ts
    symbol: defaultSectionForPhase
  - path: web/app/projects/[slug]/page.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2349
    model: claude-opus-4-8
    started: 2026-06-05T23:49:41Z
    status: completed
    ended: 2026-06-05T23:49:54Z
    summary: "Made a project open on the tab that matters for where it is in its life, instead of always landing on Sprints. Now when you open a project we look at its current phase and take you to the most useful place: an early/intake project opens on Overview, one in planning opens on the Charter/Plan, one in build or test opens on Sprints, and one shipping or in hypercare opens on its Status. Every tab is still one click away — only the default landing spot changed — and if a project's phase can't be read for any reason it safely falls back to Sprints (the previous behaviour)."
    test_plan: "Open a project in each phase and confirm the landing tab: intake→Overview, planning→Charter, build/test→Sprints, ship/hypercare→Status, closure→Overview. All other tabs (incl. /board) remain reachable. A project with no/garbled phase still lands on Sprints. Build green + deployed (3a8cec5)."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

In the intake stage of. project default to overview in plan stage default to plan etc

## Conversation

**2026-06-05 23:49 claude:** Run run-20260605-2349 completed — Made a project open on the tab that matters for where it is in its life, instead of always landing on Sprints. Now when you open a project we look at its current phase and take you to the most useful place: an early/intake project opens on Overview, one in planning opens on the Charter/Plan, one in build or test opens on Sprints, and one shipping or in hypercare opens on its Status. Every tab is still one click away — only the default landing spot changed — and if a project's phase can't be read for any reason it safely falls back to Sprints (the previous behaviour).

---

**2026-06-05 23:54 — you**

Works as described good job!
