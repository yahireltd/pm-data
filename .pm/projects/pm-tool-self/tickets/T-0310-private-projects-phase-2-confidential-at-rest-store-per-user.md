---
id: T-0310
title: "Private projects — Phase 2: confidential at-rest store + per-user MCP enforcement"
type: feature
state: triaged
created: 2026-06-08T14:58:31Z
updated: 2026-06-08T18:01:12Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: agent
  name: claude
assignee: null
acceptance_criteria:
  - A private project is omitted from MCP listings and refused on direct MCP lookup for anyone not invited — enforced per-person, not via the shared token
  - Copying the shared data repository does not reveal a private project's contents (separate restricted store or encryption-at-rest)
  - The in-product 'hidden, not yet sealed' note is upgraded to reflect real confidentiality once it's in place
  - Depends on per-user MCP identity from T-0257; storage approach confirmed per ADR-035 before build
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - security
  - meta
attention: null
---

## Problem

Phase 1 of private projects (T-0290, shipped) hides a private project in the **web app** only — enforced server-side in `access.ts`. It is honestly labelled "hidden, not yet sealed": a private project is still reachable through the automation (MCP) interface and is still readable by anyone who can copy the shared data files. Phase 2 closes those two doors for true confidentiality.

## What's needed (the two remaining doors)

1. **Per-user MCP enforcement.** The MCP uses one shared bearer token, so it can't tell users apart. Needs per-user identity on the MCP so project listings/lookups respect who's invited (this is the parked per-user MCP auth — see ADR-012, ADR-008). Until then, `pm_list_projects` / `pm_get_project` still reveal private projects to any token holder.
2. **Confidential at-rest store.** Per ADR-035, the recommended direction is a **separate restricted store** for private projects (vs encryption-at-rest with per-owner keys) — confirm this before building. Copying the shared data repo must not reveal a private project's contents.

## Acceptance criteria

- A private project is omitted from MCP listings and refused on direct MCP lookup for anyone not invited, enforced per-person (not via the shared token).
- Copying the shared data repository does not reveal a private project's contents (separate restricted store or encryption-at-rest).
- The in-product "hidden, not yet sealed" note is upgraded once confidentiality is real.

## Out of scope

- Hiding a private project from the server/infrastructure operator (inherent, per T-0290).

See ADR-035 for the phased decision and the storage recommendation. Blocked on per-user MCP auth.