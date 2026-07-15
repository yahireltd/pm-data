---
id: T-0518
title: "Customer Portal (yasite / yahirenew): login, dashboard, multi-user accounts & permissions"
type: feature
state: done
created: 2026-07-07T09:13:17Z
updated: 2026-07-15T11:16:30Z
project: yahire-website
section: null
parent: null
children: []
order: 10240
priority: p2
reporter:
  kind: human
  name: Zsolt
assignee:
  kind: agent
  name: claude-code
acceptance_criteria:
  - "[x] A customer can log in to the yahirenew portal with email + password, separate from staff auth (own PortalUser identity + portalUser component + _identity-portal cookie)."
  - '[x] "Stay logged in" keeps the session ~2 days via remember-me; logout clears it.'
  - "[x] The logged-in customer's dashboard shows their real orders across the account's merged customer id-set (master + mergedIntoCustomerID children)."
  - "[x] A customer can register via an invite link (/portal/register?token=...) for both staff-initiated and owner-initiated invites, setting their own password."
  - "[x] An account owner can invite team members (from the account's linked people), set each member's permissions from the catalog, and disable/remove them."
  - "[x] Permissions are driven by portal_permissions + portal_user_permissions (owner = implicit all); adding a new option is an INSERT, not a schema change."
  - "[x] Staff (SuperUsers + Sales Manager) can onboard/manage portal customers and the permission catalog from a dedicated page in ya-hire, without staff appearing in portal_users."
  - "[x] Password reset works via emailed token; optional per-user email OTP 2FA can be enabled."
  - "[x] The legacy yahirenew customer login is replaced/redirected to the new portal login; no customer rows are written to the internal user table."
out_of_scope: []
code_anchors:
  - path: yasite/yahirenew/config/main.php
    note: add the portalUser component (identityClass PortalUser, own cookie/session keys, authTimeout ~2 days)
  - path: yasite/common/models/PortalUser.php
    note: new IdentityInterface for portal customers; accountCustomerIds() helper resolving master + merged children
  - path: yasite/yahirenew/controllers/PortalController.php
    note: login/logout/dashboard/register + owner Users page; gated on Yii::$app->portalUser
  - path: yasite/common/models/YaCustomers.php
    note: account = master (mergedIntoCustomerID IS NULL); merged children carry master id; orders/invoices keyed by customerID and not repointed
  - path: backend/controllers (ya-hire)
    note: staff Portal Customers page (SuperUsers + Sales Manager) to onboard/manage portal accounts + permission catalog + optional impersonation
