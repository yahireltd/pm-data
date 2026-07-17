---
id: T-0623
title: Productionize per-segment ECAPA speaker separation + spoken self-introduction
type: feature
state: triaged
created: 2026-07-17T22:47:24Z
updated: 2026-07-17T22:47:24Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee: null
acceptance_criteria:
  - transcribe-speakers uses ECAPA per-segment labelling; a merged sound-alike cluster is split into the correct people
  - Spoken self-introduction is parsed and takes priority over voiceprint matching
  - Everyone re-enrolled on ECAPA from clean sources; the contaminated Zac print is replaced
  - ECAPA open-set threshold calibrated; unenrolled voices correctly stay SPEAKER_NN
  - Proven on M-015 (Ben separated from Zac) and ideally a second recording where both brothers clearly speak
out_of_scope: []
code_anchors:
  - path: scripts/mac-transcription/voiceprint_ecapa.py
    note: new ECAPA module — enroll/match/label modes, cluster-aware per-segment split + windowing + de-flicker
  - path: scripts/mac-transcription/transcribe-speakers
    note: wire ECAPA `label` mode into the render; currently applies per-cluster renames
  - path: scripts/mac-transcription/voiceprint.py
    note: old pyannote embedding — ECAPA supersedes it for accuracy/sibling separation
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 1
---

## Problem
The live speaker stack (voiceprint.py + pyannote/embedding) can't reliably attribute speakers across recordings (cross-condition ~0.60) and — critically — pyannote's diarizer MERGES sound-alike voices into one cluster (Ben+Zac in M-015), so per-cluster labelling can't separate them at all.

## Proven 2026-07-17 (the tech spike, largely de-risked)
ECAPA-TDNN (speechbrain/spkrec-ecapa-voxceleb — already installed in wxenv, torch stays pinned 2.5.1) separates them: same-voice same-recording ~0.90, cross-recording ~0.60, sound-alike siblings ~0.56, strangers ~0.10. Seeded from Ben's "Brian Badonde" anchor, we pulled Ben back out of the merged M-015 block. Calibration: Austin cross-recording 0.599; Ben/Zac 0.56.

## Built (WIP, NOT yet wired into the live pipeline)
`voiceprint_ecapa.py` (on the Mac at ~/Models/whisper/, reference copy committed to scripts/mac-transcription/). Modes: enroll / match (per-cluster) / label (cluster-aware per-segment — a cluster dominated by one voice is labelled wholesale; a cluster with 2+ strong identities is split segment-by-segment; >=1.5s windows; de-flicker smoothing). Separate `library_ecapa.json` (model-specific — do not mix with the pyannote library).

## Remaining
- Integrate spoken SELF-INTRODUCTION ("this is Ben") as the TOP-priority identity source (parser started — `import re` added, parse_intros not written yet). Gives clean in-condition anchors that dissolve the cross-condition/sibling overlap and auto-enrich enrolment.
- Re-enrol everyone on ECAPA from clean sources: Ben/Rob from /tmp/ben-rob-call-16k.wav, Austin/Effie/Jhuztine from M-003. NOTE: the current pyannote-lib "Zac" print is CONTAMINATED (a Ben+Zac blend from M-015 SPEAKER_02) — re-enrol Zac cleanly.
- Calibrate the ECAPA open-set threshold (~0.55–0.75) + margin; lean on multi-condition enrolment to lift true matches clear of the sibling band.
- Wire `label` mode into transcribe-speakers so the render uses per-segment names, not per-cluster.
- Update project_transcription_stack memory + commit final reference copies.

This is PP-012's ("The Meet Grinder") Tech-spike milestone.