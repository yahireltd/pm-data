---
id: ADR-009
slug: admin-preview-as-via-an-effective-identity-layer-impersonati
title: Admin 'preview as' via an effective-identity layer (impersonation that can only restrict, never escalate)
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0172
  - T-0195
---

## Context
Once access became scoped, admins had no way to verify what a given member or stakeholder actually sees — a misconfigured `team:`/`stakeholders:` list silently grants or hides the wrong things, and the only way to test was to log in as that person. We needed a safe way to view the app as another user. A naive impersonation (an `?as=<email>` URL override) existed for dev and is dangerous: anyone could append it to become anyone. The alternatives were to keep manual cross-account testing, or to build impersonation as a first-class, access-bounded capability.

## Decision
Introduce an `EffectiveIdentity` seam: `getEffectiveIdentity()` returns the previewed email instead of the real one ONLY when a `pm_preview_as` cookie is set AND the real signed-in user is an admin. Because every access decision runs off the effective identity, previewing can only narrow what is visible — it can never escalate, since the gate itself requires the real user to be admin. The real session is untouched, a banner offers 'exit preview', and mutations are correctly blocked while previewing a stakeholder (you see the read-only experience). The insecure `?as=` URL backdoor was removed (T-0195) so identity comes only through this admin-gated path plus the dev `pm_identity` cookie.

## Consequences
Admins can self-serve access verification, which makes the hand-edited membership model safe to operate. We accept that 'preview as' is an admin superpower (admins can view any user's scoped world) and that correctness depends on every code path reading the effective identity rather than the raw session — a discipline that must hold as new pages/actions are added. The preview state is a plain httpOnly cookie, so it is per-browser, not audited.