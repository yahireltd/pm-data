---
id: T-0573
title: ZL Run Overview Updates.
type: feature
state: triaged
priority: p2
created: 2026-07-14T16:22:23Z
updated: 2026-07-14T16:23:27Z
project: yasystem
section: null
parent: null
milestone: null
children: []
order: 41984
reporter: null
assignee: null
acceptance_criteria: []
out_of_scope: []
code_anchors: []
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs: []
labels: []
attention: null
version: 3
attachments:
  - key: tickets/T-0573/1784046183546-Screenshot_2026-07-14_at_17-22-41_.png
    filename: Screenshot 2026-07-14 at 17-22-41 .png
    content_type: image/png
    size: 168400
    uploaded_by: austin@yahire.com
    uploaded_at: 2026-07-14T16:23:04Z
---

A few things that have been a pain point for the warehouse. Zac has said the runs overview is getting cluttered with warehouse often missing the unloading on the app (they do actually unload it but do not put the counts in properly etc) 

One of the problems is that when they enter a count less than there shuld be - the system expects an issue report for the number missing (it does a check on complete unload for any counts less than expected and then triggers the issue report modal)The problem with the issue reporting from the system is that it asks you for 3 seperate counts when reporting any item issue (damaged missing dirty) then some of the options have photo required. I want to change the issue reporting view to work as it does in the driver app. This still forces photos where required but the ui and functionality are better in the driver app. 

In addition to the new UI for issue reporting, we also need a report quick missing issue. This will be for items that we enter less than the expected qty (on unload view only). It should say you have entered 20/28 do you want to report 8 missing and have a yes no button. This will make it easier for them to  report missing issues and process the unloads without as much friction. 

in addition to that on the runs overview- when the vehicle is at status back at base - can we have a manager override (same fast forward icon but in red and only allowed by certian users (we can make the backend endpoint and use RBAC to set the permissions The manager override should Mark all contracts as unloaded, create an issue report on each contract (we can create a new issue type for this (contract level - Unloaded without system count), and mark the run as complete.\
\
We also want to make a slight change to the left hand side we have an icon \<i style="margin-left:3px;font-size:1.5em; --fa-primary-color:#333 ; --fa-secondary-color:black ;float:right; " onclick="runComplete(24777, 0)" class="fas fa-fast-forward">\</i> that shows on runs with no deliveries (hence no loading needed) the fast forward button on that side currently marks the run as on the road. We want to change this to Loaded / Ready Status.
