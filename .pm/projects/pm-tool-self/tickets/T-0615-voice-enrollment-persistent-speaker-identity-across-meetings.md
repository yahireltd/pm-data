---
id: T-0615
title: "Voice enrollment: persistent speaker identity across meetings (name a voice once, auto-label forever)"
type: feature
state: triaged
created: 2026-07-17T17:04:40Z
updated: 2026-07-17T17:04:40Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: austin
assignee: null
acceptance_criteria:
  - A person can be enrolled once (from a recording where they're a known speaker) and stored in a local voiceprint library
  - transcribe-speakers auto-labels enrolled voices by NAME in future recordings; unknown voices stay SPEAKER_NN
  - Matches carry a confidence signal; low-confidence/unknown speakers are flagged, not silently guessed
  - The library is local-only (never committed / uploaded); documented as biometric-sensitive
  - "Verified: enrol Ben + Austin from one call, then a second recording auto-names them"
out_of_scope: []
code_anchors:
  - path: /opt/homebrew/bin/transcribe-speakers
    symbol: embedding match against library (Mac, outside repo)
  - path: /opt/homebrew/bin/voiceprint-enroll
    symbol: enrollment (new)
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels:
  - meetings
  - transcription
attention: null
version: 1
---

## Problem

Diarization (T-0614) labels speakers SPEAKER_00/01 per recording — the labels are arbitrary and don't persist, so a person must be re-identified every meeting. Austin wants: name a voice once, then it's auto-recognised in future recordings.

## Design (local, free, builds on the T-0614 pyannote setup)

- A voiceprint library on the Mac (~/Models/whisper/voiceprints/, JSON of {name: [embeddings]}). Not in the repo.
- transcribe-speakers extracts a pyannote embedding per detected speaker; matches each against the library by cosine similarity above a threshold → labels with the NAME; unmatched stay SPEAKER_NN.
- Enrollment: a `voiceprint-enroll <name> <audio-or-meeting>` step (or "SPEAKER_00 is Ben" confirmation on review) averages that speaker's segments into the library; corrections refine it.
- Confidence: auto-labels carry a confidence marker; low-confidence + unknown speakers are flagged for the human, never guessed silently (mirrors the AGENTS.md §4 rule).
- Privacy note: voiceprints are biometric-adjacent — local-only, never leave the Mac, documented as such.
