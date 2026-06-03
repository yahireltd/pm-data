---
id: TS-003
slug: multi-user-foundation-sso-three-tier-rbac-scoped-portal
title: "Multi-user foundation: SSO + three-tier RBAC + scoped portal"
project: pm-tool-self
created: 2026-06-03T22:23:10Z
updated: 2026-06-03T22:23:10Z
decisions:
  - "Scaffold SSO non-breaking first: Auth.js v5 + Microsoft Entra wired but dormant until creds exist, so nothing breaks before Azure setup (401139b)."
  - "Three-tier per-project RBAC: admins (config.yml access.admins) full access; members (project.team) full access to that project; stakeholders (project.stakeholders) read+comment only. access.ts resolves projectAccess(email,slug) (5a157e4)."
  - "Enforce per-action authz: requireTeam(slug) guards every mutating action in projects.ts + meetings.ts (51 fns) and checks canMutateProject, closing the cross-project crafted-request hole (aed64e1, 0acb4f0)."
  - Admin 'Preview as' impersonation honours a pm_preview_as cookie ONLY for real admins and can only RESTRICT the view, never escalate — verify member/stakeholder access without a second account (d0a3192).
  - "No pre-auth leak: signed-out root layout renders a bare login shell — no sidebar/nav/counts/project names before sign-in (aed64e1)."
actions:
  - text: Foundation shipped across 401139b→0acb4f0; remaining per-action authz on ticket/decision/sprint actions is route-gated (follow-up).
    ticket: T-0172
side_quests:
  - Login screen redesign + unified SSO identity (TopNav shows the signed-in user; pm_author synced to the real login).
problem: The pm-tool was single-user; larger projects ahead need real multi-user access — Microsoft sign-in, per-project roles, and stakeholders who see only their projects.
success_criterion: App behind Microsoft SSO; admin/member/stakeholder resolved per project; stakeholders read+comment only; all mutating actions authz-gated.
phase: build
---

# TS-003: Multi-user foundation: SSO + three-tier RBAC + scoped portal

## Problem

## Success criterion

## Decisions

## Actions

## Side-quests
