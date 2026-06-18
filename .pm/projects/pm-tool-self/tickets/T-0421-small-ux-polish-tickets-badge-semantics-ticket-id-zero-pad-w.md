---
id: T-0421
title: "Small UX polish: count/label correctness (nav + dashboard) + ticket-ID zero-pad width"
type: chore
state: in_progress
created: 2026-06-18T08:58:46Z
updated: 2026-06-18T19:08:12Z
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
  - "[x] Each top-nav badge has a clear meaning consistent with the page it opens (e.g. a tooltip + label); the Tickets badge vs Tickets page mismatch is reconciled."
  - "[x] The 'Review' badge and the Review page agree (resolve attention-flag vs state=review)."
  - The Tickets vs Inbox badge overlap is resolved (distinct meanings, or one consolidated).
  - "[x] A decision is recorded and reflected on whether the badges are global or personal (ties to T-0420)."
  - "[x] The dashboard 'Open tickets' tile either counts only open tickets (excluding done/wontfix/duplicate) or is relabelled to match what it shows."
  - "[x] Ticket IDs zero-pad to a width that keeps string-sort order correct beyond 9999 (decide 5 vs 6; decide whether to re-pad existing IDs)."
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
    status: completed
    summary: "Tidied up the little count badges in the top bar and a number on the dashboard so they say what they actually mean. The \"Review\" badge now matches the Review page exactly (it counts what's in review or flagged for your attention; before, it quietly undercounted). The \"Tickets\" badge no longer shows a number, because the Tickets page lists everything, so a count there never lined up — untriaged work is what the \"Inbox\" badge is for, which also clears up the old Tickets-vs-Inbox confusion. Every badge now has a hover tooltip explaining it. On the dashboard, the \"Open tickets\" tile used to show the grand total (including finished and won't-fix tickets); it now counts only genuinely open tickets. We also recorded a decision (ADR-044): these badges stay a global overview for admins, and the personal \"what's assigned to me\" view is handled separately by the My-work ticket (T-0420). One part is deliberately deferred: widening the zero-padding in ticket IDs (e.g. T-0421 → T-00421). Doing that for new IDs only would actually break sort order against the hundreds of existing 4-digit IDs, so it needs a one-off migration that re-pads them all at once — too risky to bundle here. Benefit: the numbers people glance at now tell the truth, and we avoided introducing a worse sorting bug."
    ended: 2026-06-18T13:57:47Z
    test_plan: |-
      1. As an admin, look at the top-bar badges. Hover each — a tooltip explains what it means.
      2. Tickets badge: should show NO number (just the label). Inbox badge: shows the count of untriaged tickets.
      3. Review badge: compare its number to the Review page's item count — they should match now (Review = in review OR attention-flagged).
      4. Dashboard "Open tickets" tile: should show only open tickets (not the grand total). Sanity-check: it should roughly match the "open" count on the All-tickets page, and be much smaller than the "total".
      5. With a project filter active (T-0362), the Review/Ready/In-progress badge numbers should reflect that project; Tickets still has no number; Inbox stays org-wide.
      6. Note for the reviewer — decision needed: the ticket-ID zero-pad width was NOT changed (see summary / ADR reasoning). If you want IDs widened, it should be a separate, coordinated re-pad migration across all existing IDs and their references. Confirm you're happy to track that separately, or send this back if you'd rather do it now.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - ui
  - dogfood
  - for-austin
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-18T13:57:47Z
version: 17
collaborators:
  - kind: human
    name: Austin Pickering
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

## Conversation

**2026-06-18 13:57 claude:** Run run-20260618-1248 completed — Tidied up the little count badges in the top bar and a number on the dashboard so they say what they actually mean. The "Review" badge now matches the Review page exactly (it counts what's in review or flagged for your attention; before, it quietly undercounted). The "Tickets" badge no longer shows a number, because the Tickets page lists everything, so a count there never lined up — untriaged work is what the "Inbox" badge is for, which also clears up the old Tickets-vs-Inbox confusion. Every badge now has a hover tooltip explaining it. On the dashboard, the "Open tickets" tile used to show the grand total (including finished and won't-fix tickets); it now counts only genuinely open tickets. We also recorded a decision (ADR-044): these badges stay a global overview for admins, and the personal "what's assigned to me" view is handled separately by the My-work ticket (T-0420). One part is deliberately deferred: widening the zero-padding in ticket IDs (e.g. T-0421 → T-00421). Doing that for new IDs only would actually break sort order against the hundreds of existing 4-digit IDs, so it needs a one-off migration that re-pads them all at once — too risky to bundle here. Benefit: the numbers people glance at now tell the truth, and we avoided introducing a worse sorting bug.

---

**2026-06-18 19:05 — Austin Pickering**

looks like the tickets count still isnt quite right need to investigate that further tomorrow
