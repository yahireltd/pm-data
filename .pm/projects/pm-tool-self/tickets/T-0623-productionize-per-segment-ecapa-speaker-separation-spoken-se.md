---
id: T-0623
title: Productionize per-segment ECAPA speaker separation + spoken self-introduction
type: feature
state: in_progress
created: 2026-07-17T22:47:24Z
updated: 2026-07-20T01:47:42Z
project: pm-tool-self
section: null
parent: null
children: []
order: 1024
priority: p1
reporter:
  kind: human
  name: Austin
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - transcribe-speakers uses ECAPA per-segment labelling; a merged sound-alike cluster is split into the correct people
  - Spoken self-introduction is parsed and takes priority over voiceprint matching
  - Everyone re-enrolled on ECAPA from clean sources; the contaminated Zac print is replaced
  - ECAPA open-set threshold calibrated; unenrolled voices correctly stay SPEAKER_NN
  - "Audio front-end normalisation: resample to 16kHz mono + loudness (EBU-R128/RMS) normalise before embedding"
  - "Embedding/score-space normalisation for cross-condition robustness: per-recording embedding mean-centering + AS-Norm score normalisation against a cohort"
  - Human-matching corrections feed back as labelled training samples (self-healing)
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
agent_runs:
  - id: run-20260719-2308
    model: claude-opus-4-8
    started: 2026-07-19T23:08:56Z
    status: in_progress
    progress:
      - at: 2026-07-19T23:19:51Z
        note: "Major milestone (commit 2a7a4d2): ECAPA per-segment labelling is live in transcribe-speakers. Library rebuilt from clean sources (Austin/Effie/Jhuztine from M-003; Ben phone + IN-ROOM; Rob; Zac from his ground-truth solo stretches). Benchmarked against Austin's 75-line human-matched M-015 ground truth: raw per-segment accuracy went 49% → 70% held-out after (a) session-mean profiles replaced per-window max (impostor inflation fix) and (b) Ben gained an in-room profile built from the human corrections — Ben's errors went 21 → 0. Confident predictions measure ~94% correct; blind smoothing measured WORSE than nothing on interleaved speech, so the shipped design names confident segments, inherits only in one-voice clusters, and renders uncertain ones as \"Name?:\" for the human matcher (whose corrections feed back into enrolment — the loop that fixed Ben). REMAINING: spoken self-intro parser, loudness + embedding mean-centering/AS-Norm, overlapped-speech flagging, corrections→enrolment automation, second-recording validation for Zac."
      - at: 2026-07-19T23:41:24Z
        note: 'Validation sweep over real recordings (commit d79f0b9). Found + fixed a critical bug: the phone system records at 8kHz and ECAPA silently expects 16kHz — every phone-derived profile was garbage (Rob scored -0.08 vs his OWN call). load_wav now always resamples; after re-enrolment the Ben/Rob call labels Ben 22/22 confident + Rob correct. Added an open-set guard: uncertain guesses only render if that person is confidently present elsewhere in the recording — the Francine/Samantha stranger calls went from misleading "Ben?" guesses to fully unnamed (zero false positives). Scorecard: M-013 held-out Austin ✓; Ben/Rob call ✓; Francine call open-set ✓; Samantha call open-set ✓; Thanos↔Nathan call (both unenrolled, strictest open-set) still diarizing. Enrolled Francine + Samantha from their calls — each then named confidently while the OTHER voice on the same call stayed unnamed (correct discrimination). Library: Austin, Effie, Jhuztine, Ben(room+phone), Zac, Rob, Francine, Samantha. NEXT VALIDATION DATA (Austin to pull from the phone system): Effie + Jhuztine calls (true held-out: enrolled in-room, tested on phone = cross-channel), plus fresh calls/recordings of Austin/Ben/Rob/Zac/Francine/Samantha as ongoing held-out tests.'
      - at: 2026-07-19T23:44:51Z
        note: "Validation sweep COMPLETE — 5/5 passed. Final: Thanos↔Nathan call (10 min, 122 segments, both voices unenrolled — the strictest open-set test) produced ZERO confidently-named segments: no false positives. Full scorecard: (1) M-013 Austin held-out cross-recording ✓ (2) Ben/Rob call — Ben 22/22 confident, Rob correct ✓ (3) Francine call — Francine named, stranger Rebecca unnamed ✓ (4) Samantha call — Sam named, caller unnamed ✓ (5) Thanos/Nathan — nobody named ✓. The system now demonstrably names enrolled people and refuses to name strangers, across phone and room audio. Awaiting from Austin for the next held-out round: Effie + Jhuztine phone calls (cross-channel test of in-room enrolments), fresh calls of already-enrolled people, and a future Zac recording."
      - at: 2026-07-20T00:00:34Z
        note: "Round-2 validation: caught and fixed the first real sibling FALSE POSITIVE. Zac's phone call ([Zac Leslie]_2010-2048) was labelled \"Ben\" at 0.56 CONFIDENT — because Ben had a phone profile and Zac didn't (channel beat identity, as predicted). Fix: enrolled Zac's phone condition from the call → re-label gives Zac 0.72–0.78 confident on all his segments, other party unnamed. Critical regression PASSED: re-ran Ben's call with Zac's new phone profile in the library — Ben still 22/22 confident, no theft in either direction. Policy confirmed (Austin): \"detect them no matter the media\" = one profile per person per channel/condition; every adjudicated recording feeds the next condition. Remaining: true held-out Zac call (next one he makes); threshold review vs the sibling band (~0.56) once the full round-2 score distribution is in; UI verification bundle zips to Downloads when the batch finishes (jhuztine cross-channel, francine2 held-out, nathan, todays, thanos2005 still processing)."
      - at: 2026-07-20T00:34:00Z
        note: 'FULL TREATMENT COMPLETE — human-in-the-loop round trip proven end to end. Austin adjudicated 5 calls in the matcher UI (machine-readable ground truth round-tripped back); 8 unconfirmed "Name?" segments were detected via the human:true flag and EXCLUDED from enrolment (Austin+Ben phone profiles rebuilt from verified-only segments). Library now 11 people, multi-condition: Austin×3, Ben×3, Effie×2, Jhuztine×2, Zac×2, Rob×2, Francine×2, Samantha, + new Nathan, Orfield, Daniel Hannaway. RESULTS vs verified truth: 33% → 94% correct after enrolment; 6 honest misses; 4 confident-wrong (2.3%) — all diagnosed as diarization artifacts, not identity confusion: 3 short greetings merged into the wrong cluster + 1 genuine OVERLAP segment (the Austin/Orfield passage Austin himself flagged). Regressions: benrob PASS, m013 PASS, thanos2005 open-set PASS (0/132). Star result: the old Thanos call now identifies Nathan (ext 2018) 75/75 confident — a TRUE held-out cross-recording detection (enrolled from a different call, different day), with unenrolled Thanos correctly unnamed. Remaining known weaknesses → next work: (a) inherited-confidence policy for short segments in dominated clusters (the 3 greeting errors), (b) overlapped-speech flagging (the Orfield error), (c) next batch of calls = pure held-out re-validation.'
      - at: 2026-07-20T01:47:42Z
        note: "HELD-OUT EXAM COMPLETE — 8 fresh June calls (all predating enrolment data), zero enrolment, scored against the pre-registered criteria. Provisional verdict pending Austin's adjudication (bundle: ~/Downloads/heldout-exam.zip): STRONG PASSES — Austin 111/111 confident across 3 fresh calls; Nathan 42/42 across 2; Ben's side of the brothers call 10/10; strangers/Thanos unnamed throughout; SIBLING SAFETY HELD: zero confident Ben↔Zac cross-labels across 4 sibling-relevant calls (the merged brothers cluster was split majority-correctly with honest ? flags). HONEST MISSES exactly where profiles are thin: Rob absent/missed on Nathan's call, Samantha missed on her own call, Zac missed on the Francine call (but detected as Zac on 2 other fresh calls). ONE anomaly to adjudicate: a 5s confident 'Austin' segment inside Zac's cluster on the 2010-2002 call (genuine cross-talk vs confident-wrong — Austin's ears decide). Per protocol: misses get enrolled from adjudicated segments and retested on the NEXT round; the anomaly is diagnosed before any further enrolment if it's confident-wrong."
