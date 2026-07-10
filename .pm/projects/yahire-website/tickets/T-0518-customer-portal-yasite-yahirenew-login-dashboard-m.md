---
id: T-0518
title: "Customer Portal (yasite / yahirenew): login, dashboard, multi-user accounts & permissions"
type: feature
state: triaged
created: 2026-07-07T09:13:17Z
updated: 2026-07-10T04:17:48Z
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
  kind: human
  name: zsolt@yahire.com
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
agent_runs: []
labels:
  - customer-portal
  - yahirenew
  - auth
attention: null
version: 24
surface: yahire-website
department: Sales
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
