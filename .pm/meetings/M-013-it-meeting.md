---
id: M-013
slug: it-meeting
title: IT Meeting
state: held
created: 2026-07-15T15:42:18Z
updated: 2026-07-22T14:11:20Z
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
outcomes: []
attachments:
  - key: meetings/M-013/1784725155074-meeting-recording-2026-07-22-1259.m4a
    filename: meeting-recording-2026-07-22-1259.m4a
    content_type: audio/x-m4a
    size: 6059482
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-22T12:59:16Z
  - key: meetings/M-013/1784729480340-meeting-recording-2026-07-22-1259-transcript.txt
    filename: meeting-recording-2026-07-22-1259-transcript.txt
    content_type: text/plain
    size: 17895
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-22T14:11:20Z
calendar:
  graph_event_id: null
  ics_url: null
kind: other
version: 7
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

_Transcribed 2026-07-22T14:11:20Z on-device._

_IT Meeting (22 Jul, 23 min). Speakers HUMAN-ADJUDICATED by Austin (Ben / Austin / Zsolt) — verified, not voiceprint guesses. Zsolt now has an in-room voiceprint from this meeting._

[00:01] Ben: It's all I'm looking for.

[00:03] Austin: It's recorded, but anyway.

[00:05] Ben: Meeting. Okay, good. Consumables, anything we need?

[00:15] Zsolt: Nope.

[00:17] Ben: Any videos?

[00:39] Austin: but now they have issues CCTV issue two cameras down temporarily oh yeah they're taking them out yeah while they're doing the mes install for the relaying of we checked that we've still got the video from unit 14 on they still have internet so it will yeah they've got internet they've got a video oh okay it's my cable yeah okay great good okay also though what were you saying about the from wednesday you were saying oh that's essential disruption that they doing the electrical stuff but we can get there anything other yes that doesn't

[01:34] Zsolt: I asked Zach, I spoke with Rob, how can they have internet, they said they can sort it out, just use the phone, you know, hotspot, if they need it, they can use the phone with their drivers. I asked Zach, he had no idea if the warehouse phones had SIM cards or not, or how many of them, but lots of people use their own phones, so that should be fine, it's not like... Maybe we have to see if we can get more information from ZEC when it's going to happen exactly.

[02:08] Ben: Or how they're going to use the desktops and logistics they can't plan on.

[02:11] Zsolt: You can't.

[02:13] Ben: But through Simcoe.

[02:14] Zsolt: Just use a phone and share it.

[02:18] Austin: Yeah, but for logistics, like planning, they need screens, big screen.

[02:22] Zsolt: So they're just sharing data on the phone and they connect to it and... Yeah, but is that electric? if that goes down yeah but uh saying if yeah it's not certain but okay okay they can just have a laptop for the time being or two so claude for the benefit of the tape there's no actions here other than just to be aware right they can send sales to bdc info and the logistics up here

[02:57] Ben: Right, well, someone keep contact with Zach about what's going on and if we feel like we need to do that.

[03:04] Zsolt: Probably just one day there might be disruptions, but... Hopefully fine.

[03:14] Ben: Super clear out August. Okay, so that is not next Wednesday, the Wednesday after. I'll be away, so don't forget, guys. Claude, set an annoying cron job every five minutes. okay just ignore everything i say okay wait no don't do that um server restarts yes except one the e server yeah we don't need it let it go it doesn't work anyway i mean the machine works yeah Users in and out. Okay, we can say that Chris is out. He hasn't fully signed on the dotted line, but he will be.

[04:00] Austin: Yeah. I'm still wondering where he is.

[04:03] Ben: Yeah.

[04:04] Zsolt: But his user is still active?

[04:07] Austin: It should have been out. We should take it off to the meeting.

[04:14] Ben: Yeah. If he calls up to inquire for some reason, why is his user inactive? Why can't he answer his emails? Just say it's temporary. Certificates. Are you bringing it in line?

[04:32] Austin: I don't automate that for the most part, I think.

[04:37] Ben: I'm not. Huh? I'm not.

[04:39] Austin: Yeah, no, I will though.

[04:41] Ben: Well, it saves you a few hours every quarter, doesn't it?

[04:44] Austin: Yeah, it's something I'm faffing about and talking about every week.

