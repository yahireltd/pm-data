---
id: T-0233
title: General Dev Tickets
type: feature
state: in_progress
priority: p2
created: 2026-06-05T15:47:52Z
updated: 2026-06-05T19:57:45Z
project: pm-tool-self
section: null
parent: null
children: []
order: 64512
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] General Dev Tickets On sidebar Link"
  - "[x] Page that displays what is described above"
  - "[x] a way to move tickets to the general tickets area on inbox / tickets page"
out_of_scope: []
code_anchors:
  - path: schemas/project.schema.json
    symbol: category
  - path: schemas/ticket.schema.json
    symbol: department
  - path: cli/src/types.ts
  - path: web/app/_lib/constants.ts
  - path: web/app/_actions/tickets.ts
    symbol: assignToSystem
  - path: web/app/dev-tickets/page.tsx
  - path: web/app/_components/DevTicketsView.tsx
  - path: web/app/_components/MoveToSystemDialog.tsx
  - path: web/app/_components/SortableInboxList.tsx
  - path: web/app/_components/Sidebar.tsx
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-1549
    model: claude
    started: 2026-06-05T15:49:01Z
    status: completed
    summary: "There's a new \"Dev Tickets\" area in the sidebar for development work on the company's systems. Each system — Yahire Website, Chair Hire London, Yasystem, or a new site you add — is its own project, and a dev ticket is filed against a system and tagged with a department (Accounts, Customer Service, Logistics, Warehouse, Drivers, Catering, Sales). The Dev Tickets page lists every system with its open tickets grouped by department, so you can see all the work for a site at a glance. To put a ticket there: on the Inbox click the new spanner icon on a row, or on any ticket use \"Move to Dev Tickets\" — pick the system (or add a new one) and the department. Systems are kept out of the normal Projects list and the dashboard so they don't clutter the delivery view."
    ended: 2026-06-05T19:53:37Z
    test_plan: Sidebar → "Dev Tickets" opens the area. On the Inbox, click the spanner on a ticket → choose "Yahire Website" + "Sales" → the ticket moves there and shows under Dev Tickets → Yahire Website → Sales. Pick "+ Add new site…", type a name → a new system is created and the ticket lands under it. On a ticket page, "Move to Dev Tickets" does the same. Confirm system projects do NOT appear in the main Projects list or the dashboard rollups, and a normal project/ticket is unaffected.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: ADR-031 records the systems-as-projects decision; the new fields are documented in the project/ticket schema JSON descriptions (category, department).
labels: []
attention:
  needed_by: agent
  reason: I cant see a way to set the project git url and the branch for that project
  since: 2026-06-05T19:57:44Z
---

We need an area for general dev tickets linked in the left side bar. This will link to a page that shows the dev tickets categorised as described below. When adding to this area we need to mark which system. Yahire Website, Chair hire london website, Yasystem,  Add new site option that saves. The general dev tickets should then be categorised by system and then  further categorised by department, Accounts, Customer Service, Logistics, Warehouse, Drivers, Catering, Sales

## Conversation

**2026-06-05 19:53 claude:** Run run-20260605-1549 completed — There's a new "Dev Tickets" area in the sidebar for development work on the company's systems. Each system — Yahire Website, Chair Hire London, Yasystem, or a new site you add — is its own project, and a dev ticket is filed against a system and tagged with a department (Accounts, Customer Service, Logistics, Warehouse, Drivers, Catering, Sales). The Dev Tickets page lists every system with its open tickets grouped by department, so you can see all the work for a site at a glance. To put a ticket there: on the Inbox click the new spanner icon on a row, or on any ticket use "Move to Dev Tickets" — pick the system (or add a new one) and the department. Systems are kept out of the normal Projects list and the dashboard so they don't clutter the delivery view.
