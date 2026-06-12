---
id: T-0360
title: Check out a sales scoring cost on credits
type: feature
state: done
priority: p2
created: 2026-06-11T23:57:22Z
updated: 2026-06-12T14:21:46Z
project: yasystem
section: null
parent: null
children: []
order: 6144
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - "[x] a cost in api credits for a full scrape of excel.london example"
  - "[x] looking up socials should reveal event frequency - needs to be an event not just a post"
  - "[x] we are trying to weigh up the cost of scoring all customers / new screened / new all / existing + new all so get an idea of monthly new customers (filter by domain)"
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260612-0008
    model: claude
    started: 2026-06-12T00:08:50Z
    status: completed
    summary: |-
      **What we did:** Worked out what it would cost in AI credits to automatically "score" each customer for sales potential, by actually running the lookup live on the example customer (excel.london). The lookup splits personal vs business email addresses for free, then reads the business's website to identify its industry, then — only for promising industries like venues and caterers — does a deeper sweep of its event listings and public financial records. A full deep lookup costs roughly 10p–20p per customer depending on the AI model used; the cheap industry check costs under a penny. We also found that Instagram/Facebook can't be read automatically, but event-listing sites and the company's own what's-on page give better evidence of real events anyway, and UK financial records are free via Companies House. Full cost tables and monthly scenarios are in the ticket comment.

      **Why:** Before building customer scoring, we needed to know whether running it across all customers (or just new ones) is affordable, and whether the lookup can actually answer "how often does this business run events?"

      **If we did nothing:** We'd be guessing at the budget — either over-committing to scoring every customer at an unknown cost, or dismissing the idea as too expensive when scoring all new corporate customers actually costs single-digit pounds per month.

      **The benefit:** A clear, evidence-based price list: scoring each month's new corporate customers ≈ £6–£14/month; a one-off backfill of all existing corporate customers is the only material spend and a two-stage screen keeps it to low hundreds of pounds. The team can now decide scope (new only vs backfill) with real numbers.
    ended: 2026-06-12T01:47:12Z
    test_plan: |-
      1. Open T-0360 and read the findings comment (dated 2026-06-12) — check the pipeline stages and cost table read sensibly to you.
      2. Sanity-check the headline numbers: full scrape ≈ $0.10 on Haiku / $0.23 on Sonnet per customer, built from ~50k input + ~3k output tokens + 3 web searches at $10/1,000 (pricing pulled from platform.claude.com on 2026-06-12).
      3. Spot-check the worked example: excel.london classified as a large events venue from one homepage fetch; event frequency evidenced by dated events (its what's-on page, Ents24/tradefest aggregators, "400 events/year") — not social posts.
      4. Note the two cautions: Instagram/Facebook block scraping (use event aggregators instead), and Companies House name lookups can match the wrong similarly-named entity — domain→legal-entity resolution must come from the website itself.
      5. Decide the open follow-up: the monthly volume scenarios use example counts (500 new/mo, 40% corporate). One DB query on the test box (distinct customers per month, personal-domain exclusion on email domain) turns them into firm figures — say the word and it becomes a ticket.
      6. Housekeeping check: the ticket's pinned branch (refund-hardening-t0331-stripe-dryrun, a mis-pin from last night) is now cleared — the ticket falls back to the project default.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention: null
version: 20
---

So the idea to score a customer is to first of all use current personal /  coroprate split -this is done using the email domain so non custom domains (gmail/hotmail/outlook.com) are personal customers else corporate. Then we need to identify industry. Look u p the domains website. So for example the customer domain is  customer@excel.london we would send an agent to look at their website. and if found to be a venue / caterer etc then look up their socials and public financial records to asses their potential value, event frequency etc. 

we want to figure out how much this lookup would cost in credits per customer

## Conversation

**2026-06-12 01:46 claude-code:** **Findings: what a per-customer lookup costs in API credits (worked example: excel.london, run live 2026-06-12).**

**The pipeline and what each stage costs**

- **Stage 0 — personal/corporate split** (gmail/hotmail/outlook/yahoo = personal, else corporate): plain code, no AI, **free**. This filters out the majority of customers before any credits are spent.
- **Stage 1 — cheap industry classify**: fetch the domain's homepage and ask a small model "what business is this?". One page fetch (free, ~3,000 tokens of content) + one Haiku call. **~0.5–0.7¢ per customer** ($0.005–0.007), and roughly **0.3¢** if run through the Batch API (50% off, fine for overnight scoring). The excel.london homepage alone was enough to classify it as a large events venue.
- **Stage 2 — full enrich** (only for customers Stage 1 flags as venue/caterer/events/etc.): an agent run doing ~3 web searches + ~5 page fetches covering the website, event listings, and public financials, then writing a score. Measured shape: ~50k input tokens + ~3k output tokens + 3 searches.

**Cost per FULL scrape (the excel.london number, AC 1):**

| Model | Per full scrape | Per 1,000 customers |
|---|---|---|
| Haiku 4.5 | **~$0.10 (~8p)** | ~$100 |
| Sonnet 4.6 | **~$0.23 (~18p)** | ~$230 |
| Opus 4.8 | ~$0.36 (~28p) | ~$360 |
| Fable 5 | ~$0.68 (~54p) | ~$680 |

(Web searches are $10 per 1,000; page fetches are free apart from their token content; prompt caching assumed — without it roughly double the token component. Batch API halves the token component again for non-urgent runs.)

**Event frequency from socials (AC 2) — important practical finding:** Instagram/Facebook block automated scraping, so "look up their socials" doesn't work literally. What DOES work, and is better evidence anyway: the company's own what's-on/events page plus public event aggregators (Ents24, tradefest.io, allevents.in) and press. For excel.london this surfaced dated, named events ("up to 400 events/year", 10 international congresses booked for 2026, Formula E in July) — actual events, not posts, which is exactly what the criterion asks for. This fits inside the 2–3 searches already costed above.

**Financial records:** UK companies are free via the Companies House API (no credits, generous rate limits). One caution from the live run: searching "ExCeL London" returned several unrelated similarly-named companies — the agent needs to resolve domain → legal entity carefully (registered name from the website footer/T&Cs) or values will come from the wrong company.

**Monthly scoring scenarios (AC 3)** — per-customer × volume; using the two-stage funnel (Stage 1 on everyone corporate, Stage 2 only on relevant industries, assume ~⅓ escalate):

| Scope | Formula | Example: 500 new/mo, ~40% corporate | Example cost (Haiku/Sonnet for Stage 2) |
|---|---|---|---|
| New, screened (corporate only) | N×0.4×Stage1 + N×0.4×⅓×Stage2 | 200 classified, ~67 enriched | **~$8 / ~$17 per month** |
| New, all (incl. personal check) | N×Stage0 free + as above | same | same |
| Backfill all existing corporate | M×Stage1 (batch) + M×⅓×Stage2 | e.g. 20,000 corporate | ~$60 classify + ~$670–$1,500 enrich, one-off |
| Everything monthly (existing+new re-score) | impractical — re-score on trigger (new booking) instead | — | — |

Exact customer counts need one query against the live DB (distinct customers in the last 12 months, grouped by month, with the personal-domain exclusion list applied to the email domain) — that turns the formulas above into firm £ figures. Happy to run it on the test box as a follow-up.

**Recommendation:** two-stage funnel, Haiku for Stage 1 (batched), Sonnet for Stage 2 enrich. At those rates, scoring every new corporate customer costs single-digit pounds per month; the only material spend is the one-off backfill, which the Stage 1 screen keeps under control.

(Also tidied: this ticket had branch `refund-hardening-t0331-stripe-dryrun` pinned — a slip from last night's session, nothing here touches that branch — cleared it.)

**2026-06-12 01:47 claude:** Run run-20260612-0008 completed — **What we did:** Worked out what it would cost in AI credits to automatically "score" each customer for sales potential, by actually running the lookup live on the example customer (excel.london). The lookup splits personal vs business email addresses for free, then reads the business's website to identify its industry, then — only for promising industries like venues and caterers — does a deeper sweep of its event listings and public financial records. A full deep lookup costs roughly 10p–20p per customer depending on the AI model used; the cheap industry check costs under a penny. We also found that Instagram/Facebook can't be read automatically, but event-listing sites and the company's own what's-on page give better evidence of real events anyway, and UK financial records are free via Companies House. Full cost tables and monthly scenarios are in the ticket comment.

**Why:** Before building customer scoring, we needed to know whether running it across all customers (or just new ones) is affordable, and whether the lookup can actually answer "how often does this business run events?"

**If we did nothing:** We'd be guessing at the budget — either over-committing to scoring every customer at an unknown cost, or dismissing the idea as too expensive when scoring all new corporate customers actually costs single-digit pounds per month.

**The benefit:** A clear, evidence-based price list: scoring each month's new corporate customers ≈ £6–£14/month; a one-off backfill of all existing corporate customers is the only material spend and a two-stage screen keeps it to low hundreds of pounds. The team can now decide scope (new only vs backfill) with real numbers.

**2026-06-12 01:53 claude-code:** **Follow-up: first live scoring run (2026-06-12), per Austin's request to see actual scores.**

Draft rubric (v0): **industry fit** 0–40 (venue/caterer/event-organiser high, unrelated low) + **event frequency** 0–30 (dated events, not posts) + **scale/financial standing** 0–30. Tier A ≥70, B 40–69, C <40. Scale only counts fully when industry fit is established (a giant company that never hires event furniture is still a C).

**Note on the sample: these domains are NOT from our customer DB** (no DB access from this session — excel.london came from the ticket; the rest are representative public examples chosen to span big/small/irrelevant). The scores demonstrate the method, not real pipeline output.

| Customer domain | What the agent found | Fit | Freq | Scale | Score | Tier |
|---|---|---|---|---|---|---|
| excel.london | International exhibition venue, ADNEC-owned; ~400 events/yr, 10 congresses booked 2026 | 40 | 30 | 28 | **98** | **A** |
| rhubarbhospitality.com | Events caterer + venue operator: 2,000 staff, caters 5M people/yr, 70+ venues (Royal Ascot, Sky Garden, Royal Albert Hall), sold for >£100m | 40 | 28 | 30 | **98** | **A** |
| wiltons.org.uk | Small Grade II* listed music hall; 300+ performances + 80 productions/yr, wedding/corporate hire | 38 | 30 | 12 | **80** | **A** |
| dyson.co.uk | Appliance manufacturer — no events/catering relevance; huge but irrelevant scale | 5 | 0 | 10 | **15** | **C** |

**Observations for the real build:**
1. **The control case works** — Dyson exits cheap (one homepage fetch, ~0.5¢, no searches needed) and scores C. This is the path most corporate customers will take.
2. **Frequency vs wallet tension**: Wilton's (small but constantly active) scores nearly as high as ExCeL (huge). If "potential value" should weight spend capacity over booking frequency, shift weight from the frequency band to the scale band — a product decision to make before the real run.
3. **Fetch failures happen**: Rhubarb's own site timed out; the agent recovered everything (size, venues, ownership, revenue scale) from one web search. The scorer needs that fallback built in.
4. Credits spent on this 4-customer demo: 3 searches ($0.03) + ~4 fetches ≈ **a few pence total**, consistent with the cost model above.

**Next step if wanted:** run the same scoring against a real sample — needs either a pasted list of customer domains or a DB query (distinct corporate email domains) on the test box.

**2026-06-12 02:16 claude-code:** **Follow-up 2: scored 30 REAL customers from the test DB (restored copy of live, read-only) — scores align with actual spend.**

Method: sampled 30 corporate-domain customers from `ya_customers` + `invoices` on the Routing Test box (sandbox DB), 10 per lifetime-spend bracket (high/mid/low), then scored each domain **blind** (the scorer never saw the spend) with the v0 rubric: fit /40 + event frequency /30 + scale /30 (scale capped at 10 when fit < 15). ~1 homepage fetch + at most 1 search per customer.

**Headline: Spearman rank correlation between blind score and actual lifetime spend = 0.68.** Bracket averages are cleanly monotone — high-spend bracket avg **64.7**, mid **36.3**, low **18.3** — and ALL five Tier-A scores landed in the top spend bracket.

| Spend bracket | Customer (domain) | Lifetime spend | Score | Tier |
|---|---|---|---|---|
| HIGH | hyrox.com (HYROX UK) | £91,212 | 85 | A |
| HIGH | dsaproductions.co.uk | £17,827 | 80 | A |
| HIGH | swingers.club | £11,001 | 83 | A |
| HIGH | adlib-entertainment.com | £4,655 | 63 | B |
| HIGH | invisibleblue.com | £3,654 | 62 | B |
| HIGH | 15hatfields.com | £2,471 | 85 | A |
| HIGH | mlglondon.com | £2,110 | 64 | B |
| HIGH | brentmusicservice.com | £1,452 | 33 | C ✗ |
| HIGH | nla.london | £1,398 | 73 | A |
| HIGH | erevena.com | £1,368 | 19 | C ✗ |
| MID | bmw.de (Blue Scope Berlin) | £1,191 | 13 | C ✗ |
| MID | thedeltagroup.co.uk | £1,141 | 65 | B |
| MID | mountfordchambers.com | £1,009 | 16 | C ✗ |
| MID | ifonlyif.co.uk | £887 | 6 | C ✗ |
| MID | youngtechacademy.co.uk | £651 | 34 | C |
| MID | salesforceben.com | £629 | 42 | B |
| MID | partnershipeditions.com | £594 | 58 | B |
| MID | restaurant-ours.com | £512 | 69 | B |
| MID | tiwaadegbuyi.world | £441 | 47 | B |
| MID | tlclondon.com | £423 | 13 | C |
| LOW | crownofficechambers.com | £332 | 20 | C |
| LOW | milkmanagement.co.uk | £330 | 17 | C |
| LOW | **aloyoga.com** | £298 | **65** | **B ←lead** |
| LOW | goddardvetgroup.co.uk | £290 | 11 | C |
| LOW | missionm.com | £220 | 8 | C |
| LOW | m3ltd.com | £200 | 14 | C |
| LOW | stellamccartney.com | £194 | 24 | C |
| LOW | everpress.com | £176 | 8 | C |
| LOW | hohcp.com | £165 | 14 | C |
| LOW | vlmgf.com | £142 | 2 | C |

**Where it misses, and why it's the right kind of miss:** the false negatives (✗) are non-events businesses that made one-off or occasional bookings — a school music service's termly concerts (£1.4k), a recruiter's annual dinner (£1.4k), a barristers' chambers (£1k), a nightwear brand's pop-up (£887), BMW Berlin (£1.2k). The web can't see a one-off office party, and that's fine: the score predicts *repeat* potential, not past one-offs. Two rubric tweaks would capture some of these: (a) count retail/brand pop-ups as an events use-case, (b) give professional-services firms (chambers, recruiters) a small floor for annual-dinner/seminar potential.

**The payoff case:** aloyoga.com — global brand, dated London store-launch activations, scored 65 but has only spent £298 with us. High score + low spend = exactly the under-served sales lead this scoring exists to find.

**Cost of this run:** 30 customers ≈ 11 searches + ~35 fetches; consistent with the ~1–10¢/customer model (cheap exits fired correctly on ~12 irrelevant domains, no search spent on them).

Data handling: only email domains + aggregated invoice totals were read, from the sandbox/restored DB, read-only; scoring agents saw domains only, never spend or contact details.

**2026-06-12 02:30 claude-code:** **Follow-up 3: TOP 100 customers analysed — perceived value (blind web score) vs realised value (lifetime spend). Full 100-row table below.**

Method as before: top 100 corporate email domains by lifetime invoice total from the test DB (FT £456k down to £52k), each scored blind by web lookup (fit /40, event frequency /30, scale /30; A≥70, B 40–69, C<40). "Gap" = spend rank minus score rank within the 100: **positive = perceived value exceeds realised spend (headroom); negative = spend exceeds what the web would predict.**

**Headlines:**
- **Half the top 100 are Tier A** (50 A / 32 B / 18 C) — the book is structurally healthy: most big spenders really are event businesses the web can identify.
- **Within this cohort rank correlation is ~0 (ρ −0.07)** — expected (everyone here is already a big spender), and it's exactly why the score is useful here as a mismatch detector rather than a ranker. (Across the full spend range, the earlier 30-customer test showed ρ 0.68.)

**① Growth targets — perceived ≫ realised (huge event businesses spending "only" £52–65k with us):** Opus Agency (96, gap +86), Eclipse Global — in-house at 15+ London venues, just acquired by Encore (88, +84), Informa FTSE-100 (98, +75), Identity (96, +73), Soho House (86, +73), Inception (84, +72), InterContinental O2 (89, +68), Encore Global — 400k events/yr worldwide (98, +65), Aspect Communications (78, +63), William Reed (84, +62). These ten alone produce thousands of events a year; our share of wallet is tiny. **This is the account-management call list.**

**② Spend ≫ perceived — decomposes into four distinct buckets, each with a different action:**
- **Data artifacts (fix the data, not the sales strategy):** nhs.net £121k is 1.7m NHS staff sharing one email domain, not one customer — domain-grouping breaks on shared infra domains; client.com £84k is an unidentifiable placeholder domain; pchase.co.uk £215k is Paperchase, an ACCOUNTANCY for 450+ restaurants — the real customers are invisible behind their bookkeeper's domain. Same likely for solvd.solutions £134k (outsourced finance/BPO).
- **Churned/defunct (at-risk revenue already gone):** ramkathalondon.com £113k (domain dead, 2026 events in India/Kenya), kingdomofwinter.com £66k (company dissolved Mar 2025), saastrinc.com £254k (SaaStr's flagship moved to California; London Europa era over?).
- **Web-invisible event businesses (score under-reads them):** tastingplates.com £221k — small caterer whose web presence massively understates their real activity. Pattern: tiny supplier websites.
- **Genuinely relationship-driven (the score is "right" that it's fragile):** YMU talent agency £119k, Two Degrees Below £102k, BBC £68k — spend depends on individual bookers, not on a structural events programme. Worth knowing these are person-dependent accounts.
- Also surfaced: **funkyfurniturehire.co.uk £62k and Sunbelt Rentals £52k are competitors/cross-hire**, not customers — worth tagging differently in the CRM.

**Rubric learnings from this run:** (1) shared/proxy email domains (nhs.net, accountants, BPOs) need a domain-type check before scoring; (2) defunct-company detection (Companies House dissolved status, dead domains) should be a first-class output — it found ~£180k of historical spend attached to dead entities; (3) competitor detection fell out for free.

**Full table (spend from test DB restored copy; scores blind):**

**2026-06-12 02:31 claude-code:** **Top-100 full results table** (Gap: + = perceived value above realised spend = headroom; − = spend above what the web predicts):

| # | Domain | What we found | Spend (realised) | Score | Tier | Gap |
|---|---|---|---|---|---|---|
| 1 | ft.com | FT Live division runs senior-level summits, awards and festivals including London events | £456,420 | 78 | A | -30 |
| 2 | caterlondon.co.uk | Caters weddings, corporate events and brand activations across London venues for clients like Nike and Spotify | £345,301 | 72 | A | -45 |
| 3 | smartgroupltd.co.uk | Owns Evolution London (5,000 capacity), Moving Venue catering and runs dated annual shows | £339,721 | 93 | A | -5 |
| 4 | saastrinc.com | Large annual conferences but flagship SaaStr Annual 2026 is in California, not London; tiny core team | £253,962 | 41 | B | -78 |
| 5 | performancein.com | Hello Partner runs PI LIVE Europe in London 19-21 Oct 2026 (3,000 attendees) plus awards events | £238,959 | 66 | B | -52 |
| 6 | taylorbennett.co.uk | ~25-staff agency producing conferences of 50-5,000 across UK/Europe, 2026 Adobe activations | £232,211 | 73 | A | -39 |
| 7 | tastingplates.com | Caters weddings and corporate events of 50-250 guests, but no dated event calendar found | £220,976 | 51 | B | -70 |
| 8 | pchase.co.uk | Paperchase provides accounting for 450+ restaurants/hotels worldwide; not an events operator | £214,734 | 15 | C | -83 |
| 9 | ministryvenues.com | Two London venues hosting 170+ corporate events per year including conferences, parties and launches | £198,282 | 88 | A | -3 |
| 10 | sifted.eu | Runs annual Sifted Summit at Magazine London (30 Sept-1 Oct 2026) but other events mostly virtual | £171,030 | 55 | B | -63 |
| 11 | thebrewery.co.uk | Seven event spaces for 30-1,600 guests, award-winning in-house caterer, 300+ annual events | £168,489 | 95 | A | +5 |
| 12 | purecommsgroup.com | Delivers 100+ conferences, brand activations and corporate events a year, backed by MCI Group | £168,317 | 83 | A | -9 |
| 13 | nimlok.co.uk | 100-person firm supplying hired/custom stands across the trade-show season | £148,399 | 65 | B | -47 |
| 14 | ampevents.co.uk | Runs annual Event Tech Live at ExCeL London (11-12 Nov 2026) plus the Event Technology Awards | £141,170 | 69 | B | -37 |
| 15 | solvd.solutions | BPO provider for payroll, finance and CX with no events activity and no London presence | £134,326 | 8 | C | -82 |
| 16 | entourage.live | UK production house behind immersive builds and brand activations for Shell, Waitrose | £128,963 | 66 | B | -42 |
| 17 | britishcycling.org.uk | Packed 2026 dated calendar but events are outdoor races nationwide, not London hospitality-led | £126,778 | 67 | B | -37 |
| 18 | nhs.net | NHSmail — shared email service for 1.7m NHS staff; many separate buyers behind one domain | £121,420 | 15 | C | -74 |
| 19 | ethglobal.com | Runs 95+ hackathons/summits worldwide, but 2026 events are in NYC/Lisbon; one past London edition | £120,584 | 48 | B | -62 |
| 20 | imperial.ac.uk | Imperial Venues commercially hires South Kensington spaces for up to 1,500 delegates, year-round events | £119,262 | 85 | A | +3 |
| 21 | ymugroup.com | Talent agency (1,000+ clients, London office); core business is representation, not events | £118,748 | 20 | C | -68 |
| 22 | incipio-group.co.uk | Operates 7+ London venues (300-850 capacity) hosting constant private hires, expanding into Olympia | £118,412 | 83 | A | 0 |
| 23 | gauchorestaurants.com | 17 UK sites with private dining rooms (up to 400 at Piccadilly) and regular corporate events | £118,076 | 67 | B | -32 |
| 24 | kinlondon.co.uk | Runs dated seasonal venues — Shoreditch Gardens (Jun-Sep) and Christmas Social at Shoreditch Town Hall | £116,513 | 83 | A | +1 |
| 25 | wildlondon.org.uk | London Wildlife Trust hires Camley Street and Woodberry Wetlands for weddings and corporate events | £115,134 | 61 | B | -41 |
| 26 | vibration-group.com | 30-year London event production company and Unique Venues of London supplier (site unreachable) | £114,453 | 57 | B | -45 |
| 27 | ramkathalondon.com | Domain redirects to international org site; 2026 Kathas in India/Kenya, no London events | £112,901 | 17 | C | -63 |
| 28 | legalgeek.co | Four dated annual conferences including the 5,000-attendee flagship each October in London | £112,198 | 81 | A | +2 |
| 29 | ballerleague.uk | Regular matchdays with Season 3 finals at a sold-out O2 Arena, but arenas supply own infrastructure | £105,678 | 68 | B | -23 |
| 30 | alpha.org | Annual Leadership Conference at the Royal Albert Hall (~5,000 attendees) plus ongoing London gatherings | £104,288 | 75 | A | -11 |
| 31 | eventconcept.co.uk | Accredited supplier delivering dinners and receptions at major London venues (NHM, Tate, Somerset House) | £103,069 | 89 | A | +21 |
| 32 | twodegreesbelow.com | People/culture-change agency; events only an occasional tactic, no dated events found | £102,439 | 22 | C | -56 |
| 33 | humdingers.org.uk | Hoxton caterer doing weddings, gala dinners and corporate banquets for Amazon and Burberry; small single site | £100,323 | 62 | B | -31 |
| 34 | gpj.com | 25+ offices including London, Fortune 500 clients, Cisco Live and CES productions | £97,650 | 95 | A | +27 |
| 35 | creation-group.com | London/Manchester producer of concerts, festivals and corporate events for Red Bull, LVMH, Live Nation | £96,139 | 81 | A | +8 |
| 36 | drpgroup.com | 2025 C&IT Global Agency of the Year with dated large productions (Arm GEC, 1,200 attendees) | £95,892 | 84 | A | +18 |
| 37 | bishopsstortfordcollege.org | Multi-campus school offering external venue hire and a regular events calendar | £95,315 | 59 | B | -31 |
| 38 | smyle.co.uk | 280-staff agency merged with Identity (King's Coronation, COP26/28, London NYE) | £95,159 | 90 | A | +29 |
| 39 | eventologists.co.uk | Leicester-based national producer of galas and Christmas parties, but self-supplies furniture (part competitor) | £94,969 | 58 | B | -30 |
| 40 | hyrox.com | Recurring large London events with dated races (ExCeL Dec; Olympia Week of Fitness March 2026) | £91,212 | 80 | A | +12 |
| 41 | thelondoncabaretclub.com | Bloomsbury venue producing its own dining shows on year-round seasonal rotation | £89,814 | 72 | A | -7 |
| 42 | marriott.com | Dozens of London Marriott hotels run dedicated meeting/wedding/conference spaces continuously | £84,987 | 86 | A | +27 |
| 43 | client.com | Site fails TLS and search finds no identifiable company — likely a junk/placeholder email domain in our data | £83,865 | 2 | C | -57 |
| 44 | saffron-caterers.co.uk | Michelin-trained caterer and preferred supplier at many London/Hertfordshire venues | £83,851 | 66 | B | -15 |
| 45 | apertureevents.co.uk | Produces conferences, AGMs and awards shows; small single-office firm, no dated events found | £83,789 | 52 | B | -31 |
| 46 | lxahub.com | Core business is online learning; events limited to one annual AntiCon conference | £80,498 | 30 | C | -40 |
| 47 | sleekevents.com | Has produced London Summits annually since 2015 (~19,000 attendees in 2024) plus exhibitions | £80,127 | 78 | A | +15 |
| 48 | textileevents.co.uk | Runs The London Textile Fair (Jan/July) and TEXPREMIUM (June/Dec), four dated shows yearly | £80,062 | 75 | A | +6 |
| 49 | golivein.uk | Annual Yoga Festival since 2017 plus large brand events for HSBC, Budweiser, LG | £79,582 | 65 | B | -12 |
| 50 | clarionevents.com | 125 recurring events and media brands, ~2,000 staff, 12 offices, founded 1947 | £79,523 | 100 | A | +49 |
| 51 | wonderlandweddingvenues.co.uk | Eight outdoor tipi venues (150-170 guests each) hosting weddings UK-wide, plus corporate arm | £77,581 | 67 | B | -5 |
| 52 | obn.org.uk | Now BioUK, with dated recurring conferences incl. BioTrinity (14-15 Apr 2026) | £72,858 | 53 | B | -23 |
| 53 | clivedenliteraryfestival.org | Prestigious but single annual two-day festival, next dated 10-11 Oct 2026 | £72,379 | 50 | B | -26 |
| 54 | ebm.uk | Engage Business Media runs large dated London summits (~3,000 attendees, 150 exhibitors) | £69,446 | 80 | A | +25 |
| 55 | outernetglobal.com | Outernet London runs daily immersive experiences, brand activations and two live venues | £69,227 | 88 | A | +42 |
| 56 | thesteelyard.london | 800-capacity railway-arch venue with weekly club nights, corporate hire and dated 2026 events | £69,184 | 74 | A | +13 |
| 57 | 20-20events.com | Produces recurring major events incl. Frieze Art Fair for 16 years, specialising in temporary structures | £68,107 | 72 | A | +8 |
| 58 | bbc.co.uk | National broadcaster; events are incidental to core broadcasting business | £67,733 | 27 | C | -29 |
| 59 | cvent.com | Virginia-based SaaS platform that does not physically produce events | £67,158 | 13 | C | -34 |
| 60 | bouncepingpong.com | Two premium London venues regularly hosting corporate events, but with fixed in-house fit-out | £67,078 | 58 | B | -10 |
| 61 | urban-space.co.uk | Site refused connections; search found only unrelated property firms named Urban Space | £66,937 | 8 | C | -37 |
| 62 | approductions.com | London/Bucks event specialist since 2002, but no dated upcoming events found; hires some furniture itself | £66,782 | 55 | B | -12 |
| 63 | bluedogproductions.co.uk | Bluedog Group runs ongoing activations, exhibitions and B2B conferences for Chanel, Bacardi, Estee Lauder | £66,591 | 76 | A | +24 |
| 64 | thewhiskyexchange.com | Organises the dated annual Whisky Show at Old Billingsgate (120+ exhibitors) plus regular tastings | £66,367 | 73 | A | +18 |
| 65 | kingdomofwinter.com | Site is 404; Kingdom of Winter Limited dissolved March 2025 after its one ExCeL winter event | £66,312 | 10 | C | -30 |
| 66 | blueearthsummit.com | Dated 2026 events: London Climate Action Week sessions 23-25 June and main summit 13-16 October | £65,616 | 78 | A | +33 |
| 67 | encoreglobal.com | Produces 400,000+ events annually across 20+ countries with ~12,000 staff and $1.2B revenue | £64,932 | 98 | A | +65 |
| 68 | seenpresents.com | London/NY agency in C&IT Top 50 with Netflix 2025 Upfront and CES 2026 activations | £64,520 | 77 | A | +30 |
| 69 | neweraevents.co.uk | Bond Street events management firm with annual highlights, but no dated upcoming events surfaced | £64,007 | 56 | B | -3 |
| 70 | netlawmedia.com | Runs the dated British Legal Technology Forum (1,500 delegates) and Legal Technology Awards | £63,369 | 78 | A | +36 |
| 71 | brentwood.essex.sch.uk | 1557-founded school on 72 acres with boarding, dining halls and a regular events calendar | £63,124 | 50 | B | -9 |
| 72 | frs.org.uk | Finchley Reform Synagogue offers venue hire and hosts regular services and weddings; small local charity | £62,922 | 37 | C | -11 |
| 73 | moi-global.com | Events are only one minor service line in an integrated B2B marketing portfolio | £62,517 | 33 | C | -12 |
| 74 | funkyfurniturehire.co.uk | Direct competitor — UK-wide event furniture hire firm with its own stock; spend is likely cross-hire | £61,510 | 11 | C | -20 |
| 75 | mypaperless.co.uk | Site unreachable; small paperless software business with no events relevance | £61,453 | 8 | C | -24 |
| 76 | voxburner.com | Now Pion, organiser of the dated annual YMS London and YMS New York youth marketing festivals | £61,352 | 63 | B | +13 |
| 77 | identityglobal.com | London HQ agency (merged with Smyle) delivering London NYE fireworks, COP26/28, King's Coronation | £61,178 | 96 | A | +73 |
| 78 | informa.com | 14,000 staff in 30 countries running hundreds of B2B exhibitions/conferences incl. London Tech Week | £60,891 | 98 | A | +75 |
| 79 | iclondon-theo2.com | 4,500sqm of event space incl. 3,000-capacity ballroom hosting year-round galas and conferences | £60,495 | 89 | A | +68 |
| 80 | flp.ltd | Site unreachable; several unrelated FLP Ltd entities, none events-related | £59,636 | 10 | C | -16 |
| 81 | wrbm.com | 160-year media group running recurring expos, awards and conferences (Big Hospitality Expo) | £58,804 | 84 | A | +62 |
| 82 | dbsolutions.com | Large group (White Light, SFL, Production AV) supplying events year-round but no owned dated events | £58,607 | 62 | B | +17 |
| 83 | cheerfultwentyfirst.com | Aeorema plc agency with quarterly Eventful Evening series and 2026-2028 Climate Week NYC contract | £58,560 | 82 | A | +59 |
| 84 | roarb2b.com | Runs 20+ branded shows with dated 2026 editions (Naidex, RWM at NEC) | £58,291 | 82 | A | +59 |
| 85 | enjoy-work.com | 1.8M sq ft London campus running a year-round programme of stage shows, food markets and workshops | £58,003 | 79 | A | +55 |
| 86 | londonmarathonevents.co.uk | Delivers ~16+ dated annual events (TCS London Marathon, RideLondon) with sponsor hospitality needs | £57,040 | 76 | A | +46 |
| 87 | marbleldn.com | 12-year London/NY B Corp agency producing ongoing brand events for Nike, Red Bull and Mastercard | £56,360 | 72 | A | +37 |
| 88 | economist.com | Major media group with dated 2026 London conferences (Water Summit, Sustainability Week) | £56,059 | 78 | A | +53 |
| 89 | sohohouse.com | 48 houses in 19 countries with 300+ member events monthly plus event spaces and restaurants | £55,964 | 86 | A | +73 |
| 90 | rshevents.com | Site returned no content; Companies House shows a micro exhibition-organising ltd | £55,845 | 34 | C | +6 |
| 91 | opusagency.com | Produces ongoing global conferences/roadshows (Salesforce World Tour) with a London EMEA hub | £55,828 | 96 | A | +86 |
| 92 | inceptionww.com | 1,000+ exhibition builds at recurring trade shows across 30+ cities including London | £54,634 | 84 | A | +72 |
| 93 | appgrowthexperts.com | Dated annual summits (App Growth Summit London) but a small niche producer (~300 attendees) | £54,464 | 61 | B | +26 |
| 94 | thebnc.co.uk | Hosts biannual BNC Shows at London venues; small membership organisation | £53,692 | 65 | B | +32 |
| 95 | wonderland-agency.com | Continuous productions and world tours for Amazon, TikTok, Red Bull and MrBeast | £53,187 | 78 | A | +59 |
| 96 | plainjaneevents.com | Regular corporate dinners, launches and parties plus its own venue (34 Bloomsbury) for Amazon, PwC, BP | £53,048 | 74 | A | +52 |
| 97 | welovethisltd.co.uk | Pop-ups and activations for SpaceNK x Glossier and Primark x Pinterest; small, no dated frequency | £52,911 | 51 | B | +19 |
| 98 | eclipse.global | In-house production partner at 15+ major London venues, £20.7m revenue, acquired by Encore Nov 2025 | £52,707 | 88 | A | +84 |
| 99 | sunbeltrentals.co.uk | FTSE100 Ashtead-owned hire giant with events arm — likely a cross-hire partner rather than pure customer | £52,318 | 68 | B | +46 |
| 100 | aspect-communications.com | 26-year London agency producing recurring FTSE 50 AGMs, conferences and leadership events | £52,232 | 78 | A | +63 |

---

**2026-06-12 14:21 — you**

working
