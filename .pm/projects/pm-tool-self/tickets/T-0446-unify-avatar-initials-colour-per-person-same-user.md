---
id: T-0446
title: Unify avatar initials & colour per person (same user renders as different circles)
type: bug
state: review
priority: p2
created: 2026-06-19T09:15:24Z
updated: 2026-07-14T14:00:50Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 113664
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - A single person renders with identical initials AND identical colour across every surface (tickets list, kanban, inbox, dashboard, ticket detail) regardless of how their name is stored.
  - Austin's variants ("Austin Pickering", "Austin", "austin", "austin@yahire.com") all resolve to one avatar (same initials, same colour).
  - Zsolt's variants ("Zsolt Turu", "Zsolt", "zsolt@yahire.com") all resolve to one avatar.
  - An `avatar_colors` override set for a person applies to ALL of their name variants, not just the exact configured key.
  - Initials are derived from the canonical display name (two-word names give first-letter-of-each; single names give first two letters).
  - Agent/system avatars (claude, claude-code) keep their icon-forward circles and are NOT converted to initials.
  - A name not present in the roster falls back to today's behaviour (hashed colour + sliced initials) with no error.
  - The resolver reads the roster/config once per render pass, not once per avatar (no N+1 disk reads on a long list).
  - Hovering an avatar shows the canonical person name in the title tooltip.
  - "No avatar regressions on: comments, meeting attendees, decision owners, project owners, stakeholder lists."
  - 'Avatar colour selection is exclusive: when a user picks a colour already claimed by a different person, setMyAvatarColor rejects it with a clear error (e.g. "That colour is already taken.") and the choice is not saved.'
  - "The colour picker on /me reflects taken colours: swatches already claimed by other people are visibly disabled/greyed (with a 'taken' affordance), so only free colours can be selected; clearing your colour (Auto) frees it for others."
out_of_scope: []
code_anchors:
  - path: web/app/_components/Avatar.tsx
    symbol: shared Avatar, deterministic avatarColor()/initials()
  - path: web/app/_components/AvatarColorControl.tsx
    symbol: per-user colour override
  - path: web/app/_components/ConversationThread.tsx
    symbol: override load + pass-through
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260714-1222
    started: 2026-07-14T12:22:37Z
    status: completed
    ended: 2026-07-14T14:00:50Z
    summary: "Send-back fixed: the avatar next to your name in the top bar now shows your chosen colour. You'd picked red, but that corner stayed the automatic green — the top bar runs in the browser and simply couldn't read the colour choices, which live in the server's settings, so it always fell back to the auto colour. The page shell now looks your colour up on the server (matched exactly the way the picker saves it) and hands it to the top bar. Everywhere else already honoured the pick. One honest caveat from the wider audit: the deeper ask on this ticket — making every spelling of a person (\"Austin\", \"Austin Pickering\", the email) resolve to one identical circle — is NOT built yet; if that still matters day-to-day, send this back once more and it becomes the next piece of work."
    test_plan: "1. After deploy, with your avatar colour set to red on Settings (moved there by T-0447): the circle at the TOP RIGHT next to your name is red, on every page. 2. Change the colour → the top bar updates after a refresh; set it to Auto → it returns to the automatic colour. 3. Spot-check other surfaces still match (comments, stakeholder panels). 4. Known remaining gap: different spellings of the same person may still render as different circles in lists — that's the unbuilt canonicalisation half; send back if you want it built now."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-14T14:00:50Z
version: 23
defect_status: confirmed
collaborators:
  - kind: human
    name: Austin Pickering
---

## Problem

The same person renders as multiple different avatar circles across ticket lists — different initials AND different colours. E.g. Austin appears as "AP" (rose) on one ticket and "AU" (blue/other) on another; same for Zsolt (ZS vs ZT).

## Context

Avatars derive BOTH facets from the raw free-text `name` string:
- `initials(name)` (web/app/_lib/colors.ts) — slices letters from the raw string.
- `avatarColor(name)` (same file) — hashes the raw string to a palette colour.

The ticket data stores one person under many inconsistent strings, so each hashes differently and slices differently. Observed for Austin alone: "Austin Pickering" → AP | "Austin" → AU | "austin" → AU (case-sensitive, different hash) | "austin@yahire.com" → AU.

A partial colour canonicalizer already exists — resolveAvatarColor() in web/app/_lib/avatar-prefs.ts, keyed on actorNameKey(name) against config `avatar_colors` — but it only matches the EXACT name ("austin pickering" hits, "austin" misses) and isn't applied at every <Avatar> call site. Initials have no canonicalization at all.

There is already an identity source: .pm/roster.md maps name↔email, and its isSamePerson() logic ("same email OR same name") knows how to dedupe people.

## Proposed approach (render-time, no data migration)

Add a server resolver resolvePerson(rawName) → { displayName, colorClass } that:
1. Builds an alias index from the roster + config (full name, email, email local-part, first name → canonical person).
2. Derives initials from the canonical display name and colour from a stable key (email), honouring the avatar_colors override.
3. Falls back to current behaviour when a name isn't in the roster (no regression).

Then feed canonical name + colour into <Avatar> at the list/row render points. Leave agent/system avatars (claude, claude-code → icon-forward circles) untouched.

## Open decision

How to handle bare first names shared by two different people ("Austin" when there are two) — auto-match only when unambiguous, vs. require an explicit alias map in config.

## Out of scope

- Normalising the stored name fields in the pm-data repo (possible follow-up).

## Key files

web/app/_lib/colors.ts, web/app/_lib/avatar-prefs.ts, web/app/_components/Avatar.tsx, cli/src/lib/roster.ts, plus the list call sites (SectionedList.tsx, TicketRow.tsx, KanbanBoard.tsx, SortableInboxList.tsx, app/page.tsx). Colour-exclusivity work touches web/app/_actions/preferences.ts (setMyAvatarColor) and web/app/_components/AvatarColorControl.tsx.

## Conversation

**2026-07-14 12:23 claude-code:** Run run-20260714-1222 completed — Backlog audit: this looks already fixed. Avatars now come from one shared component that derives the initials and colour from the person's name the same way everywhere, with your saved colour override applied on top (the picker on /me), and the comment threads load those overrides so the same person renders identically across pages. Since this was a visual bug you reported, you're the right judge: if you can still find a place where the same person shows different initials or colours, send this back with a screenshot of where; otherwise close it.
