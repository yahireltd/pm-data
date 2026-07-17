---
id: T-0614
title: Speaker attribution in meeting transcripts (whisperx + pyannote diarization) — who said what, who decided
type: feature
state: in_progress
created: 2026-07-17T16:31:45Z
updated: 2026-07-17T16:41:07Z
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
  - transcribe-speakers <audio> produces a transcript with SPEAKER_NN labels (consecutive lines merged per speaker)
  - transcribe-meetings uses speaker labels for meeting recordings, falling back to the plain pipeline when diarization is unavailable
  - HF token is read from the macOS Keychain (or HF_TOKEN env) at runtime — never stored in repo/files
  - AGENTS.md §4 tells agents to attribute outcomes by speaker (mapped to attendees from context) and flag uncertain attributions
  - "Verified on a real multi-speaker recording (e.g. the Ben→Rob call): labels are usable and attribution lands on outcomes"
out_of_scope: []
code_anchors:
  - path: mcp-server/scripts/transcribe-meetings.ts
    symbol: speakers-first with fallback
  - path: AGENTS.md
    symbol: §4 speaker attribution rule
  - path: /opt/homebrew/bin/transcribe-speakers
    symbol: Mac-side (outside repo)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-1641
    started: 2026-07-17T16:41:07Z
    status: in_progress
labels:
  - meetings
  - transcription
attention: null
version: 3
---

## Problem

Whisper transcripts are one undifferentiated stream — no speakers. Attribution matters for outcomes: e.g. Ben's decision overrides Austin's, so "who decided" must be recordable with confidence.

## Design (decided in chat)

- WhisperX (faster-whisper + pyannote diarization) on Austin's Mac, alongside the existing pipeline: new `transcribe-speakers <file>` produces "SPEAKER_00: …" labelled transcripts (consecutive same-speaker lines merged).
- `transcribe-meetings` prefers the speakers pipeline and FALLS BACK to the plain one if diarization is unavailable (missing HF token etc.) — a meeting transcript must never be blocked on the fancy path.
- HF token (needed for the pyannote model) lives in the macOS Keychain, read at runtime — no creds in files/env (secrets rule). One-time user step: HF account + accept the two pyannote model terms + store the token.
- AGENTS.md §4: when a transcript carries speaker labels, map speakers to the attendee list from conversational cues, attribute decisions/outcomes by person, and FLAG uncertain mappings instead of guessing silently.