relates: []
blocks: []
blocked_by: []
duplicates: []
duplicate_of: null
agent_runs:
  - id: run-20260710-0528
    model: claude-opus-4-8
    started: 2026-07-10T05:28:16Z
    status: completed
    policy_ack:
      branch: master
      branch_source: project
      allow_commit: false
      allow_push: false
      acknowledged_at: 2026-07-10T05:28:16Z
    ended: 2026-07-10T05:30:08Z
    summary: |-
      Delivered a full customer portal for the Yahire website where customers log in to a private area, separate from staff logins, to see and manage their account. It covers: logging in with email + password and "stay logged in"; two-step verification (a one-time code emailed when signing in on a new device, with trusted devices skipping it); a dashboard and orders list showing the customer's real orders across all their merged customer records; an order detail page with a delivery and collection progress timeline; one-click re-ordering of a past order (pre-filled and handed to the quote page); viewing invoices; managing delivery addresses and requesting billing-address changes; and requesting changes to their account details — both routed to staff to review and apply. Company accounts are multi-user: an account owner can invite colleagues, set what each can do from a permissions list, and disable/remove them. Staff get a dedicated "Portal Customers" admin page (open to superusers and sales managers, with the permissions catalogue kept to superusers) plus a portal panel on each customer's profile to invite customers, manage logins, and approve address/detail change requests.

      Why it matters: previously customers had no self-service — every order status query, address change, or re-order went through the sales team by phone/email. The portal lets customers help themselves and gives staff a tidy back-office to manage it, while keeping customer logins completely separate from internal staff accounts and never writing customers into the staff user table. Without it, that manual load would have kept growing as the customer base does.

      This session's additions on top of the core build: a login lockout (temporary block after 5 failed password attempts) as extra brute-force protection on top of the mandatory login code; a "your trusted devices" list on the account page so a customer can see where their login is remembered and remove a specific device; giving sales managers access to the staff Portal Customers page (minus the permissions catalogue); clearer selected-tab styling and a fix for a customer-search dropdown that was intermittently invisible; and turning the account-details change request into a proper tracked request that staff Apply/Dismiss (same model as billing-address changes) so applied changes land on the customer record and flow onto future orders.
    test_plan: |-
      Requires three tables to exist in the `yasite` DB (all created): portal_login_throttle, portal_detail_requests, plus the earlier portal_* tables. Test the customer site (yahirenew) and the staff system (ya-hire).

      ## Customer login & 2FA
      - [ ] Log in with a portal email + correct password on a fresh browser → prompted for the emailed 6-digit code → enter it → reach the dashboard.
      - [ ] Tick "keep me logged in" / pass 2FA → the device is trusted; log in again from the same browser → no code asked.
      - [ ] LOCKOUT: enter a wrong password 5 times → on the 5th you see "Too many failed attempts… ~15 minutes" and even the CORRECT password is refused until the window passes (or the portal_login_throttle row is cleared). Wait/clear → correct login works and clears the counter. A 30-min gap between attempts resets the count.

      ## Trusted devices (Account page)
      - [ ] Account page lists each trusted device: "Chrome on Mac" style label, IP, last used, trusted-since, with "· This device" on the current one.
      - [ ] Log in from a second browser → both appear. Click Remove on a non-current one → it disappears and will require a code next time on that device. "Forget all" still works.

      ## Orders / order detail / re-order
      - [ ] Dashboard and Orders list: clicking an order number opens the in-portal order detail page (NOT the external contract view). Delivery + collection timelines show sensible steps.
      - [ ] Try an order id that isn't yours in the URL → friendly "not found", not an error page.
      - [ ] Re-order a confirmed order → preview shows items + accessories → pick a saved delivery (and collection) address → Continue → lands on the quote page pre-filled with items, accessories, the chosen address, and the LOGGED-IN person's contact details (not the account owner's). Only dates left to choose.

      ## Addresses & billing request
      - [ ] Add/edit a normal delivery address; billing address is not directly editable → submit a billing change request → emails accounts@ + the account manager, and a pending request appears on the customer's profile in ya-hire.
      - [ ] On the customer profile: Apply (with confirm) writes the new billing address to the record; Dismiss (with confirm) discards. Text sits with padding, note on its own line.

      ## Account-detail change request (NEW, tracked like billing)
      - [ ] Account page → "Request a change to your account details": Company/Account email/Account phone pre-filled → edit one → Send → success + pending notice; sending with nothing changed is refused.
      - [ ] Staff customer profile shows a blue "Account details change requested" box with current → requested per field → Apply (confirm) writes company/email/phone onto the ya_customers record (verify it actually changed) → Dismiss (confirm) discards. Confirm the applied details then show on a fresh quote/order for that customer.

      ## Team management (account owner) & invites
      - [ ] Owner → Users page: invite a colleague from the account's contacts, tick permissions, disable/remove. Invite email arrives with a register link.
      - [ ] Register via the invite link → set password → land in the portal with the granted permissions (e.g. a member without view_invoices can't see Invoices).

      ## Staff Portal Customers page (ya-hire)
      - [ ] As a SUPERUSER: all three tabs (Accounts & invites, Invite a customer, Permissions). Search, invite a customer (owner/member), resend/cancel invite, enable/disable, reset password, manage a user's permissions, add/disable/remove a catalogue permission.
      - [ ] As a SALES MANAGER: can reach the page (via the "Open in portal admin" link on a customer profile) and use Accounts & invites + Invite a customer, but the Permissions tab is HIDDEN and forcing it via a crafted request is refused.
      - [ ] Selected tab is clearly highlighted (light-blue box, not just an underline). Customer-search dropdown appears reliably and styled on every attempt.

      ## Cross-impact / regression
      - [ ] my-quote page still works for a NORMAL (non-portal) quote — the address/contact pre-fill only kicks in for a portal re-order.
      - [ ] Staff refund flow's confirmation prompt still works (we bypassed the global yii.confirm override for portal buttons only).
      - [ ] Changing/resetting a portal password still signs the customer out of trusted devices (2FA required again).
      - [ ] Best-effort behaviour: if any portal_* table were missing or mail failed, pages must not 500 — the request still saves where applicable and failures are logged (check app log category portal.* on a forced failure).

      ## Housekeeping (staff action, not code)
      - [ ] Disable the unused "place_orders" permission on the Permissions tab (decided: re-order stays available to anyone who can view orders).
    records:
      docs: none-needed
      tech_session: none-needed
      status_note: none-needed
labels:
  - customer-portal
  - yahirenew
  - auth
attention: null
version: 34
surface: yahire-website
department: Sales
milestone: MS-001
---

## Goal
A customer portal on **yasite → yahirenew** where customers log in to see/manage orders, invoices, quick re-order, addresses & phone. Multi-user company accounts: a main account holder manages their own team (invite, set permissions, remove). Auth is **separate from internal staff** — its own identity + tables.

Work happens on branch **`customer-portal`** (yasite repo).

## Key decisions (agreed)
- **Separate customer identity + tables** — never mix with the internal `user`/RBAC staff table. New `PortalUser` identity.
- **Isolation:** add a second `portalUser` component to the `yahirenew` tier (own `_identity-portal` cookie + distinct session keys), so customer auth is fully separate from the existing staff `user` component. New portal **replaces** the legacy yahirenew customer login (redirect old `/login`, retire `actionLogin`/`actionSignup`).
- **Auth features:** email + password; "stay logged in" (~2 days via `authTimeout`/remember-me); password reset (token); **optional** email OTP 2FA (per-user, hashed code, ~10-min expiry, rate-limited). Email OTP is 2-step verification, weaker than TOTP — fine for v1, TOTP later.
- **Account model:** an account = a **master `YaCustomers`** record (`mergedIntoCustomerID IS NULL`). "My orders/invoices" = `customerID IN (master + all merged child ids)` (historical rows are NOT repointed on merge). "People to invite" = master customer + `ya_customers_linked_emails` + `ya_customer_contact` + merged `ya_customers` records.
- **Permissions = catalog + mapping (not boolean columns)** so new options never need a schema change: `portal_permissions` (catalog) + `portal_user_permissions` (per-user grants). `role` is a preset — **owner = implicit all**; members get explicit grants. Owner assigns the existing catalog options to their people; they don't create option types.
- **Two management surfaces:**
  - **Staff (ya-hire):** a simple, dedicated "Portal Customers" page — open to **SuperUsers + Sales Manager** (reuse existing roles; one-time route enable). Onboard customers (send invite), manage all accounts, manage the permission catalog, and (optional, audited) **impersonate** a customer for support.
  - **Account owner (yahirenew dashboard):** a "Users" page to manage their own team — invite from the account's linked people, tick permissions per user, disable/remove.
- **Invites:** two sources, one shared `/portal/register?token=…` flow — **staff → customer** (onboard as account owner) and **owner → team** (sub-users). Tracked in `portal_user_invites` (`source` = staff|owner, `invitedByStaffUserID` / `invitedByPortalUserID`, intended `permissions`).

## Tables already created (in the `yasite` DB)
- **`portal_users`** — id, accountCustomerID (master ya_customers.id), email (unique), passwordHash (null until invite accepted), authKey, fName, sName, phoneNo, role (owner|manager|member), status (0 disabled / 5 invited / 10 active), twoFaEnabled, passwordResetToken, createdAt/updatedAt/lastLoginAt. *(The original boolean `can*` flags were dropped in favour of the catalog model below.)*
- **`portal_user_invites`** — id, accountCustomerID, email, fName, sName, role, `permissions` (JSON/CSV of permKeys to grant on accept), token (unique), source ('staff'|'owner'), invitedByStaffUserID, invitedByPortalUserID, status, expiresAt, createdAt.
- **`portal_login_otp`** — id, portalUserID, codeHash, expiresAt, usedAt, attempts, createdAt.
- **`portal_permissions`** — id, permKey (unique), label, description, category, sortOrder, active. Seeded: view_orders, place_orders, view_invoices, pay_invoices, manage_addresses, manage_account, manage_users.
- **`portal_user_permissions`** — id, portalUserID, permissionID, grantedBy, createdAt (unique portalUserID+permissionID).

## Build order (slices)
1. **Customer core (first):** `PortalUser` identity + `portalUser` component → login + remember-me → **dashboard showing real orders** (across merged id-set). Seed a test invite + test user.
2. Register-from-invite page (both invite flows).
3. Owner **Users** page (invite from linked people, per-user permission toggles from the catalog, disable/remove).
4. Password reset → optional email OTP 2FA.
5. **Staff "Portal Customers" page in ya-hire** (SuperUsers + Sales Manager): onboard/invite, manage accounts, manage catalog, optional audited impersonation.
6. Invoices, quick re-order, address/phone management.

## Open items to confirm
- Staff page confirmed in **ya-hire** gated by SuperUsers + Sales Manager (vs a simpler check).
- Whether to include **impersonation** for support (+ an audit log table before go-live).
- Password/lockout policy; GDPR/PII considerations for customer accounts.

## Conversation

**2026-07-09 09:55 claude-code:** **Progress — Orders, delivery status & re-order (slice 6), on branch `customer-portal`**

Built and working in dev (not yet committed/deployed — Zsolt to review & ship):

**Order detail + delivery/collection status page**
- Each order now opens an in-portal detail page showing hire dates, total, status, and a step-by-step delivery **and** collection progress timeline (Scheduled → Out for delivery → En route → Arrived → Delivered). Live-track links appear when a run is out.
- The order number is safe to share/change in the URL — a customer can only ever see their own account's orders (anything else shows a friendly "not found" message, not an error page).

**Dashboard fix**
- On the dashboard, the "Recent orders" number was opening the external contract view in a new tab. It now opens the in-portal order detail page, matching the Orders list.

**Re-order a past order**
- Every order has a **Re-order** button. It opens a preview showing the order's items and their accessories.
- The customer picks a **saved delivery address** (and collection address — same or different) from their account, then continues to the normal quote page with the **items, all accessories, their contact details, and the chosen address already filled in**. They only need to choose new hire dates and submit, which creates a fresh quote for the sales team.
- Deliberately **not carried over**: fees and other non-hire lines from the original order. Pricing uses **current rates**, not the old order's prices.
- Fixed a display glitch where the pre-filled address showed an unlabelled postcode box.

**Navigation**
- Added a "Visit website" link to the portal top bar (opens the main site in a new tab), so customers can get back to the shop from anywhere in their account.

**Notes / not done yet**
- Re-order is available on confirmed **orders** only — **quotes** can't currently be re-ordered (raised as a possible follow-up for reviving old/expired quotes).
- The re-order contact details pre-fill the **account owner's** name/email/phone; if a team member places it, it still shows the owner's details for now (easy to switch to the logged-in user later).

**2026-07-09 12:56 claude-code:** **Progress — Staff "Portal Customers" management in ya-hire (slice 5)**

Built and committed on the ya-hire branch `portal-customer-management` (this is the internal staff system, a separate codebase from the customer-facing site). Not yet reviewed/merged — Zsolt to check & ship.

Staff now have two ways to manage portal customers:

**1. A dedicated "Portal Customers" admin page** (superusers only) with three tabs:
- **Accounts & invites** — a searchable list of every portal login and every pending invitation across all accounts (search by email, name, company or account number; filter by active / disabled / pending / expired). For each person, staff can: enable or disable their login, send them a password-reset email, clear their "trusted devices" (so they'll be asked for a login code again), change exactly what they're allowed to do, invite a colleague onto their account, and resend or cancel a pending invitation.
- **Invite a customer** — search for an existing customer and send them a portal invitation, choosing whether they come in as the account owner or a limited team member. It won't let you double-invite someone who already has a login or a live invite.
- **Permissions** — manage the master list of what portal users can be allowed to do. Staff can add a new permission, turn one on/off, or remove one, and see how many users currently have each. Adding a new option never needs a developer.

**2. A portal panel on each customer's profile page** — when viewing a customer in the normal sales screen, staff see whether that customer is on the portal (and who's registered), and can invite them or a colleague, or resend/cancel an invite, without leaving the page. Superusers get a shortcut through to the full admin page above.

All invitations go out as a branded Yahire email with a link to set a password and get started; links expire after 14 days.

**Still open / to decide before this slice is "done":**
- **Who can access it.** The dedicated admin page is currently superusers-only, while the panel on the customer profile is open to a wider set of sales/accounts roles. The ticket originally said "SuperUsers + Sales Manager". We should pick one rule and make both match.
- **No menu link yet** to the dedicated admin page — right now it's only reached via the shortcut from a customer's profile. Fine if that's intended, otherwise it needs a menu entry.
- **Impersonation** (a staff member viewing the portal as a customer, for support) is still not built — it's flagged as optional and would need an audit log added first.

Note: some staff-side approval of customer address changes (part of slice 6) also lives in this same ya-hire branch, so slices 5 and 6 both touch the staff system, not just the customer site.

**2026-07-09 13:24 claude-code:** **Progress + decisions — 09 Jul 2026 (review with Zsolt)**

**Decisions locked in (closes several open items):**
- **Staff access to portal management — leave as-is for now.** No change needed.
- **Impersonation — not needed.** Dropping it from scope (and with it the audit-log table that would have been required first).
- **Legacy yahirenew customer login — not applicable.** That old login isn't used on the yahirenew site, so there's nothing to redirect/retire.

**Fixes made today (customer site + staff system, not yet committed — Zsolt to review & ship):**
- **Re-order now uses the details of whoever is placing it.** Previously a re-order always pre-filled the account owner's name/email/phone even if a team member did it. Now it uses the logged-in person's own contact details (the company and account, and pricing, still come from the account). Phone falls back to the account's number only if that person has none saved.
- **Billing address change — Apply/Dismiss now ask for confirmation.** On the customer's profile in the staff system, the "Apply to billing address" and "Dismiss" buttons now show a confirmation pop-up first, to prevent accidental clicks.
- **Tidied the billing-request box** on that page — added padding so the text isn't flush against the edge, and put the customer's note on its own line.
- **Billing-request email failures are now logged.** When a customer requests a billing address change, the site emails accounts@yahire.com + the account manager. That send is best-effort; if it silently fails (e.g. mail service auth), it's now written to the app log so a missed notification can be diagnosed rather than looking identical to success.

**Still open:**
- **Password / lockout policy** — one decision left: minimum password rules and whether to lock an account after repeated failed logins (brute-force protection). Everything else (reset link, optional email login codes) is already in.
- **Menu link** to the central "Portal Customers" admin page — Zsolt to add later; for now it's reached via the shortcut on a customer's profile.
- **Re-ordering old quotes** (as opposed to confirmed orders) — still a possible future follow-up, not built.

Net: all six build slices are functionally in place across the customer site and the staff system. Remaining work is the password-policy decision, the menu link, and reviewing/shipping the uncommitted changes above.

**2026-07-09 13:27 claude-code:** **Decision — password / lockout policy settled (09 Jul 2026)**

This open item is now closed. No new work needed — the policy is already in place and judged reasonable:

- **Password rules:** minimum 8 characters, must include an uppercase letter, a lowercase letter and a number. Enforced when a customer registers or resets their password. **Kept as-is.**
- **Two-step verification is always on:** logging in from a new/untrusted device always requires a one-time code emailed to the customer, and that code step is limited to 5 tries before a new code must be requested.
- **Failed-password lockout:** intentionally **not added**. Because the emailed code is mandatory on any untrusted device, guessing a password alone doesn't grant access, so a separate password lockout isn't needed for launch.

**Optional future hardening (not doing now):** add a light throttle on the password step (e.g. temporarily block an email after ~5 failed attempts) as defence-in-depth. Logged as a possible follow-up only.

With this, the only remaining open items on the portal are the menu link to the staff "Portal Customers" page (Zsolt to add) and reviewing/shipping the uncommitted changes.

**2026-07-09 13:33 claude-code:** **Code review of the uncommitted portal changes — 09 Jul 2026**

Reviewed all the not-yet-committed work across both codebases (the customer site: order detail, re-order, address handling, contact pre-fill; and the staff system: the billing Apply/Dismiss confirmations and tidy-up). Overall it's in good shape — no broken flows, nothing that would error, and customer data is properly protected (a customer can only ever see their own account's orders). One nice safety improvement noticed: changing or resetting a password now also signs the customer out of "trusted" devices, so the emailed login code is required again everywhere.

