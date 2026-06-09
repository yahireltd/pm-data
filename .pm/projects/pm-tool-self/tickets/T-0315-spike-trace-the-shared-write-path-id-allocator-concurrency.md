---
id: T-0315
title: "Spike: trace the shared write path + id allocator (concurrency)"
type: spike
state: review
created: 2026-06-09T16:50:45Z
updated: 2026-06-09T22:26:54Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] Determine whether all entity mutations funnel through ONE shared read-modify-write function, or each surface (web server actions / CLI / MCP tools) writes files independently"
  - "[x] Locate the id allocator and how creates read/increment/write the counter across all id spaces (T- global, plus per-project M-/MS-/ADR-/SPR-/TS-)"
  - "[x] Output: a short write-path + id-allocator map, a firmed a+b estimate, and any scope tweaks for the atomic-id and version-check tickets"
out_of_scope: []
code_anchors:
  - path: cli/src/lib
  - path: mcp-server/src/tools/create-ticket.ts
  - path: web/app/_actions/tickets.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260609-1703
    model: claude-opus-4-8
    started: 2026-06-09T17:03:16Z
    status: completed
    ended: 2026-06-09T17:04:20Z
    summary: "We checked how the tool saves data, to size the concurrency work before building it — so we build the right thing without surprises and don't over-engineer. Findings: the save logic lives in two near-identical copies (one used by the website and command line, one for the AI/agent connection), so fixes land in two clear places rather than scattered everywhere. The id-numbering can hand the same number to two things created at the same instant. Every save rewrites the whole record. And there is no \"has this changed since you opened it?\" check at all today. The upshot: the planned \"make it safe\" work — race-proof id numbering plus a change-detection check — is about a week, and the cheap, independent id fix can go first."
    test_plan: |-
      ## Findings (verify against code)
      - **Write path = two near-duplicate libs:** `cli/src/lib/io.ts` (`writeMarkdown`/`atomicWrite`) is imported by web **and** CLI; `mcp-server/src/lib/io.ts` (`writeMarkdownAtomic`) is a near-copy for the MCP. So a+b lands in **2 places**, not one chokepoint, not 3x.
      - **ID allocation is racy:** `cli/src/lib/ids.ts` `allocateId()` does read-config → +1 → write-config (race window); ~9 per-project allocators rescan the dir for max+1 (`nextDecisionId`, `allocateMeetingId`, …); plus the `mcp-server/src/lib/ids.ts` duplicate (`nextTicketId`/`nextProjectId`). Fix = a lock (or persistent atomic counters) around allocate+write, in both libs. **~2 days.**
      - **Version check:** add a `version` frontmatter field + reject-on-stale in `writeMarkdown` (cli) and `writeMarkdownAtomic` (mcp). This catches concurrent *server-side* saves for free (the version rides in the loaded record). The **human stale-edit** also needs the loaded version carried through the web edit path — likely a few shared pieces (`EditableField` + the field-setter actions), not every form. **~3–5 days.**
      - Writes are whole-file; `cli/src/lib/io.ts atomicWrite` lacks fsync (the MCP has it) — add for crash-safety.
      - **Smell:** the cli↔mcp lib duplication (`io.ts` + `ids.ts`) is a parity risk worth unifying separately.

      ## Estimate: a+b ≈ ~1–1.5 weeks. Recommend T-0316 (atomic ids) first — cheap, independent, kills the id-collision risk that has actually bitten us.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - concurrency
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-09T17:04:20Z
version: 5
---

## Problem

Before building the concurrency fixes (atomic id allocation + optimistic version checks), confirm the write architecture — it's the biggest swing factor on effort. Do all entity mutations funnel through ONE shared read-modify-write function (then the fixes land in a single place), or does each surface — web server actions, CLI, MCP tools — do its own file I/O (then the work multiplies ~3x)? ADR-025 implies a shared core, but verify.

## Output

A short map of the write path + the id allocator, a firmed a+b estimate, and any scope adjustments to the atomic-id and version-check tickets.

## Context

Decomposed from T-0270 (concurrent user editing); this sprint takes the "a+b — make it safe" approach (race-safe ids + optimistic version checks), not real-time collaborative editing.

## Conversation

**2026-06-09 17:04 claude-code:** Run run-20260609-1703 completed —

**What we did:** We mapped exactly how the tool saves data and hands out id numbers, before building the concurrency protections.

**Why:** To size the work properly and put the fixes in the right places — without surprises or over-engineering.

**What would have happened if we did nothing:** We would have built blind. The save logic lives in two near-identical copies (one for the website + command line, one for the AI/agent connection), id numbering could hand the same number to two things created at the same instant, every save rewrites the whole record, and there was no "has this changed since you opened it?" check at all — fixes aimed at the wrong place would have missed surfaces.

**The benefit:** A clear map and a firm ~1-week plan: ship the cheap, independent id fix first, then the change-detection layer — each in the two known chokepoints.
