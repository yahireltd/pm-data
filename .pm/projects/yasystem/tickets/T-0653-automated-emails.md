---
id: T-0653
title: Automated emails
type: feature
state: review
priority: p2
created: 2026-07-23T14:37:31Z
updated: 2026-07-23T19:23:37Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 44032
reporter: null
assignee:
  kind: agent
  name: claude
acceptance_criteria:
  - create a reusable way to create automated emails for logistics
  - create the template & improve the wording
  - a way to send a test email to see the formatting
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260723-1438
    model: claude
    started: 2026-07-23T14:38:58Z
    status: completed
    summary: "Built the automated customer-email system for logistics and shipped its first email: \"No Legal Parking\". When a driver can't find legal parking at a job, one button press (after a confirmation) immediately emails the customer that parking is difficult and charges may be passed on — no phoning the office, no typing. Customer service can edit the wording themselves on a new \"Automated Emails\" page under Logistics, send themselves test copies, and switch each email on or off; the parking email ships switched OFF until the wording is approved, so nothing reaches customers yet. Safety rules: each email can only be sent once per job visit (delivery and collection each get one), so a driver can't accidentally spam a customer, and pressing the button before marking arrival also records the arrival automatically. The button is available everywhere drivers and staff work: on the driver app's My Runs cards and job screens (Android 1.0.15 released tonight; iPhone build ready, awaiting TestFlight submission), on the drivers' web my-runs pages, and in the office run-planner under a new Emails dropdown (future emails like \"failed job\" and \"15 minutes away\" slot into the same dropdown with no new plumbing). Along the way we found and fixed why app updates were looping (the update button was serving an old file from the marketing website, and the build had a stale internal version), fixed an access rule that made the app report \"could not reach the server\", and made the version check platform-aware so iPhone drivers can never be locked out of the app by an Android-only release. If we'd done nothing, drivers would keep ringing the office about parking while customers stayed uninformed, and tonight's release would have locked iPhone drivers out of the app entirely."
    ended: 2026-07-23T19:23:37Z
    test_plan: |-
      **Prep:** the template is DISABLED — real sends refuse until you enable it on Logistics → Automated Emails.

      1. **Manager page:** Logistics → Automated Emails → edit the body text, save, "Send test to me" → email arrives with [TEST] prefix, real job details in the tokens, T&Cs link clickable, from logistics@yahire.com, order number in the subject.
      2. **Draft-state refusals (before enabling):** press the parking button in the driver app, driver web my-runs, and run-planner Emails▾ → all refuse with a "disabled" message; confirm the customer got nothing and nothing was logged.
      3. **Enable the template**, then in the driver app (1.0.15) expand an UNCOMPLETED delivery on My Runs (completed jobs deliberately hide the button — footer must say Start/Continue, not "View") → amber "No Legal Parking — Email Customer" under Navigate → confirm dialog mentions it will record arrival → send → success alert; check the customer email AND that the job is now marked arrived (runs overview).
      4. **No-spam rule:** press again → button greyed "Parking Email Sent ✓"; try the SAME job from run-planner Emails▾ → Parking → refusal naming the contract ("only once per job"). The COLLECTION of the same contract must still be allowed its own send.
      5. **Driver web:** my-runs and my-runs-offline → "No Parking" button next to Running Late / Failed Job → Swal confirm → same send/refusal behaviour. Check a non-driver login cannot POST /run-contracts/send-customer-email.
      6. **Run-planner cosmetics:** shortened labels (Contract / Split / Call / Comment / PDF) and the Emails dropdown on both the editable and read-only planners.
      7. **Edge cases:** a contract with no email address → named refusal; app button pressed with no signal → "check signal" message and nothing sent; arrival auto-record when GPS is denied still records (without coords).
      8. **Cross-impact:** job screens' I Have Arrived / Report Issue / Bad Load unchanged; JobCard breaks/memos render normally; cust_contact_send_logs shows "Logistics: No Legal Parking (Delivery)"/"(Collection)" entries.
      9. **Release checks:** Android driver on 1.0.13 opens app → update wall → Update downloads 1.0.15 from www.yahire.com and installs (wall gone); iPhone driver on 1.0.13 is NOT walled (server advertises 1.0.13 to iOS); after `eas submit` + adding build 13 to TE/DR TestFlight groups, iPhone drivers receive the parking button without a beta re-review.
    records:
      docs: updated
      tech_session: none-needed
      status_note: none-needed
      docs_note: "driver-app/docs/DEPLOYMENT.md rewritten: four Android version sync points (untracked build.gradle!), www.yahire.com APK upload step, platform-split advertised versions, hard update-wall warning, iOS buildNumber-only release flow, mdm allowActions gotcha for new api-drivers routes."
    artifacts:
      - type: commits
        detail: a4134883 (email framework + manager page + app API), f4a23677 (standalone button + once-per-job), 8e87952d (run-planner Emails dropdown), 6a8054c9 (my-runs web buttons + RBAC migration), 54d9c279 (box-local APK download route), c1137f73 (1.0.14 bump), d51a4fec (mdm gate fix + 1.0.15), e343647b (platform-aware app-version), 3e945071 (driver-app 1.0.15 source), 42ed6d5b (DEPLOYMENT.md)
      - type: release
        detail: Android 1.0.15 (versionCode 15) staged on both boxes + www.yahire.com; iOS 1.0.13 build 13 built on EAS (ff21c838), submission pending Apple login; live advertises Android 1.0.15 / iOS 1.0.13
      - type: migrations
        detail: m260723_150000 (templates+menu), m260723_160000 (once_per_job, un-seed issue type), m260723_170000 (my-runs route auth) — applied live+test
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-23T19:23:37Z
version: 10
---

