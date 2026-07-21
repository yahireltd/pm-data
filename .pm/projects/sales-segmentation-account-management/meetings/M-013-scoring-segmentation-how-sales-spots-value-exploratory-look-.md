---
id: M-013
slug: scoring-segmentation-how-sales-spots-value-exploratory-look-
title: Scoring & segmentation — how sales spots value + exploratory look at the suggested processes (Ben / Austin / Nathan)
state: held
created: 2026-07-17T16:15:22Z
updated: 2026-07-21T17:09:58Z
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
  - key: meetings/M-013/1784653333824-meeting-recording-2026-07-21-1426-transcript.txt
    filename: meeting-recording-2026-07-21-1426-transcript.txt
    content_type: text/plain
    size: 68412
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-21T17:02:13Z
  - key: meetings/M-013/1784653544502-meeting-recording-2026-07-21-1456-transcript.txt
    filename: meeting-recording-2026-07-21-1456-transcript.txt
    content_type: text/plain
    size: 25347
    uploaded_by: transcribe-worker
    uploaded_at: 2026-07-21T17:05:44Z
calendar:
  graph_event_id: null
  ics_url: null
kind: scoping
version: 16
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
## Transcript — meeting-recording-2026-07-21-1426.m4a

_Transcribed 2026-07-21T17:02:13Z on-device._

_Part 1 of 2 (recorded 14:26). Speaker labels HUMAN-ADJUDICATED by Austin (611 segments, 352 corrected) — names are now verified, not voiceprint guesses._

[00:00] Ben: in that cool yeah so the purpose of this meeting for the benefit of the tape is to get a sense of what the process is although it's not well defined and documented I described it to the guys like when you hear a bunch of kids singing and none of them are hitting the right note and then you get an average and overall it sounds like harmonious kind of thing it's not quite the right analogy because really what we're trying to do is decipher a sensible process that's going on whilst suggesting a more distinct process to achieve updated aims if you like so yeah that's the purpose of these questions they're quite open And then we'll use AI to, we're not going to necessarily ask everyone everything, but we'll use AI to sort of aggregate the questions and give us a sense of that harmony. Does that make sense? Cool. You all right if I just keep off?

[01:01] Austin: Yeah.

[01:04] Ben: Cool. So on spotted opportunity, how do we recognize customers worth investing in?

[01:11] Nathan: So referring to what's that come through the car?

[01:13] Ben: Yeah.

[01:14] Nathan: Yep. So, uh, the multiple things I'll be looking at initially will be the company that's come through. No, you'll be looking at their email address. They're all going to obvious ones has events in the name, caterers in the name, um, stuff. If it's a name that I actually just don't recognize, sometimes it could say like at Gemini.

[01:33] Ben: Yeah.

[01:34] Nathan: I would usually look them up just because then you end up finding out that Gemini events or whatever. So you're basically looking to see if their industry is, so that would be the first one the second part of it I would be looking at the venue so and that's dual purpose that could be a venue that we don't go to that's something that we might be interested in new business going to go or it's like this it could be a venue we go to but why is this person having this event there you're looking at the the postcode for the venues are not necessarily the customer venues yeah when you come to some address address yeah yeah yeah yeah not their address so a good example was the b and q did an event with me last summer um and that was at uh one of my venues but then when i started emailing this guy it was like b and q events so b and q if that came through you won't think of anything of it but they have an event side it that they're doing events probably in london you know they might have free events in london so so yeah that's what we do start off with the actual uh the client who they are are they industry if they're not industry um then look to see where it's going if it's just going to like their offices it's probably nothing that interesting if it's going to a venue it means even if they might not be an interesting thing yeah the venue could be or they're a company that is not industry but they do events

[03:01] Austin: Sorry, what about keywords? Do you look for any particular keywords in the company name or the URL?

[03:06] Nathan: Yeah, so that would always be, again, it's like industry-led, so events, production, catering, there'd be quite a few. Those three would be agencies. Obviously, agency is quite broad, but a lot of the time, agency usually decides to...

[03:24] Austin: There'll be stuff that's within that are useful.

[03:25] SPEAKER_04: Exactly, yeah.

[03:25] Nathan: So it could be brand agency, which again, they're interesting. So yeah, definitely URL.

[03:30] Ben: So you're coming from a perspective of mostly industry-focused, where they're most likely to be our biggest customers as well. So what about corporates that are... We get things like schools and film companies and...

[03:46] Nathan: yeah so yeah actually to go off of that there's multiple things so two more of the things so one would be the size of the order scope of the order as you said a school yeah so It's rare that if a company's doing something large, it kind of plays into the venue stuff. Like B&Q, if they were doing an event and it was at a park I'd never heard of, that's still someone that could likely do these quite regularly. School, obviously, is a perfect example in terms of they're doing loads of folding chairs, they have a need for it. So usually size would be a thing. If it was a corporate and it wasn't very big, then it wouldn't flag up. But what would flag up is if there is a lot of the same domain. So maybe it's the first quote that they've got. four contracts i would at least look in to see what that is now that could be it's actually just one other person and they had one event yeah it was hair delivering collection and 10 quotes right yeah but if it's not um obviously you take out what gmail's about but usually there's need there again that could be not very high value so i do sometimes find this and you look it up and it's like an architect company that must just get glasses here and there but they have ordered from then but if the question was how do you identify it that would still be part of the identification process cool and okay so there's a there's separation yeah because we're also interested for the company's sake on

[05:25] Ben: ones that don't stand out like golden nuggets, like the architect that might have hired glasses for their quarterly business meeting or whatever, right? Because that could be a decent lifetime value if you add it all up.

[05:37] Nathan: They might not know that we do chairs and that.

[05:40] Ben: Exactly. So in either instance, right, so once you know a bit more about the customer, how do you approach that differently?

[05:50] Nathan: Yeah. So obviously I don't deal with these anymore. So if I dealt with these, this would be something that I would ask them. Yeah. It depends on which area we're focusing on out of those ones. If it was a case that it was, you know, the one, the small one, say it was the architect person. Um, need for those kind of things but if it's like yeah we just need glasses for some events that we do you kind of figure out so you'd be talking to them there are some places where you can actually look it up but like you can go on their website and then they might do have like the events that they do that gets into the weeds a little bit um but then if i use the b&q as the example yeah once i started contacting him in his signature it said b&q event manager um and then you go from there okay so what kind of events do you do yeah okay but exactly yeah um because we have an opinion that they will let you know if you haven't confirmed with what you think is correct yeah so obviously when i was i don't even delegate the quotes now when i was delegating the quotes i would put in some there'd be two parts where i would put in you know in terms of closing so say if i was showing this helio i'd say these are the things that i would usually talk about in this quote then also i'd

[07:20] Ben: about the qualifying yeah what they do so to your mind what would stop us from it's clear that if you've got evidence that you're an events company or i've seen this is your 15th order with us right that justifies a direct qualifying question um but what would stop us generically saying is this a one-off event i don't think it you would look seem stupid if you're talking about ai you'd seem stupid if it was a wedding

[07:50] Nathan: for other people, for actual like a human, then for them to ask it for every corporate thing wouldn't be an issue. I know that obviously this is the wrong way round, but Furniture UK, every single order, if you order from them, in 10, 11 months time, you get an automated thing to say.

[08:13] Ben: and it's every single time yeah so for the purpose of the tape just underscore the point that our head of sales said it seems to be nothing to lose to ask is this regular events so in the instance where it looks like a wedding because it's middle of august then it's a shibari chairs for example right um But it could be a wedding planner.

[08:33] Nathan: For sure.

[08:33] Ben: But, you know, they're using Gmail because they're a small business and stuff like that.

[08:37] Nathan: Also, it could just be a party. It could be that's been their birthday, isn't it? Their birthday. So there is an element, yeah. So are you referring to AI asking this question?

[08:45] Ben: No, I'm referring to AI assistants to guide the sales process. That would be useful. Produce useful outcomes, basically.

[08:53] Nathan: Yeah. I don't think there's any issues of asking. Realistically, one part of the sales process think oh this is a wedding clearly a wedding i'm not going to ask are you doing a repeat event yeah yeah i also think as you said the first question one of the first questions that people should be asking is what what kind of event is it so that kind of preludes that because if they say you know she said it's a wedding or they could then say we're on a wedding planner yeah yeah i think also asking company information um you know what type of company it is quite quite valuable um in terms of what we've been looking at the segments are very important um so like if it's uh And that gets really tricky because they would be very, very different, I'm assuming.

[09:47] Austin: You'd have different outcomes for them, yeah.

[09:49] UNKNOWN: Yeah.

[09:50] Ben: If you had to put a number on it, what percentage do you think of the non-golden nugget type ones we do actually have meaningful open questions with the customer about their lifetime value?

[10:03] Nathan: I wouldn't say it's very high. Yeah. Yeah, I'd say it's quite low.

[10:05] Ben: Yeah.

[10:06] Nathan: Yeah, I mean, from the... Because especially the inbound, even though obviously at various points we've tried them to get a lot more, and Ray was very good at it, but now I think even, I don't know how much that's even going on now, to be quite open and ask these questions and identify, you just slip back into, without prompts, you just go into a bit of a money-making mode, especially at that period, you know. So, yeah, I don't think we're very strong on that at all. I actually don't think it's even we ain't got the time because if you actually attribute the time to it it won't take that long I think it's brain space the wellness meeting and you're talking about being in alpha and beta brain space, once you are just ragging through it, you're not thinking about that, you're just thinking about closing this deal. Especially, again, it's slightly adjacent to this, but say our reviews right now, we're getting reviews left behind. And that's because there's an incentive to £10.

