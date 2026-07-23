---
id: T-0653
title: Automated emails
type: feature
state: in_progress
priority: p2
created: 2026-07-23T14:37:31Z
updated: 2026-07-23T14:38:58Z
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
    status: in_progress
    summary: Claimed via web UI
labels: []
attention: null
version: 7
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
