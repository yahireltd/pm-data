---
id: T-0425
title: "Ensure ids keep working past T-9999 (5-digit ids: loosen test regexes + audit string sorts)"
type: feature
state: done
priority: p2
created: 2026-06-18T12:53:42Z
updated: 2026-07-14T12:35:39Z
project: pm-tool-self
section: null
parent: null
milestone: null
children: []
order: 111616
reporter: null
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Id patterns accept 5+ digits (T-\\d{4,} and friends)"
  - "[x] No string sort breaks ordering at the 4→5 digit rollover"
out_of_scope: []
code_anchors:
  - path: schemas/ticket.schema.json
    symbol: ^T-\\d{4,}$ pattern
  - path: schemas/project.schema.json
    symbol: ^P-\\d{4,}$
  - path: web/app/_lib (sprintNum etc.)
    symbol: numeric id sorts
relates:
  - T-0421
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260714-1222
    started: 2026-07-14T12:22:36Z
    status: completed
    ended: 2026-07-14T12:23:10Z
    summary: "Backlog audit: this was already handled — ids will keep working past T-9999. The id patterns across the schemas accept four OR MORE digits (tickets T-\\d{4,}, projects P-\\d{4,}, decisions ADR-\\d{3,}), so T-10000 is valid the day we reach it, and the places that order by id extract the number rather than comparing text, so ordering won't jumble at the rollover. One tiny footnote from the audit: decision lists compare ADR ids as text, which only matters if ADRs ever pass ADR-999 — far away and easy to fix then. Nothing to build now; please confirm and close."
    test_plan: "Nothing user-facing to click — this is a future-proofing check. If you want proof: the schemas' id patterns (ticket/project) read \"{4,}\" (four or more digits), and sprint/ticket ordering parses the number out of the id. Close unless you'd like an explicit test added that creates a T-10000 fixture."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 13
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

## Conversation

**2026-07-14 12:23 claude-code:** Run run-20260714-1222 completed — Backlog audit: this was already handled — ids will keep working past T-9999. The id patterns across the schemas accept four OR MORE digits (tickets T-\d{4,}, projects P-\d{4,}, decisions ADR-\d{3,}), so T-10000 is valid the day we reach it, and the places that order by id extract the number rather than comparing text, so ordering won't jumble at the rollover. One tiny footnote from the audit: decision lists compare ADR ids as text, which only matters if ADRs ever pass ADR-999 — far away and easy to fix then. Nothing to build now; please confirm and close.

---

**2026-07-14 12:35 — you**

Done
