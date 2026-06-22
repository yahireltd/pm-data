---
id: T-0425
title: "Ensure ids keep working past T-9999 (5-digit ids: loosen test regexes + audit string sorts)"
type: feature
state: triaged
priority: p2
created: 2026-06-18T12:53:42Z
updated: 2026-06-22T18:05:18Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 111616
reporter: null
assignee: null
acceptance_criteria:
  - "Decide approach: sort ids by numeric suffix (no migration, recommended) vs widen zero-pad + re-pad every existing id (migration) — record the choice"
  - Ids sort correctly beyond 9999 (string-sorts audited and fixed per the chosen approach)
  - Test id regexes accept 5+ digit ids (\d{4,}) — round-trip.test.ts:226, smoke.test.ts:94
  - A quick check confirms creating/rendering/linking/sorting a >T-9999 id works end to end
out_of_scope: []
code_anchors: []
relates:
  - T-0421
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 5
---

## Problem

Confirm tickets (and other ids) keep working once we cross T-9999 into 5-digit ids.

## Provenance — deferred from T-0421

The ticket-ID zero-pad concern was first raised and **deliberately deferred** in **T-0421** (run `run-20260618-1248`): widening the pad for new IDs only would break sort order against the hundreds of existing 4-digit IDs, so it needs a one-off re-pad migration — judged too risky to bundle into that UX-polish chore. This ticket owns the follow-through. (No code change landed in T-0421 for this.)

## Context

Investigated 2026-06-22 (code at `StreamBoxApp/pm-tool`). The core is already safe:
- **Allocation is safe** — ids come from a monotonic counter (`nextTicketId`, `mcp-server/src/lib/ids.ts:33`); `padStart(4,"0")` pads but never truncates, so ticket 10000 → `T-10000`, no crash, no collision.
- **Parsing is safe** — production regexes match `T-(\d+)` (variable width, `ids.ts:78`), so 5-digit ids parse fine.

The only real exposure is **lexicographic sort**: `T-10000` sorts *before* `T-0999` anywhere ids are sorted as strings.

## Decision needed — two ways to fix the sort

- **Option A (recommended): sort by numeric suffix.** Stop string-sorting ids; order by the parsed number. No migration, mixed 4/5-digit ids are fine. Plus loosen the test regexes below.
- **Option B (T-0421's instinct): widen zero-pad + re-pad migration.** Bump `padStart` to 5–6 and run a one-off migration re-padding every existing id *and its cross-references*. Keeps lexicographic sort valid but is an estate-wide, riskier migration.

## Design notes — the concrete gaps

1. **Tests hardcode 4 digits** — `/^T-\d{4}$/` (`mcp-server/tests/round-trip.test.ts:226`) and `/^P-\d{4}$/` (`cli/tests/smoke.test.ts:94`) will fail at 10000. Loosen to `\d{4,}`.
2. **Lexicographic sort** — audit string sorts of ids; under Option A, order by numeric suffix.

Not a crash risk — a "pick A or B, then loosen test regexes + fix the sort" task.