---
id: T-0259
title: Admin / user management
type: feature
state: in_progress
priority: p2
created: 2026-06-05T22:05:52Z
updated: 2026-06-08T12:24:29Z
project: pm-tool-self
section: null
parent: null
children: []
order: 81920
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - user Rbac managment
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260605-2207
    model: claude
    started: 2026-06-05T22:07:12Z
    status: in_progress
    summary: Claimed via web UI
    progress:
      - at: 2026-06-08T12:24:29Z
        note: |-
          First slice shipped (commit fb0e1fc on master). Built an admin-only Settings → Users & roles console covering global admins + per-project team membership (scope agreed with Austin; stakeholders left to their existing per-project editor).

          What's in:
          - requireAdmin() guard added to web/app/_lib/access.ts — admin-only, closing the privilege-escalation gap where requireTeam() (no slug) would let any project member manage the admin list.
          - web/app/_actions/settings.ts — addAdmin/removeAdmin/listUsers, writing access.admins in .pm/config.yml. Last-admin removal blocked (no lockout); adds are idempotent + email-validated.
          - web/app/_components/AdminsEditor.tsx — client editor mirroring ProjectMembersEditor; remove-self confirms first.
          - web/app/settings/page.tsx — admins editor + one ProjectMembersEditor per project (reuses existing addProjectMember/removeProjectMember) + read-only "everyone with access" summary.
          - Sidebar/SidebarNav — admin-only "Settings" link + icon.
          - Per-view help doc (web/app/help/content/settings.ts) + /help registry + /settings path mapping.

          Typecheck clean (only the unrelated pre-existing @types/hast error remains). Not yet deployed; no visual verification because local has no .pm data — verify on live after deploy.
    test_plan: |-
      After deploying this commit to live:

      1. Sign in as an admin. Confirm a new **Settings** item appears at the bottom of the left sidebar; open it (`/settings`).
      2. **Administrators**: add a test email → it appears in the list and the new admin gains access. Remove it via the ✕ on hover.
      3. **Last-admin guard**: try removing the only/last admin → it should refuse with "Cannot remove the last administrator." Removing yourself should pop a confirm first.
      4. **Project membership**: add/remove a member on a project card → reflected on that project, and in the "Everyone with access" summary.
      5. **Access control**: sign in (or preview-as) a non-admin → `/settings` should redirect to /me, and the Settings link should not appear.
      6. Click the "?" on the Settings page → the Users & roles help drawer opens; it's also browsable at /help.
labels: []
attention: null
---

We should have a way to update users and admins (any logged in admin can do this)
