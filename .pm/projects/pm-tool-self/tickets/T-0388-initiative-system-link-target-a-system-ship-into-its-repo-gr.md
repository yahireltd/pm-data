---
id: T-0388
title: "Initiative → System link: target a system, ship into its repo, graduate work to BAU"
type: feature
state: triaged
created: 2026-06-16T17:10:34Z
updated: 2026-07-14T14:36:39Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
assignee: null
acceptance_criteria:
  - An initiative project can declare the system project it targets, visible on both projects
  - Graduating/closing the initiative routes its remaining open tickets to the target system's backlog (BAU handover)
  - The link is settable in the web UI and via MCP
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - m-009
  - modeling
  - sales
attention: null
version: 3
---

## Problem
A large change to an existing system has no first-class link to the system it changes. Concrete case: the Sales transformation (PP-002, to be converted to an initiative) ships into **Yasystem** (P-0008, `kind: system`, repo `Ya-Hire-Management`). Today you either make a *separate* project (orphaned from the system) or bury the work as system dev-tickets (losing the guided lifecycle). Neither expresses "this initiative changes that system."

## Proposal
- Let an **initiative declare a target system** (and optionally specific surfaces).
- Show the link **both ways** (initiative ↔ system).
- When a milestone/slice clears hypercare, **graduate** its tickets into the system's BAU (dev-tickets, by department) via a handover record.

## Context
M-009 design review; the run-vs-change framing (see the linked ADR). The initiative shares Yasystem's repo and inherits its "ask before any push/commit to master" guardrail.

## Acceptance criteria
- An initiative can reference a target system project (+ optional surfaces).
- The link is visible from both the initiative and the system.
- A shipped milestone can graduate its tickets to the target system as BAU, recorded as a handover.
