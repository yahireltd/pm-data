---
id: T-0421
title: "Small UX polish: count/label correctness (nav + dashboard) + ticket-ID zero-pad width"
type: chore
state: in_progress
created: 2026-06-18T08:58:46Z
updated: 2026-06-18T13:12:46Z
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
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - Each top-nav badge has a clear meaning consistent with the page it opens (e.g. a tooltip + label); the Tickets badge vs Tickets page mismatch is reconciled.
  - The 'Review' badge and the Review page agree (resolve attention-flag vs state=review).
  - The Tickets vs Inbox badge overlap is resolved (distinct meanings, or one consolidated).
  - A decision is recorded and reflected on whether the badges are global or personal (ties to T-0420).
  - The dashboard 'Open tickets' tile either counts only open tickets (excluding done/wontfix/duplicate) or is relabelled to match what it shows.
  - Ticket IDs zero-pad to a width that keeps string-sort order correct beyond 9999 (decide 5 vs 6; decide whether to re-pad existing IDs).
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1248
    model: claude
    started: 2026-06-18T12:48:46Z
    status: in_progress
    summary: Claimed via web UI
labels:
  - ui
  - dogfood
  - for-austin
attention: null
version: 6
---

## Problem

Minor findings from dogfood use. (For Austin.)

### Top-nav badges — unclear, partly mislabelled, all global

The top-nav badges only render for admins and count **globally** (not "assigned to me"). What each counts today (`layout.tsx`):

* **Tickets** = tickets in state `inbox` (untriaged) across all projects — but the Tickets *page* shows ALL tickets, so the badge never matches the page. 

* **Inbox** = inbox tickets not closed — overlaps almost entirely with the Tickets badge.

* **Review** = tickets with an `attention` flag set — NOT `state = review`, so the label can mislead vs the Review page.

* **Ready** = `state = ready` with no assignee (unclaimed).

* **In progress** = `state = in_progress`.

Make each badge's meaning clear and consistent with the page it opens, resolve the **Tickets-vs-Inbox overlap**, fix the **"Review" label/semantics mismatch**, and decide whether badges stay **global** or become **personal** (ties to T-0420). A tooltip per badge would help.

### Dashboard "Open tickets" tile shows the total, not open

On the home dashboard (`app/page.tsx`), the **"Open tickets"** StatTile uses `all.length` — *every* visible ticket (e.g. 200 total) — but is labelled "Open." Real open (excluding done/wontfix/duplicate) is far lower (e.g. 69, which the `/tickets` page shows correctly). Fix: relabel to **"Total tickets"** or count only non-closed states.

### Ticket-ID zero-pad width

IDs use `padStart(4)` (`cli/src/lib/ids.ts:35`). Not a cap — `T-9999` rolls to `T-10000` fine — but **string-sort breaks past 9999** (`T-10000` sorts before `T-9999`). Bump the pad width (5 or 6) so lexicographic sort + visual width stay consistent. Affects new IDs only unless historic IDs are re-padded.
