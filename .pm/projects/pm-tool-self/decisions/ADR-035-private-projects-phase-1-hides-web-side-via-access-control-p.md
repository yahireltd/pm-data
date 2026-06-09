---
id: ADR-035
slug: private-projects-phase-1-hides-web-side-via-access-control-p
title: "Private projects: Phase 1 hides web-side via access control; Phase 2 (confidential storage + per-user MCP) deferred"
state: accepted
decided: 2026-06-08
decided_by:
  - austin
  - claude
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0290
---

## Context

We want "private projects" (T-0290): a project visible only to its owner and invited people, hidden from other admins. The honest reality is that real confidentiality means closing three doors: (1) the web interface, (2) the automation/MCP interface, (3) the at-rest files. Doors 2 and 3 are large — the MCP uses one shared bearer token (can't tell users apart; per-user auth was parked, see ADR-012/ADR-008), and every project is a plain-text file in one shared data repo.

## Decision

Ship in two phases, and be honest in-product about which door is closed.

**Phase 1 — "hidden, not yet sealed" (THIS ticket, built now).** Add `private: boolean` + `invited: string[]` to a project. Enforce visibility **server-side in the web app** via `access.ts`: `projectAccess` / `accessibleProjects` / `visibleProjectSlugs` return "none"/omit a private project for anyone not in its allowed set (invited ∪ team ∪ stakeholders) — **other admins included**. The project-page gate (`canSeeProject` in layout.tsx) refuses direct access, not just hides links. Anti-lockout: turning privacy on adds the current user to `invited`. The control carries a plain-language note that this is **not** yet confidential from the MCP interface or from anyone who can read the shared files.

**Phase 2 — true confidentiality (DEFERRED, separate ticket).** Requires per-user identity on the MCP (so listings/lookups respect who's asking) AND a confidential at-rest store. **Storage direction (recommended, to confirm before Phase-2 build): a separate restricted store** for private projects rather than encryption-at-rest with per-owner keys — it's far simpler to reason about and audit, avoids key-management/rotation complexity, and matches the existing filesystem-first model. Encryption-at-rest stays the fallback if a separate store proves impractical.

## Consequences

- Phase 1 is genuinely useful (declutter + casual privacy from other admins in the app) and ships without touching the MCP or storage.
- The product never over-promises: the in-app note + this ADR state plainly that the server operator (us) can still access private projects, and that Phase 1 is web-only.
- Phase 2 is gated on per-user MCP auth — it can't be faked with the shared token, so we don't pretend to enforce privacy there in Phase 1.
- `private`/`invited` are optional and default off; existing projects are unaffected.
