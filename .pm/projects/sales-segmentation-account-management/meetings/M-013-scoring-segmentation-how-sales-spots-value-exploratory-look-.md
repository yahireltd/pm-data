---
id: M-013
slug: scoring-segmentation-how-sales-spots-value-exploratory-look-
title: Scoring & segmentation — how sales spots value + exploratory look at the suggested processes (Ben / Austin / Nathan)
state: scheduled
created: 2026-07-17T16:15:22Z
updated: 2026-07-21T15:58:17Z
scheduled_at: 2026-07-21T14:15:00+01:00
duration_minutes: 60
location: TBD (in person / call)
project: sales-segmentation-account-management
pre_project: null
milestone: null
organizer:
  kind: human
  name: Austin
stakeholders:
  - name: Ben
    role: Business SME & Systems Architect
    channel: email
    contact: ben@yahire.com
    internal: true
    notify_on:
      - state_change
      - meeting_held
      - outcome_recorded
      - comment_added
      - assigned
      - meeting_scheduled
  - name: Austin
    role: Developer & System SME
    channel: email
    contact: austin@yahire.com
    internal: true
    notify_on:
      - state_change
      - comment_added
      - assigned
      - meeting_scheduled
      - meeting_held
      - outcome_recorded
  - name: Nathan
    role: Sales Manager SME
    channel: email
    contact: nathan@yahire.com
    internal: true
    notify_on:
      - outcome_recorded
agenda:
  - topic: "Where we are: segments & scoring (Phase-2 milestone 1) — full hired base scored (9,074 domains, segment + tier), segment-research findings: segment/sub-type × repeat predicts £ far better than score"
    ticket: T-0456
    duration_min: 10
  - topic: "DISCOVERY (main purpose): how does a salesperson spot value in a particular customer or quote? Run survey §1 Spotting Opportunity + §2 Qualification with Nathan; compare sales instincts with the AI score+segment live on /segment-research"
    ticket: T-0473
    duration_min: 20
  - topic: "Exploratory: Ben's process levels (System / Quick / In-depth / Lifetime / Golden Nugget) — survey §5's 2×2 (order size × repeat likelihood) IS this grid; sense-check against the real £-per-cell numbers with Nathan"
    ticket: T-0496
    duration_min: 15
  - topic: "Sales question survey — plan the rollout (who gets asked which of the 10 sections; not every question of everyone); goal: capture the essence of the current process"
    ticket: T-0485
    duration_min: 10
  - topic: "Next steps: prototype process design keeping existing workflow similarity; visible for interrogation/confirmation by Nathan, Taran, Sam + wider team (implementation not before Nathan returns from leave)"
    duration_min: 5
outcomes: []
attachments:
  - key: meetings/M-013/1784645782925-meeting-recording-2026-07-21-1456.m4a
    filename: meeting-recording-2026-07-21-1456.m4a
    content_type: audio/mp4
    size: 6307197
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-21T14:56:26Z
  - key: meetings/M-013/1784648446636-meeting-recording-2026-07-21-1426.m4a
    filename: meeting-recording-2026-07-21-1426.m4a
    content_type: audio/x-m4a
    size: 18154190
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-21T15:40:47Z
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
version: 11
---

## When / who
**Tuesday 21 July 2026, 14:15** — **Ben, Austin, Nathan.**

## Purpose (Austin)
Main purpose: discuss **customer scoring and segmentation** — specifically **how a salesperson spots value in particular customers and quotes** — and talk through the **suggested process levels** in a bit more detail. Exploratory: we are still working the segmentation side before moving on to sales-process design.

## Context — Ben's Phase-2 update (email to Nathan)
Phase 2 "Effectiveness / operating engine" early discovery, milestones 1–4:
1. **Define segments/scoring** — "at the cusp of breakthroughs" on which customer types/scores should undergo what level of process (and possibly account management).
2. **Process definition** — still vague; Ben remains sold on his levels concept (System / Quick / In-depth / Lifetime / Golden Nugget) but what these mean in practice needs defining. To be qualified to define them, we need to understand the current reality — hence:
3. **Sales question survey** — Austin + Ben survey the team (not every question of everyone) to capture the essence of the current process, then respond with a prototyped process.
4. **How it will go down** — once clarity on scores+process: design the process keeping as much existing-workflow similarity as possible → visible for interrogation/confirmation by Nathan, Taran, Sam + wider team → then implementation. Not before Nathan returns from leave.

## The sales questionnaire — how it maps to THIS meeting
Ben's survey has 10 sections + performance. **Most relevant on Tuesday (Nathan = Head of Sales, before his leave):**
- **§1 Spotting Opportunity** → agenda item 2 verbatim: what makes a lead interesting / not / a false positive; *"describe what happens NOW when a hot lead/golden nugget comes in — who speaks to them, what's saved where, how tracked"* (current-state walkthrough to capture before his leave).
- **§2 Qualification & Understanding** → item 2: what questions reveal a bigger opportunity; **how do you assess potential vs current spend** (this is exactly our potential/share-of-wallet model — compare his mental model with ours); *"could qualification questions slip into normal exec handling?"* → our early-qualify concept.
- **§5 Approach by Customer Type** → item 3: the **2×2 (small/large order × low/high repeat likelihood) IS Ben's 5-process grid** — and we now have real £ per cell (repeat cells hold ~86% of all realised £; one-off+large is rare: only ~10 customers). Ask Nathan the §5 questions, then reveal the cell numbers. Also §5's *"who should handle different customers — straight to AM or sit with execs?"* = the standing Incubation-ownership question.
- **§4 Prioritisation** → item 3 support: "which customers deserve almost no sales time" — data says the System cell = ~60% of hired customers but ~7% of £.
- **§10 Venue Cash Orders** → raise briefly: connects to the venue-attribution gap (Code Node "2 vs 9") — who develops venue cash orders AND how we credit their £ correctly.
**Defer to later sessions / other people:** §3 (process detail), §6 (customer knowledge → account-plan template), §7 (maturity), §8 (ownership), §9 (opportunity management), performance — these inform process design + the T-0537 account-plan work, after the survey rollout.

## Prep / drill-in material
- **/segment-research** (live backend): drillable correlation research — segment & repeat beat score as £ predictors; every number drills to real customers → their contracts. Have it open for item 2: "pick a customer you rate — see what the AI said" and the reverse.
- Scoring status: **9,074 domains scored & segmented = the entire hired-customer base** (zero remaining with hire history). Open decision to raise: the ~14k quote-only leads (score-on-demand vs top-slice vs bulk).
- Strategic account plans thread (T-0537): Tate/Olympia/etc. templates + Nathan's completeness review — the top end of the process spectrum.

## Note
Nathan attending before his paternity leave starts (late July) — bank his §1/§2/§5 answers now; wider-team survey + interrogation of the prototyped process come after his return.
