# Adaptive Digital Discipleship Platform --- Development Document Pack

**Status:** Implementation baseline for a controlled pilot\
**Version:** 1.0\
**Date:** 2026-10-11

## Purpose

This pack defines the product, discipleship framework, technical
architecture, API/data contracts, security and safeguarding rules, test
strategy, sprint execution plan, and coding-agent guardrails for an
offline-first, network-governed digital discipleship platform.

The platform supports three pathways: **Formation**, **Restoration**,
and **Biblical Discernment**. It helps people begin or resume a journey
at any time without shame or punitive treatment. It does not replace
local churches, pastoral ministry, human relationships, or accountable
theological review.

## Documents

1.  `PRODUCT.md` --- product scope, roles, workflows, measurable
    outcomes, and exclusions.
2.  `DISCIPLESHIP_FRAMEWORK.md` --- pathway model, content standards,
    progression, theological governance, and adaptation rules.
3.  `ARCHITECTURE.md` --- technology stack, system boundaries, offline
    design, domain model, and deployment.
4.  `API_AND_DATA_CONTRACTS.md` --- canonical entities, API conventions,
    endpoint contracts, validation, and synchronization semantics.
5.  `SECURITY_AND_SAFEGUARDING.md` --- privacy, access control, pastoral
    boundaries, AI safeguards, and incident handling.
6.  `TEST_STRATEGY.md` --- required automated/manual tests and release
    gates.
7.  `SPRINT_PLAN.md` --- ordered sprint backlog with task IDs,
    dependencies, and acceptance criteria.
8.  `AGENTS.md` --- binding instructions for AI coding agents.
9.  `DECISION_LOG.md` --- confirmed decisions, assumptions, and items
    that require an accountable owner.
10. `docs/database.md` --- authoritative single source of truth (SOT)
    for the database schema, canonical tables, fields, constraints,
    indexes, and client-side Dexie.js offline stores.
11. `docs/BENCHMARK_REVIEW.md` --- comprehensive benchmark review
    against 5 industry platforms, detailing theological and architectural
    mediations, gap analysis, and phased enhancements.

## How to use this pack

-   Treat these documents as the source of truth for the pilot.
-   Product and theological owners must approve content, doctrinal
    standards, consent language, and safeguarding procedures before real
    participants are onboarded.
-   Build one vertical slice at a time. Do not start later-sprint
    features while required acceptance criteria from the current sprint
    are failing.
-   If a requirement conflicts with law, security, or safeguarding, stop
    the affected work and raise a decision; do not silently override the
    guardrail.
-   Every requirement must be traceable to a task and a test or review
    method.
-   Any change to scope, schema, permissions, synchronization semantics,
    or theological policy must be recorded in `DECISION_LOG.md`.

## Pilot success definition

The pilot is ready only when a participant can be enrolled through a
traceable outreach/event/missioner/ministry source, access any assigned
pathway, complete and resume approved lessons online or offline,
synchronize without data loss or duplicate activity, and optionally
connect with a permitted mentor. Authorized ministry users can view
aggregate enrolment reporting without obtaining unrestricted access to
participants' private reflections or sensitive pastoral details.
