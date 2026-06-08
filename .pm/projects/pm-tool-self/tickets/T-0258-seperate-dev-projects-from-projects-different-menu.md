---
id: T-0258
title: seperate dev projects from projects - different menu label and sub menu
type: feature
state: done
priority: p2
created: 2026-06-05T21:43:28Z
updated: 2026-06-08T16:15:03Z
project: pm-tool-self
section: null
parent: null
children: []
order: 79872
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] dev projects are seperated"
out_of_scope: []
code_anchors:
  - path: web/app/_components/Sidebar.tsx
    lines: 58-72
  - path: web/app/_components/SidebarNav.tsx
    symbol: SidebarNav
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1433
    model: claude-opus-4-8
    started: 2026-06-08T14:33:42Z
    status: completed
    ended: 2026-06-08T14:33:56Z
    summary: Work on the tool itself no longer clutters the list of your real projects in the left-hand menu. Before, the pm-tool dogfood project sat right at the top of the projects list, mixed in with the actual work projects. Now the menu has a separate "Dev projects" heading underneath the normal "Projects" list, and any project marked as a development/meta project drops into that section instead. This keeps the main project list focused on real work while still letting you reach the dev project in one click. The grouping uses the existing "meta" tag a project already carries, so nothing had to be re-entered, and the extra heading only appears when there's at least one dev project to show.
    test_plan: |-
      1. Open the app as an admin and look at the left sidebar.
      2. Under "Projects" you should see only the real work projects; the pm-tool dogfood project ("pm-tool — dogfood improvements") now appears under a separate "Dev projects" heading below.
      3. Click the dev project → it opens and expands its sub-nav exactly like any other project.
      4. If there were no dev/meta projects, the "Dev projects" heading would not appear at all.
      Note: a project is treated as a dev project when it carries the "meta" label; tag any future tool-development project with "meta" to have it grouped here.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
---

We have the dev project showing up on the top of our projects sepeate dev projects from others

## Conversation

**2026-06-08 14:33 claude:** Run run-20260608-1433 completed — Work on the tool itself no longer clutters the list of your real projects in the left-hand menu. Before, the pm-tool dogfood project sat right at the top of the projects list, mixed in with the actual work projects. Now the menu has a separate "Dev projects" heading underneath the normal "Projects" list, and any project marked as a development/meta project drops into that section instead. This keeps the main project list focused on real work while still letting you reach the dev project in one click. The grouping uses the existing "meta" tag a project already carries, so nothing had to be re-entered, and the extra heading only appears when there's at least one dev project to show.

**2026-06-08 15:59 claude:** Done — two changes (commit af5bef3):

1. The two sidebar groups are now labelled **"Existing Projects"** and **"Projects In Development"**.
2. There's now an explicit way to set which group a project is in: on a project's **Overview** there's a **"Project stage"** toggle — **Existing** or **In development** — instead of it being driven by a hidden tag. Flip it and the project moves between the two sidebar groups. (Your existing dogfood project stays under "In Development" automatically, so nothing's lost.)

Needs a deploy before it shows on the live site. Sending back to review.

**2026-06-08 16:11 claude:** **Closing summary — follow-up change (sidebar grouping rework)**

**What we did:** Renamed the two left-hand project groups to **"Existing Projects"** and **"Projects In Development"**, and added a simple **"Project stage"** toggle on each project's Overview so you can set which group a project belongs to.

**Why we did it:** The first version decided the grouping from a hidden "meta" tag that you couldn't see or change, and the headings ("Projects" / "Dev projects") weren't clear. You asked for plainer names and a way to set a project's stage yourself.

**What would have happened if we did nothing:** Grouping would stay controlled by an invisible tag only an agent could set — you'd have no way to move a project between "existing" and "in development", and the headings would stay ambiguous.

**The benefit:** You can now sort any project into Existing or In Development in one click from its Overview, and the sidebar says plainly what each group is. (Your dogfood project stays under "In Development" automatically, so nothing was lost.)

---

**2026-06-08 16:15 — you**

All done