[04:48] Ben: Claude, automate all the certificates. Thank you. Joel's roadmap. Okay, so you've been working on product. sales stuff with yeah and then the customer portal yeah so yeah when the sales milestones are a little bit more complex come up then we'll need to do more planning and we want to close the milestones as we close them because basically two are closed now already I think it's a good practice just to have even just a tick in the box on the system only like you know everything's happening which is fed back from your users and all that stuff yeah Good. Austin's roadmap, so sales and logistics, right?

[06:02] Austin: Yeah, sales project, logistics rollout, which is done almost. Just a few little bugs today where they were trying to run about eight different solves at the same time, and the server just fell behind.

[06:16] Ben: Okay, so there's a limit to the solves.

[06:18] Austin: To block concurrent solves at the same time because the server can't handle it. up.

[06:28] Ben: Okay. And is that our server? Is that your server?

[06:31] Austin: It's our server, that one.

[06:33] Ben: Cool. All right. Well, if we do need to do that, then we might need to open up our server if they're using it massively.

[06:40] Austin: Yeah, we can either upgrade the server to let them run more than one at a time and restrict to how many we want to run at a time, basically.

[06:50] Ben: Okay, cool. And that's something that needs the old hypercare to make sure that all the jobs are getting covered.

[07:03] Austin: there's nothing missing.

[07:04] Ben: Yeah. And if it's not?

[07:07] Austin: If it's not, I will send an email.

[07:08] Ben: Okay. You ran them as well?

[07:11] Austin: Yeah.

[07:12] Ben: Magic. Okay, good. And then you've also had the accounts fixes. Is that all behind you now?

[07:20] Austin: Well, there's two parts for that one, isn't it? The automatic posting is going fine, running every day. Part two.

[07:35] Ben: Great. Okay. And also you're doing sales, which doesn't say sales up there as well. So we've had it.

[07:47] Austin: Yeah. And the project management tool itself.

[07:49] Ben: Oh yeah.

[07:51] Austin: Still adding little bits of website, this meeting stuff that came up on Friday.

[07:56] Ben: So we really want to aim for that clean place.

[07:59] Austin: Where? working on is not contaminated by loads of other bits so we're a bit more yeah yeah so logistics is wrapping up this project management tool is almost wrapping up um so kind of free to do the sales and bits for accounts cool because i put a bounty on the first officially started

[08:21] Ben: We can put that down to phases, because the sales project is a four phase bonanza. So we have officially kicked off the sales project as a set of phases. Oh, that's a good thing. Yeah, so we want to be able to look at the roadmap in this meeting and say that that echoes reality.

[08:58] Austin: Yeah, I have to click on it now and see what it looks like. It's just, if we've not put dates on anything, then it's going to look a bit weird.

[09:06] Zsolt: Yeah, it did. Thanks for that.

[09:11] Austin: Yeah, yeah, yeah.

[09:12] Ben: Joel's got a phase one sprint and Joel's got a stock one sprint and that's it. And then there's the unsigned.

[09:20] Austin: Yeah, I think they're mine.

[09:27] Ben: so yeah so a bit of work just to bring that so then this would represent clarity of what we believe is going on the illusion of yeah yeah nice um meetings um okay crossovers crossovers those things yeah but not really you're doing a lot of stock stock yeah right now I was on logistics Good. Well, there's going to be a lot of sales crossovers.

[10:14] Austin: Yeah. From our chat with Nathan yesterday, it seemed a lot of work might be needed on the CRM as well. New things that they need, types of activities. Yeah.

[10:27] Ben: Yeah, so Jorg's part of the project, it does touch on the CRM, but it says we're not going to do a full overhaul there. That might be an extra... a couple of times about that yeah and and those those sorts of discovery things are going to point to a lot of we had this this guided questions with Nathan basically um it was about two hours of like brainstorming and understanding what's going on and stuff like that um and yeah there was another thing I put that in an email to you actually that um Nathan wanted um exhibition skin

[11:08] Austin: It might have been a firm decision.

[11:10] Ben: Yeah, it might have been a firm decision and clause been like, yeah, action, action, action, action for everything as well.

[11:15] Austin: Yeah, I can go back over it and say that weren't actually decided and get updated.

