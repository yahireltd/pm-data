---
id: ADR-016
slug: org-level-meetings-share-the-m-nnn-id-space-and-a-single-aut
title: Org-level meetings share the M-NNN id space and a single auto-accumulating roster
state: accepted
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0185
---

## Context
Not every meeting belongs to a project (all-hands, vendor calls). We needed project-less meetings with full parity, and they needed attendees — but a project-less meeting has no project stakeholder register to quick-pick from. Two design questions: give org meetings their own id namespace (e.g. OM-NNN) or share M-NNN; and where does a no-project meeting's attendee quick-pick come from. A separate namespace means new id rules everywhere; a separate roster per scope means re-typing the same people.

## Decision
Org meetings keep the same M-NNN id space but live in a distinct on-disk bucket (.pm/meetings/ vs a project's meetings dir, project: null in frontmatter). Because ids are shared, every MUTATION resolves through resolveMeeting(id, project): an explicit slug targets that project's bucket, null/undefined targets the org bucket — fixing the bug where a projects-first id scan landed an org-meeting write on a colliding project M-NNN. Introduce one new entity, the org roster (.pm/roster.md, a managed markdown doc mirroring conventions.md), which auto-accumulates every stakeholder added anywhere (project, ticket, or meeting), deduped by name/email with best-effort non-blocking writes. A project-less meeting's 'from register' quick-pick reads the roster; a project meeting still reads its project register.

## Consequences
Org meetings get full parity without a parallel id scheme, and attendees stay reusable across scopes via the shared roster. Trade-offs: id uniqueness is no longer global — an M-NNN is only unique within its bucket, so every meeting lookup/write must be bucket-qualified (project vs org) or it resolves wrong; and the roster is a new always-growing artifact that every stakeholder-add path must remember to upsert into.