---
id: T-0614
title: Speaker attribution in meeting transcripts (whisperx + pyannote diarization) — who said what, who decided
type: feature
state: review
created: 2026-07-17T16:31:45Z
updated: 2026-07-17T17:44:23Z
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
    status: completed
    ended: 2026-07-17T17:44:23Z
    summary: "Meeting transcripts now separate who is speaking, so decisions can be attributed to a person. Plain transcription turned every meeting into one undifferentiated wall of text — you couldn't tell whose words were whose, which matters when (say) Ben's call overrides someone else's. This adds local speaker separation (\"diarization\") on your Mac: the transcriber now labels each turn with a speaker, and consecutive lines from the same person are grouped. Proven on the real Ben→Rob phone call — the two voices came out cleanly separated across the whole conversation on noisy 8kHz phone audio. The meeting worker uses this automatically and quietly falls back to plain transcription if the speaker tools aren't available, so a transcript is never blocked. The agents' rulebook was also updated: when a transcript has speaker labels, map speakers to the real attendees from context and attribute each decision to a person (noting when one person's call overrides another), flagging any uncertain mapping rather than guessing. Follow-on T-0615 (shipped alongside) makes those speaker labels turn into actual names automatically. Everything runs offline on your Mac — no cloud, no cost, audio never leaves the machine."
    test_plan: "1. On the Mac: transcribe-speakers <any recording> → the transcript shows SPEAKER_00/SPEAKER_01 labels, grouped by speaker. (Verified live on the Ben→Rob call: clean two-speaker separation.) 2. transcribe-meetings drains the queue using the speaker pipeline; if the HF token / models aren't set up it falls back to plain transcription (no meeting blocked). 3. A future multi-person meeting recording (e.g. the accounts M-003, re-transcribing) shows 3 speakers. 4. Setup reproducibility: scripts/mac-transcription/README documents the whole install incl. the torch==2.5.1 pin. NOTE: the one-time gotcha was whisperx shipping torch 2.8 (breaks pyannote loading) — pinned to 2.5.1."
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: AGENTS.md §4 speaker-attribution rule + scripts/mac-transcription/README
labels:
  - meetings
  - transcription
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-17T17:44:23Z
version: 4
---

## Problem

Whisper transcripts are one undifferentiated stream — no speakers. Attribution matters for outcomes: e.g. Ben's decision overrides Austin's, so "who decided" must be recordable with confidence.

## Design (decided in chat)

- WhisperX (faster-whisper + pyannote diarization) on Austin's Mac, alongside the existing pipeline: new `transcribe-speakers <file>` produces "SPEAKER_00: …" labelled transcripts (consecutive same-speaker lines merged).
- `transcribe-meetings` prefers the speakers pipeline and FALLS BACK to the plain one if diarization is unavailable (missing HF token etc.) — a meeting transcript must never be blocked on the fancy path.
- HF token (needed for the pyannote model) lives in the macOS Keychain, read at runtime — no creds in files/env (secrets rule). One-time user step: HF account + accept the two pyannote model terms + store the token.
- AGENTS.md §4: when a transcript carries speaker labels, map speakers to the attendee list from conversational cues, attribute decisions/outcomes by person, and FLAG uncertain mappings instead of guessing silently.

## Conversation

**2026-07-17 17:44 claude-code:** Run run-20260717-1641 completed — Meeting transcripts now separate who is speaking, so decisions can be attributed to a person. Plain transcription turned every meeting into one undifferentiated wall of text — you couldn't tell whose words were whose, which matters when (say) Ben's call overrides someone else's. This adds local speaker separation ("diarization") on your Mac: the transcriber now labels each turn with a speaker, and consecutive lines from the same person are grouped. Proven on the real Ben→Rob phone call — the two voices came out cleanly separated across the whole conversation on noisy 8kHz phone audio. The meeting worker uses this automatically and quietly falls back to plain transcription if the speaker tools aren't available, so a transcript is never blocked. The agents' rulebook was also updated: when a transcript has speaker labels, map speakers to the real attendees from context and attribute each decision to a person (noting when one person's call overrides another), flagging any uncertain mapping rather than guessing. Follow-on T-0615 (shipped alongside) makes those speaker labels turn into actual names automatically. Everything runs offline on your Mac — no cloud, no cost, audio never leaves the machine.
