---
id: TS-004
slug: microsoft-graph-comms-calendar-for-meetings
title: Microsoft Graph comms + calendar for meetings
project: pm-tool-self
created: 2026-06-03T22:23:11Z
updated: 2026-06-03T22:23:11Z
decisions:
  - dispatch(event) server-action hook fires meeting_scheduled/held/outcome_recorded fire-and-forget, so a comms failure never breaks the action (ADR-005, 4deedea).
  - "Fixed the recipient resolver: it expected the legacy stakeholder shape (channels[]+contact{}) but every face now writes single channel+string contact, so it only ever picked the log channel — email silently never sent for tickets AND meetings (4deedea)."
  - Real Graph calendar events with the meeting's email attendees; store calendar.graph_event_id so a re-push PATCHes instead of duplicating (f0ed6ff).
  - "Graph 400 fix: event.start/end.dateTime must be bare local wall-clock with no trailing Z when timeZone is set separately; graphDateTime() normalises to UTC wall-clock (5f7de2f)."
  - Free/busy clash check via Graph getSchedule for internal attendees; merge RSVP so a clash only counts when busy AND not accepted; external attendees 'not checked' (066096b, 70debe7).
  - "Mailbox-scoped event self-heal: changing the sender (it@→support@) orphaned events (404); store calendar.organizer_mailbox and re-create rather than 404 a PATCH (70debe7)."
actions:
  - text: Graph comms + calendar shipped (T-0170/0171/0173 done).
    ticket: T-0171
side_quests:
  - Meeting attendees default into meeting comms (empty notify_on now includes meeting lifecycle events) + a manual 'Send reminder' button (9941fa9).
problem: "Meetings carried stakeholders but sent nothing: no emails on lifecycle events and 'Push to calendar' was a stub."
success_criterion: Scheduling/holding/outcome emails opted-in stakeholders and pushes a real Graph calendar event; internal attendees get a free/busy clash check.
phase: build
---

# TS-004: Microsoft Graph comms + calendar for meetings

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
