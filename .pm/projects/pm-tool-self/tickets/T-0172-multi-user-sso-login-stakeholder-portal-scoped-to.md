---
id: T-0172
title: "Multi-user: SSO login + stakeholder portal scoped to your projects"
type: feature
state: in_progress
priority: p2
created: 2026-06-02T18:59:20Z
updated: 2026-06-03T15:23:01Z
project: pm-tool-self
section: null
parent: null
children: []
order: 44032
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] Microsoft SSO (Entra) login; getCurrentIdentity() resolves the signed-in person"
  - "[x] A logged-in stakeholder sees only the projects they are a stakeholder on (scoped by email)"
  - Stakeholders can read + comment but cannot change PM-owned items (authz enforced)
  - Hosted on AWS, secrets from AWS Secrets Manager
out_of_scope: []
code_anchors:
  - path: web/app/_lib/access.ts
  - path: web/app/_lib/identity.ts
  - path: web/app/me/page.tsx
  - path: docs/multi-user-setup.md
  - path: web/auth.ts
  - path: web/app/api/auth/[...nextauth]/route.ts
  - path: web/app/_components/AuthButtons.tsx
  - path: web/middleware.ts
  - path: web/app/layout.tsx
  - path: web/app/_lib/access.ts
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
---

## Problem

_What's wrong / what's needed?_

## Context

_Background, screenshots, customer quotes._

## Design notes

_How we'll fix it._

## Foundation shipped (2026-06-02)

Built the identity/scoping/authz layer, decoupled from SSO so it works now:
- web/app/_lib/access.ts — stakeholderProjects(email), isStakeholderOnProject, capability policy (read+comment yes, edit_pm_owned no).
- web/app/_lib/identity.ts — getCurrentIdentity() seam (cookie today; swap to the Entra session — one-line change).
- web/app/me/page.tsx — scoped portal: a person sees only their projects, read+comment framing. Testable via /me?as=<email>.

## Remaining (pick up tomorrow — needs Azure)

Microsoft SSO (Entra) + NextAuth wiring, authz enforcement on PM-owned actions, and AWS hosting. Step-by-step runbook: docs/multi-user-setup.md.

## SSO scaffolded (2026-06-03)

Microsoft sign-in is wired via Auth.js v5, non-breaking (app runs without login until creds exist):
- web/auth.ts (MicrosoftEntraID provider; creds from env or AWS Secrets Manager `pmToolAuth`; exports auth/handlers/signIn/signOut + ssoConfigured), the /api/auth/[...nextauth] route, sign in/out buttons, sidebar 'Your projects' link.
- getCurrentIdentity() reads the session first, falls back to the cookie / ?as= for dev.

Remaining: (1) Austin creates the Azure web redirect URI + client secret + the pmToolAuth secret (runbook step 2); (2) enforce-login middleware behind PM_REQUIRE_AUTH; (3) authz enforcement on PM-owned server actions; (4) AWS hosting.

## Access control enforced (2026-06-03)

Whole app behind login + role gating:
- Login wall (web/middleware.ts): unauthenticated → redirected to /me (sign in). /me + /api/auth public.
- Roles from .pm/config.yml `access.team` (PM/Dev emails). Team = full access; everyone else who signs in = stakeholder.
- Root layout (server) is the authoritative gate: stakeholders may only open /me + projects they're a stakeholder on; all PM routes (tickets/inbox/dashboard/…) redirect them to /me. Sidebar is role-aware.

Remaining hardening: per-server-action authz (assertStakeholderCan on PM-owned mutations) so a stakeholder can't mutate via a direct action call; unify the top-right identity with the SSO session; AWS hosting.

## Hardening + identity unified (2026-06-03)

- Signed-out = clean login shell (root layout renders no sidebar/nav/counts/project names before auth; only /me).
- Per-action authz: requireTeam() guards all 51 mutating actions in projects.ts + meetings.ts (stakeholders read+comment only; comments live in conversation.ts and stay open).
- Identity unified: TopNav shows the SSO user + Sign out, and syncs pm_author to the signed-in identity (drives comments/claims/author displays).

Remaining: per-action gating on the other action files (tickets/decisions/sprints — currently protected by route gating since stakeholders can't reach those pages); hide PM controls on stakeholder-visible meeting pages; AWS hosting.

## Per-project teams (2026-06-03)

Three-tier RBAC, ready for multi-project/multi-user:
- admins (.pm/config.yml access.admins) — full access to every project + the global aggregate pages.
- members (project.team — new schema/type field) — full access to THAT project; they work inside it.
- stakeholders (project.stakeholders) — read + comment on that project.
- /me is everyone's home (accessible projects + access level); admins get the global nav, others a stripped sidebar. Global pages are admin-only; project pages gate on per-project access. Mutations require admin-or-member (requireTeam → hasTeamAccess).

Remaining: a UI to manage project.team (today it's a frontmatter field); per-project (not coarse) mutation gating to close the cross-project direct-POST gap; hide PM controls on stakeholder-visible pages; AWS hosting.

## Members UI + stakeholder view (2026-06-03)

- Project-members UI: ProjectMembersEditor on the Overview tab (add/remove devs/PMs by email → project.team), backed by addProjectMember/removeProjectMember (team-gated). No more YAML editing.
- Stakeholder view: project pages now read-only for stakeholders by REAL access (not just ?view=), and ProjectTabs hides the execution tabs (Board/List/Backlog/Sprints/Meetings/Decisions/Tech-sessions/Time-plan/Hypercare) — stakeholders see Phase/Charter/Milestones/Status/Overview, read-only.

Remaining: per-project (not coarse) mutation gating (#2 — close the cross-project direct-POST gap); AWS hosting.

## Preview as (2026-06-03)

Admin-only impersonation to verify what a member/stakeholder sees. getEffectiveIdentity honours a pm_preview_as cookie ONLY when the real user is an admin (never escalates). The whole access layer (layout gating, sidebar, /me, read-only, tabs, requireTeam→mutations blocked) uses the effective identity. PreviewAsControl on the project Overview (pick a member/stakeholder) + a PreviewBanner with Exit. TopNav also hides New-ticket/quick-add for non-admins.

## Per-project mutation gating (2026-06-03)

Closed the cross-project gap: requireTeam(slug) now checks canMutateProject (admin OR member of THAT project), not coarse team access. Every mutating action in projects.ts + meetings.ts threads its project slug (slug / projectSlug / input.project / found.project for meeting-stakeholder actions). Reads (getProjectForUI) ungated; createProject stays coarse (team). A member of A can no longer mutate B via a direct POST.

RBAC epic essentially complete. Remaining: AWS hosting (deploy).
