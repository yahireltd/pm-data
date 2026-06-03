---
id: ADR-001
slug: keep-filesystem-first-as-source-of-truth-retire-th
title: Keep filesystem-first as source of truth; retire the index + write-hardening debts
state: accepted
decided: 2026-05-29
decided_by:
  - austin (human)
  - claude (agent)
project: pm-tool-self
supersedes: null
superseded_by: null
tickets: []
---

# ADR-001: Keep filesystem-first as source of truth; retire the index + write-hardening debts

## Context

The README advertises a SQLite/fts5 index that does not actually exist — every read is a full
filesystem walk over `.pm/`. There is also no file locking and a single shared id counter, which
means concurrent writers risk last-write-wins clobbering and duplicate ids. These are real debts,
not theoretical ones, and they get worse as agents and humans operate the tool in parallel.

## Decision

Keep markdown-in-git as the **source of truth**. The durable, human-readable, diffable artefacts
stay on disk and in version control.

- Later, build a read-only index that is **rebuilt on change**, **gitignored**, **derived**, and
  **never canonical** — purely an accelerator for reads, safe to delete and regenerate.
- Add file locking and safer id allocation to close the concurrency holes.
- For the multi-tenant future, go **HYBRID**: a server of record owns live coordination
  (id allocation, who-claimed-what, access control, cross-tenant queries) while the files remain
  the durable artefact layer.

## Consequences

- Agents and humans keep reading raw files — no behaviour change today.
- We do **not** migrate to a database now; the index, when it lands, is derived and disposable.
- The README's index claim is corrected to match reality until the derived index actually exists.
- The hybrid model is the deliberate target for multi-tenancy, not an accident of growth.
