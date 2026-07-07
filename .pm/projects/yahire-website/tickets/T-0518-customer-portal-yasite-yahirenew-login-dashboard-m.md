---
id: T-0518
title: "Customer Portal (yasite / yahirenew): login, dashboard, multi-user accounts & permissions"
type: feature
state: triaged
created: 2026-07-07T09:13:17Z
updated: 2026-07-07T11:21:05Z
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
  - A customer can log in to the yahirenew portal with email + password, separate from staff auth (own PortalUser identity + portalUser component + _identity-portal cookie).
  - '"Stay logged in" keeps the session ~2 days via remember-me; logout clears it.'
  - The logged-in customer's dashboard shows their real orders across the account's merged customer id-set (master + mergedIntoCustomerID children).
  - A customer can register via an invite link (/portal/register?token=...) for both staff-initiated and owner-initiated invites, setting their own password.
  - An account owner can invite team members (from the account's linked people), set each member's permissions from the catalog, and disable/remove them.
  - Permissions are driven by portal_permissions + portal_user_permissions (owner = implicit all); adding a new option is an INSERT, not a schema change.
  - Staff (SuperUsers + Sales Manager) can onboard/manage portal customers and the permission catalog from a dedicated page in ya-hire, without staff appearing in portal_users.
  - Password reset works via emailed token; optional per-user email OTP 2FA can be enabled.
  - The legacy yahirenew customer login is replaced/redirected to the new portal login; no customer rows are written to the internal user table.
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
version: 7
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
