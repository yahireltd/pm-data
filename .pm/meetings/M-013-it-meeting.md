---
id: M-013
slug: it-meeting
title: IT Meeting
state: held
created: 2026-07-15T15:42:18Z
updated: 2026-07-22T14:22:45Z
scheduled_at: 2026-07-22T11:00:00Z
duration_minutes: 30
location: IT Office
project: null
pre_project: null
milestone: null
organizer:
  kind: human
  name: unknown
stakeholders:
  - name: Austin
    channel: email
    contact: austin@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-04T01:26:11Z
    role: Dev
    notify_on:
      - comment_added
      - state_change
      - meeting_held
      - outcome_recorded
      - assigned
      - meeting_scheduled
  - name: ben
    channel: email
    contact: ben@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-06T01:44:37Z
    role: SME
    notify_on:
      - assigned
  - name: Zsolt Turu
    channel: email
    contact: zsolt@yahire.com
    internal: true
    added_by:
      kind: human
      name: Austin Pickering
    added_at: 2026-06-10T00:37:41Z
    role: Dev
    notify_on:
      - assigned
agenda:
  - topic: "Consumables/Parts: Anything we need?"
  - topic: Any other Major server / voodoo / interruption / user issues?
  - topic: Super clear out Aug! Keep ticket numbers down
  - topic: Server restarts, every Wednesday, Sage, Insp, Yii - LET GO
  - topic: Users In and Out.
  - topic: Certificates Sept (need bringing in line)
  - topic: "Zsolt`s Road Map: Sales, Venue DB, Stock, Speed, YaBundles, Annuals"
  - topic: Austin`s Road Map –  (Session testing), Route Planning, Capacity
  - topic: Any crossover things to discuss.
  - topic: Recap on releases from last week, What’s coming?
  - topic: 2022 Nike tick Yahire
  - topic: PHP 8 – Oct? DONE – NEED FOR Yahire website now.
  - topic: Check ahref weekly 1,545k (1,501k), Health 91 (91)
  - topic: "Google analytics: Bounce rate 44.40% (41.60%), Session duration 5m 11s (5m 15s), Users 3.4K (3.7k)"
  - topic: Check website errors
  - topic: Change linux to Ubuntu 22.04+ for php 8+ on yasite
  - topic: Pizza party for cabinet re-wiring - remove servers & move network drives
  - topic: BL Off 4th-18th Aug
  - topic: Austin Off September 4th (around a week not sure on exact dates yet)
outcomes:
  - description: Deactivate Chris's user — he's out (not fully signed yet, but will be). If he calls asking why his user's inactive / can't answer emails, say it's temporary.
    recorded_at: 2026-07-22T14:21:59Z
    owner:
      kind: human
      name: Austin
  - description: Automate certificate renewals (currently manual — saves a few hours each quarter).
    recorded_at: 2026-07-22T14:22:04Z
    owner:
      kind: human
      name: Austin
  - description: Block/limit concurrent logistics solves — ~8 at once overloaded the server today. Cap concurrency; optionally upgrade the server to allow more than one at a time. Confirm via hypercare that all jobs are still covered.
    recorded_at: 2026-07-22T14:22:09Z
    owner:
      kind: human
      name: Austin
  - description: Define what the 2022 "night tick" (Yahire) means — nobody currently remembers; it's been on the list since 2022.
    recorded_at: 2026-07-22T14:22:14Z
    owner:
      kind: human
      name: Austin
  - description: Allow Claude access to the Google stats (ahrefs / analytics) so meeting recaps aren't stale — the numbers are currently the same as last week.
    recorded_at: 2026-07-22T14:22:19Z
    owner:
      kind: human
      name: Austin
  - description: Keep contact with Zach re the warehouse electrical disruption (the Wednesday after next) and confirm exact timing. Hold the phone-hotspot contingency for drivers/warehouse internet. Awareness item — no build action, just stay ready.
    recorded_at: 2026-07-22T14:22:24Z
    owner:
      kind: human
      name: Zsolt
  - description: Close out current-project milestones (logistics rollout, PM-tool) before Ben's leave — kill the current projects and put them behind us. Includes a milestone push on the PM tool.
    recorded_at: 2026-07-22T14:22:29Z
    owner:
      kind: human
      name: Austin & Zsolt
    due: 2026-08-04
  - description: "Review the Nathan discovery transcript: downgrade Claude's over-eager \"actions\" to the items that were actually decided, and seed an ideas bank for the submission-portal project from the full transcript."
    recorded_at: 2026-07-22T14:22:35Z
    owner:
      kind: human
      name: Austin
  - description: Run discovery sessions next week with the sales team (a subset of the Nathan questions) and with Sophie (she handles customer growth). Needs time to percolate before concepts.
    recorded_at: 2026-07-22T14:22:40Z
    owner:
      kind: human
      name: Ben
    due: 2026-07-31
  - description: "Arrange the cabinet re-wiring / \"pizza party\" during Ben's leave (4–18 Aug): remove servers and move network drives."
    recorded_at: 2026-07-22T14:22:45Z
    owner:
      kind: human
      name: Austin & Zsolt
attachments:
  - key: meetings/M-013/1784725155074-meeting-recording-2026-07-22-1259.m4a
    filename: meeting-recording-2026-07-22-1259.m4a
    content_type: audio/x-m4a
    size: 6059482
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-22T12:59:16Z
  - key: meetings/M-013/1784730101983-meeting-recording-2026-07-22-1259-transcript.txt
    filename: meeting-recording-2026-07-22-1259-transcript.txt
    content_type: text/plain
    size: 18640
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-22T14:21:41Z
calendar:
  graph_event_id: null
  ics_url: null
kind: other
version: 18
reminders: []
recurrence:
  unit: week
  interval: 1
follows: M-010
next: M-016
---

# IT Meeting

## Pre-meeting notes

_Agenda is in frontmatter._

## Minutes

_Filled during/after._

## Transcript — meeting-recording-2026-07-22-1259.m4a

_Transcribed 2026-07-22T14:21:41Z on-device._

_IT Meeting — 22 Jul 2026, ~23 min. Speakers HUMAN-ADJUDICATED by Austin (Ben / Austin / Zsolt; one unidentified in-room voice shown as "(unidentified)"). Cleaned for readability from the verbatim on-device transcript (the raw machine transcript is kept as the `-transcript.txt` attachment). Zsolt now has an in-room voiceprint from this meeting._

## Summary

**Facilities / warehouse**
- CCTV: two cameras temporarily down during the mesh install / re-cabling; video from Unit 14 still up and internet still available — no lasting issue.
- Warehouse electrical work (the Wednesday after next) may cause ~1 day of disruption. Contingency: phone hotspots for drivers; Zach unsure whether warehouse phones have SIMs, but people use their own. Logistics planning needs screens → a laptop or two can bridge it. **Awareness item** — keep contact with Zach and confirm exact timing.

**Users / IT housekeeping**
- Chris is out (not fully signed yet, but will be) — his user is still active and should be deactivated. If he asks, say it's temporary.
- Weekly Wednesday server restarts continue, except the E-server (left as-is — the box works, the service doesn't).
- Certificates aren't automated — Austin to automate (saves a few hours each quarter).
- PHP 8 (needed for the Yahire website): **done**.
- "Night tick" (dated 2022) — nobody remembers what it means; needs defining.
- Phone opening hours adjusted (after back-and-forth with Darren): 7:30 weekdays, 8:30 weekends.

**Roadmaps / delivery**
- Logistics rollout almost done. Bug today: ~8 concurrent solves overloaded the server → block concurrent solves; option to upgrade the server and cap concurrency. Needs hypercare to confirm all jobs are covered (Austin to email if anything's missing).
- Accounts: automatic posting running fine daily; part two pending.
- PM tool almost wrapping up (website bits + Friday's meeting features). Logistics + PM-tool wrapping frees Austin for sales + accounts.
- Sales project officially kicked off as a **four-phase** project — roadmap needs dates + sprints so it echoes reality (currently Zsolt has a phase-1 sprint + a stock sprint; some unassigned ones are Austin's).
- Two of Zsolt's milestones already closed — practice is to close milestones (even just a tick) as they land.

**Sales discovery / CRM**
- Nathan discovery session yesterday (~2 hrs guided questions) points to significant CRM work (new activity types); no full CRM overhaul planned — that would be extra scope.
- Claude over-marked that meeting's items as "actions" — Austin to review and downgrade the ones that weren't actually decided.
- Direction: keep the full meeting transcripts and feed them into an ideas bank for the submission-portal project, rather than relying on auto-extracted actions — the transcript is the most valuable artifact.

**Recap — released last week**
- ~83 commits: PM-tool meeting-intelligence stack (recording this meeting) + mobile UI; Ya-Hire sales segmentation + insights pages; route planner; accounts integrity + auto-posting; stock management (supplier inline editing, sale-stock adjustment audit + report, quality checks, failure points, photo grade cards, multi-photo, quick-fill).
- Everyone's logging work through the PM tool (tickets; website via the customer portal; ESI + new initiatives).

**Analytics / monitoring**
- Google stats not yet pulled by the tool → allow Claude access so recaps aren't stale (numbers currently the same as last week).
- Website errors: false alarm (Ben was looking at a different environment). "No contracts converted by 3pm" — a red herring.
- Some confusion over which servers are on Ubuntu vs Linux/EC2 after the PHP-8 move — to confirm.

**Time off / forward planning**
- Ben off 4–18 Aug (WFH Mon 3rd to help pack). Before he leaves: close out current-project milestones (logistics, PM-tool) — kill the current projects and put them behind us — then look further ahead at the more complex sales-project parts and the product work.
- Austin finds it hard to get ahead on the sales project → may pick a different milestone; Zsolt's milestones implicitly involve both of them.
- Next week: Ben to run discovery with the sales team (a subset of the Nathan questions) + Sophie (she handles customer growth) — needs time to percolate before concepts.
- Cabinet re-wiring / "pizza party": arrange during Ben's leave (remove servers, move network drives).
- Austin off ~4 Sept (~a week, exact dates TBC). Zsolt: August bank holiday + a December holiday.

## Action points
_Also recorded as structured outcomes on this meeting._
1. **Deactivate Chris's user** (he's out) — Austin. If he asks, say it's temporary.
2. **Automate certificate renewals** (saves a few hours/quarter) — Austin.
3. **Block / limit concurrent logistics solves** (server can't handle ~8 at once); optionally upgrade the server + cap concurrency — Austin.
4. **Define what the 2022 "night tick" means** — nobody currently remembers — Austin.
5. **Allow Claude access to the Google stats** (ahrefs / analytics) so recaps aren't stale — Austin.
6. **Keep contact with Zach re the warehouse electrical disruption** + confirm exact timing; hold the phone-hotspot contingency (awareness item) — Zsolt.
7. **Close out current-project milestones** (logistics, PM-tool) before Ben's leave on 4 Aug — Austin & Zsolt.
8. **Review the Nathan discovery transcript**: downgrade over-eager "actions" to real decisions, and seed an ideas bank for the submission-portal project — Austin.
9. **Run discovery sessions next week** with the sales team and with Sophie — Ben.
10. **Arrange the cabinet re-wiring** during Ben's leave (remove servers, move network drives) — Austin & Zsolt.

## Transcript (cleaned, adjudicated speakers)

[00:03] Austin: It's recording, but anyway.

[00:05] Ben: Okay, good. Consumables — anything we need?

[00:15] Zsolt: Nope.

[00:17] Ben: Any CCTV videos?

[00:39] Austin: There's a CCTV issue — two cameras temporarily down while they do the mesh install and re-cabling. We checked: we've still got the video from Unit 14, and they still have internet, so it's fine — they've got internet and video. Also, on Wednesday there's essential disruption while they do the electrical work, but we can still get there. Anything else? No.

[01:34] Zsolt: I asked Zach and spoke with Rob about how they'd keep internet — they said they can sort it, just use phone hotspots if needed; the drivers can use their phones. Zach had no idea whether the warehouse phones have SIM cards, or how many, but lots of people use their own phones, so that should be fine. We should get more info from Zach on exactly when it's going to happen.

[02:08] Ben: Or how they'll use the desktops — logistics can't plan without them.

[02:11] Zsolt: You can't.

[02:13] Ben: But through a SIM...

[02:14] Zsolt: Just use a phone and share it.

[02:18] Austin: Yeah, but for logistics planning they need screens — a big screen.

[02:22] Austin: So they'd share data off the phone and connect to it — but is that on the electric that's going down? It's not certain. Okay — they can just have a laptop or two for the time being. So, Claude, for the benefit of the tape: there are no actions here other than to be aware. Sales can fall back to BDC info, and logistics up here.

[02:57] Ben: Right — someone keep contact with Zach about what's going on, in case we need to act.

[03:04] Zsolt: Probably just one day of possible disruption. Hopefully fine.

[03:14] Ben: Super clear-out — August. That's not next Wednesday, the one after; I'll be away, so don't forget. (Joking: "Claude, set an annoying cron job every five minutes"... no, don't do that.) Server restarts — yes, except the E-server; leave it, it doesn't work anyway (the machine's fine). Users in and out: Chris is out — he hasn't fully signed yet, but he will.

[04:00] Austin: Yeah. I'm still wondering where he is.

[04:04] Zsolt: But his user is still active?

[04:07] Austin: It should be off — we should deactivate it after the meeting.

[04:14] Ben: Yeah. If he calls asking why his user's inactive or why he can't answer his emails, just say it's temporary. Certificates — are you bringing them in line?

[04:32] Austin: I don't automate that, for the most part.

[04:37] Ben: I'm not either.

[04:39] Austin: Yeah — but I will.

[04:41] Ben: It saves you a few hours every quarter, doesn't it?

[04:44] Austin: Yeah, it's something I faff about with every week.

[04:48] Ben: "Claude, automate all the certificates." Thanks. Zsolt's roadmap — you've been on the sales/product work and the customer portal. When the more complex sales milestones come up we'll need more planning; and let's close milestones as we close them — two are closed already. Even a tick in the box is good practice so everyone can see what's happening. Good. Austin's roadmap — sales and logistics, right?

[06:02] Austin: Yeah — sales project, and the logistics rollout, which is almost done. Just a few little bugs today: they were trying to run about eight solves at once and the server fell behind.

[06:16] Ben: So there's a limit to the solves.

[06:18] Austin: We should block running concurrent solves at the same time, because the server can't handle it.

[06:28] Ben: Is that our server or your server?

[06:31] Austin: It's our server.

[06:33] Ben: If they're using it massively, we might need to open it up.

[06:40] Austin: We can either upgrade the server to allow more than one at a time, and restrict how many run concurrently.

[06:50] Ben: And that needs the hypercare, to make sure all the jobs get covered.

[07:03] Austin: Nothing's missing.

[07:04] Ben: And if it is?

[07:07] Austin: I'll send an email.

[07:08] Ben: You ran them as well?

[07:11] Austin: Yeah.

[07:12] Ben: Magic. And the accounts fixes — all behind you now?

[07:20] Austin: Two parts. The automatic posting is fine, running every day. Part two's pending.

[07:35] Ben: Great. And you're doing sales too, which isn't on the board.

[07:47] Austin: Yeah — and the PM tool itself.

[07:51] Austin: Still adding little bits — website, and this meeting stuff that came up on Friday.

[07:56] Ben: We really want to aim for that clean place —

[07:59] Austin: — where what you're working on isn't contaminated by loads of other bits. So logistics is wrapping up, the PM tool is almost wrapping up, which frees me for the sales and accounts bits.

[08:21] Ben: We can break that into phases — the sales project is a four-phase bonanza. We've officially kicked it off as a set of phases. We want the roadmap in this meeting to echo reality.

[08:58] Austin: I'll click into it — if we've not put dates on anything it'll look a bit weird.

[09:06] Zsolt: Yeah, it did.

[09:12] Ben: Zsolt's got a phase-one sprint and a stock sprint, and that's it. Then there's the unassigned ones.

[09:20] Austin: Yeah, I think those are mine.

[09:27] Ben: So a bit of work to bring that in, so it represents clarity of what we believe is going on. Meetings — crossovers? Not really; you're on stock right now, you were on logistics. Well — there'll be a lot of sales crossovers.

[10:14] Austin: Yeah. From our chat with Nathan yesterday, a lot of CRM work might be needed — new activity types they need.

[10:27] Ben: Zsolt's part of the project touches the CRM, but we're not doing a full overhaul there — that'd be extra. Those discovery sessions will point to a lot. We did about two hours of guided questions with Nathan — brainstorming and understanding what's going on. And there was another thing I emailed you about — Nathan wanted an exhibition feature.

[11:08] Austin: It might have been a firm decision.

[11:10] Ben: It might've been a firm decision — and Claude marked everything as an action.

[11:15] Austin: Yeah, I can go back over it and update the ones that weren't actually decided.

[11:19] Ben: The whole point is it's an action/decision tool for meetings like that, where we aggregate the sales team's answers to get a sense of it, then maybe drive actions. So really the transcript is the most important thing.

[11:43] Austin: So that's not an issue.

[11:45] Ben: Right. So we may need to populate an ideas bank for the submission-portal project from that meeting.

[11:55] Austin: Yeah — it's how I ask Claude to handle the transcript. We can do anything with it.

[12:02] Ben: I think we just save up all the transcripts — surveying the whole team for every question is an unusual activity, so full transcripts are probably more appropriate.

[12:21] Austin: The transcript was pretty accurate.

[12:22] Ben: Yeah, I had a little breeze through it.

[12:24] Austin: It was like two hours of...

[12:40] Ben: You can see the conversation might not be labelled perfectly, but here are the questions and the answers — find out what other questions were asked, the ideas, then summarise where there's consensus, where there isn't, and the extra ideas. And you can always listen back. I found that conversation very useful. Recaps —

[13:13] Austin: 83 commits: the whole PM-tool meeting-intelligence thing we're using to record this meeting; the mobile UI so we can record properly on the phone; the Ya-Hire sales segmentation work and insights pages I showed you the other day; the planner; and the accounts integrity fixes and auto-posting. On stock management: supplier inline editing, sale-stock adjustment audit and report, full quality checks, failure points, photo grade cards, multi-photo, quick-fill. Anything else you can think of?

[14:22] (unidentified): Yeah, I think that's about right.

[14:24] Ben: Is everything going through the tool for you as well?

[14:27] Zsolt: Yeah, I'm using it —

[14:29] Austin: Cool.

[14:29] Zsolt: — logging everything, tickets, everything that's been done.

[14:34] Ben: If we end up doing bits on the website etc., that's included in there — we just have to remember them.

[14:40] Austin: The website's included.

[14:43] Zsolt: That's why you have the customer portal.

[14:46] Austin: You've got the ESI project there. We've got initiatives — new projects — and then the two existing ones.

[14:58] Austin: So little dev updates and stuff go there.

[15:01] Zsolt: Darren — what he sent today, realising how the website's been working after six or seven years with no change. I did adjust the phone opening hours — back and forth with Darren — it's 7:30 weekdays and 8:30 weekends.

[15:31] Ben: We can choose to say nothing to anyone, and then Rob won't mention it.

[15:43] (unidentified): Yeah, yeah.

[15:46] Austin: I couldn't really say no.

[15:52] Ben: If people want to change things in the moment out of frustration, fine — it's not the end of the world, but it's not the beginning of it either. Okay. Night tick, Yahire — action needed. What is a night tick?

[16:16] Austin: I don't think anyone knows anymore — it's been that long.

[16:18] Ben: "Claude, define what we mean by the night tick." Thanks.

[16:21] Austin: It says 2022 on there.

[16:30] Ben: PHP 8 in October.

[16:33] Austin: Done.

[16:45] Ben: Does this thing update the notes as well?

[16:47] Austin: It'll update the outcomes.

[16:50] Austin: I can get it to update the notes too, if we want.

[16:52] Ben: It's not that much — one or two things.

[17:01] Ben: Does it pull the Google stats?

[17:04] Austin: It doesn't.

[17:06] Ben: Action point — allow Claude access to the Google stats. This is the same stats as last week, then.

[17:14] Austin: Oh, yeah.

[17:22] Ben: Just give me good numbers. Website errors — you had to check that the other day. Actually you didn't; I got excited because I was looking at a different environment. And there were no contracts converted today by 3pm — but I was just drifting and pressing buttons.

[17:52] Austin: What was that about?

[17:55] Zsolt: When we changed the PHP we changed server — it's now Ubuntu, isn't it?

[18:01] Austin: They're all on Ubuntu now, aren't they?

[18:03] Zsolt: No. The system was on EC2, on Linux.

[18:08] Austin: But now it's on Ubuntu.

[18:10] Zsolt: And the website — no, that's...

[18:13] Austin: I use my connect script now, so I don't even know.

[18:31] Zsolt: I've never been there, so I can't check.

[18:36] Ben: Pizza party — you two should arrange one while I'm away. (Joking about Sean doing 24-hour shifts / Thailand.)

[18:44] Zsolt: Yeah, at least five.

[18:54] Ben: I highly encourage you to get this off the minutes — the cabinet re-wiring. So I'm off the 4th to the 18th of August. You guys should look at your projects and think about what's been the bottleneck — probably too many things. The 4th is a Tuesday; I'll probably work from home the Monday to help my wife pack. Tee up a few things before I go.

[19:46] Austin: I think, to do more.

[19:48] Ben: Definitely kill the current projects and put them behind us — a milestone push on the PM tool too; let's close some milestones off. Then look at the more complex parts of the sales project, and the product thing — look further ahead: what do we need to coordinate with people, feedback, and so on. With Shandor away that might be an issue — we might need to sit down with Justine about her stock thing, or pick up the next meaty sales milestone and flesh it out in full. Same for you.

[20:48] Austin: With the sales project it's a bit difficult for me to get ahead — I've tried a few times and not quite hit the mark. We may need to pick something else out.

[21:08] Ben: True. There are also milestones within Zsolt's project that implicitly involve you both, so we could look at that. The project's laid out linearly — milestones and phases — so we don't want to get too far ahead of ourselves. Next week I intend to sit down with the sales team — not all the questions like we did with Nathan, just the relevant ones. That'll need time to percolate before we come up with concepts.

[21:48] Austin: Sophie's session is quite needed too — she deals with the growth of the customers.

[21:56] Austin: So it's important we talk to her.

[21:57] Ben: Yeah — and as I expect from those questions, it's all done in memory and personality. Austin's off September 4th — around a week, no more, exact dates TBC. And Zsolt, where are you going? Somewhere to get tobacco.

[22:45] Zsolt: And then just December, for the holiday.

[22:48] Ben: You've got the August bank holiday coming up too.

[22:55] Ben: All right, good. Anything else at all? Thank you very much. Thanks, Claude — thanks for listening.