**One decision this review surfaced (for Zsolt):**
- There's a permission called **"place orders"** in the catalogue that currently isn't actually used anywhere. Meanwhile the **Re-order** feature is available to anyone who can *view* orders. So a team member given view-only access can still start a re-order. Re-order only creates a *quote* for the sales team to confirm (not a firm booking), so this may well be fine — but if "place orders" is meant to control who can start new orders, Re-order should be tied to it instead. **Decision needed:** either link Re-order to the "place orders" permission, or accept that viewing implies re-ordering (and treat "place orders" as unused for now).

**Minor note (no action needed now):** the new confirmation pop-ups on the staff billing Apply/Dismiss buttons rely on that page being a normal full-page load, which it is today. Flagged only so it's on record if that page is ever converted to load its content in the background.

Zsolt is committing the changes himself. Aside from the "place orders" decision above, the remaining open items are unchanged: add the menu link to the staff Portal Customers page, and the possible future follow-up of re-ordering old quotes.

**2026-07-10 04:04 claude-code:** **Decision — "place orders" is not a real permission (10 Jul 2026)**

Clarified with Zsolt: anyone can place an order on the public website, so gating anything on a "place orders" permission doesn't make sense.

- **Re-order stays available to anyone who can view orders** — no code change. The earlier open question (should Re-order require `place_orders`) is closed: no.
- **Cleanup:** the seeded `place_orders` entry in the permission catalogue should be **disabled** so it stops showing up as an assignable checkbox when owners/staff set team-member permissions (it controls nothing). This is a one-click "Disable" on the staff Portal Customers → Permissions tab. Reversible; no schema change. (The catalogue lives in the production database, so it's a data toggle, not a code change.)

Remaining open items after this: add the menu link to the staff Portal Customers page, and the optional password-step throttle. Everything in the original acceptance criteria is done and committed.

**2026-07-10 04:09 claude-code:** **Added — login lockout after repeated failures (10 Jul 2026)**

Implemented the optional brute-force hardening we'd parked. On the customer portal login:

- After **5 failed password attempts**, that person is **temporarily blocked for 15 minutes** — during the block the password isn't even checked; they see a clear message: *"Too many failed attempts. Please try again in about 15 minutes, or reset your password below."* (the reset link is right there).
- A **successful login clears the count** straight away, so a customer who just mistyped a couple of times isn't affected once they get it right.
- Counting is done per email **and** device/network, so someone repeatedly guessing gets blocked at their end **without** being able to lock a real customer out of their own account.

This sits on top of the existing protections (the emailed login code is still always required on a new device), so it's defence-in-depth — it mainly stops repeated automated guessing and the log-noise/server load that comes with it.

No database change was needed (it uses the app's short-term cache, which clears itself after the block expires). Uncommitted on branch `customer-portal`.

**Test steps (for review):** try logging in with a real portal email and a wrong password 5 times → on the 5th you should see the "too many attempts" message and be unable to log in even with the *correct* password until ~15 min pass (or clear the app cache). Confirm a normal correct login still works first time on a fresh browser.

**2026-07-10 04:17 claude-code:** **Update — login lockout now uses a shared database table (10 Jul 2026)**

Revised the login-lockout hardening from my earlier note. The behaviour a customer sees is unchanged (after 5 failed password attempts they're temporarily blocked for ~15 minutes, with a clear "try again shortly or reset your password" message; a correct login clears it immediately, and it's counted per email + device/network so no one can lock a real customer out of their own account).

**What changed under the hood:** instead of the app's short-term cache, the attempt counts now live in a small shared **database table** (`portal_login_throttle`). This makes the limit exact even if the site runs across more than one web server behind a load balancer — a browser reload or new tab can't reset it, because it was never stored in the browser to begin with.

**Two operational notes:**
- **One-time setup:** a new table needs creating in the portal database before this takes effect. The SQL is saved in the code at `yahirenew/sql/portal_login_throttle.sql`. (I don't touch the production database directly, so Zsolt runs this.)
- **Safe rollout:** the throttle is best-effort — if the table isn't there yet, or the database has a hiccup, login simply carries on working normally (it "fails open"). So it can't break logins, and deploy order doesn't matter. The emailed login code on new devices remains the real protection; this is defence-in-depth on top.

Still to test before shipping (once the table exists): 5 wrong passwords → the block message appears and even the correct password is refused until the window passes; a normal correct login works first time. Uncommitted on branch `customer-portal`.

**2026-07-10 04:33 claude-code:** **Update — Sales Manager access + clearer tabs on the staff Portal Customers page (10 Jul 2026)**

Two changes on the staff "Portal Customers" admin page (ya-hire), both uncommitted on branch `portal-customer-management`:

**1. Sales Managers can now use the page (but not the permission catalogue).**
Previously the page was superusers-only. Now Sales Managers can get in and use **Accounts & invites** and **Invite a customer** — i.e. see/search portal logins and invites, invite customers and colleagues, resend/cancel invites, enable/disable a login, reset a password, and set an individual user's access. What they **cannot** do is the **Permissions** tab (the master catalogue of what permissions exist) — that stays superusers-only.

This is properly enforced, not just hidden: the Permissions tab isn't shown to them, they're bounced off it if they try to reach it directly, and the underlying "add/enable/remove a permission type" actions are refused server-side even if the request is forged. Superusers are unchanged (all three tabs).

Also extended the "Open in portal admin" shortcut on a customer's profile to Sales Managers (it was superusers-only), otherwise they'd have no way to actually reach the page.

**2. The tabs are now much clearer.**
The selected tab was only marked by a thin underline, which was easy to miss. Now the tabs are boxed with rounded tops, hover feedback, and the **active tab has a light-blue background and border** so it's obvious which section you're in.

Note: this revisits the earlier "leave staff access as-is" decision — Zsolt asked specifically for Sales Managers to have this page (minus the catalogue), which matches the original ticket intent of "SuperUsers + Sales Manager". Worth a quick check logged in as a Sales Manager (no Permissions tab) and as a SuperUser (all tabs).

**2026-07-10 04:49 claude-code:** **Added — trusted-devices list on the customer's Account page (10 Jul 2026)**

Improved the Two-factor authentication section of the portal Account page. Previously it only showed a count ("2 trusted devices skip the code") with a single "Forget all" button. Now the customer sees an actual **list of their trusted devices** — the ones where the emailed login code is currently skipped — with, for each:

- A friendly device name worked out from the browser (e.g. "Chrome on Mac", "Safari on iPhone")
- The IP address it was trusted from, when it was last used, and when it was first trusted
- A clear **"This device"** highlight on the one they're using right now
- A **Remove** button to revoke that one device (with a confirm), alongside the existing **Forget all trusted devices**

This is a standard security-hygiene feature (like Google/Facebook "your devices"): a customer can see everywhere their login is remembered and kick a specific old/shared/public computer off without signing out everywhere.

**Notes:** removing a device just means it'll ask for a login code next time (it doesn't forcibly log anyone out mid-session). Removal is locked to the logged-in user's own devices, so a tampered request can't affect anyone else. No database change — all the needed details (browser, IP, dates) were already being stored.

Uncommitted on branch `customer-portal` (PortalController + the account view).

**Test:** sign in with "keep me logged in" so the device gets trusted → Account page lists it as "This device"; sign in from a second browser → both appear; Remove a non-current one → it disappears and will require a code next time.

**2026-07-10 05:10 claude-code:** **Added — account-detail change requests, now tracked like billing (10 Jul 2026)**

Upgraded the "Request a change" section on the customer's Account page from a single free-text box (which just emailed the team) into a proper tracked request, working the same way as a billing-address change request. Table `portal_detail_requests` created in the `yasite` database.

**Decision behind it:** the portal shows two separate things — the person's **portal login** (their name/login-email/phone) and the **account record** (the company's details that orders and invoices are attached to). We deliberately kept everything **request-only, no self-editing**, and this request targets the **account record** so that once staff apply it, the new details flow onto the customer's future orders.

**Customer side (portal → Account):**
- Instead of one message box, there are now separate fields for **Company, Account email, Account phone**, pre-filled with the current values — the customer edits what they want changed and sends.
- Submitting saves a tracked request and emails **accounts@ + the account manager** a clear "current → requested" comparison.
- Shows a note if they already have a request awaiting review, and makes clear this does **not** change their portal login email — only the account contact details.

**Staff side (ya-hire customer profile):**
- A new "Account details change requested" box (styled blue, to tell it apart from the amber billing one) lists each pending request showing **current → requested** per field.
- **Apply** writes the company/email/phone onto the customer record; **Dismiss** discards it — both behind the same confirmation pop-up, and recorded (who/when). Same staff-permission gate as the billing action.

**Notes:** best-effort throughout (a missing table or mail hiccup won't break the page or lose the request silently — failures are logged). No change to the billing flow. The old free-text-to-info@ behaviour is fully replaced.

Uncommitted: yasite `customer-portal` (PortalController, account view) and ya-hire `portal-customer-management` (SalesController).

**Test:** on the portal, change Company/phone on the Account page → Send → request saved + email sent, pending notice appears → on the customer's profile in the staff system, the blue box shows current → requested → Apply updates the customer record (check it lands on the ya_customers record); Dismiss discards.

**2026-07-10 05:30 claude-code:** Run run-20260710-0528 completed — Delivered a full customer portal for the Yahire website where customers log in to a private area, separate from staff logins, to see and manage their account. It covers: logging in with email + password and "stay logged in"; two-step verification (a one-time code emailed when signing in on a new device, with trusted devices skipping it); a dashboard and orders list showing the customer's real orders across all their merged customer records; an order detail page with a delivery and collection progress timeline; one-click re-ordering of a past order (pre-filled and handed to the quote page); viewing invoices; managing delivery addresses and requesting billing-address changes; and requesting changes to their account details — both routed to staff to review and apply. Company accounts are multi-user: an account owner can invite colleagues, set what each can do from a permissions list, and disable/remove them. Staff get a dedicated "Portal Customers" admin page (open to superusers and sales managers, with the permissions catalogue kept to superusers) plus a portal panel on each customer's profile to invite customers, manage logins, and approve address/detail change requests.

Why it matters: previously customers had no self-service — every order status query, address change, or re-order went through the sales team by phone/email. The portal lets customers help themselves and gives staff a tidy back-office to manage it, while keeping customer logins completely separate from internal staff accounts and never writing customers into the staff user table. Without it, that manual load would have kept growing as the customer base does.

This session's additions on top of the core build: a login lockout (temporary block after 5 failed password attempts) as extra brute-force protection on top of the mandatory login code; a "your trusted devices" list on the account page so a customer can see where their login is remembered and remove a specific device; giving sales managers access to the staff Portal Customers page (minus the permissions catalogue); clearer selected-tab styling and a fix for a customer-search dropdown that was intermittently invisible; and turning the account-details change request into a proper tracked request that staff Apply/Dismiss (same model as billing-address changes) so applied changes land on the customer record and flow onto future orders.

---

**2026-07-10 05:38 — you**

tested, any new updates/features to be discussed later

**2026-07-13 10:24 claude-code:** **Follow-up polish — staff "Portal Customers" page (ya-hire) + portal reset email (13 Jul 2026)**

A round of usability and tidy-up work, mostly on the staff Portal Customers admin page. On ya-hire branch `portal-customer-management` (uncommitted, for review) unless noted.

**Customer-facing (yasite `customer-portal`, committed):**
- The **password-reset email** sent from the portal login page is now branded to match our other portal emails (pink header, proper button, footer) instead of the previous plain-text version.

**Staff Portal Customers page (ya-hire):**
- **Colour-coded the action buttons** on the Accounts & invites tab so they read at a glance — green = enable / invite, red = disable, blue = reset password, amber = trusted devices.
- **"Permissions" and "Invite a member" now open in a pop-up window (modal)** instead of a panel appearing at the top of the page — cleaner and keeps you in context; the Save/Cancel buttons sit in the modal footer.
- **Success and error messages now show as a small pop-up notification** (top-right), and the confirmation prompts use the same styled pop-ups, for a consistent look.
- **"Invite a member" now validates before sending** — you must pick a contact or enter an email, enter a first name + surname when typing details in manually, and tick at least one permission; otherwise it lists what's missing.
- **"Forget devices" is now "Trusted devices"** — it opens a list of the devices where that customer currently skips the login code, showing browser/OS, IP address, when it was first trusted and last used. You can forget a single device or forget them all from there (each with a confirmation). Note: this is the trusted-device list, not a full login history (we don't record every individual login).
- **Adding a permission is stricter and simpler** — key, label and description are now *all* required (was key + label only), and the manual "sort order" box is gone: new permissions are numbered automatically (highest + 10).

**Housekeeping (DB):** dropped four unused columns from `portal_users` (`canOrder`, `canViewInvoices`, `canManageAddresses`, `canManageUsers`) — leftovers from before the permissions-catalogue model; confirmed nothing in any codebase reads them.

No other database changes; the trusted-devices list reuses details we already store. Worth a quick click-through on the Portal Customers page once the ya-hire branch is reviewed/shipped.