[11:27] Ben: forefront of their mind yeah but they don't have that incentive there is an incentive people grow the company and you get more sales and more commission therefore but but yeah we are looking to make it more direct in fact in your inbox will be um the draft sales executive role responsibility which is basically about this whole thing that we're talking about here so um Okay, so anything that would stand out as something that would disqualify someone, a customer, that's not interesting?

[11:59] Nathan: Again, as you said, anyone technically could be a... It depends where you're looking for. Could they order from us? nugget are obviously two very wide yeah polemic things um and realistically 99 of our clients will fall between this range yeah like the one percent that literally will not use us again how could you even quantify them because technically if i've used you one off in 2000

[12:28] UNKNOWN: 21 for my barbecue I might use you again for my kid's first birthday yeah so technically there everyone has got one that's what he said about a wedding you see if someone's wedding they still might use you in five years time yeah so it's about ROI really on this stuff so first of all we want to make sure that salespeople are dealing with people actions that are going to have more output right and it's about statistical understanding of right well if you had a barbecue then you ask a cohort or only X amount likely

[12:58] Ben: to repeat and stuff like that so uh so probably to defo when you think about what we've done in terms of uh the the thresholds and before that the quickies thresholds and stuff like that you know if they're out So we're basically taking that same concept so we can cut down that sea of quotes a bit more commercially sensibly and bringing the lifetime equation into it is basically what we're trying to achieve, I think.

[13:27] Nathan: Yeah, the way we're looking, I know we discussed this briefly with, I hope it wasn't that briefly, with Axel, was say in three or four years' time, what our revenue would be a year higher and, you know, say it's 50 million, for argument's sake. everything that we do, everything that is technically managed will step up. So at the moment, we've got people that are silvers that give us 11 grand a year or maybe 8 grand a year. That, when you're making 15 million, is not actually, you know, you're going to need a lot of them to make 15 million. Yes. So now, obviously, we've spoken about the incubation accounts and, you know, maybe that starts from, I think it was maybe we said 3K. And then after that, you have all the yard memberships. So when you ask the question in terms of How many resources do you put on every single call? In my mind, if we don't believe there is a chance that they would be at least an incubation account, then I think you're starting to spend resources on...

[14:27] Ben: realistically the system books in half those jobs anyway and then you're talking about your membership yeah you're certainly right i think the idea is to is to push the human up the value chain and get better in the systematic stuff um but it's also to be effective at all levels as well um so if you're using an analogy of a gold miner right well there might be a team of people with giant diggers doing a big thing and then there might be a bunch of a bunch of like you know people that are kind of impoverished that are scraping around with with sieves at one end to try and get bits and bobs to maximize that gold mine if you like um but also by virtue of learning that trade in lower values right it stems the expertise to uh to do that so understanding how to incubate a small account and be trusted with something small you can gradually grow that that portfolio for sure

[15:18] Nathan: So where I sometimes get a little shook with this is say it's like a corporate company and they're doing a £400 quote. And they're like, yeah, we might have a few, two or three times a year. Again, we're talking about maybe £1,200 revenue over a year. That wouldn't even be an incubation account, but they do have a requirement, an ongoing requirement. How many resources would we want to dedicate to that?

[15:43] Ben: definitely minimal but we want to make sure that they have the tools the salesperson has the tools to nudge them into whatever the next level is even if it's like I'll send you a link and you can sign up and add your people that doesn't necessarily mean that I'm going to do some proactive work on you I'm going to be responsible and sit in meetings talking about have they gone up or down and stuff like that quite where those thresholds lie we don't know yet we'll start reasonably and then we're going to adjust okay what sort of things might look interesting on the surface but then later prove to be a dead end so with a new lead that looks like a possible goal of a nugget it's crazy I'd say this sometimes

[16:35] Nathan: to be honest every industry for certain reasons I could say small caterers we think oh god this could be really good and maybe not but I also think small venues they are very hit or miss the reason being especially I've noticed we've discussed this before i had a meeting with a company called unit x they've got two venues you know there's a decent amount of money goes there it's not crazy but you look at their supplies list and they've got 12 caterers on there they've got four event agencies we're one of four furniture suppliers and when you say that you think god no matter events you do one of four furniture suppliers this must be really good but then obviously so many of the furniture orders go through the caterers so you think okay well this is great it's a dry hire venue this is something that we can be we're on for suppliers this everyone's patting themselves on the back um but you actually end up don't making a lot of money from there because it's caterers that whole piece as well exactly yeah depends on the type of event um but yeah or then maybe the event agency that's using it will then bring in whoever they're in so you really do need to it's not a negative because if you were in with all those caterers then great yeah but there's more obstacles so yeah i think the

[17:49] Ben: So that's like a share of wallet kind of thing, if you like, although it's a venue doing referrals or the caterer in this instance. And then there's obstacles to overcome. So whether that's a person, that's the decision maker, or it's a bunch of people at different organisations that are decision makers behind that. So then where that ought to sit is something like this has an ex-potential as an entity.

[18:13] Austin: It's difficult to...

[18:14] Nathan: and then this is the accounts plan to overcome that if you like right yeah but that accounts plan that needs to be you have an account plan on every single caterer in there yeah every single production company because technically you've won yeah you actually don't really need it's hard to do much more with the venue because the venue like you and they said we've got surprises they can't do much more yeah so actually the account plans is with all the other companies that go there yeah that's basically tricky obviously your question probably wasn't more aimed at those kind of things it's probably more no no it is good to know i think the way to navigate that as well is is is to understand the performance in different segments for different types

[19:08] Ben: there a particular segment of carers where we do better with and why so so part of what we're going to do is have that learning organization where we do have that time to sit back and look at these results in in succinct ways and say how do we how do we change this yeah i think segmentation is brilliant because i'd imagine there's probably a hell of a lot of venues in london that we're on a sort

[19:36] Nathan: seems interesting and we're actually on the supplies list from years ago and we're still on it yeah so yeah that would be a very interesting segment yeah cool so if someone

[19:52] Ben: turned out not to be an opportunity today what information should we retain so we don't start from scratch if they return so you have a meaningful conversation with them but nothing comes of it they don't go ahead for the order etc etc but if you were picking that up again from the next sales person who'd already done that what would you want to see there to save you time and to give you to become yeah that's a good point i mean that's a kind of sometimes

[20:16] Nathan: the dashboard can be because the information if the salesperson has done it correctly would be on there yeah how much information they put on there can vary wildly currently dashboard so it would be you know CRM yeah so we're adding customer intel knowledge into the won't actually relate to this. So again, if they are asking, say if I, this is what I just said to myself, if I called, you know, it's Tara. When the home ranger, he's like, oh, sorry, I'm sorry. Yeah, I'm going to meet with Ben and Austin.

[21:00] Ben: You need me now?

[21:04] Austin: All right.

[21:06] Nathan: Um, so yeah, so if I was doing it and I was calling this client and, um, let's just say we, I didn't know they weren't industry, they were corporate and I don't know what their kind of, um, their gig is. And, you know, I asked the question, like you do regular events. If they said not really, I won't really mark that anywhere. Yeah. And that's from myself. We would probably do more notes than a lot of the others. So I would say a lot of this isn't marked, but I do think where we fall short on the system, that's really hard to get everyone to use it. But I know we started with the, what kind of company they were. yeah so um if anything we need multiple tabs like that so if you guys are thinking the way you're hearing this is that you're kind of bait uh creating an ai script for a salesperson they open up a quote and they can kind of there's certain questions that you'd want them to ask it's not quite a script it's just pieces of information that we want to capture about the company um that are useful to uh segmentation and uh scoring perfect if that's the case yeah i think there's got to be multiple drop downs as you said what type of company they are so if you do And then one of them could be regular events, ad hoc events, rare events, something like that. And yeah, they prompted to ask that question. They're like, yeah, this is just a, you know, one could just be a personal event.

[22:27] Austin: If you ask them that, do you do regular events? And they say, not really. You can't record that. That means someone's likely to ask that question again if it's not recorded.

[22:36] SPEAKER_05: So it's a good question.

[22:40] Nathan: a hundred percent in a way again if we're talking about uh uh time you know resource resources i won't put that down just i'm like they're not really going to cut back in contact like i'm not going to put it down there no one's going to read it like you know if anything i'm spending more time and obviously we don't really have a perfect place to know this so i'm just putting this in my chase dashboard yeah it needs to be centralized and really easy to record yeah so i do like the idea of um you know obviously as you said type of company but

[23:13] Ben: you know events industry regular events ad hoc events yeah rare yeah no this is a good conversation because the sort of ulterior motive to having this conversation other than um picking up uh bits and bobs is is to develop that sort of shared sense of what that might that process look like what where are those gaps as well it's like yeah a lot of our information is uh to use a fancy word ephemeral it's gone or you mentioned Sophie who's doing business development might have got some supplies years ago learning same would apply if you know if an account manager left and they picked up the last person's account where does that like what do we actually know about that account where's that history and stuff like that I think those account plans are a big step towards that because you can read the account plan this is our history is an actual section in there and stuff but yeah so how do we develop a system that guides the process and captures the information. And certainly for me, I'm leaning towards like qualification of customers. That's a big theme and basically serving the database and the knowledge of the company as well as part of the...

