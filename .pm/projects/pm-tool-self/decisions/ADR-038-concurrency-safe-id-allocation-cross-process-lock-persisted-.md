---
id: ADR-038
slug: concurrency-safe-id-allocation-cross-process-lock-persisted-
title: "Concurrency-safe id allocation: cross-process lock + persisted counters"
state: accepted
decided: 2026-06-09
decided_by:
  - austin
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0270
  - T-0316
  - T-0317
---

# Concurrency-safe id allocation: cross-process lock + persisted counters

## Context

pm-tool's data is files on one server, written by three separate processes — the web app, the CLI, and the MCP server (agents). Id allocation read the current maximum (a counter in config.yml for global T-/P-/S- ids, or a directory scan for per-project M-/ADR-/MS-/… ids) and added one, with no locking. Two creates landing together could read the same maximum and hand out the same id; the per-project scanners were also reimplemented inside each create tool. Combined with whole-file writes, this is the clashing-id / lost-edit risk raised in T-0270.

## Decision

One cross-process advisory lock wraps every id allocation: `withLock` takes an O_EXCL lock file on the shared store, steals a stale lock after 10s, and is synchronous (the allocators are sync). Counters persist in `.pm/config.yml` — the existing global counters plus `id_counters.perProject[scope][space]` for per-project ids — so uniqueness no longer depends on file-write timing; a directory scan is kept only as a defensive seed/fallback (first use, or files added by hand). A single shared `allocateProjectId(space, scope, dir, prefix, pad)`, mirrored in the cli and mcp-server id libs, is the one allocator every create routes through.

## Rollout

Staged on the live write path, each step independently deployable and tested (8-way concurrent allocation → all-distinct ids):
1. Lock the global allocators (T-/P-/S-) — shipped.
2. Add allocateProjectId + route each entity across all three surfaces together — decisions + meetings shipped; milestone / sprint / tech-session / status-note / incident / handover / pre-project to follow.

## Alternatives considered

- Per-caller locks around scan→write: correct, but spreads lock logic across ~25 call sites and holds the lock across the slower file write.
- O_EXCL reservation on the target filename: fails — two creates with different titles produce different filenames for the same id, so the reservation never collides.

## Consequences

- All allocations serialise on one brief lock; at this team's create rate the contention is negligible.
- The two id libs (cli, mcp-server) still duplicate the allocator — unifying them is deferred to a separate ticket.
- This covers concurrent CREATES. Lost-update protection for concurrent EDITS of the same record is the separate optimistic-version-check work (T-0317).