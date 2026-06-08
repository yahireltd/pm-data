---
id: T-0290
title: Private / confidential projects — visible only to the owner and invited people, hidden from other admins
type: feature
state: review
created: 2026-06-06T01:37:53Z
updated: 2026-06-08T14:59:59Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - A project can be marked private and given an invited list (the owner plus invited people)
  - In the WEB app, a private project is hidden from and access-refused to anyone not in its allowed set (invited + team + stakeholders) — other admins included — across menus, projects list, dashboard, and direct project URLs, enforced server-side (not merely hidden links)
  - Turning on privacy doesn't lock out the person doing it (they're auto-added to the invited list)
  - The control states plainly this is Phase 1 — hidden in the web app, NOT yet confidential from the automation interface or the shared data files
  - The storage approach for true confidentiality is decided in an ADR before Phase 2 (ADR-035); Phase 2 itself is tracked separately (T-0310)
out_of_scope:
  - Phase 2 — true confidentiality from the automation (MCP) interface and at-rest files (tracked as T-0310, blocked on per-user MCP auth)
  - Hiding a private project from the person who operates the server / infrastructure
  - Encrypting or changing existing non-private projects
code_anchors:
  - path: web/app/_lib/access.ts
  - path: web/app/layout.tsx
  - path: web/app/_lib/pm.ts
  - path: mcp-server/src
  - path: schemas
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260608-1459
    model: claude-opus-4-8
    started: 2026-06-08T14:59:21Z
    status: completed
    ended: 2026-06-08T14:59:59Z
    summary: "You can now mark a project \"private\" so it's hidden from other admins, and choose exactly who's allowed in. On a private project's overview there's a privacy switch and an invite-by-email list; once it's private, everyone except the people you invite (plus the project's own team and stakeholders) stops seeing it — it disappears from their menus, project list, dashboard and search, and the server actually refuses to open its pages by direct link, not just hides the link. Crucially this now applies to other admins too, who could previously see every project. Turning privacy on automatically keeps you on the invite list so you can't lock yourself out. We were deliberately honest about the limits: the control says in plain words that this is \"hidden, not yet sealed\" — it hides the project inside this web app, but it is NOT yet confidential from the agent/automation interface or from anyone who can read the shared data files. Making it truly confidential (a separate secure store and per-person checks on the automation side) is a larger second phase, written up in a decision record (ADR-035) and tracked as its own ticket (T-0310), because it depends on giving each person their own identity on the automation interface. This phase delivers real \"declutter + privacy from other admins in the app\" today without over-promising secrecy."
    test_plan: |-
      Phase 1 (this run):
      1. As an admin, open a project's Overview → a "Private project" control sits under the team editor. Toggle "Make private".
      2. Add an invited email; confirm you (the toggler) remain able to see/manage it (auto-added to invited).
      3. Sign in (or preview-as) a DIFFERENT admin who isn't invited → the private project is absent from their sidebar, /projects, dashboard, and search, and visiting /projects/<slug> directly redirects to /me (server-refused, not just hidden).
      4. Add that other admin to the invite list → it reappears for them.
      5. A project's own team member / stakeholder still sees it when private.
      6. Make it public again → it returns to every admin's view.
      7. Read the in-control note — it states plainly this is web-only ("hidden, not yet sealed").
      NOT in this run (Phase 2 / T-0310): the project is still visible via the MCP tools and still readable in the raw data files — that's the deferred confidentiality work.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: ADR-035 records the phased decision + storage direction; schema descriptions document private/invited. Phase 2 split out as T-0310.
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-06-08T14:59:59Z
---

## Problem

Today an admin can see every project in the tool — there is no way to keep a project visible to just yourself, and a few people you choose, while hiding it from other admins. We want "private projects": a project that only its owner and explicitly-invited people can see, genuinely hidden from all other admins.

## What we decided we want

- A project can be tagged **private**, with a small list of people allowed in (the owner plus anyone they invite).
- A private project is **invisible and inaccessible to every other admin and non-invited person** — not merely hidden in the interface, but actually not reachable by the normal ways into the tool.
- The bar is confidential **from other admins and users of the tool** — not from whoever runs the server itself (that's us). Hiding it from the person who operates the infrastructure isn't achievable for a tool we host, and chasing it would balloon the work for no real gain.

## The honest reality (why this is bigger than a toggle)

The tool stores every project as plain-text files in one shared data repository, on a shared server, reachable through a single shared automation token. So "hide it in the interface" is easy, but real confidentiality from other admins means closing three doors:

1. **The interface.** Hide a private project everywhere (menus, lists, dashboard, search, activity) AND make the server refuse direct access for anyone not invited — not just hide the link.
2. **The automation interface.** It currently uses one shared token, so it can't tell users apart. Real privacy needs each person to have their own identity there, and every listing/lookup to respect who's allowed in. (This is the per-user access work previously parked — see ADR-012 and ADR-008.)
3. **The stored files.** Anyone who can copy the shared data repository can read every project's files today. A private project must be stored so that copying the shared repo does NOT reveal it — either kept in a separate, restricted place, or scrambled (encrypted) so the files are useless without a key the other admins don't have.

Door 3 is the crux and needs a deliberate design decision before we build.

## Suggested phased plan

- **Phase 1 — quick win ("hidden, not yet sealed").** Add the private tag + invited list, and hide private projects from other admins everywhere in the interface and in the automation project-list. Honest framing: good for decluttering and casual privacy, with a clear in-product note that it is hidden but not yet confidential.
- **Phase 2 — true confidentiality.** Per-user identity on the automation interface + a confidential store (separate location or encryption) so other admins genuinely can't reach it by any normal path. This is the real lock, and what was chosen.

## Open decision (needs an ADR first)

How private projects are stored so the shared data repository doesn't expose them: a separate restricted store, or encryption-at-rest with per-owner keys. This single decision shapes most of the build, so it should be settled in an ADR before Phase 2 work starts.

## Conversation

**2026-06-08 14:59 claude:** Run run-20260608-1459 completed — You can now mark a project "private" so it's hidden from other admins, and choose exactly who's allowed in. On a private project's overview there's a privacy switch and an invite-by-email list; once it's private, everyone except the people you invite (plus the project's own team and stakeholders) stops seeing it — it disappears from their menus, project list, dashboard and search, and the server actually refuses to open its pages by direct link, not just hides the link. Crucially this now applies to other admins too, who could previously see every project. Turning privacy on automatically keeps you on the invite list so you can't lock yourself out. We were deliberately honest about the limits: the control says in plain words that this is "hidden, not yet sealed" — it hides the project inside this web app, but it is NOT yet confidential from the agent/automation interface or from anyone who can read the shared data files. Making it truly confidential (a separate secure store and per-person checks on the automation side) is a larger second phase, written up in a decision record (ADR-035) and tracked as its own ticket (T-0310), because it depends on giving each person their own identity on the automation interface. This phase delivers real "declutter + privacy from other admins in the app" today without over-promising secrecy.