[24:29] Nathan: processed as well yeah um two things on that one i think was just going off there and you know supplies this is quite an interesting one where i know that we can like add commission and add discount on at the moment but it's like on the customer profile second page at different areas realistically on that first page you need to be able to see you know as we said type of company how regularly they do events are we on the supplies list that's a question that

[24:53] Austin: that information anywhere discounts commission you know maybe there's a couple of extra stuff yeah i've been doing a lot of scoring and placement so we have a lot of that information already available now and done by scraping the company websites all these customers so put that anywhere you need to see it yeah because i think that'd be good for anyone especially if someone was new taking over um there was no point i was going to say um the thing is with that though we need to if it's uh done by

[25:24] Nathan: yeah make sure you're speaking to the right person that's actually reminds me of my other point um them doing events and them requiring our services are two different things so there are companies that are i've spoke to companies that are production companies and they're using that they need to show this event and i'm like okay so you production companies are regularly furniture and they're like if i might as

[25:55] Ben: a good caveat to do yeah if you want to ask in two separate questions i actually think the better question to ask rather than do you do many events is how regularly do you require yes that's your services so that's like a situation where look you've the salesperson's given some information to effectively disqualify some of that potential of that customer if you like right and um and then that sits there um as a sort of captured truth if you like like a rescore of that customer to down then it's rescoring to downgrade but then things going to change as well right so say say it says potential million and then salesperson says 10 grand right and then you know more stuff comes through and then that might trigger someone to have a look at that and be like okay we need to redo this it's not been done for two years um i do think um yeah so just on that yeah so like you know rather than like do they do events it's regular need ad hoc need no need rather than

[26:47] Nathan: yeah the events but um in terms of like revenue potential revenue i know that a lot of my account managers struggled at that part of the strategic plan because it is very hard hell hard like the way the way i was speaking to them is i kind of just did it on a basis of how many events you think happen there so then they go on okay well likely is they got two a week or something or whatever maybe quite period so let's say they do 50 events which you know what give or take and then what do you think the average furniture order value so say five grand and i

[27:31] Ben: yeah so you've got like a segment benchmark and then you've got underperformers overperformers why are they underperforming and then on the more strategic accounts you need to find out a little bit more about their budget and you know what share you actually have i think yeah i think market share is really important that's what i was speaking to a lot of the account managers about because obviously again you say like bdc say there's a million pounds you know it goes into it on furniture and catering um

[27:57] Nathan: We are on, I don't know, 200K. And realistically, the most you hire is going to get over the next three years could be 600K. You're never going to have the full pie.

[28:08] Ben: Yeah.

[28:08] Nathan: So that's where it gets rich.

[28:11] Ben: Yeah. It's a sort of fiction, isn't it?

[28:16] Nathan: It is, yeah.

[28:16] Ben: The customer themselves probably doesn't know the exact answer anyway. That's a future prediction. So when a lead comes in that is a hot lead, golden nugget, What is its pathway, broadly speaking? Like what happens to them?

[28:32] Nathan: So that would be straight to new business. Depends on the situation. So it depends if we already know that they're a golden nugget or not. Because if it's someone that I, again, I don't do this, but if I pass this to Helio and I'm like, this is a really interesting client and I know they're going to be a really interesting client, then I might get Helio to do the quote. in contact with my new business team. Okay. There was something that happened recently where Helio did a quote for Compass Group, who are obviously a humongous group.

[29:08] Ben: Yeah, that's good.

[29:09] Nathan: Yeah, completely wasn't aware of who they were. So, you know.

[29:12] Ben: Yeah. So that'll be different.

[29:14] Austin: Are you getting minutes to pay in the end? They were having trouble with their card the other day.

[29:18] Nathan: Yeah, I got them to ask everybody to sort you. They had four different cards, it seemed a bit weird.

[29:22] Ben: Yeah, they all fell out, yeah. Okay, so the sales execs will be leaned on to do some of the legwork on the early qualifying and then we'll pass you over.

[29:30] Nathan: I think there should be at least a small amount of qualifying done by them.

[29:34] Ben: Yeah, yeah.

[29:35] Nathan: Because what you'll often find as well is that the person that they're speaking to might not be the best person to speak to you Sophie's time calling that person when really we don't have a particular anxiety that if we don't put our best people on it now then we're going to fuck it up it needs to go straight to them potentially not I think obviously it always varies but if they're coming in for a quote they're not thinking about if I was myself, if I was working for Compass Group and I came and I was like, I need 200 banquet chairs and this person calls me

[30:27] Ben: quite over and then sometimes that would be probably better for an after sales call wouldn't it yeah sometimes it is done after and before so they've got the questions for um a small organization even like a you know say cater london or something like that where it's more likely to be someone not far removed from the top right um It's very different to the questions that you'd ask in a big organisation because that's really more like proof that, oh, by the way, we've been serving five of your branches for two years, Sophie's saying, and now I'm going to go to a bigger decision maker or something like that. So it's different qualifying.

[30:58] Nathan: You say not far removed, but even Kate of London, the ladies who obviously would sit in the same room as the people who are probably managing it, they're still not going to be the people to talk to about that because they're still getting told who's That should be one of the qualifying questions. It's kind of your last qualifying question. You qualify them. You're like, cool, this is it. I want to put you in for my new business. Obviously, I want to make sure that... Would you be the best person to speak to about suppliers or would it be someone else? And then they usually go, it'll be Siobhan.

[31:32] Ben: Yeah, we'd love to open an account with you who's got the authority to sign the forms or that sort of thing. Cool. All right, so... and whatnot but I mean for you it seemed like a sound process then if the golden nugget is flagged because it's detected it's compass group that goes to Sam and Sam decides whether he wants to deal with it or assign it to Sophie to Sophie or to Helio or yeah I mean Sam I don't know how much he gets involved with the building of and chasing of quotes now I'm not too sure I know he does like overflow but I don't think because he used to do the high value quotes that were not account managed however I think we're

[32:20] Nathan: get involved with too much but yeah it would be up to he would be the nervous system to delve out great okay cool the difference for that though is obviously because we have quite a different a few different formats of people getting close so someone actually could just call up and speak to helio and helio's dealing with it i don't know if that compass group came through the car or if they called they could have easily have called and he's just built it and didn't know and therefore sam wouldn't see it until after yeah the fact cool good

[32:47] Ben: Qualification and understanding. How do we decide how much opportunity exists? That's the purpose of the questions. So what questions reveal that an opportunity is larger than it first appears?

[33:02] Nathan: I think obviously similar things that we said, you know, what type of company are they industry led and how regularly they require furniture is purely on repeat business.

[33:13] Austin: It's really like...

[33:18] Nathan: value yeah yeah uh obviously on a smaller scale you've got upsell cross sell on that particular quote i think we're talking more about opportunity in the future so yeah okay so there's not there's not instances where you're like oh actually i spoke to that company like two years ago wow they're on like 15 grand now Not really. I have to say you do kind of know early on. It's like when you meet a new partner, you're like, you know what I mean? You're not like... Yeah, so I wouldn't say... There's not many people that have really shocked me.

[34:00] SPEAKER_05: Mm-hmm.

[34:01] Nathan: But maybe they have. I mean, I don't have eyes everywhere.

[34:03] Austin: You start to pick them up and they grow naturally anyway. Once they have the first order, you may not know they're going to be big. And then by the time they get to the fourth one, then you realise, oh, this is actually doing quite a lot better.

[34:14] Nathan: Yeah, I would say that's a good point. I'd say more so from what you said, there's not many people that I'm like, I don't think these would be anything and then they're 15 grand. I'd say more of it is like, I think that these could be 10 grand and they actually end up 50 grand.

[34:26] Ben: Yeah.

[34:26] Nathan: That I think, you know that they're going to be half decent. You just don't know actually how...

[34:31] Ben: big the opportunity is at the end of the day the proof is in the pudding as well and it and it's hard to find out so it's mine and austin's job really to work that out we're looking in your best segments there's outliers in a negative sense that are not very effective right and maybe there's some data imprints of those that might disqualify them from being your best if you like right yeah know places like schools like abelio is one of our biggest outliers right it's a railway replacement company it's given us like probably half a million quid over the years or something right i mean that's a perfect example of the things that we're talking about because say abelio weren't on our system and they came through with crowd control barriers yeah right now one of their quotes

[35:09] Nathan: There is a high potential that we would turn up our nose.

[35:13] Ben: Yeah.

[35:14] Nathan: You know, might just chase it, try and buck it in, and that's it.

[35:16] Ben: 100 crown control power is 11pm in the heart of Essex.

[35:19] Austin: Exactly. That's a hard one to put that value in, though.

[35:22] Nathan: Yeah, it's true. But what we're saying, what we were talking about there is, you know, it's not necessarily the value of the job. It's we would hopefully figure that out with this questionnaire.

[35:31] Ben: Yeah.

[35:41] Nathan: Our current way of doing it, we would lose that. We would not pick them up.

