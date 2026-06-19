---
id: T-0446
title: Unify avatar initials & colour per person (same user renders as different circles)
type: bug
state: ready
priority: p2
created: 2026-06-19T09:15:24Z
updated: 2026-06-19T09:17:36Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 113664
reporter: null
assignee:
  kind: human
  name: austin@yahire.com
acceptance_criteria:
  - A single person renders with identical initials AND identical colour across every surface (tickets list, kanban, inbox, dashboard, ticket detail) regardless of how   their name is stored.
  - Austin's variants ("Austin Pickering", "Austin", "austin", "austin@yahire.com") all resolve to one avatar (same initials, same colour).
  - Zsolt's variants ("Zsolt Turu", "Zsolt", "zsolt@yahire.com") all resolve to one avatar.
  - An `avatar_colors` override set for a person applies to ALL of their name variants, not just the exact configured key.
  - Initials are derived from the canonical display name (two-word names give first-letter-of-each; single names give first two letters).
  - Agent/system avatars (claude, claude-code) keep their icon-forward circles and are NOT converted to initials.
  - A name not present in the roster falls back to today's behaviour (hashed colour + sliced initials) with no error.
  - The resolver reads the roster/config once per render pass, not once per avatar (no N+1 disk reads on a long list).
  - Hovering an avatar shows the canonical person name in the title tooltip.
  - "No avatar regressions on: comments, meeting attendees, decision owners, project owners, stakeholder lists."
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
version: 14
defect_status: confirmed
---

\## Problem

&#x20; The same person renders as multiple different avatar circles across ticket lists —

&#x20; different initials AND different colours. E.g. Austin appears as "AP" (rose) on one

&#x20; ticket and "AU" (blue/other) on another; same for Zsolt (ZS vs ZT).

&#x20; \## Context

&#x20; Avatars derive BOTH facets from the raw free-text \`name\` string:

&#x20; \- \`initials(name)\` (web/app/\_lib/colors.ts) — slices letters from the raw string.

&#x20; \- \`avatarColor(name)\` (same file) — hashes the raw string to a palette colour.

&#x20; The ticket data stores one person under many inconsistent strings, so each hashes

&#x20; differently and slices differently. Observed for Austin alone:

&#x20;   "Austin Pickering" → AP   |   "Austin" → AU   |   "austin" → AU (case-sensitive,

&#x20;   different hash)   |   "austin\@yahire.com" → AU

&#x20; A partial colour canonicalizer already exists — resolveAvatarColor() in

&#x20; web/app/\_lib/avatar-prefs.ts, keyed on actorNameKey(name) against config

&#x20; \`avatar\_colors\` — but it only matches the EXACT name ("austin pickering" hits,

&#x20; "austin" misses) and isn't applied at every \<Avatar> call site. Initials have no

&#x20; canonicalization at all.

&#x20; There is already an identity source: .pm/roster.md maps name↔email, and its

&#x20; isSamePerson() logic ("same email OR same name") knows how to dedupe people.

&#x20; \## Proposed approach (render-time, no data migration)

&#x20; Add a server resolver resolvePerson(rawName) → { displayName, colorClass } that:

&#x20; 1\. Builds an alias index from the roster + config (full name, email, email

&#x20;    local-part, first name → canonical person).

&#x20; 2\. Derives initials from the canonical display name and colour from a stable key

&#x20;    (email), honouring the avatar\_colors override.

&#x20; 3\. Falls back to current behaviour when a name isn't in the roster (no regression).

&#x20; Then feed canonical name + colour into \<Avatar> at the list/row render points.

&#x20; Leave agent/system avatars (claude, claude-code → icon-forward circles) untouched.

&#x20; \## Open decision

&#x20; How to handle bare first names shared by two different people ("Austin" when there

&#x20; are two) — auto-match only when unambiguous, vs. require an explicit alias map in

&#x20; config.

&#x20; \## Out of scope

&#x20; \- Normalising the stored name fields in the pm-data repo (possible follow-up).

&#x20; Acceptance criteria:

&#x20; \- A person shows the same initials and colour in every list/row regardless of how their name is stored (Austin always AP/rose; Zsolt always one circle).

&#x20; \- avatar\_colors overrides apply across all name variants of that person, not just the exact key.

  - Agent/system avatars (claude, claude-code) are unchanged. 

&#x20; \- No regression for names not in the roster (fall back to current hash/initials).

&#x20; Key files: web/app/\_lib/colors.ts, web/app/\_lib/avatar-prefs.ts, web/app/\_components/Avatar.tsx, cli/src/lib/roster.ts, plus the list call sites (SectionedList.tsx,

&#x20; TicketRow\.tsx, KanbanBoard.tsx, SortableInboxList.tsx, app/page.tsx).
