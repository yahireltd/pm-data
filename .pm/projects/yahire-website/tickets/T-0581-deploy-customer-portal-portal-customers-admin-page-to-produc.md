---
id: T-0581
title: Deploy customer portal + /portal-customers admin page to production
type: chore
state: triaged
created: 2026-07-15T11:16:37Z
updated: 2026-07-15T11:16:37Z
project: yahire-website
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p1
assignee: null
acceptance_criteria:
  - customer-portal branch merged to master and deployed (yasite/yahirenew)
  - portal-customer-management branch merged and /portal-customers admin page live on Site Manager
  - Outstanding DB changes applied in production (portal_admin_audit created; dead can* + sortOrder columns dropped)
  - Live portal login URL reachable and staff can open /portal-customers
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
The customer portal (built in T-0518) and its Site Manager admin page (`/portal-customers`) live on feature branches. To start pilot testing they need to be live in production.

## Context
- yasite branch: `customer-portal` (portal itself — yahirenew)
- ya-hire branch: `portal-customer-management` (the `/portal-customers` admin management page)
- Also outstanding schema tidy-ups flagged during the build: drop dead `can*` columns on `portal_users`, drop `sortOrder` on `portal_permissions`, create `portal_admin_audit` table.

## Scope
Merge both branches, run the DB migrations/SQL, and confirm the live login URL + admin page are reachable in production.