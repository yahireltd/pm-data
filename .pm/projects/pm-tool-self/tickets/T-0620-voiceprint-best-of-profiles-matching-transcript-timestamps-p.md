---
id: T-0620
title: Voiceprint best-of-profiles matching + transcript timestamps (proven on the "brothers" sound-alike test)
type: feature
state: review
created: 2026-07-17T21:10:06Z
updated: 2026-07-17T21:11:19Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p2
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - match scores each speaker against every enrolment per name and takes the best (not the mean)
  - transcribe-speakers output prefixes each turn with [MM:SS] from the segment start
  - Austin's cross-recording M-015 score rises above the 0.55 threshold after rich re-enrolment (measured 0.709)
  - "No false positive: unenrolled/other voices are not named; sound-alike Zac resolves to Zac once enrolled"
  - Reference copies in scripts/mac-transcription/ match the deployed /opt/homebrew/bin + ~/Models/whisper versions
out_of_scope: []
code_anchors:
  - path: scripts/mac-transcription/voiceprint.py
    symbol: cmd_match
    note: "best-of-profiles: refs as list per name, max(cosine) over profiles"
  - path: scripts/mac-transcription/transcribe-speakers
    symbol: render heredoc
    note: ts() helper + [MM:SS] prefix per merged turn
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260717-2110
    model: claude-opus-4-8
    started: 2026-07-17T21:10:47Z
    status: completed
    ended: 2026-07-17T21:11:19Z
    summary: "We made the meeting voice-recognition noticeably more reliable and made transcripts easier to use. Two changes: (1) when the system decides who a voice belongs to, it now compares against each saved sample of a person and picks the best match, instead of blending all their samples into one average — averaging a phone recording with an in-room recording used to produce a blurry reference that matched neither well. (2) Every line of a transcript now shows a timestamp, so you can jump to that moment in the recording and cite when something was said. We proved it on a real meeting with two brothers who sound alike (Austin and Zac, Zac being unenrolled): beforehand Austin's own voice was only weakly recognised (and the unenrolled brother came dangerously close to being mistaken for Ben); after re-recording Austin's voice profile properly, his recognition score more than doubled and crossed the confidence line, while nobody was mislabelled. Without this, the feature would keep failing to recognise people across different recordings and — worse — risk putting the wrong person's name against a decision, which is exactly what the \"who decided what\" feature must never do. The benefit: reliable, safe attribution that improves as we add more voice samples."
    test_plan: |-
      1. Timestamps: on the Mac run `transcribe-speakers <any short audio>` and confirm every turn is prefixed `[MM:SS]` (or `[H:MM:SS]` past an hour).
      2. Naming still works (regression): `cd ~/Models/whisper && HF_TOKEN=$(security find-generic-password -s huggingface -a whisperx -w) ./wxenv/bin/python voiceprint.py match /tmp/m015-jokey.wav /tmp/m015-jokey.diarize.json` → expect SPEAKER_01 → Austin (~0.709) and SPEAKER_02 → Zac (~1.0), both named, no other voice named.
      3. Best-of-profiles: Austin has 2 saved profiles (thin M-013 + rich M-003); confirm his match uses the better one (score ~0.709, not the ~0.5 average). Add a deliberately-bad 2nd profile to someone and confirm it does NOT drag their score down.
      4. Open-set safety: confirm an unenrolled voice still returns SPEAKER_NN (name null) — the 0.55 gate must stay; do NOT lower it.
      5. Cross-impact — the overnight worker: `transcribe-meetings` calls `transcribe-speakers`, so drain the queue on one real recording and confirm the attached transcript carries names + `[MM:SS]` and the meeting dequeues.
      6. Reference copies: `diff scripts/mac-transcription/voiceprint.py ~/Models/whisper/voiceprint.py` and `diff scripts/mac-transcription/transcribe-speakers /opt/homebrew/bin/transcribe-speakers` → both identical.
      Note: Zac's ~1.0 is same-clip (enrolled+tested on M-015), so it proves the mechanism, not generalisation — a future Zac recording is the true test. Ben is still phone-only; needs an in-room sample.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-17T21:11:19Z
version: 4
---

## Problem
Speaker recognition was unreliable in two ways: (1) thin/single-condition voiceprints didn't match across recordings (a 3s enrolment or a phone-only print scored too low on a different recording), and (2) transcripts had no timestamps, so you couldn't navigate the audio or cite when something was said.

## Context — the "brothers test" (M-015)
Tested on a real meeting with Austin + Zac, where Zac is Ben's sound-alike brother and is NOT enrolled.
- Before: Austin (enrolled thin, 3s from M-013) scored only **0.315** on M-015 → rejected. Unenrolled Zac scored **0.508 vs Ben's phone print** — a near-miss just under the 0.55 gate.
- Key insight: a thin true-match (0.315) scored BELOW an imposter sibling (0.508), so no threshold could separate them → **enrolment quality, not the threshold, is the lever.** The 0.55 gate itself is essential because meetings are open-set (unenrolled people always show up).

## What changed
- `voiceprint.py` match: **best-of-profiles** — score each speaker against every enrolment per name and take the max (was: mean of all enrolments, which blurs multi-condition prints).
- `transcribe-speakers` render: prefix each turn with `[MM:SS]`.
- Re-enrolled Austin richly (55 turns from M-003) + added Effie, Jhuztine, Zac.

## Result
After: Austin **0.315 → 0.709** on M-015 (cross-recording — the honest proof). Zac now best-matches Zac, not Ben. No false positives.

## Conversation

**2026-07-17 21:11 claude-code:** Run run-20260717-2110 completed — We made the meeting voice-recognition noticeably more reliable and made transcripts easier to use. Two changes: (1) when the system decides who a voice belongs to, it now compares against each saved sample of a person and picks the best match, instead of blending all their samples into one average — averaging a phone recording with an in-room recording used to produce a blurry reference that matched neither well. (2) Every line of a transcript now shows a timestamp, so you can jump to that moment in the recording and cite when something was said. We proved it on a real meeting with two brothers who sound alike (Austin and Zac, Zac being unenrolled): beforehand Austin's own voice was only weakly recognised (and the unenrolled brother came dangerously close to being mistaken for Ben); after re-recording Austin's voice profile properly, his recognition score more than doubled and crossed the confidence line, while nobody was mislabelled. Without this, the feature would keep failing to recognise people across different recordings and — worse — risk putting the wrong person's name against a decision, which is exactly what the "who decided what" feature must never do. The benefit: reliable, safe attribution that improves as we add more voice samples.
