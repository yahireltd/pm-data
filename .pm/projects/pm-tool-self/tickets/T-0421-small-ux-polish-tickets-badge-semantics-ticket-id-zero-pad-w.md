---
id: T-0421
title: "Small UX polish: top-nav badge semantics + ticket-ID zero-pad width"
type: chore
state: triaged
created: 2026-06-18T08:58:46Z
updated: 2026-06-18T09:12:23Z
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
  - Each top-nav badge has a clear meaning consistent with the page it opens (e.g. a tooltip + label); the Tickets badge vs Tickets page mismatch is reconciled.
  - The 'Review' badge and the Review page agree (resolve attention-flag vs state=review).
  - The Tickets vs Inbox badge overlap is resolved (distinct meanings, or one consolidated).
  - A decision is recorded and reflected on whether the badges are global or personal (ties to T-0420).
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
version: 2
---

## Problem

Minor findings from dogfood use. (For Austin.)

### D. Top-nav badges — unclear, partly mislabelled, all global
The top-nav badges only render for admins and count **globally** (not "assigned to me"). What each counts today (`layout.tsx`):

- **Tickets** = tickets in state `inbox` (untriaged) across all projects — but the Tickets *page* shows ALL tickets, so the badge never matches the page.
- **Inbox** = inbox tickets not closed — overlaps almost entirely with the Tickets badge (two badges, ~same thing).
- **Review** = tickets with an `attention` flag set — NOT `state = review`, so the label can mislead vs what the Review page lists.
- **Ready** = `state = ready` with no assignee (unclaimed).
- **In progress** = `state = in_progress`.

Wanted: make each badge's meaning clear and consistent with the page it opens, resolve the **Tickets-vs-Inbox overlap**, fix the **"Review" label/semantics mismatch**, and decide whether badges stay **global** or become **personal** (ties to T-0420). A tooltip per badge would help.

### E. Ticket-ID zero-pad width
IDs use `padStart(4)` (`cli/src/lib/ids.ts:35`). Not a cap — `T-9999` rolls to `T-10000` fine — but **string-sort breaks past 9999** (`T-10000` sorts before `T-9999`). Bump the pad width (5 or 6) so lexicographic sort + visual width stay consistent. Affects new IDs only unless historic IDs are re-padded.