labels: []
attention: null
version: 13
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

## Conversation

**2026-07-19 23:06 claude-code:** **Accuracy strategy (Austin, 2026-07-20) — attribution is IMPERATIVE.** The meeting product fails if "who decided what" is wrong, so getting speaker attribution right is core, not polish. Agreed it's a ladder, not a single fix:

1. **Per-person capture** — each attendee records their own mic via the app → one clean stream per person → attribution is essentially perfect (no diarization, no sibling problem, no normalisation needed). This is the primary reliability mechanism; everything else is the fallback for guests / single-device recordings.
2. **Multi-condition voice training** — worth investing in; lifts a person's match score clear of look-alikes for the single-device path. Ceiling: it can't fix the diarizer *merging* two sound-alikes into one cluster, or rapid interleaving where one segment contains both people (the M-015 case).
3. **Normalisation** — polish, worth doing, but the useful kind isn't the obvious kind. Loudness + resample front-end = small win. The lever that actually helps cross-condition is *embedding/score-space* normalisation: per-recording mean-centering + AS-Norm. Raw audio EQ can't recover the bandwidth a phone threw away.
4. **Human matching (the matcher tool)** — the honest safety net for the interleaved-sibling worst case. Crucially, every correction becomes a labelled training sample, so the system self-heals with use.

