---
id: T-0425
title: "Ensure ids keep working past T-9999 (5-digit ids: loosen test regexes + audit string sorts)"
type: feature
state: triaged
priority: p2
created: 2026-06-18T12:53:42Z
updated: 2026-06-22T14:03:50Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 111616
reporter: null
assignee: null
acceptance_criteria:
  - Test id regexes accept 5+ digit ids (\d{4,})
  - Id sorts are ordered by numeric suffix (string-sorts audited), not raw string
  - A quick check confirms creating/rendering/linking/sorting a >T-9999 id works
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
version: 3
---

## Problem

Confirm tickets (and other ids) keep working once we cross T-9999 into 5-digit ids.

## Context

Investigated 2026-06-22 (code at `StreamBoxApp/pm-tool`). The core is already safe:
- **Allocation is safe** — ids come from a monotonic counter (`nextTicketId`, `mcp-server/src/lib/ids.ts:33`); `padStart(4,"0")` pads but never truncates, so ticket 10000 → `T-10000`, no crash, no collision.
- **Parsing is safe** — production regexes match `T-(\d+)` (variable width, `ids.ts:78`), so 5-digit ids parse fine.

## Design notes — two real gaps to close

1. **Tests hardcode 4 digits** — `/^T-\d{4}$/` (`mcp-server/tests/round-trip.test.ts:226`) and `/^P-\d{4}$/` (`cli/tests/smoke.test.ts:94`) will fail at 10000. Loosen to `\d{4,}`.
2. **Lexicographic sort risk** — anywhere ids are sorted *as strings*, `T-10000` sorts *before* `T-0999`. Audit string sorts of ids and order by the numeric suffix instead.

Not a crash risk — a "loosen the test regexes + audit string-sorts" chore.