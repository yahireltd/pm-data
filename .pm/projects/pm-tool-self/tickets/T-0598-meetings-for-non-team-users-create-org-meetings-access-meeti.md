---
id: T-0598
title: "Meetings for non-team users: create org meetings + access meetings you're invited to + see them on /me"
type: feature
state: review
created: 2026-07-16T14:25:40Z
updated: 2026-07-16T14:32:55Z
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
  - A non-team signed-in user (e.g. Effie) can open /meetings, see org meetings, and create one
  - Org-meeting operations (agenda/outcomes/stakeholders/reminders/notes/state) work for any signed-in user; project-meeting edits remain team-only; delete + held-revert remain admin-only
  - A user on a project meeting's stakeholder list can open that meeting's page even without project membership
  - /me shows a 'Your meetings' section with upcoming meetings the user is invited to (org + project), each linking through
  - Members/stakeholders see Meetings in the sidebar; admins' experience unchanged
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
    ended: 2026-07-16T14:32:55Z
    summary: "People like Effie and Jhuztine can now actually use meetings. Three walls came down. First, org-wide meetings are now the company's shared space: anyone signed in can open the Meetings page, read any org meeting, create one, and run it — agenda, outcomes, attendees, reminders and notes — without needing to be on a project team. Second, being invited to a PROJECT meeting now means something: if your email is on that meeting's attendee list, you can open that meeting's page even when you're not on the project — exactly Effie's situation at the accounts meeting. Third, discovery: the \"My work\" page now has a \"Your meetings\" section listing upcoming meetings you're invited to (org or project), so signing in no longer shows \"nothing on your plate\" when you have a meeting to attend; members and stakeholders also get the Meetings link in their menu. Guard-rails kept: editing a project meeting still needs the project team, and deleting a meeting or reverting a mis-clicked \"held\" stays admin-only. If we did nothing, every non-team attendee would keep bouncing off the tool at exactly the moment we ask them to use it. Benefit: invite someone to a meeting and the tool actually works for them."
    test_plan: '1. After deploy, preview-as (or sign in as) Effie: /me now shows a "Your meetings" section listing M-003; the sidebar shows Meetings. 2. As Effie, open the M-003 link → the meeting page loads (previously bounced to /me). Confirm she can READ agenda/outcomes/transcript; editing controls that touch the project should still refuse politely (team-only). 3. As Effie, open Meetings in the nav → the org meetings index loads; create a test org meeting → works; add an agenda item + outcome on it → works (org meetings are open to signed-in users). 4. Delete that test org meeting as Effie → refused (admin-only). 5. As a signed-out visitor: /meetings redirects to sign-in (nothing public). 6. Admin regression: your own /meetings, project meetings, and gates behave exactly as before. 7. Cross-check the invite grant is per-meeting: as Effie, try another accounts-integrity page (e.g. the project board) → still bounced to /me.'
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - access
  - dogfood-finding
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-16T14:32:55Z
version: 4
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
