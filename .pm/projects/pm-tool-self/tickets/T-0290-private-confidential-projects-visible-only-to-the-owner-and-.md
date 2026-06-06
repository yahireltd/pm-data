---
id: T-0290
title: Private / confidential projects — visible only to the owner and invited people, hidden from other admins
type: feature
state: triaged
created: 2026-06-06T01:37:53Z
updated: 2026-06-06T01:37:53Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - A project can be marked private and given a list of people allowed in (the owner plus invited people); everyone else cannot see it
  - A private project is absent from every other admin's interface — menus, projects list, dashboard, tickets, review, activity, and search — and the server refuses direct access to it for anyone not invited (not merely hides the link)
  - "The automation interface no longer reveals a private project to anyone not invited: listings omit it and direct lookups are refused, enforced per-person rather than via one shared token"
  - Copying the shared data repository does not reveal a private project's contents to someone not invited (achieved via a separate restricted store or encryption-at-rest)
  - A short written threat model states plainly who can still access a private project (the server / infrastructure operator), so the product never over-promises privacy
  - The storage approach (separate store vs encryption) is decided in an ADR before the build of true confidentiality starts
out_of_scope:
  - Hiding a private project from the person who operates the server / infrastructure (they inherently can access what they host)
  - Encrypting or changing existing non-private projects
  - A compromised-server or nation-state threat model
code_anchors:
  - path: web/app/_lib/access.ts
  - path: web/app/layout.tsx
  - path: web/app/_lib/pm.ts
  - path: mcp-server/src
  - path: schemas
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

Today an admin can see every project in the tool — there is no way to keep a project visible to just yourself, and a few people you choose, while hiding it from other admins. We want "private projects": a project that only its owner and explicitly-invited people can see, genuinely hidden from all other admins.

## What we decided we want

- A project can be tagged **private**, with a small list of people allowed in (the owner plus anyone they invite).
- A private project is **invisible and inaccessible to every other admin and non-invited person** — not merely hidden in the interface, but actually not reachable by the normal ways into the tool.
- The bar is confidential **from other admins and users of the tool** — not from whoever runs the server itself (that's us). Hiding it from the person who operates the infrastructure isn't achievable for a tool we host, and chasing it would balloon the work for no real gain.

## The honest reality (why this is bigger than a toggle)

The tool stores every project as plain-text files in one shared data repository, on a shared server, reachable through a single shared automation token. So "hide it in the interface" is easy, but real confidentiality from other admins means closing three doors:

1. **The interface.** Hide a private project everywhere (menus, lists, dashboard, search, activity) AND make the server refuse direct access for anyone not invited — not just hide the link.
2. **The automation interface.** It currently uses one shared token, so it can't tell users apart. Real privacy needs each person to have their own identity there, and every listing/lookup to respect who's allowed in. (This is the per-user access work previously parked — see ADR-012 and ADR-008.)
3. **The stored files.** Anyone who can copy the shared data repository can read every project's files today. A private project must be stored so that copying the shared repo does NOT reveal it — either kept in a separate, restricted place, or scrambled (encrypted) so the files are useless without a key the other admins don't have.

Door 3 is the crux and needs a deliberate design decision before we build.

## Suggested phased plan

- **Phase 1 — quick win ("hidden, not yet sealed").** Add the private tag + invited list, and hide private projects from other admins everywhere in the interface and in the automation project-list. Honest framing: good for decluttering and casual privacy, with a clear in-product note that it is hidden but not yet confidential.
- **Phase 2 — true confidentiality.** Per-user identity on the automation interface + a confidential store (separate location or encryption) so other admins genuinely can't reach it by any normal path. This is the real lock, and what was chosen.

## Open decision (needs an ADR first)

How private projects are stored so the shared data repository doesn't expose them: a separate restricted store, or encryption-at-rest with per-owner keys. This single decision shapes most of the build, so it should be settled in an ADR before Phase 2 work starts.