[35:44] Ben: You don't want to optimise for outliers, right? In many ways, you don't want to because then that means you're dealing with everyone. So if you've got to go to extra lengths to find are you the next Abellio or not, people are probably not. I think it's striking a balance. But yeah, if you've got a sturdy process, that's like... corporate then we're probably going to ask you a question if we get a chance basically and then you know i want to actually look if this random railway company starts to hire five times from us in a year then we can see that and we can do something about it yeah there's only so much you can do for their first quote because you Yeah.

[36:34] Nathan: It's tricky though because obviously, say, I actually don't know the value of their orders, but say they get like £500 worth of crowd control barriers and it goes to Essex kind of thing and it doesn't look that good. When you say realised value, if that order doesn't go well, they may never come back again.

[36:49] Ben: Yeah.

[36:49] Nathan: Where there is still a lot of value there. I know that they do a lot of the after events now, which is really good. But would that even be one that they after event? like that this process the qualifying needs to be good if they've said that they do regular they have a regular need then i feel like that realized value is kind of there already so yeah yeah if we properly qualify the customer there's argument to say that there's something like a potential value and then the proper qualification might change that potential value if they say yes we do regular events we do this 10 times a year then maybe there could be some yes and they could then say they do regular events and then

[37:27] Austin: that should flag something as well. These have been sort of scored too highly in a sense to downgrade them.

[37:33] Nathan: I really do think we should change the wording to be regular needs because I think there's some people that do events all the time and won't have a need. So a regular requirement of furniture or of Yaha products, that I think is the key rather than a regular event.

[37:47] Ben: So that's the killer question to say, are you going to be a lifetime value customer to us?

[37:52] Nathan: Basically, yeah. Look, it's one thing that where it's going to get a bit grey is Sam initially started writing these guys to find opportunities. And, you know, Ray was very good at picking them out. I don't know much too much about Frank with it. But Helio was sending stuff over like a Turkish barber or like a dish like a bathhouse. And, you know, he was like, oh, they said that they do require stuff regularly. And it's like they don't. But maybe this guy's just said to him, like, yeah, yeah. I mean, it has to be an element of the person.

[38:26] Austin: That's the question, but the other things are the industry and the segment and the company sides.

[38:31] Ben: Yeah, that's a good point. Yeah, so that's a good example. So look, Ray's got an intuition so she can sort of run through this sea of quotes and spot the golden nuggets naturally, right?

[38:40] Nathan: She's been in the industry.

[38:41] Ben: She's been in the industry. Yeah, yeah, yeah. Whereas for everyone else, it's all just data that they're coming to terms with, right?

[38:47] Nathan: Yeah, but that's like doing this, I'm conscious of how much, because especially, you said you sent me an email about it, but there's some kind of incentive for a sales executive to get in, we could really pile it up. Like Sam and Sophie come in the next day and there's a shit tonne. It's like, this could be, this could be, this could be. So your thing, would it be valuing their potential growth as well? Or potential money?

[39:14] Austin: It's basically a score. the company size itself.

[39:29] Nathan: So Abelio actually would score horrifically low, even on your thing.

[39:36] UNKNOWN: Just because I am.

[39:37] Ben: So, you know, the purpose is not to do, to overwhelm people, right? If anything, that's kind of what we have as a,

[39:51] Nathan: so yeah enough i actually think we're talking about something completely different than issues that we've had in the past i actually think the sea of quotes is the thing that we handle the best right now but as in the sales executives in terms of like chasing the money yeah the sea of quotes has been the best i think what we're talking about now is opportunity of the quote oh yeah yeah uh which obviously we've never been perfect at yeah i think we've conquered the sea of the actual service team oh that's good it's just so that they're not overwhelmed by it they're still structured and on top one book five yeah and all three of them were fine no issues yeah yeah so it's a resource thing as well and focus thing right um yeah but obviously now if we're talking about changing the role and you know they have incubation and they have to do quite a lot of this yeah and that means the resources might be stretched busy period yeah yeah so the assumption is that working on the lifetime value is going to produce more yeah money for us basically which is it's unproven because we're already kind of doing it is is the thing but it's just being able to

[40:53] Ben: see that so then you can feed that back and say alright yes look that works and you know this person is selling well to these and not so well to these and that sort of stuff.

[41:04] Austin: How do you spell Abelio?

[41:11] SPEAKER_05: A-B-E-L-L-I-O.

[41:16] Austin: They might have a funny e-mail domain.

[41:19] Ben: Oh, they're that account company, aren't they? Yeah, but they did, from what I remember, they did have a low score in the street fit and whatnot, and they definitely outperformed, you know.

[41:30] Austin: It is an outlier, though.

[41:30] Ben: They are an outlier. So we're not going to optimize these videos. There's no one else in our top 10 customers that is like them, right? It's just an unusual relationship. They could say, but I'm going to... I mean, if anyone ever looked at how much they're spending on crack control barriers at their place, then they'd buy some or like, this is not for granted.

[41:55] Nathan: That is correct.

[41:57] Ben: cheap on crowdies people can get crowdies for like £2.50 yeah I think if they genuinely have to have it gone at that time then it's good value because that's expensive for everyone anyways okay so I'm skipping a few questions how often does that question uncover something worthwhile and what normally prompts you to ask those questions because we kind of covered that quite in detail over a large conversation what information helps you understand whether someone could be a valuable long-term customer again it's a sort of What info would help you understand whether someone could become a valuable long-term customer? Oh, yeah. What would actually help you if you had that information? I think that's what that means there. Like if you open up a page or your salesperson did. Oh, and you... Yeah, yeah.

[42:42] Nathan: So understanding before you qualify it, what your thoughts are. Yeah. Yeah, I mean, I don't really know how the system could help because... realistically as the things that we mentioned it's the keywords in the url what type of company they are how big where's the event going and how what the value of it is and you know if there's a lot of similar orders on the same domain that's kind of already in your face it's up to the salesperson being switched on enough to see it

[43:11] Ben: But the AI brings in evidence like, look, these guys run free annual shows across the globe and that sort of stuff.

[43:21] Nathan: Well, I guess that's what it's breaking down then. If that's the case, what type of company it is, what type of venue this is, is this just a house or is this actually a designated event space? Obviously, they already see the value. So, yeah.

[43:35] Austin: So, Golden... good signals there, wouldn't it?

[43:47] Nathan: Two really good signals. I mean, the venue, there's not many... I did a meeting with New Business this morning and they're looking at some venues that we can target that we've never really gone to. It's slim pickings. We kind of know all the venues now. So, say there is a big job at Magazine, the venue would already be kind of known, but you would instantly think that corporate event, if they're doing an event at Magazine, it's either going to be an annual one or it's...

[44:14] Ben: usually an annual one like or you know they do it regularly so yeah basically a big venue is just a qualifier of them being a good person yeah so do you think there's um so ai can go and look at their website and research various informations about them and whatnot right um but in terms of internal data of like the items and the postcode how how much of a correlation do you think there is between that and good customer outcomes

[44:46] Nathan: postcodes especially you know if anyone's doing an event at magazine or doing a business science center or excel they are a company that do events tell a lie ones with like bdc anywhere i think that's an exhibition you could have someone who's just an exhibit stand that would be okay so order size over something like that plus plus then you would yeah would look like something interesting yeah yeah then in regards to types of furniture it could be interesting because obviously folding chairs a thousand folding chairs which to be honest in five years time i don't know if we're going to be doing those jobs last place actually if we have a bigger space we might as well keep the folding chairs but you know so folding chairs is the the dog item so usually a folding chair will not be a good signal but if there's a black lush sofa on something black modular sofa it's quite rare that people would get those products that is not in the industry yeah could it happen for sure but it would be quite a strong indicator so not necessarily value of the order i mean valuable order is obviously one yeah we need to flag those.

[45:53] Austin: We can maybe start off giving them a way to flag the items that they would normally look at and then save that information and then pick it up.

[46:01] Ben: Claude, we'd like to highlight the VIP items. That's a bit of legwork that needs to be done to support this.

[46:08] Nathan: Thanks, Claude.

[46:09] Ben: Cheers. Okay, so assess potential versus current spend. Let's just take into account that you know well. Right. How is that done? The potential spend versus the current spend.

[46:25] Nathan: So, yeah, so as I said earlier, basically, I'll do quantify how many jobs So BDC is a perfect example. It's 52 weeks. They do probably, on average, two events there a week. Some weeks, like this week, there's nothing going on, but then some weeks there might be four, quite a period, I think. So if you average that out at 100 events a year, and then you say every single event maybe has a spend of, an average spend of, let's say, 10 grand, then you've got a million pounds.

[47:03] Ben: So it's just the extrapolation from that deep intel.

[47:07] Nathan: Yeah. I do, yeah. Quantity of events, average order value.

[47:13] Ben: Yeah.

[47:14] Nathan: And when I do that, I do factor in... as you said the knowledge is quite important because i know that a business science center have got tables and chairs themselves so there are some events where literally they're only like two grand might go there because they use all the bdc stuff um so so there could be a number of different methodologies to assess the potential we spend on

[47:34] Ben: on one that's manually calculated. So for example, you could go directly to the customer if you had a great relationship with them and they could tell you, look, we spend 200 grand with Furniture High UK, we do this, you know. 100%, yeah.