[11:19] Ben: I think the whole thing is that basically that is an action decision-making tool, right, for meetings like that, where it's actually what we're doing is we're aggregating all of the sales team's answers to get a sense of it. And then from that, we might do an action tool, if you like. So really the transcript is probably the most important.

[11:43] Austin: So that's not an issue.

[11:45] Ben: Yeah. Yeah. So those might be the side that we need to populate information from that meeting to an ideas bank for the submission portal project.

[11:55] Austin: Yeah. It's how I asked Claude to do the outcome show or what to do with the transcript. So we can do anything with it.

[12:02] Ben: Yeah. Cool. I think we just probably have to save up all the transcripts unless we're going to make our own discovery because it's quite an unusual activity to survey the whole team for all of the questions about it. of that, basically. Full transcripts, I think, might be more appropriate.

[12:21] Austin: But the transcript was pretty accurate.

[12:22] Ben: Yeah, I had a little breeze through it.

[12:24] Austin: It was like two hours of... Yeah, some of it will...

[12:40] Ben: really avoid that you can see that the conversation might not be labeled correctly and like and here's the questions and here's the answers to find out what other questions were asked and ideas and then like you know summarize everyone's at a big table like this is what they do this where there's consensus where there's not consensus these are the extra ideas and all that stuff yeah yeah and you can always listen back to it yeah but i found it very useful actually that conversation yeah um recaps recap so

[13:13] Austin: uh 83 commits uh the whole pm tool intelligence meeting thing that we're using now to record this meeting yeah yeah um mobile user interface in order so we could record the meeting properly on the phone yeah um your higher cell segmentation so i've done that work the insights pages and you know that little group of pages that i showed you the other day yeah which i'll be coming back to i think planner and also order the accounts, integrity fixes, auto posting stuff. Charlotte's been working on stock management, supplier inline editing, sales stock adjustment, audit and report, full quality checks, feature, failure points, photo grade cards, multi photo, quick fill, the IT Anything else you can think of there?

[14:22] SPEAKER_02: Yeah, I think that's about right.

[14:24] Ben: So is everything for you going through the talk as well?

[14:27] Zsolt: Yeah, I'm using it.

[14:29] Austin: Cool.

[14:29] Zsolt: Logging everything, tickets, everything's been done.

[14:32] Ben: Okay.

[14:33] Zsolt: So it's everything.

[14:34] Ben: Cool. Well, if we end up doing stuff in whatever, on the website and stuff like that, that is included in there, we just have to remember them, right?

[14:40] Austin: The website's included.

[14:41] Ben: The website's included.

[14:43] Zsolt: That's why you have the custom portal.

[14:46] Austin: Okay, cool. You've got the ESI project there. And I just... We've got initiatives, which are new projects, and then you've got the two existing... Oh, yes.

[14:57] Ben: Oh, yeah, yeah.

[14:58] Austin: So little dev updates and stuff going there.

[15:01] Zsolt: Tarans and Dior. idea whatever what he sent today when he realized that how the website working after seven years or six years yeah there's been no change in that way um i did adjust the uh the phone open i was uh often back and forward with darren and it was 7 30 uh weekdays and 8 30 weekends

[15:31] Ben: Well, we can choose not to say anything to anyone, and then what will happen is that Rob's not going to mention it. Hello?

[15:43] SPEAKER_02: Yeah, yeah.

[15:46] Austin: I couldn't really say no.

[15:52] Ben: Yeah. If people want to serve the purpose of whatever's frustrated them in the moment and change things, then we're going to fine. It's not the end of the world, but it's not the beginning of it either. Good. Okay. Night tick, you're higher. Action needed. What is a night tick?

[16:16] Austin: I don't think anyone knows anymore. It's been that long.

[16:18] Ben: Claude, you need to define what we mean by the night tick. Thanks.

[16:21] Austin: It says 2022 on there.

[16:30] Ben: PHP 8 in October.

[16:33] Austin: Done.

[16:45] Ben: Does this thing update the notes as well?

[16:47] Austin: It will update the outcomes.

[16:49] Ben: Okay.

[16:50] Austin: I can get it to update the notes if we want to.

[16:52] Ben: Yeah, I mean, it's not that much changes, does it? One or two things and stuff like that.

[16:56] Zsolt: I think I'll remove something and not do anything.

[17:01] Ben: Does it research the Google stats?

