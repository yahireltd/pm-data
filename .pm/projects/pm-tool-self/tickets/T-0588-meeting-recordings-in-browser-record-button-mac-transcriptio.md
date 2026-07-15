---
id: T-0588
title: "Meeting recordings: in-browser Record button + Mac transcription worker + transcript on the meeting"
type: feature
state: in_progress
created: 2026-07-15T13:55:53Z
updated: 2026-07-15T13:56:21Z
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
  - A Record button on the meeting page captures mic audio in the browser and attaches the recording to the meeting (works in Chrome + Safari, clear permission/error states)
  - "pm_list_meeting_audio returns audio attachments lacking transcripts with presigned URLs; pm_attach_meeting_transcript adds the .txt attachment and a ## Transcript body section"
  - "Running transcribe-meetings on the Mac drains the queue end-to-end: recording → transcript visible on the meeting"
  - A transcribed recording no longer appears in the queue (no double-transcription)
  - "Summary/outcomes flow: an agent can read the transcript via pm_get_meeting and record outcomes — no cloud API involved"
out_of_scope: []
code_anchors:
  - path: web/app/_components/MeetingAttachments.tsx
    symbol: MeetingRecorder
  - path: mcp-server/src/tools/meeting-audio.ts
    symbol: pm_list_meeting_audio / pm_attach_meeting_transcript
  - path: scripts/transcribe-meetings.ts
    symbol: Mac worker
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260715-1356
    started: 2026-07-15T13:56:21Z
    status: in_progress
labels:
  - meetings
  - comms
attention: null
version: 3
---

## Problem

Meetings/calls get transcribed by hand off-tool (record somewhere, run whisper on the Mac, paste results). Austin wants the loop inside the tool: a Record button on the meeting that captures audio on the device and attaches it; then transcription lands the text back on the meeting so an agent can draft the summary/outcomes in-session.

## Design (decided via AskUserQuestion)

Transcription runs on Austin's Mac mini (Metal + large-v3 + VAD already set up) — the server has no GPU. Summaries stay agent-in-session (no API integration).

1. WEB — MeetingRecorder client component in the meeting Attachments card: mic capture via MediaRecorder (mp4/webm by browser), live timer, stop → uploads through the existing meeting-attachment path (S3, T-0334).
2. MCP — pm_list_meeting_audio: audio attachments across all meetings lacking a transcript, each with a presigned download URL; pm_attach_meeting_transcript: stores the transcript as a .txt attachment AND appends a "## Transcript" section to the meeting body (agent-readable via pm_get_meeting).
3. MAC — scripts/transcribe-meetings.ts (bun + MCP client): polls the queue, downloads, reuses the local `transcribe` pipeline (large-v3 + VAD anti-loop), posts transcripts back. Run manually or via launchd.
