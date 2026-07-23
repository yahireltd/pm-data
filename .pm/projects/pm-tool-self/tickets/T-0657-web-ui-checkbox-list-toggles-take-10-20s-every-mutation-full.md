---
id: T-0657
title: Web UI checkbox/list toggles take 10–20s — every mutation full-scans the store and revalidates the entire app
type: bug
state: triaged
created: 2026-07-23T22:38:24Z
updated: 2026-07-23T22:38:24Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: austin
  channel: terminal
  contact: austin@yahire.com
assignee: null
acceptance_criteria:
  - A checkbox toggle (acceptance criteria or any small list op) round-trips in well under 1s on the production-size store
  - A scoped mutation no longer calls revalidatePath("/", "layout") — only the affected route(s)/tag(s) are invalidated
  - findTicketById no longer parses every ticket file to find one id (filename prefix match or id index)
  - Store readers are memoized per request/render pass so one page render parses each file at most once
out_of_scope: []
code_anchors:
  - path: web/app/_actions/tickets.ts
    symbol: revalidateAll
  - path: web/app/_actions/tickets.ts
    symbol: toggleAcceptanceCriterion
  - path: cli/src/lib/query.ts
    symbol: findTicketById
  - path: web/app/_components/AcceptanceCriteria.tsx
    symbol: AcceptanceCriteria
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem

Austin reports checkbox toggles in the web UI take 10–20 seconds to persist. Diagnosed 2026-07-23 (Claude, terminal session). The slowness is NOT the filesystem writes — it's read amplification and cache-nuking around them:

1. **Every list-op action full-scans the store to find its ticket.** `toggleAcceptanceCriterion` → `findTicketById` (cli/src/lib/query.ts:496) iterates `loadAllTickets(root)`, parsing every ticket file in every project (~450+ live tickets) to match one id — on every single click.
2. **Every mutation invalidates the whole app.** `revalidateAll()` (web/app/_actions/tickets.ts:123) calls `revalidatePath("/", "layout")` — the comment says "Cheap because all reads are local files", which was true in v0.1 but no longer holds: the store now has hundreds of tickets and meetings carrying full transcripts in their bodies.
3. **The follow-up `router.refresh()` re-renders the whole route tree cold.** With the global invalidation, every layout/page segment re-runs, and the store readers (plain sync `readFileSync` from cli/src/lib) have no per-request memoization — nav badges, project nav, lists and the detail page each re-parse the store independently, so one render can mean many full-store scans, all blocking the event loop (concurrent users queue behind each other).

The actual mutation is two atomic writes (ticket + `bumpProject`), a few ms. Everything else is overhead.

## Fix directions (in bang-for-buck order)

1. **Memoize store readers per render pass** — wrap `loadAllTickets` / project readers in React `cache()` at the web boundary so one request parses each file at most once. No architecture change; likely the biggest single win.
2. **Narrow the revalidation** — `revalidatePath("/tickets/" + id)` plus the specific list routes (or move to `revalidateTag`), instead of the root layout. A checkbox toggle does not need to invalidate every page for every user.
3. **Cheap id lookup** — ticket filenames start with the id (`T-NNNN-slug.md`), so `findTicketById` can match on filename prefix per tickets dir instead of parsing every file.
4. If still needed: an in-process mtime-invalidated read cache. Filesystem stays source of truth (per ADR-001 — this is a cache, not a resurrected index).

## Context

- The optimistic-UI convention (ADR-027, T-0196) already masks latency on the acceptance-criteria checkboxes specifically, but other checkbox surfaces without optimistic handling show the full 10–20s, and the global invalidation slows every subsequent navigation for everyone.
- Cost grows with the store — meeting transcripts (e.g. M-013's) now ride in bodies that get parsed on every scan.