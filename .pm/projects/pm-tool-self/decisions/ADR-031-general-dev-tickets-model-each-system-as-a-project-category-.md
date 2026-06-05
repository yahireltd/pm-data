---
id: ADR-031
slug: general-dev-tickets-model-each-system-as-a-project-category-
title: "General Dev Tickets: model each system as a project (category:system)"
state: accepted
decided: 2026-06-05
decided_by:
  - Austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0233
---

## Context

We needed a home for ongoing development work against the company's systems (Yahire Website, Chair Hire London, Yasystem, …) — work that isn't a normal delivery project but still needs to be organised, grouped by system and by department.

Two models were considered: (a) project-less tickets tagged with `system` + `department`, or (b) each system as a real project. Austin chose (b).

## Decision

Model each system as a real project flagged `category: "system"`. Dev tickets are assigned to a system project and carry an optional `department` (a fixed list: Accounts, Customer Service, Logistics, Warehouse, Drivers, Catering, Sales). A "Dev Tickets" sidebar area (`/dev-tickets`) lists each system with its open tickets grouped by department. System projects are filtered OUT of the normal Projects list and the dashboard rollups so they don't clutter delivery views. Moving a ticket in (from the inbox row or a ticket page) picks a system — an existing system project, a canonical default created on demand, or a brand-new site — plus a department.

## Consequences

- Any surface that lists "projects" for delivery must exclude `category === "system"` (done for the Projects page + dashboard rollups); new project surfaces should do the same.
- System projects still carry the full project machinery (charter/phases). It's unused for them and not hidden in this pass — a possible follow-up if it proves noisy.
- The "systems" list is simply the set of system projects; "add new site" creates one. The three canonical defaults are offered in the move dialog and created on first use.
- Departments are a fixed code constant (`web/app/_lib/constants.ts`), not per-org configurable.
- New fields are additive + optional, so existing projects/tickets round-trip unchanged.