Hi Austin,

 

As discussed we would like to automate some of the logistics emails, things to bear in mind, the templates are old and could do with revising, I think this would be best placed for customer service to determine the wording but I know they are busy. And the email title should contain the job number for reference and searching if we need to check the emails at a later date

 

When failing a delivery or collection, I envision it being via the failed job issue report, maybe we could ask a couple questions to make the email more specific, for example have you called the customer? Have you rung the doorbell? Then the email could say we called and rang at the bell and had no response. Essentially theres a couple of scenarios when you failed a job, i.e. the goods were still in use so we are unable to collect, the goods cannot be located on site so we are unable to collect. Or there could be no access to the building, but we have managed to contact the customer but they are not onsite and cannot get us access to the collection address, or we have no access and are unable to contact the customer on the numbers provided.

 

 

1.      No Legal Parking

 

Trigger: button on the drivers app, (maybe have similar button on run planner in case we have to trigger from logistics)

 

Template:

Title: Yahire \<Delivery / Collection> \<Order Number>

“ Our driver is currently onsite for the \<delivery collection> of hired goods, unfortunately he is unable to find any legal parking.

 

As per our [T\&C’s](https://www.yahire.com/hire-terms "https://www.yahire.com/hire-terms") if we incur any parking tickets, this may be passed on to yourselves.

 

Please feel free to contact us if you have any queries”

## Conversation

**2026-07-23 15:30 claude:** Run run-20260723-1438 completed — Built the reusable automated-email framework for logistics and the first email — "No Legal Parking" — scoped per Austin to just that template for now. Email wording now lives in the database, editable on a new Logistics → Automated Emails page, so customer service can revise it any time without a developer; every template has a "Send test to me" button that emails the staff member a true-to-life render (real order number, name and time window) so formatting can be checked before anything goes near a customer. Real sends always carry the order number in the subject for later searching, are sent from logistics@yahire.com, and are logged against the contract in the same audit trail as other customer emails.

The driver-app trigger needs NO app update: the app fetches its issue list from the server, so a new "Parking → No legal parking" issue was seeded, and logging it automatically emails the customer — with two safety layers: the template ships DISABLED (drivers can log the issue immediately, but no customer email goes out until it's enabled on the new page after wording review), and an email problem can never break the driver's sync. The trigger mechanism is generic — any future issue option can be wired to any template by setting one field, no code. Failed-job scenario emails and a run-planner button were deliberately deferred per Austin's scoping.

**2026-07-23 16:55 claude-code:** **Revisions per Austin + full deployment (23 Jul):**

1. **Parking is a standalone button, not an issue type.** The seeded Parking issue was removed; the driver app instead gets dedicated endpoints: POST /api/drivers/customer-email {runContractId, templateKey} (app confirms, then sends immediately) and GET /api/drivers/customer-email-status (so the app can grey the button). The app-side button itself is app-team work — server contract is ready.

2. **No-spam rule is once per JOB** (contract + movement): the delivery and the collection each get one send — parking trouble at delivery usually repeats at collection. Tracked as separate send-log events ("Logistics: No Legal Parking (Delivery)"/"(Collection)"); repeat attempts get a named refusal; a genuine mail failure withdraws the log entry so retries aren't permanently blocked.

3. **Run-planner trigger added**: job-row buttons slimmed (Contract / Split / Call / Comment / PDF) and a new amber "Emails" dropdown with Parking as the first option (failed-job + 15-minute emails to join later). Confirm dialog, immediate send, same service and gates as the driver button — so the once-per-job rule holds across both surfaces.

**Deployed to the routing test box AND live**; both migrated. The template remains DISABLED everywhere — test sends work for wording review, real sends refuse with a named reason until it's enabled on Logistics → Automated Emails. Also shipped alongside: the sandbox nightly refresh now preserves auth_assignment (test-box access grants survive; re-grant yesterday's tester once, tonight onward it sticks).
