---
id: T-0588
title: "Meeting recordings: in-browser Record button + Mac transcription worker + transcript on the meeting"
type: feature
state: review
created: 2026-07-15T13:55:53Z
updated: 2026-07-15T14:05:31Z
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
    status: completed
    ended: 2026-07-15T14:05:31Z
    summary: "Meetings can now be recorded in the tool, and the recording turns into a transcript on the meeting — without any cloud transcription service. On a meeting page there's now a \"Record meeting\" button next to Attachments: it records through the device's microphone with a live timer, and \"Stop & attach\" saves the audio onto the meeting like any other file. Because the server has no graphics chip (transcribing there would take hours), the heavy lifting happens on Austin's Mac: a new one-command worker, transcribe-meetings, fetches any recordings that don't have transcripts yet, transcribes them locally with the best-quality Whisper set-up (including the anti-repetition fixes), and posts each transcript back onto its meeting — both as a text file attachment and as a readable \"Transcript\" section. From there the existing flow takes over: an agent reads the transcript straight off the meeting and drafts the summary and outcomes for review, keeping everything free and private. Already-transcribed recordings drop out of the queue automatically, so the worker can run on a schedule safely. If we did nothing, recordings would keep living outside the tool with hand-carried transcripts. Benefit: record in the meeting, run one command, and the minutes material is on the meeting."
    test_plan: "1. Deploy, then open a meeting (e.g. M-010). In the Attachments area click \"Record meeting\" — the browser asks for mic permission; a red dot + timer appears. Speak a few sentences, click \"Stop & attach\" → an audio file (meeting-recording-….m4a/webm) appears in Attachments and plays back via its link. 2. Deny mic permission once → a clear error message, no crash. 3. On the Mac, run `transcribe-meetings` → it reports the recording, transcribes, and \"transcript attached\"; refresh the meeting: a -transcript.txt attachment AND a \"## Transcript — <file>\" section in Notes/body are there. 4. Run `transcribe-meetings` again → \"Nothing to transcribe\" (no double-transcription). 5. Ask an agent: \"read M-010's transcript and draft outcomes\" → pm_get_meeting returns the body incl. transcript; outcomes recordable per the existing flow. 6. Safari AND Chrome record OK (mp4 vs webm). 7. Regression: normal file upload/view/remove on meeting attachments unchanged; ticket attachments unchanged. NOTE: my MCP session predates these tools — the worker uses its own fresh connection so it works right after deploy; for ME to call the new tools directly, restart the session."
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - meetings
  - comms
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-15T14:05:31Z
version: 4
---

## Problem

Meetings/calls get transcribed by hand off-tool (record somewhere, run whisper on the Mac, paste results). Austin wants the loop inside the tool: a Record button on the meeting that captures audio on the device and attaches it; then transcription lands the text back on the meeting so an agent can draft the summary/outcomes in-session.

## Design (decided via AskUserQuestion)

Transcription runs on Austin's Mac mini (Metal + large-v3 + VAD already set up) — the server has no GPU. Summaries stay agent-in-session (no API integration).

1. WEB — MeetingRecorder client component in the meeting Attachments card: mic capture via MediaRecorder (mp4/webm by browser), live timer, stop → uploads through the existing meeting-attachment path (S3, T-0334).
2. MCP — pm_list_meeting_audio: audio attachments across all meetings lacking a transcript, each with a presigned download URL; pm_attach_meeting_transcript: stores the transcript as a .txt attachment AND appends a "## Transcript" section to the meeting body (agent-readable via pm_get_meeting).
3. MAC — scripts/transcribe-meetings.ts (bun + MCP client): polls the queue, downloads, reuses the local `transcribe` pipeline (large-v3 + VAD anti-loop), posts transcripts back. Run manually or via launchd.

## Conversation

**2026-07-15 14:05 claude-code:** Run run-20260715-1356 completed — Meetings can now be recorded in the tool, and the recording turns into a transcript on the meeting — without any cloud transcription service. On a meeting page there's now a "Record meeting" button next to Attachments: it records through the device's microphone with a live timer, and "Stop & attach" saves the audio onto the meeting like any other file. Because the server has no graphics chip (transcribing there would take hours), the heavy lifting happens on Austin's Mac: a new one-command worker, transcribe-meetings, fetches any recordings that don't have transcripts yet, transcribes them locally with the best-quality Whisper set-up (including the anti-repetition fixes), and posts each transcript back onto its meeting — both as a text file attachment and as a readable "Transcript" section. From there the existing flow takes over: an agent reads the transcript straight off the meeting and drafts the summary and outcomes for review, keeping everything free and private. Already-transcribed recordings drop out of the queue automatically, so the worker can run on a schedule safely. If we did nothing, recordings would keep living outside the tool with hand-carried transcripts. Benefit: record in the meeting, run one command, and the minutes material is on the meeting.
