---
id: T-0584
title: In-portal help/guide — plain-English explanations of pages & features
type: feature
state: triaged
created: 2026-07-15T11:54:36Z
updated: 2026-07-15T11:54:36Z
project: yahire-website
section: null
parent: null
milestone: MS-001
children: []
order: 1024
priority: p2
assignee: null
acceptance_criteria:
  - A Help/Guide section is reachable from within the portal navigation
  - "Explanations cover the core pages & features: dashboard, orders, invoices, reorder, addresses, users & permissions, and account/security (password reset, 2FA, trusted devices)"
  - Each topic is written in plain, friendly, non-technical language and kept short
  - Content is presented in a clean, scannable layout (e.g. collapsible sections/cards) that reads well on desktop and mobile
  - A customer with no prior briefing can find and understand how to do the core tasks from the help section alone
out_of_scope: []
code_anchors:
  - path: yahirenew/controllers/PortalController.php
  - path: yahirenew/views/portal
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
Customers using the portal for the first time need to understand what each page/function does and how things work — without having to ask us. There's currently no in-portal guidance.

## What we want
A friendly, easy-to-follow help section inside the portal that explains the main pages and features in plain English — short, clearly worded, not technical or overwhelming.

## Scope
- Cover the core areas: dashboard, viewing orders, viewing invoices, placing a reorder, managing addresses, managing users & permissions, and account/security (login, password reset, 2FA, trusted devices).
- Each topic: what it's for + how to use it, in a couple of simple sentences.
- A clean, scannable presentation — e.g. a Help page with collapsible sections / cards, reachable from the portal nav. Should look tidy and be pleasant to read on mobile too.

## Notes
- Tone: welcoming and simple — write for a non-technical customer, avoid jargon.
- Nice-to-have: short contextual hints/tooltips on the pages themselves, and content that's easy for staff to reword later.