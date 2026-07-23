---
id: T-0653
title: Automated emails
type: feature
state: review
priority: p2
created: 2026-07-23T14:37:31Z
updated: 2026-07-23T15:30:51Z
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
    summary: |-
      Built the reusable automated-email framework for logistics and the first email — "No Legal Parking" — scoped per Austin to just that template for now. Email wording now lives in the database, editable on a new Logistics → Automated Emails page, so customer service can revise it any time without a developer; every template has a "Send test to me" button that emails the staff member a true-to-life render (real order number, name and time window) so formatting can be checked before anything goes near a customer. Real sends always carry the order number in the subject for later searching, are sent from logistics@yahire.com, and are logged against the contract in the same audit trail as other customer emails.

      The driver-app trigger needs NO app update: the app fetches its issue list from the server, so a new "Parking → No legal parking" issue was seeded, and logging it automatically emails the customer — with two safety layers: the template ships DISABLED (drivers can log the issue immediately, but no customer email goes out until it's enabled on the new page after wording review), and an email problem can never break the driver's sync. The trigger mechanism is generic — any future issue option can be wired to any template by setting one field, no code. Failed-job scenario emails and a run-planner button were deliberately deferred per Austin's scoping.
    ended: 2026-07-23T15:30:51Z
    test_plan: |-
      On the test box (deployed + migrated; live follows when Austin is happy):

      1. Menu + access: Logistics → Automated Emails appears in the sidebar and opens for a Transport Manager / SuperUser; a plain logistics user without those roles gets a permission error.
      2. Test send: on the No Legal Parking card, click "Send test to me" → email arrives with subject "[TEST] Yahire <Delivery/Collection> <order no> — parking difficulty on site", body rendered with a real contract's name/order/window, T&Cs as a clickable link. Nothing appears in cust_contact_send_logs (tests are unlogged).
      3. Wording edit: change the body, Save, send another test → new wording. Tokens render (try adding {date} {window}).
      4. Draft gate: with the template still DISABLED, simulate the driver trigger (POST /api/drivers/sync/issue with a runContractId + the "No legal parking" option id 100 on the test box) → issue saves, response says customerEmailed: false, log line notes the template is disabled — NO customer email.
      5. Enable + real trigger: tick Enabled, Save, repeat step 4 against a test contract whose email is YOURS → email arrives (no [TEST] prefix), and cust_contact_send_logs gains a row for that contract under event "Logistics: No Legal Parking".
      6. Driver app: the app's issue list now shows Parking → No legal parking (server-fed; may need the app's issue cache to refresh).
      7. Cross-impact: log any OTHER issue type from the app → saves exactly as before (customerEmailed: null in response, no email); disruption-notifications page unaffected.
      8. Go-live: git pull + php yii migrate on live; template arrives DISABLED — review wording, test-send, then enable when ready.
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels: []
attention:
  needed_by: human
  reason: Agent finished — confirm and close, or send back
  since: 2026-07-23T15:30:51Z
version: 8
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