[47:45] Nathan: So it's something that, yeah, Tara and I have discussed before. I remember when we met, so you know Mike Kershaw's, what's his name? catering guy richard groves yeah richard groves so we tell him i had a meeting with him and he went to jimmy gatsby at the time and he would go to this venue uh i think it was the one in camden roundhouse and he would go like okay so this is the amount of orders that we've had how many events have you done who would we've what percentage of money have got we got kind of like and he would ask that question so that is definitely something um the only thing is the certain places each furniture company they can tell you the amount of events they've had probably whereas someone who orders directly themselves like a caterer cater london will be able to look at their spreadsheet and look how much money has gone into

[48:46] Ben: So you think it's rational to see that as a spectrum of trustable data? So, for example, at the very top, you'd have someone show you their accounts and what they spent last year on furniture hire. And secondarily, someone telling you, thirdly, extrapolating, and fourthly, guesstimating, and fifthly, a segment-based potential score from the AI, something like that. Yeah.

[49:13] Nathan: Yeah, I mean, typically, if they just tell you, they could be lying. So, yeah, I do think, yeah, actually seeing it. The odds of you actually seeing it is, like, very rare.

[49:23] Ben: Cool. How are you doing for time, Niamh?

[49:26] Nathan: Probably got another 15 minutes, if that's okay.

[49:30] Ben: Right, cool. Let's keep it punchy. Okay, sales process and relationship development. So we want to find out about what happens after a lead looks promising. So in some instances, Helio might do some pre-qualification and then Sophie follows it up or Sophie might follow up an existing order, you know, recent orders and then go and contact them. Is there a specific process or is there a different pathway that might?

[50:07] Nathan: Yeah, I've covered both of them there. So yeah, the first one would be a flagging from the sales executive that dealt with it. Yeah, on a page made for her. I might be wrong, but I think there was a page that was made for her. Okay.

[50:34] Ben: I know that she... Yeah, there was, yeah, by Thanos.

[50:38] Nathan: I don't know how much, obviously, Taryn's kind of set them up on another path. So I could ask her to see what she still does on that front. But yeah, there was a page.

[50:49] Ben: And Taryn has an aura in this as well. He looks at customers and asks questions.

[50:53] Nathan: 100%. Taryn would be a bit more direct with the person that dealt with it. But yeah, I'm sure he probably flags it. Obviously, he flags also stuff that he sees, you know, LinkedIn, stuff that's not even on our system. So that's another way of getting leads.

[51:06] Ben: Okay. Yeah.

[51:07] Nathan: But yeah, we did have a key account button where you could press it and it used to get sent through to them, but people didn't use it.

[51:14] Ben: Okay. So we need to look at the source as well. So there's various sources responding to direct to customer previous contracts and previous customers and things from the ether as well.

[51:29] SPEAKER_02: Okay.

[51:30] Ben: So is it just Sophie gives him a call when it tries to make a meeting?

[51:35] Nathan: Yes, basically. So obviously for new business so depending on how it's come through obviously if someone like Helio has passed it along I've passed stuff along to Sophie quite regularly I would give her the information that I know or the the way that I would go down so she would potentially try it with initial call or an email if it was an email then she would be looking to get a zoom meeting something I've just looked at me like this could be an interesting person kind of vibe so then she would do the qualifying process and then as I now said I've sent them the strategic plan they are then they can do a diluted version and then see how it progresses it is something that needs quite a lot of work on because the different stage of it of you know them getting a quote the quote getting handled it getting passed to Sophie Sophie doing extra qualification you know what kind of corporate agreements are Does it get transferred to an account manager? What is the process for that? What does the account manager need to know? All of that needs a good few hours, I think.

[52:57] Ben: Yeah. So you say you've got a plan?

[52:59] Nathan: I made a diluted version of the account plan for them.

[53:02] Ben: Can you send me that? Sure. That's great.

[53:05] Nathan: I also, when I was in the new business meeting this morning, discussing about them creating... managing those accounts but then they also obviously can add stuff onto the dashboard so they both have access to that dashboard because at the moment which i really don't like is they just use google docs um which is great but then it's off system no time stamps you know if you delete it it's gone like i really don't like that um so i think it needs to be system orientated sure so yeah because if sophie does something we want to know what comes of that a year down the line

[53:47] Ben: yeah what worked what didn't work so we do need to have a speak with Sophie and it's great that you've got a plan there as well because we need to make sure we're aligned on how we work with this just that there's a we've only done one page at three so I just want to check which ones are the most essential at this stage this was always designed to be able to come back it's basically I've just got a meeting at half three with a client that's all okay all right so yeah we've got another 15 minutes now Are you sure you don't need more time than that in between?

[54:18] Nathan: It's someone I've worked with for years. Okay.

[54:22] Ben: Is it okay? Should we just carry on in order then if we might get a chance to come back? Good. Cool. Prioritization. How should limited sales time be allocated? So first of all, what customers deserve almost no sales time? Personals?

[54:38] Nathan: I wouldn't say personal, because obviously a wedding, you know, if a wedding's a grand and a half, then that should take it.

[54:43] Ben: Personal's under pressure, yeah.

[54:45] Nathan: It depends how you want to look at it, because obviously we're talking a lot about opportunities and growth. The caveat to that is we actually don't know if option A can give us five grand.

[55:06] Austin: It's only a potential.

[55:09] Nathan: I think the can goes, must goes. What I would say is that's actually a really good point. Obviously, at the moment, the pending quotes, it puts them into a lot of this done on value, but it's also done on distance and quantity of goods price compared to transport price. there.

[55:37] Ben: I think it's really kind of like an equation. For example, if a wedding is 99% of the time just a one-off customer and they're £1,500 and then you've got a corporate customer that's £1,000 but they do that every year, then the work that the salesperson does on that particular thing, so say the conversion rate to ongoing customer is... the conversion rate of this or 20 and the same for the wedding right well you might not make as much year one but then year two year three you know you're leveraging the future yeah lifetime value of that so um yeah i haven't got a clear framework in my mind about how that works but i think there is something to do with like the conversion and So if you can just say to someone that we've got all of these perks, companies like you, you sign up, I'll send you the forms, you get a 5% discount on this order. And then, you know, once you've filled out all the qualification questions for us, and then, you know, thereafter, every time you come back, you know, and we market you and all of that sort of stuff.

[56:46] Nathan: Yeah. Marketing could be an issue. And obviously that's where I guess you need them to fill it out for the year membership because people don't want marketing, do they?

[56:53] Ben: Probably.

[56:54] Nathan: But there is, it's an interesting one, there's this company called, I know his name's Gene. goes through the car, it goes to my space, Evolution London. It comes through, I call him, I email him, nothing, nothing. But he's got 100% closing ratio. He just books in, just books in.

[57:08] Austin: He's got something to do.

[57:11] Nathan: So, you know, someone like that is someone that, you know, if we're talking about dedicating time, you'd look at it and think, oh, well, but if they've got 100% closing ratio, is there more or less time needs to be spent on them? Because if not, I'd have less.

[57:25] Austin: No need.

[57:25] Nathan: Exactly.

[57:26] Austin: If they're already closing.

[57:27] Nathan: Yeah. So that could be a factor.

[57:29] Ben: factor that you have in there yeah do you work on the ones where where everything's working you work on your strengths your weaknesses as well yeah it's basically it's a question isn't it um if you had an extra hour every day which customers would you spend it on now you from from a feeling of myself or um business development let's just say business development so where i think that would be at the moment with what our capabilities are i do think it would be um

[57:59] Nathan: exhibitor organizers exhibition organizers exhibition organizers okay high value money places that things that we're good at doing um repeat annual money

[58:13] Ben: you know this is something uh important as well is that uh the business needs to focus in segments as well so the businesses say we're going after caterers this year we're going after exhibition organizers right and then all of a sudden they might be more in vogue and more interest because we're actually invested in that area we're getting traction in that area people are talking about us that sort of stuff right now we've done that before i mean you know sophie

[58:37] Nathan: multiple times it's gone right down the catering routes yeah and then we are going targeting exhibition almost but you know i remember getting got sophie on venues like target all the venues like we definitely do that but i don't think you know i was speaking to sophie about a week or two ago i don't think she's backed by resources i think what we're talking about now is something that's very helpful for us we said you know previously she'll call this person see what kind they're interested in. Oh, you are interested? Right, let me give you an account manager. There's no resources backed there. You know, if we're talking about when she used to go after caterers, I know we brought a few things in, but there was no backing. Like, what is she allowed to offer? You know, what is her plan? You know, so she's not had any material or immaterial resources to put in. So I do think we've done that. I just don't think we've done it well.

[59:22] Ben: We're going fishing without bait in the rod.

[59:25] Austin: Isn't it really? Yeah. You can try and target subjects, but how well you do it is...

[59:29] Nathan: It's much more important. Someone could call every single quote that they've got if they just I also think, you know, I actually spoke to Tom about this, and you guys are probably speaking about, like, when we get into this super hub, it still seems we are going to be very every single market, let's attack. Because, like, even when I think about my account manager, and I'm going through about, you know, let's say just 10 of them, they're all different. So one of them's a brand agency, one of them's a certain type of production company, another type of production company, a caterer and all this. Like, we're attacking on so many fronts that, in a way, we don't do anything well. Well, I actually don't think we do anything well.

