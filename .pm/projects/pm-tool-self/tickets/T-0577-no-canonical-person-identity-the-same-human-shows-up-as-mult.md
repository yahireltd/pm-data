---
id: T-0577
title: No canonical person identity — the same human shows up as multiple people (name/email variants + duplicate picks)
type: bug
state: triaged
created: 2026-07-15T10:46:08Z
updated: 2026-07-15T10:48:13Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Zsolt
  channel: dogfood
assignee: null
acceptance_criteria:
  - A person resolves to a single canonical identity (email when available, else a normalised name) — entering a known person via a name or email variant maps to that same identity instead of creating a new person.
  - The same person cannot be added twice to the same entity (stakeholders, assignees/collaborators, meeting attendees, workstream owners, reporter).
  - Avatar colour + initials derive from the canonical identity, so one person always renders as one avatar (not a different colour per spelling).
  - Existing near-duplicates (e.g. 'Zsolt' / 'Zsolt Turu' / 'zsolt@yahire.com') can be reconciled/merged, or are at least surfaced so they can be cleaned up.
  - Avatar colours are unique per user — once a colour is chosen by one user it can't be picked by another (removed from the options in the colour picker), so people who share the same initials still render distinctly.
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - identity
  - roster
  - stakeholders
  - avatars
  - dogfood
attention: null
version: 3
---

## Problem
The same human (e.g. **Zsolt**, and likewise **Austin** / **Ben**) can be selected multiple times, or in different forms, so they show up as **several different people** across the tool.

## Root cause
There is **no canonical person entity** — a person is a loose `(name, channel, contact)` tuple:
1. **Weak dedupe key.** The roster de-dupes by *email when present, else lowercased name* (`web/app/_actions/roster.ts`). So `Zsolt` (no email), `Zsolt Turu`, and `zsolt@yahire.com` don't collapse to one person.
2. **Avatars keyed on the raw name string.** `avatarColor(name)` / `initials(name)` (`web/app/_lib/colors.ts`) hash the exact name, so each spelling gets a different colour/avatar — visually reads as different humans.
3. **No "already added" guard by identity.** Nothing prevents adding the same person twice to one entity (stakeholders, assignees/collaborators, meeting attendees, workstream owners).

## Repro
1. On any project, add a stakeholder **"Zsolt"**, another **"Zsolt Turu"**, and another **"zsolt@yahire.com"**.
2. All three are accepted as separate people, each with a different avatar colour, and all remain selectable elsewhere → one person, three identities.

## Expected
- Resolve people to a **single canonical identity** (email preferred; else a normalised name) on entry, matching against the roster including name variants.
- **Block duplicate** picks of the same identity within one entity.
- Derive **avatar colour/initials from the canonical identity** so a person is always one avatar.
- A way to **reconcile/merge** existing duplicates.

## Code anchors
- `web/app/_lib/colors.ts` — `avatarColor(name)` / `initials(name)` keyed on the raw name string.
- `web/app/_actions/roster.ts` — the roster dedupe identity (email-or-lowercased-name).
- `web/app/_components/AddStakeholderDialog.tsx` — free-text name + roster typeahead entry point.
- `web/app/_components/AssigneePicker.tsx` — assignee/collaborator picking.
- `web/app/_lib/actors.ts` — actor/person handling.

## Note
Bigger than a UI quick win — it's a small **data-model / identity** change (canonical identity + normalisation + merge), so it likely wants a short design pass before build.

## Conversation

**2026-07-15 10:48 claude-code:** **Added requirement (Zsolt):** avatar colours should be **unique per user** — when one user picks a colour, it becomes unavailable to everyone else in the picker. Combined with initials, that keeps two people distinguishable even when their initials match. Enforce in `web/app/_components/AvatarColorControl.tsx` (filter out already-taken colours) plus wherever the per-user colour is stored/resolved.
