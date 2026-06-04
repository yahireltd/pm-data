---
id: ADR-013
slug: use-microsoft-graph-as-the-calendar-backend-real-invites-fre
title: Use Microsoft Graph as the calendar backend — real invites, free/busy clash check, and mailbox-scoped RSVP
state: proposed
decided: 2026-06-04
decided_by:
  - claude-code
project: pm-tool-self
supersedes: null
superseded_by: null
tickets:
  - T-0171
  - T-0173
---

## Context
pm-tool meetings were filesystem-only records: scheduling one did nothing in anyone's real calendar, there was no way to detect attendee conflicts, and no way to know who had actually accepted. We needed meetings to behave like real invites. Options considered: (a) generate .ics attachments and email them (no free/busy, no authoritative RSVP, fragile cross-client), (b) integrate a third-party scheduling SaaS, or (c) drive Microsoft Graph directly, reusing the cert-based app-only auth the email channel already had (creds runtime-loaded from AWS Secrets Manager, never on disk). The org already lives in M365.

## Decision
Drive Microsoft Graph for the whole meeting lifecycle. pushToCalendar creates/PATCHes a real /users/{from}/events event with stakeholders as attendees so Graph sends the invites; checkMeetingAvailability runs getSchedule for internal (same-tenant) attendees as a free/busy clash check and reads the event's own attendee list for authoritative accepted/declined/tentative/pending RSVP. Graph event ids are mailbox-scoped, so the meeting persists calendar.organizer_mailbox alongside graph_event_id; reads/updates resolve against that mailbox, and if the configured sender has since changed we create a fresh event (self-heal) instead of 404-ing on a PATCH. Calendars.ReadWrite is required (email needed only Mail.Send). Graph's dateTime quirk (bare local wall-clock, zone named separately — a Z is a 400) is normalized in graphDateTime().

## Consequences
Meetings now drop real invites and surface clash/RSVP data, reusing one auth path for mail and calendar. Cost: a hard dependency on M365/Graph and the broader Calendars.ReadWrite scope; free/busy and RSVP only work for tenant mailboxes (external attendees come back 'unknown'), and the meeting frontmatter now carries calendar plumbing (graph_event_id + organizer_mailbox) that must be kept consistent with the sending mailbox.