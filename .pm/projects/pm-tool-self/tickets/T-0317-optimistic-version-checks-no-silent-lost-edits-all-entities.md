---
id: T-0317
title: Optimistic version checks — no silent lost edits (all entities)
type: feature
state: in_progress
created: 2026-06-09T16:51:53Z
updated: 2026-06-09T20:56:18Z
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
  - Each entity carries a version (hash or counter); a write includes the version it was based on, and the shared write path re-reads + rejects a stale write so a concurrent change is never silently overwritten -- across ALL entity types (tickets, meetings, decisions, sprints, milestones, status notes, etc.)
  - The read-modify-write is serialized per entity so two simultaneous saves can't interleave (web / CLI / MCP)
  - Web edit forms capture the version on load and send it; on a stale reject the form PRESERVES the user's unsaved text and shows 'this changed since you opened it' so they re-apply over the latest without retyping
  - Agent (MCP) writes are covered by the server-side serialization + version check; verified by a concurrent-edit test (two saves from the same stale base -> the second is rejected, nothing lost)
out_of_scope:
  - Auto-merge of conflicting same-field edits / a merge UI (the deferred 'conflict UX' polish, option d)
  - Real-time collaborative editing (CRDT / OT)
code_anchors:
  - path: cli/src/lib/io.ts
    symbol: writeMarkdown
  - path: mcp-server/src/lib/io.ts
    symbol: writeMarkdownAtomic
  - path: web/app/_components/EditableField.tsx
  - path: schemas
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260609-1840
    model: claude-opus-4-8
    started: 2026-06-09T18:40:39Z
    status: in_progress
    progress:
      - at: 2026-06-09T18:41:10Z
        note: "Scoped + designed. Mechanism: a `version` integer per record; readMarkdown returns it; writeMarkdown (both io libs) locks the file, re-reads the current version, and (when enforcement is on) rejects a write whose version != current (someone saved in between), else writes version+1. The version rides IN the frontmatter, so server-side load->mutate->write (CLI/MCP/in-request web) carries it for free — no threading through ~100 call sites; only the HUMAN stale-edit (open form -> someone else saves -> you save over them) needs the browser's loaded version sent back through the web edit path (EditableField + field-setter actions) + a conflict UX (keep the user's typed text, \"this changed, reload\"). Scoping found: the 15 entity schemas are strict (additionalProperties:false) + validated on write, so `version` must be added to ~13 schemas (decision already has it); the write chokepoint is the 2 duplicated io libs (cli + mcp). Staged rollout (mirrors edge-auth): (1) add version to schemas + maintain it in read/write (stamp version+1, NO reject) — backward-compatible; (2) add the reject behind a flag (PM_VERSION_ENFORCE), deploy off, shadow-log would-be conflicts, then enable; (3) web conflict UX. ~3-5 days; HIGH blast radius (changes every write)."
      - at: 2026-06-09T18:59:43Z
        note: "Step 1 SHIPPED + verified (f02aa8e). Every entity now carries a `version` number maintained by the write layer: all 12 entity schemas declare it, and both write chokepoints (cli writeMarkdown = web+CLI, mcp writeMarkdownAtomic) stamp version = max(on-disk, in-memory) + 1 on every save. Stamp-only — nothing is rejected yet, fully backward-compatible. Validated: typechecks + web build clean; smoke: create->1, edit->2, stale write stays monotonic, non-entity files untouched; this very progress note is the live test (it should stamp a version onto the T-0317 ticket file). Found + logged for step 2: comms is a THIRD write path (bare writeFileSync in remind.ts reminders_sent + graph-poll.ts email->ticket) — no stamping, not atomic; must route through the shared writer before enforcement or its writes bypass the version check. Next: step 2 — rejection behind PM_VERSION_ENFORCE with shadow-logging first, then step 3 the web conflict UX (keep unsaved text)."
      - at: 2026-06-09T19:10:45Z
        note: "Step 2 SHIPPED in shadow mode (8c86b2d). The write layer now detects stale saves: every entity write is serialized per file (lock), the claimed base version is compared against disk, conflicts are always journal-logged, and with PM_VERSION_ENFORCE=1 a stale save is rejected with StaleWriteError (file left intact) instead of silently overwriting. Deployed with the flag UNSET = shadow mode — we watch the journal for false conflicts during normal use before flipping enforcement on (one-line server env change). BONUS closures: comms was a third, unguarded write path — its meeting-reminder ledger and email->ticket writes now go through the shared atomic+stamped writer, and its private ticket-id allocator (a missed T-0316 race — raw config.yml read-modify-write) now takes the shared lock. Validated: typechecks, comms 38 tests, web build, full conflict matrix smoke (shadow allows+logs, enforce rejects+file intact, fresh edits + versionless legacy writes pass in both modes, no leftover locks). Remaining: let shadow soak, enable PM_VERSION_ENFORCE on the server, then step 3 — web edit forms carry the loaded version and keep the user's unsaved text on a stale reject."
      - at: 2026-06-09T19:19:54Z
        note: "Step 3 SHIPPED for tickets (e74615e) — the pilot entity for the conflict UX. The ticket page now sends the version it rendered with on every inline edit (title, type, priority, assignee, due, estimate) and on body saves; the server rejects a stale edit instead of silently overwriting, and the control keeps the user's typed text open with a plain \"this changed since you opened it — your text is kept here, reload and re-apply\" note. The body editor (the highest-stakes lost-edit: long prose) stays open with the full text on a reject. Versionless callers are untouched. This check is deterministic at the action layer — it works NOW, independent of the io-level PM_VERSION_ENFORCE backstop (still in shadow, 0 conflicts logged so far). REMAINING on T-0317: (1) extend the same form wiring to the other entities (decisions, meetings, projects, milestones, sprints — same mechanical pattern as the ticket pilot); (2) flip PM_VERSION_ENFORCE on after the shadow soak (one-line server env change). VERIFY (human, ~2 min): open the same ticket in two browser tabs; edit + save the title in tab A; then in tab B edit the description and save — tab B must show the red \"changed since you opened it\" note with your text still in the editor; reload tab B and re-apply — saves fine."
      - at: 2026-06-09T20:56:18Z
        note: "LIVE VERIFIED with a real agent-vs-human race (with Austin, on T-0319). Round 1 exposed a genuine hole: a record never written since versioning shipped carries no version, so the page made no claim and the concurrent agent edit went undetected (most existing records were in that state). Fixed + deployed (1666d53): a versionless page claims version 0, so any stamped write in between is caught — first-touch races included. Round 2 PASSED end-to-end: Austin opened T-0319 in the browser, the agent saved a label change over MCP behind the tab's back, Austin then saved a description edit — the save was rejected with the keep-your-text message (\"changed by someone else after you opened it... your text is kept here\") and his text stayed in the editor. The exact scenario this ticket was opened for, demonstrated working in production."
labels:
  - concurrency
attention: null
version: 4
---

## Problem

The data is files with no version check. You open a record; someone else (or an agent) saves a change to it; you save — and your save writes the whole record back from the version *you* loaded, silently wiping theirs. Last-write-wins, no warning. This is the "I have it open, then Zsolt opens, then an agent updates" case exactly.

## Approach

Optimistic concurrency: a version per entity + a serialized read-modify-write in the shared data layer + reject-on-stale. Thread the expected version through the web / CLI / MCP write APIs. On the web, the edit forms keep the user's unsaved text when a save is rejected, so they re-apply over the latest rather than retyping.

## Context

The "a" of the a+b approach (T-0270 / SPR-029). Scope = all entity types; conflict behaviour = reject-but-keep-your-text (decided with the user). Allocator/write-path confirmed by the spike (T-0315).