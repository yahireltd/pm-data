---
id: T-0421
title: "Small UX polish: Tickets badge semantics + ticket-ID zero-pad width"
type: chore
state: triaged
created: 2026-06-18T08:58:46Z
updated: 2026-06-18T08:58:46Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p3
reporter:
  kind: human
  name: Zsolt
  channel: email
  contact: zsolt@yahire.com
assignee: null
acceptance_criteria:
  - The Tickets badge and the Tickets page present a consistent, understandable relationship (badge meaning clear, or page scoped to match).
  - Ticket IDs zero-pad to a width that keeps string-sort order correct beyond 9999 (decide 5 vs 6; decide whether to re-pad existing IDs).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 1
---

## Problem

Two minor findings from dogfood use. (For Austin.)

### D. Tickets badge vs list mismatch
The top-nav **"Tickets" badge counts untriaged tickets** (work needing triage), but clicking **Tickets opens the full list of all tickets** — so the badge number never matches the page, which is confusing. Either make the badge's meaning clear, relabel it, or scope the page to match.

### E. Ticket-ID zero-pad width
IDs use `padStart(4)` (`cli/src/lib/ids.ts:35`). Not a cap — `T-9999` rolls to `T-10000` fine — but **string-sort breaks past 9999** (`T-10000` sorts before `T-9999`). Bump the pad width (5 or 6) so lexicographic sort + visual width stay consistent. Affects new IDs only unless historic IDs are re-padded.