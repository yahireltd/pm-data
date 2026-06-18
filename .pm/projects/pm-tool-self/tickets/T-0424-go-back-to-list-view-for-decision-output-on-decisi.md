---
id: T-0424
title: Go back to list view for decision output on decisions page
type: feature
state: done
priority: p2
created: 2026-06-18T12:45:36Z
updated: 2026-06-18T13:04:36Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 103424
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Decisions are now list view"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260618-1249
    model: claude
    started: 2026-06-18T12:49:54Z
    status: completed
    summary: "Changed the project Decisions page back to a simple top-to-bottom list. A recent design experiment had switched it to a grid of cards; the ask was to go back to the plain list because it's easier to scan. The list keeps the two useful additions from the experiment: the search box with quick state filters (All / Proposed / Accepted / Superseded / Rejected), and the small avatar icons showing who made each decision. If we'd done nothing, the page would have stayed as cards, which were harder to read at a glance. Benefit: decisions are quick to scan down the page again, while search, filtering, and the \"who decided\" icons all still work."
    ended: 2026-06-18T13:03:04Z
    test_plan: |-
      1. Open a project's Decisions page (e.g. pm-tool-self → Decisions).
      2. Confirm decisions now appear as a single top-to-bottom list of rows, not a grid of cards.
      3. Type in the search box — the list should narrow to matching titles.
      4. Click the filter tiles (All / Proposed / Accepted / Superseded / Rejected) — the list should filter to that state; click again or "Clear" to reset.
      5. Confirm each row shows: the decision title, its state, the kind tag (if any), the decider avatar icons, and the date.
      6. Click a row — it should open that decision's detail page.
      7. Edge cases: a project with no decisions shows the friendly empty state; a search with no matches shows the "No decisions match your search" message.
      8. Cross-impact: this component (DecisionsView) is only used on the project Decisions page — no other screen needs re-checking.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 8
---

Keep the search and the user icons but revert to the list for the actual decisions

## Conversation

**2026-06-18 13:03 claude:** Run run-20260618-1249 completed — Changed the project Decisions page back to a simple top-to-bottom list. A recent design experiment had switched it to a grid of cards; the ask was to go back to the plain list because it's easier to scan. The list keeps the two useful additions from the experiment: the search box with quick state filters (All / Proposed / Accepted / Superseded / Rejected), and the small avatar icons showing who made each decision. If we'd done nothing, the page would have stayed as cards, which were harder to read at a glance. Benefit: decisions are quick to scan down the page again, while search, filtering, and the "who decided" icons all still work.

---

**2026-06-18 13:04 — you**

all done