[17:04] Austin: It doesn't.

[17:06] Ben: Action point. Allow Claude access to the Google stats. It's probably good. Yeah, this is the same stats from last week then.

[17:14] Austin: Oh, yeah.

[17:22] Ben: um just don't tell me that that's true and tell me this is updated great just give me good numbers um website errors i think you had to check that the other day oh no that you didn't i got excited because i was looking at a different environment right and yeah you had to and um there's been no contracts converted today what the hell because i was like i was just like drifting and pressing buttons and stuff like no contracts converted like 3 p.m Linux Ubuntu.

[17:52] Austin: What was that about?

[17:55] Zsolt: I mean, when we changed the PHP, we changed server. This time it's now Ubuntu, isn't it?

[18:01] Austin: They're all on Ubuntu now, aren't they?

[18:03] Zsolt: No, they're not. The system was on EC2. That was on Linux.

[18:08] Austin: But now it's on Ubuntu.

[18:10] Zsolt: And the website. No, that's...

[18:13] Austin: I use my Kinect script now, so I don't even know.

[18:31] Zsolt: I've never been there, so I can't check.

[18:36] Ben: Pizza party. I think it sounds like you guys should arrange one in the time that I'm away. Sean needs to do a few 24-hour shifts as well.

[18:44] Zsolt: Yeah, at least five.

[18:46] Ben: So he can live his double life out in Thailand at the same time.

[18:48] Zsolt: I mean, that needs sorting and everything, so it's nice and tidy.

[18:54] Ben: I highly encourage you guys to get this off our meeting minutes. I think in that time I could have just trained up myself and worked out how to do it. No offence, guys. I know you don't want to do it, but I'm going to have to make you. Okay. covering it rewiring okay cool so i'm off the fourth to the 18th of august i think you guys should look at your projects and think about what things has been the bottleneck on which is probably too many things right um and let's let's try and um so the fourth is actually a tuesday i probably will work from home on monday just to help the wife pack and all of that sort of stuff probably tee up a few things before you go yeah especially

[19:46] Austin: I think to do more.

[19:48] Ben: Yeah. I think definitely killing the current projects and putting those things behind us. Right. So short performance needed on the PM tool as well. Yeah. I forget exactly what the milestones are, but some milestones, let's close that off. And then, yeah, let's look at like, you're starting to explore more complex parts of the sales project as well. And you've got the product thing. Let's look a bit further ahead and say, well, look, and stuff like that and what what do we need to do to coordinate with people and feedback and stuff like that with shandor away that might be a bit of a an issue so you know maybe we might need to sit down with justine and find out about her stock thing yeah or maybe we need to do whatever the next one and the sales thing is pick up the next meaty um milestone and uh and flesh that out in full And same for you.

[20:48] Austin: With the sales project, it's a little bit difficult for me to get ahead. I tried a few times to get ahead and not quite hit the mark. So I think we may need to pick something else out to

[21:08] Ben: thinking um that's true there's also milestones within jolt's one which is implicit that you guys will both be working on it as well in some way shape or form so we could look at that yeah because uh because the the project is laid out in a linear way both within the milestones and the phases as well right um and so we don't want to get too entangled and further ahead of from ourselves um next week uh uh intend to sit down with the sales team i said Not all of the questions like we did with Nath, just some of the relevant questions to them. But then that's going to need time to percolate and sit before we can come up with concepts and stuff like that, you know.

[21:48] Austin: I think Sophie's one's quite needed as well because she sort of deals with the growth of the customers, doesn't she?

[21:55] Ben: Yeah, yeah.

[21:56] Austin: So it's quite important we talk to her.

[21:57] Ben: Yeah, and I think as I expected through those questions is like, yeah it's all just done in memory and personality yeah um cool uh and Austin's off September 4th okay so I don't know he has a little bit of time before uh but only around the week here yeah it's gonna be no more than a week cool and then Joe just okay where you going yeah okay somewhere somewhere somewhere to get tobacco um and Yeah.

[22:45] Zsolt: And then just for December, but this holiday.

[22:48] Ben: Yeah. So you've got the August bank holiday coming up as well.

[22:52] Zsolt: Yeah.

[22:55] Ben: All right. Good. Any other thing else at all? Thank you very much. Thanks, Claude. Thanks for listening.