[1:00:08] Ben: Yeah, yeah. And I think segment specialists need to emerge as well, right? And we need to have growth targets per segment. we're gonna grow 10%, exhibition organizers 30%, what furniture do we need to buy? All that sort of stuff, right? So a customer being strategic needs to not just mean, yeah, we'd really like to get that money. It means that we're prepared to actually, this is what we're going for. caterers this is what i'm about with sophie you know she was tasked multiple times to go and tackle those caterers all of them turn around and say we need cages and it's like cool you know no self-respecting caterer doesn't have cages yeah so this is something with the with the strategic account plans as well is like right we can't just say what you want we need to know what they want and report that back to the company the company needs to learn and change um

[1:01:08] Nathan: yeah so so the next question was what would you actually do differently and it sounds like focus on strategic stuff that's backed by resources yeah and i would say a lot of that has flagged up quite a lot in the past i think a lot of people get too focused on items i think items is very important i actually think infrastructure and the way we go about certain things it's just uh perfect example would be exhibitor portals you know that's nothing to do with items i think exhibitor about attacking these people you know if they for you to actually get a buy from them they're like oh we use forms for the things but go show me um obviously send us your example but we turn around we say here's a pdf and can get him to fill out this order form you won't get an email back yeah i actually think venues is where we've got the most traction and controls is where we should probably carry on maximizing that's that's my thought it's true but then i do think their venues have uh sometimes the block Yeah, it's furniture hire companies because they go directly to the furniture hire companies. You have your unit X where all the furniture going in there is through the caterers. So it is good to focus on venues, but it's only one side of it. I think we did that five, ten years ago, however long it was, five years ago. We only focused on venues and then we actually came into that issue where it's like, oh, we're not actually getting any traction here.

[1:02:33] Ben: Yeah. Yeah. Yeah, it's an interesting one. Okay, cool. So, yeah. So the next question was what actually, what currently stops you from doing that, doing something different? Is it that alignment between the company governance and I don't know, what do you think?

[1:02:57] Nathan: Oh, in terms of exhibition organizers?

[1:03:01] Ben: So anyway, I concluded in my notes that we did a big conversation there, but if you had an extra hour every day, which customers would you focus? And you said exhibition organizers. I don't know, I can for the answer to what you do differently. Yeah.

[1:03:17] Nathan: I mean, to be honest, that's something that, well, I do think right now we said we had this new business meeting. So Sam and Sophie, like kind of say what they've done and what they're going to do to Taryn and myself. I'm just there because I'll eventually take over from Taryn. And Taryn was basically like, and I feel for those two because basically Taryn was like, we just want new. a venue before loads of times then no like so they obviously got some venues and realistically the venues are probably shit like there's no point so then Tara Lynn said today I'll need to be exhibitor organisers you know stuff like that which is still new it's just you know it's going to Olympia so they probably thought oh is that new because it's going to Olympia but it technically is you know now this is the focus on that but even now I'm you know sitting here actually talking about this I need to kind of speak to those two Sam and Sophie because again we're giving them a direction, but we're just pointing over there without, you know, they're going off into the desert with no water, you know? Okay. If they now reach out to this thing and like, let's hope to get bites again, are they backed? If we, if we are now focusing on exhibitor organizers, organizers, we should have this exhibitor portal rather than you just contacting them straight off the bat, you know, wait a couple of weeks. And if we get an exhibitor portal up, we go, Hey, we do events at Olympia, this is our exhibit portal, we're not giving them the resources. So yeah, what I would do now in my position, we've kind of got them on that path, but we really do need to back them.

[1:04:40] Austin: Of course. The exhibit portal is pretty much there. You probably may check out everything. So I could put that up on a test server and let these guys round with it.

[1:05:04] Nathan: So use literally an event that I did earlier in the year and go through it like that. We'll see if it works.

[1:05:09] Ben: What it sounds like is that we need to actually decide with more clarity because there's no good saying, you know, go and fish in that pond. All right, now go and fish in that pond. It's got to be a new one. But if you haven't worked out the pond and the right equipment for it and brought that over and seen how much you can get.

[1:05:24] Nathan: uh you know new business development there's a there's a thin line between new and actually development right yeah um that's true i i think the other problem is it is um it's not black and white you can't have it's not uh it's not input output you know it's not like i've sent 10 emails and you know i'm getting x back you might not get any back do we know if they're working you never get any you don't get response so much you don't know what works and what doesn't did you only get that inquiry because that it or is a new person we have quite a limited data it's very hard to say why someone's gone ahead or not go ahead 100 yeah yeah um yeah i think they're lacking resources which I think I should probably get.

[1:06:07] Ben: And what about the visibility? Because you do a whole load of stuff that's very frustrating. It's mostly no's and new business development, right? And getting ghosted. But then over a span of time, you end up with a bunch of venues and caterers and stuff like that. You need that visibility to see how is that developed.

[1:06:24] Nathan: One thing that I think would be very good...

[1:06:28] SPEAKER_04: I'm free.

[1:06:29] Nathan: I'm free. Sorry, my phone's popping off, so I'm just making sure it's not. Would be very good. Would be, again, like BDC. Say BDC or Olympia. At the moment, we have a page where it's a venue database and you go on all the orders there. What it doesn't have is the orders that we never even quoted, like an event.

[1:06:52] Austin: Okay, yeah, so previous events that they've held that we never even got a quote for.

[1:06:56] Nathan: Do you know what I mean?

[1:06:56] Austin: You're completely excluded from that.

[1:06:58] Nathan: So I know that Sam and Sophie were talking to you about a calendar.

[1:07:01] Austin: Yeah, I was still trying to find out from Sophie what she actually means. The last question I asked her, was it a calendar for events that we are due to go to?

[1:07:09] Nathan: No, it's more about like, okay, the Chelsea Flower Show is in June the 10th next year. Let's put it in a calendar so we know where.

[1:07:18] Austin: Are they customer-linked events?

[1:07:22] Nathan: This way it gets tricky. I think they just kind of want something where they can put it in, they can both see it. I don't think it needs to be. I think a manual probably could work.

[1:07:28] Austin: I remember that early scoring page I made, it showed when their next event was that was coming up.

[1:07:32] Nathan: Yeah, stuff like that. That could fill it out, yeah.

[1:07:35] Ben: So you've got that on a sort of like what markets are happening in our marketplace kind of.

[1:07:46] Nathan: the system say BDC right say if I actually had it and it said 2026 and then 2027 basically I filled it out and it auto populates it saying okay the health optimization summit we did it 2025 on this date it was on this date this was amount of quotes and contracts for it and then you can see and it can because there's a page for BDC what's on you can pull the abstract like all that information yeah next year yeah and then you know it could be like this was the name of the event it was great we didn't win but it was contacted Red, we never contacted. Red, we contacted, didn't win. Green, we did it. Something like that.

[1:08:19] Ben: Yeah, just a little marketing thing of one opportunity creation.

[1:08:23] Austin: Yeah, did we get spoke for it? Yeah.

[1:08:32] Ben: this person we never even contacted them because it's like a month ago yeah hopefully they'll all be red or green there's also like a spend of an event which might not actually belong to one particular organizer or one particular customer if you like right you know it's like you know this is just the chelsea collateral i'm sure someone runs it but it's probably like there's lots of customers that go there you better finish but yeah thank you no worries i should come back um

[1:08:54] Nathan: But yeah, that would be a train of thought. You know, that's something we just spoke about now. With Sophie, instead of randomly contacting exhibitor organisers, they've got a calendar of, okay, this is all of Olympia. So this is the ones that we need to contact. Did we contact them last year? Did we win? And then exhibitor portals to contact. This is what I'm saying. I feel sometimes you're just getting told to go into the desert without a bar.

## Transcript — meeting-recording-2026-07-21-1456.m4a

_Transcribed 2026-07-21T17:05:44Z on-device._

_Part 2 of 2 (recorded 14:56). Auto-labelled with the in-room voiceprints banked from Part 1 adjudication — 68% confident, `?` = uncertain. Adjudicate to finish._

[00:00] Ben?: I'm going to skip sales approach by customer type.

[00:05] Ben: The purpose of that is to understand does our approach change depending on the customer value and potential? I think the answer to that is yes. But the question specifically, we're asked about small order, low repeat likelihood, small order, high repeat likelihood, large order, like repeat, large order, high repeat likelihood.

[00:30] Nathan: covered that a little bit yeah we're there just talking about denominations which could be quite difficult for us to say yeah like you know again if you're a small order and you do 300 pound but you do it 10 times a year you spend 30 grand for three grand should you be an incubation account should you not should still be in your membership yeah i think that's more about denominations cool um all right so let's just get that rubbish questions customer knowledge um

[00:56] Ben?: Any particular information that the system should capture or should be captured in that system?

[01:01] Ben: So if you inherited an account, what information would help you immediately? What information is absolutely essential? What information is useful but not essential? And if a salesperson after I kind of asked this question before, what knowledge would be lost?

[01:15] Nathan: Okay, yeah, really good. So obviously, depending on the type of company they are, but... A lot of this can be found on the system, but is it done in a very good way?

[01:26] Nathan?: I'm not too sure.

[01:30] Nathan: In regards to information that is definitely needed, you would need to know who the main stakeholders are there. So who works there?

[01:42] Nathan?: What are their roles?