Bumped to p1 — this determines whether the meeting product is useful at all.

**2026-07-19 23:13 claude-code:** **Overlap is a distinct, harder problem (Austin, 2026-07-20).** The Ben/Rob phone call was the easy case (dissimilar voices, clean turn-taking). M-015 had two SIMILAR speakers sometimes talking AT THE SAME TIME — and that must be handled. Key insight: on a single mic, overlapping words are lost at the *transcription* stage (whisper transcribes the dominant voice), so no amount of labelling recovers them. Strategy, ranked:
1. **Per-device capture solves overlap completely** (two simultaneous voices = two clean streams, both transcribed) — strengthens the case for it as the primary mode (PP-012).
2. **Detect + flag honestly** (near-term, this ticket): pyannote overlapped-speech-detection → mark regions "[overlapping — X + Y]" rather than silently mislabelling. Trust over false precision.
3. **Source separation spike** (later): SepFormer / target-speaker extraction using enrolled ECAPA prints to split 2-speaker mixtures, then transcribe each stream. Research-grade on room audio — trial, don't promise.

Progress this run: ECAPA library rebuilt from clean sources — Austin 153 windows (M-003), Effie 76, Jhuztine 5, Ben 13 (call), Rob 4. Zac next, from his clean solo phone-call stretches in M-015 per Austin's matcher ground truth.

**2026-07-20 00:51 claude-code:** **PRE-REGISTERED held-out validation protocol (fixed before data is collected — no goalpost-moving).**

Tonight's 94% is on the enrolment calls (semi-circular). The genuine proof = a FRESH batch of recordings, zero enrolment this round, scored blind against Austin's adjudication. One true held-out pass already exists (Nathan: enrolled from one call, detected 75/75 on a different day's call).

**Test material wanted:** 1–2 NEW calls per enrolled person (Austin, Ben, Zac, Rob, Effie, Jhuztine, Francine, Samantha, Nathan, Orfield, Daniel Hannaway), including at least one fresh Zac AND one fresh Ben call (sibling stress), plus a few stranger/customer calls (open-set). Later: one recorded MEETING (tests the room channel for phone-only profiles).

**Pass criteria (per call, decided now):**
1. Each enrolled person's talk time is majority-named correctly (confident) on their channel.
2. ZERO confident-wrong names on segments ≥2s. (Sub-2s greetings are a known diarization weakness — tracked separately, not a pass/fail item.)
3. Sibling test: fresh Zac call → Zac, fresh Ben call → Ben; neither ever confidently labelled as the other.
4. Strangers/customers stay unnamed (open-set).
5. Regression suite (the 9 adjudicated recordings with ground truth) still passes after ANY library or code change.

**Failure handling:** a miss = enrol that condition and retest on the NEXT fresh recording (never the same one). A confident-wrong = stop, diagnose, fix before any further enrolment.
