---
id: T-0598
title: "Meetings for non-team users: create org meetings + access meetings you're invited to + see them on /me"
type: feature
state: done
created: 2026-07-16T14:25:40Z
updated: 2026-07-17T19:57:20Z
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
  - "[x] A non-team signed-in user (e.g. Effie) can open /meetings, see org meetings, and create one"
  - "[x] Org-meeting operations (agenda/outcomes/stakeholders/reminders/notes/state) work for any signed-in user; project-meeting edits remain team-only; delete + held-revert remain admin-only"
  - "[x] A user on a project meeting's stakeholder list can open that meeting's page even without project membership"
  - "[x] /me shows a 'Your meetings' section with upcoming meetings the user is invited to (org + project), each linking through"
  - "[x] Members/stakeholders see Meetings in the sidebar; admins' experience unchanged"
out_of_scope: []
code_anchors:
  - path: web/app/_lib/access.ts
    symbol: requireSignedIn / isMeetingInvitee
  - path: web/app/_actions/meetings.ts
    symbol: requireMeetingAccess
  - path: web/app/layout.tsx
    symbol: meetings gate exceptions
  - path: web/app/me/page.tsx
    symbol: Your meetings section
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260716-1426
    started: 2026-07-16T14:26:04Z
    status: completed
    ended: 2026-07-16T14:56:58Z
    summary: "Meetings now work for non-team people — scoped to what's theirs. Round 1 opened the doors: anyone signed in can create and run org-wide meetings; being on a project meeting's attendee list grants access to that meeting page; \"My work\" lists the meetings you're invited to; members and stakeholders get the Meetings menu link. Round 2 (from Austin previewing as Effie) tightened what was over-opened: the Meetings page no longer shows every org meeting to everyone — non-admins see only meetings they're part of (invited org meetings, their own projects' meetings, and project meetings they're invited to), while admins keep the full view. Creating a meeting now automatically puts YOU on it as Organizer, so your own meeting can't vanish from your scoped view — and organizers receiving reminders is what people expect anyway. And the PM/Stakeholder view switcher — an admin preview tool that was showing for everyone — is now admin-only. Edits to project meetings still need the project team; deleting and un-holding stay admin-only. Benefit: invite Effie to a meeting and the tool shows her exactly her meetings — nothing more, nothing missing."
    test_plan: "1. After deploy, preview-as Effie: the sidebar has NO PM/Stakeholder toggle; Meetings shows ONLY M-003 (+ anything else she's invited to) — the IT Meeting / PM Tool / Logistics org meetings are gone. 2. As Effie, create a test org meeting → it appears in her list (she's auto-added as Organizer) and she can edit its agenda/outcomes; delete stays refused. 3. Her /me \"Your meetings\" lists M-003 and her new meeting. 4. As YOU (admin): Meetings shows everything as before, toggle present, subtitle unchanged. 5. New meetings you create now include you as an Organizer stakeholder — check a fresh one (you'll also get its reminder emails, which is intended; shout if you'd rather not). 6. Invite-scoping edge: add Effie to a meeting on a project she can't see → it appears in her Meetings list and opens; the project's other pages stay blocked."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - access
  - dogfood-finding
attention: null
version: 13
---

## Problem

At the M-003 accounts meeting, Effie signed in and saw "nothing on your plate" — she couldn't reach the meeting she was invited to. Three walls: /meetings (org) is admin-only; creating any meeting requires team membership; and being on a MEETING's stakeholder list grants nothing — only project-level membership counts. Austin's model: anyone signed in can create/use org-wide meetings, and being on a meeting's stakeholder list grants access to THAT meeting.

## Design

- Org-wide meetings become the shared space: /meetings + /meetings/[id] open to every signed-in user; creating and operating org meetings (agenda, outcomes, stakeholders, reminders, notes) requires being signed in, not team. Pre-project meeting creation stays team-gated; delete + held-revert stay admin.
- Meeting-level invites: a signed-in user whose email is on a project meeting's stakeholder list can VIEW that meeting page (edits remain team-gated server-side).
- Discovery: /me gains a "Your meetings" section — upcoming meetings (org + any project) where the user is on the stakeholder list, linking to each.
- Sidebar: members/stakeholders get the Meetings link.

## Conversation

**2026-07-16 14:32 claude-code:** Run run-20260716-1426 completed — People like Effie and Jhuztine can now actually use meetings. Three walls came down. First, org-wide meetings are now the company's shared space: anyone signed in can open the Meetings page, read any org meeting, create one, and run it — agenda, outcomes, attendees, reminders and notes — without needing to be on a project team. Second, being invited to a PROJECT meeting now means something: if your email is on that meeting's attendee list, you can open that meeting's page even when you're not on the project — exactly Effie's situation at the accounts meeting. Third, discovery: the "My work" page now has a "Your meetings" section listing upcoming meetings you're invited to (org or project), so signing in no longer shows "nothing on your plate" when you have a meeting to attend; members and stakeholders also get the Meetings link in their menu. Guard-rails kept: editing a project meeting still needs the project team, and deleting a meeting or reverting a mis-clicked "held" stays admin-only. If we did nothing, every non-team attendee would keep bouncing off the tool at exactly the moment we ask them to use it. Benefit: invite someone to a meeting and the tool actually works for them.

**2026-07-16 14:57 claude-code:** Round 2 (commit e14cb5b), from Austin previewing as Effie: (1) the Meetings page was showing EVERY org meeting to any signed-in user — it's now scoped for non-admins to just the meetings they're part of (invited org meetings + their projects' + invite-only project meetings); admins keep the full calendar. (2) Creating a meeting now auto-adds the creator as an Organizer stakeholder — otherwise a non-admin's own new meeting would disappear from their scoped view. (3) The PM/Stakeholder view switcher was rendering for everyone — it's an admin preview affordance and is now admin-only. Re-test in preview-as-Effie: no toggle, and Meetings shows only M-003 + anything she's invited to.

---

**2026-07-17 19:57 — you**

Done and tested