[01:43] Nathan: Who does the ordering? Who's in charge of certain what is also important which is only now starting to realistically get noted and would be something that would have get lost and would still get lost if some people left now is pain points what are the customers pain points um what would a pain point look like you know for example um they yeah so you know it could be one that um matt has up with is that when we deliver it we need to half the chairs they don't want it to be you know something quite innocuous but this is information that does need to be locked um yeah pain points could be um you know even response time when i was having a meeting with unit x and you know i said to him it was like what are your pain points when it comes to your suppliers and he basically said um you guys are brilliant at responding we've got some people that uh for you

[02:42] Nathan?: who's a furniture company.

[02:43] Nathan: So, you know, they come here quite a lot, but that might take days, a week to respond to the question. So yeah, that's a pain point.

[02:50] Nathan?: Now, are these logged on our system?

[02:51] Ben?: Yeah. You know, not very well. Yeah.

[02:53] Nathan: Obviously when you do a strategic account plan, it's locked.

[02:56] SPEAKER_01: Yeah.

[02:56] Nathan: You know, other stuff, obviously venue, venue information, very important operation information, operational information for venues and potentially others, like caterers, doesn't need to have cages, all that kind of stuff. Discounts, commissions, that's kind of already noted down there. so yeah i would say like there's where we've made this strategic account plan a lot of the information on there kind of needs to even though it's great it's not a document it should also be some of it should be like filtered onto the system where it's you know straight in eyesight okay yeah i was thinking about this the and again we're talking about high level strategic account think in terms of you know smaller accounts you still want to know who the decision makers are and who does the ordering etc um but i do like the idea of a different type of customer profile for strategic accounts yes of course yeah um specifically for strategic or you know even managed accounts i do think managed accounts it doesn't need to be as in-depth i'm not too sure but um things like um pain points is a perfect example that could be like a tab what are the pain points of this company? So, you know, it's the same to our menu on the side where it's like sales, logistics.

[04:14] Nathan?: One of them just says pain points.

[04:15] Nathan: You know, you're on the Hurlingham Club thing and you click on pain points.

[04:18] SPEAKER_01: Do, do, do, do, do, do, do, do.

[04:19] Austin: Try and categorise them as well as possible if we're going to have them there.

[04:22] SPEAKER_01: 100%.

[04:23] Austin: Is that pain point stock? Is it service?

[04:25] Austin?: Is it, you know?

[04:26] SPEAKER_01: For sure.

[04:26] Nathan: Yeah, yeah. Stock, service. You know, response time is service.

[04:31] Nathan?: So, yes, yeah.

[04:32] Nathan: It would usually fall into those two brackets, I guess.

[04:34] Nathan?: Yeah.

[04:35] UNKNOWN: Yeah.

[04:35] Ben: And that's part of our accounts plans now, is that right?

[04:37] Ben?: Yeah, yeah.

[04:38] Ben: Cool, so it'd be good to... Actually, I've already got five or six of them, actually. It'd be good to start to aggregate what these things are and how we can categorise them.

[04:52] Ben?: 100%.

[04:53] Nathan?: But you know what?

[04:54] Nathan: The amount of conversations between them and customer service, we know that somebody has stopped using us because of that. Because after every single thing you're telling me about all these missing ones, things that are not even directly related to sales can be big pain points for clients.

[05:09] Ben: So if someone's on leave, then what do they tell them about their account? So someone's going to look after the BDC? for them to download from you?

[05:19] Nathan: Yeah, so realistically, depends how long you're off for. I probably should have done a lot more now because I'm going to be off for a month. But it's tricky because if a new one comes in, there are quite a lot of information that to cover it, a BDC, you would need to cover it in a massive document.

[05:40] SPEAKER_01: Yeah.

[05:42] Nathan: I don't think... As bad as it sounds, sometimes it's a bit like, look at the previous orders.

[05:47] Nathan?: It's kind of a vibe, which we use a lot.

[05:51] Nathan: Yeah, it's probably not noted very well.

[05:54] Ben?: It's not noted very well. Yeah.

[05:56] Ben: So I think you want that sort of role in relevant history, if you like. You know what I mean? Which might be derived from the account plan. What is it that's been going on here?

[06:05] Nathan?: What matters?

[06:08] Nathan: So if you go on a profile, you know, you can look at view all activities. I think it's not presented well.

[06:16] Ben: So it's just strewn between all the different activities.

[06:18] Nathan: Yeah. I think that needs a massive overhaul.

[06:20] Ben: They're nice themselves a lot, aren't they?

[06:22] Ben?: A lot of it.

[06:23] Nathan: But then there's, you know, stuff like, you know, commission expiry or case, you know, or... Sometimes it's really hard to go and find it.

[06:34] Austin: I think it's the activity types even on there.

[06:36] Austin?: They don't cover everything.

[06:37] Austin: There's no activity type for a phone call or an email, for example.

[06:40] Nathan?: Yeah, we just class that as a chase. This is it, or a meeting. Definitely a whole overhaul.

[06:49] Nathan: And the way I would look at it is, say you go into BDC, you should just click on it and go, okay, these are the lists of all the different ones.

[06:56] Nathan?: And you can just then easily see Showcase.

[06:57] Nathan: You click on it and it shows the dates that Showcase was done, rather than having to scroll through.

[07:01] Ben: all that so that was the salesperson's notes about the history what's the sort of activity history what's the cases history what's the showcase history etc etc you want that all visible basically yeah one of the big issues we've got is um how people use the system and how whether they use it correctly whether they use it and so

[07:28] Nathan: and it's very hard to, you know, it's the same, like, I'm assuming you were a gamer, I don't think you're a gamer anymore, but like, yeah, so, World of Warcraft, you change your UI to have, some people have the map down here, some people have the map up here, you know what I mean, like, sticky key, you know, keybinds and all this, you use it differently. The issue that we've got sometimes is, you know, even not using it. So, you know, someone like Paola barely uses the system.

[07:50] SPEAKER_01: Yeah.

[07:51] Nathan?: Barely uses it.

[07:51] Nathan: You look on her dashboard, it looks like she's done fuck all, but she has chased them, she just doesn't log it.

[07:57] Ben: out to air and might as well start a world war three kind of thing yeah we do have systems that are going down this road it needs to be moved it needs to be recorded it's useless yeah it's there's definitely got to be a level of hygiene that the you know because you don't own that account, the company does. That's what Axel says, right? And I think that's a very good thing, right? So yes, it is your account, but you're a steward of this account now on behalf of the company, right? And so the company doesn't just require you to have great relationships. It also needs you to plan in such a way that it's going to maximise it. And if anything was to happen and that account was transferred for whatever reason, that we'd not lose all of that knowledge because you've got a Google Docs on them or it's all up there.

[08:42] Ben?: Yeah, exactly.

[08:43] SPEAKER_02: Yeah.

[08:44] Ben?: Cool.

[08:45] Ben: And definitely continuity relationships. I'm sure that once these things start to pan out that we're going to start to think about, you know, look, it's not just the stakeholders, it's about what they know and what experiences these guys have had together and how do we, you know, if you were going to hand over a business design centre in six months, I'm sure you're going to speak to...

[09:05] Nathan: whoever you intend to hand it over and bring them down to site and meet people and go out on nights out or whatever it is right so that's the other thing so obviously we've got one on the system so we're talking about you know in-depth strategic account plans where the moment you go on the system you click on one of the tabs that say profile and but you know hopefully more people more people keep doing that but it doesn't save you any qualitative information about that person what is your relationship so obviously when we've done the strategic account plans yeah um the document then will show you their name their thing what relationship you've got any notes yeah um so you know that is something that would be great to be pulled through because that's very important um yeah you have the and introduce someone you don't know what they're like like when I pass over when I've passed over my accounts before say an example would be Draco Morgan to Kelly I say correct this is Gabby she's going to be the main thing she's a Brazilian fiery lass as long as you do well you know it's all good but that is important information if I had just you know dropped down dead Kelly wouldn't know certain things you know so

[10:10] Nathan?: There's a hell of a lot of information you can learn.

[10:11] Ben: No job done dead until we sort that shit out.

[10:17] Ben?: Cool. Yeah.

[10:18] Ben: There's lots of food for thought in customer knowledge and stuff and not making it too onerous on sales, but at the same time, telling them that this is part of the job. Now, I think it's got to come, right?

[10:28] Nathan: I think it's got to come. Yeah, I do think that the push and pull of... You open up a commission structure where you get more money, the more bookings you have.

[10:50] Nathan?: her strength is innovation.

[10:52] Nathan: But then she's messaged me to say, oh, Taryn said that I can have this account.

[10:55] Nathan?: Taryn's like, yeah, I've given her. I'm like, she's struggling. Like, she's not doing system hygiene.

[11:00] Ben?: Yeah, don't give more work. Yeah, yeah, yeah.

[11:02] Nathan: And then she would be the best person to handle that account. But even so, like, you know, if someone's not doing it, then you have other people that really do great system hygiene, but then actually how much of that gets put through into actual innovation work probably does more than some other people. But if you look at the system, it looks like one person's done an unbelievable amount of work.

[11:17] Nathan?: Yeah, yeah. And there needs to be a middle ground. Yeah.

[11:20] Ben: And yeah, I think if you have a look at the role description that I've sent you, it's similar format to the other three. So it's a service exec, but similar format to the other three. And there is like, this is what the expectations are kind of thing. And this is, you know, why you'd get a new account and be trusted with moving up a phase to senior account manager or whatnot.

[11:43] Ben?: Account maturity. Okay.

[11:44] Ben: So the idea of account maturity is that It's a spectrum, right? A company that you've had a decades relationship with and you've got most of their spend is a very mature account and a company that's brand new is not the same. So its purpose is to understand how relationships evolve and so how we could look at our accounts and say, yeah, look, these are very well-developed accounts in terms of their maturity and these aren't. What stages does an account go through? How would you describe that from you to mature?

[12:20] Nathan: Really poor at documenting it realistically and a lot of it, similar to what Austin said a bit earlier, it's like... don't really know how this happened you know they did have definitely quite a lot of you know i can tell you some of the ones that we've got a great relationship with and i can kind of pinpoint certain points where that definitely furthered it but it's not been documented in that fashion you know um so if i said we did have all the logs of what Incipio Group would be a perfect example. If we don't have it, then they won't use us. But if we have it, then they use us 100%. And obviously, we've got a very close relationship with them. Not just Matt, who's obviously really close to them, but quite a wide range. We've gone to a lot of their events with a wider team. And we've invited them to stuff as well. The social side of it would be very important. But if we weren't good, then we won't get it.

[13:16] Nathan?: So it's...

[13:19] Nathan: But there are many factors. I actually did want to go through with Taryn and actually kind of list... you know a never-ending list of different things you can do there is a point where it's like there's only certain amount of things you can do even if it's like 30 different things there is like you know okay go down with your delivery team or invite them to a social you know after a certain amount of time there isn't you know give them a present exactly yeah and there's not you know there isn't a never-ending things you can do you know we're not going to send them to the grand prix or anything you know you can even list that as a gift the activities i think there should also be a thing as okay innovation what are the different things that you can offer um but where we i think have been very poor i said the documentation is actually getting to the uh itty gritty so it's hippie a group we've never really spoken to them about their pain points they had a need they started using us they liked us they use us we've never discussed about you know why we haven't just growing up truly ever really very grown

[14:23] Nathan?: good infrastructure or something.

[14:25] Ben: So describe that as the type of sales activities or sales activity milestones. So if you have a meaningful conversation with them, then that's essentially a milestone. If you have a history of meaningful conversations, like eventually they're showing your accounts and saying, look, we are the only people that you guys use or there is 20% left, but that's because you don't have this item, right? Then that's a high level of maturity. So it's definitely new and mature. I'm not sure if there is an extramillionaire but in between there's different things as well as in there for example we had Tate who they were an account where that was six seven years ago but they probably hardly used us and they've come back again so yeah this is this we're starting from scratch but yeah I don't know I'm just trying to sort of work out how we would grade maturity and and link that into milestones and things that happen yeah in a way it's kind of we're learning on the job now Tate is a perfect example of building through milestones obviously they're going to come to be

[15:39] Nathan: so yeah but yeah I don't know it's hard to actually put information into that in terms of like how do you make it mature so another concept we were talking about was we were talking about in the bank percentage right so what makes a customer ours right yeah I guess the only thing with that is fallibility so obviously you have banked trust wrong we have fucked up an incipient drug or our team were really rude or something like that so overcoming a problem is a sign of maturity massive yeah do you also have bank trust if that if we didn't have a relationship with them that could have mean that we never use it withdrawal from the we make withdrawals from these relationships basically but how do you put a number on that yeah well issue resolution potential issue severity like banked trust if i say to you incipio that would be a lot of bank trust how would you put a number on that because once if if she left if any left successful orders we're doing something along the way of getting feedback on the orders right successful yeah clone uh so it's banked in terms of every time you do a successful yeah yeah are you happy with us yeah yeah because yeah i mean this is where we're talking about in terms of um overhauling the dashboard we could spend fucking hours on that you know a big thing you know do an event yeah i call or email them and say you know how it'll go and then you know they say it went really well yeah and what i do because it seems the most logical thing is i copy and paste their answer into it yeah so then if i'm going back to it the next year when i'm doing a repeat event i can just read their answer obviously just look at the email um but the system won't the system logs it but it doesn't log it as good or bad it's just qualitative information

[17:35] Ben: or just a neutral face if it was like yeah this is really good but this was a great kind of thing so you know so one day you should be able to walk into a meeting with an accountant and say look the last year we've had 24 orders with you there was two of them which didn't go well the rest seemed to go well what I noticed about those two was yes we have come and given the refund and everything that this was the problem that's happened this is what's happening in our organization to change that do you know what I mean and that sort of builds the the longer term trust as well kind of thing like this is how we're growing together and

[18:03] Nathan: yeah and whatnot that'd be really good because that's the thing like moving venues i do jobs for them all the time i'll have no end of post event feedbacks in there but i'd have to go through them all just to see how it went like even if i said the team that if you said to me oh there's that one order that wasn't very good sometime last year i wouldn't know where to find it like i'd have to go through so this this this becomes an essential part of account management then if we if we're listening yeah

[18:30] Ben: I'm back to going to another meeting.

[18:37] Ben?: Ownership.

[18:38] Ben: What should ownership of an account actually mean?

[18:44] Nathan?: Yeah. So you're talking about an account manager.

[18:47] Nathan: Yeah. Yeah. So I think aligned with Yaha's values that is, you know, trying to... get as much money as possible or potential value as possible whilst keeping the customer happy. I think for too long we kind of focused on the latter without the former or the tools to understand how to do the former.

[19:12] Ben: So what should someone do to own ownership of a customer?

[19:16] Nathan: Like, what should someone do to own ownership?

[19:20] Ben: Yeah, to earn ownership, sorry.

[19:21] Nathan?: Oh, earn ownership, customer.

[19:23] Nathan: As in, what should someone do to get a new account?

[19:26] Nathan?: Yeah.

[19:27] Nathan: Okay, well, I think the first one that, yeah, multifaceted, how the resource is to do it. If someone can't handle any more accounts, then they shouldn't have any more accounts.

[19:37] Nathan?: That's something that would come through.

[19:39] SPEAKER_01: Yeah.

[19:39] Nathan: Exactly, competition. people are not doing the hygiene or whatever people are not doing the tasks so that's one prerequisite two is doing well on the others so if you're doing really badly with all yours then you shouldn't really get new ones potentially three would be I think in the future specialisation should I say industry segment yeah I love the idea of Kelly being our catering yeah account manager for experienced enough experienced enough okay yeah and any particular work on that account or that customer yeah I would say yeah if they know him perfectly before but the other thing I would say is not necessarily just experience but skill set I think sometimes we pretend that, because we have one account manager role. Well, technically we've got two with Frankie.

[20:40] Nathan?: But they are three wildly different people.

[20:43] Nathan: You know the Matt and Paola will be well matched for certain accounts and not well matched for other accounts.

[20:52] Ben?: Okay. So there's a fit as well, yeah? 100%.

[20:56] Austin: That's more of the human sort of feeling, isn't it?

[20:59] Ben: Yeah, I'm going to give you an account that this suits you and you're on top of your shit.

[21:03] SPEAKER_02: Exactly, yeah.

[21:07] Nathan: And skill set, sometimes it doesn't necessarily refer to just bad. It might be they excel at certain things that is actually very needed.

[21:14] Nathan?: You know, a BDC is very...

[21:20] Nathan: that i wouldn't want to give that to someone who is a relatively system illiterate um if someone was extremely system illiterate i'd rather give it to that person than someone else okay so kelly's better at system than paula it's interesting for me becomes part of the say you know your skill set you'll hit your four out of six on this hex graph um yeah um the only thing i would say like because it definitely is i do think that some people can only improve to certain levels of certain things and other people will never get to that do you know yeah um so for example like actual innovation kelly and matt will never be as good as what power is now but all three of them can improve how it can improve um writing really, really good professional emails, there's someone that would really struggle to do that. Like that, you know, you can't take someone back to school and like fucking, you know, go through year five and six of English, year 10 and 11 of English. So there are still, even though it could be a development thing, there are still ceilings.

[22:27] SPEAKER_02: Yeah.

[22:28] Ben?: Cool.

[22:30] Ben: So once they've got ownership of the customer, what do they need to do to justify continued ownership?

[22:35] Nathan?: Sorry, repeat that one again.

[22:37] Ben: With their customers, what do they need to do to... You've got a meeting in France.

[22:40] Ben?: That's right, just one more.

[22:42] Ben: What do they need to do to justify continuing the ownership?

[22:47] Ben?: Keeping that account.

[22:48] Nathan: Yeah, I mean, where it needs to be improved now is system hygiene and understanding the customer. kind of goes hand in hand you know do we have understanding it's just not on the system or it's on the system that we don't have understanding you kind of need both um so fill in their accounts plans as well fill in their accounts plans yeah i mean you sometimes wish that you know there was like a you know football when they have like a a have our quiet season but you actually have like a whole pre-season or you know you're not actually working yeah if we actually just weren't doing any work now and we're just doing all the stuff we could get on with it but it's never really the case um but yeah and um obviously uh touch points monitoring you know if they haven't done anything on there for three months then what are we doing here yeah kind of like yeah so that's going to come down to the manager's cadence and visibility that you have to see what they've actually done as you

[23:49] Ben: Thank you, Nathan.

[23:50] Ben?: Yeah? Yeah.

[23:51] Ben: Well, there's a little bit more, but whenever you've got the chance.

[23:56] Ben?: We'll see. Yeah, yeah. Thanks, man. Good.
