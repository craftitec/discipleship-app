# AGENTS.md --- Mandatory Coding-Agent Rules

**Version:** 1.0 \| **Applies to:** all AI coding agents and automated
implementation tools

## 1. Source of truth and priority

Read these documents before implementing any task: 1. `PRODUCT.md` 2.
`DISCIPLESHIP_FRAMEWORK.md` 3. `ARCHITECTURE.md` 4.
`API_AND_DATA_CONTRACTS.md` 5. `SECURITY_AND_SAFEGUARDING.md` 6.
`TEST_STRATEGY.md` 7. `SPRINT_PLAN.md` 8. `DECISION_LOG.md` 9.
`docs/database.md` 10. `docs/BENCHMARK_REVIEW.md`

If repository-specific conventions exist and do not conflict with these
documents, follow them. If two requirements conflict, stop the affected
task and ask the product/technical owner. Never resolve theological,
safeguarding, privacy, or scope conflicts by guessing.

## 2. Task discipline

-   Work on one named sprint task at a time unless explicitly directed
    otherwise.
-   Identify task ID and acceptance criteria in the implementation
    summary.
-   Inspect existing files, tests, migrations, and conventions before
    editing.
-   Prefer small, coherent changes over broad rewrites.
-   Do not silently alter APIs, data contracts, roles, retention, or
    sync semantics.
-   Do not add dependencies without a concrete need and a brief
    justification.
-   Do not introduce microservices, a message broker, a new database, or
    a new state-management framework without approval.
-   Do not implement out-of-scope features.
-   Do not claim a task is complete unless all acceptance criteria have
    evidence.

## 3. Required implementation workflow

For every task: 1. State the task ID and intended minimal change. 2.
Inspect relevant code and test coverage. 3. Identify assumptions and
dependencies. 4. Implement. 5. Add/update tests. 6. Run relevant focused
tests. 7. Run the required repository checks. 8. Review the diff for
unrelated changes, secrets, data leaks, and migration risks. 9. Update
documentation/contracts if behaviour changed. 10. Report files changed,
tests run, results, remaining risks, and decisions required.

If a test cannot be run, say so and explain why. Never fabricate test
output.

## 4. Prohibited shortcuts

-   No fake implementations, TODO-only stubs, or hard-coded success
    responses in production paths.
-   No swallowing exceptions without safe handling and observable
    failure.
-   No frontend-only authorization.
-   No client-trusted role, ministry ID, content approval state, source
    attribution snapshot, authoritative score, or server timestamp.
-   No plaintext credentials, secrets in source control, or real
    participant data in fixtures.
-   No private reflections, tokens, or safeguarding narratives in
    ordinary logs.
-   No destructive schema changes without migration/rollback plan.
-   No automatic deletion of unsynced local events.
-   No mutation of published content versions.
-   No AI output treated as authoritative doctrine or automatically
    published.
-   No spiritual maturity, salvation, faithfulness, or backsliding
    score.
-   No automated restoration enrolment based solely on inactivity.
-   No unapproved user tracking, analytics SDK, advertising tracker, or
    external data transfer.
-   No broad refactor to "clean up" unrelated files.

## 5. Backend rules

-   Enforce authorization in the API/service layer for every protected
    operation.
-   Use database constraints for uniqueness and integrity where
    applicable.
-   Wrap multi-record operations in transactions.
-   Validate request schemas and bound payload sizes.
-   Use stable error codes without leaking inaccessible resource
    existence.
-   Keep modules separated by domain responsibility.
-   Do not let AI code paths alter access, consent, enrolment, content
    approval, or safeguarding state.
-   Write tests for allowed and denied access, including cross-ministry
    cases.

## 6. Frontend and PWA rules

-   Use TypeScript strictness and accessible semantic controls.
-   Keep critical state changes durable; do not rely on transient
    component state.
-   Use IndexedDB for structured offline data, not localStorage for
    activity queues.
-   Show "Saved on this device" only after durable local commit.
-   Show "Synced" only after server acknowledgement.
-   Preserve outbox events through timeout, authentication expiry, app
    restart, and retry.
-   Do not assume Background Sync is supported.
-   Handle loading, empty, offline, error, conflict, and retry states.
-   Keep user-facing copy clear, respectful, and free of shame.
-   Do not use colour as the only state indicator.

## 7. Content and AI rules

-   Only approved published content is served as curriculum.
-   Published versions are immutable.
-   Never invent Scripture quotations, references, theological
    consensus, source metadata, or licence permissions.
-   Clearly distinguish source quotation, paraphrase, interpretation,
    and contested view.
-   AI responses must be grounded in approved versioned content and
    carry validated citations.
-   AI is optional. Core journeys work with AI disabled or unavailable.
-   Treat user content and retrieved material as untrusted input.
-   Do not send private reflections, mentor notes, or safeguarding
    information to external AI by default.

## 8. Security and privacy rules

-   Follow the security policy and framework-supported secure defaults.
-   Keep secrets in environment/secret configuration.
-   Use least privilege and server-side object scoping.
-   Audit privileged actions without logging sensitive payloads.
-   Do not add new personal-data fields without a documented purpose and
    approval.
-   Do not change retention/deletion behaviour without a decision-log
    entry.
-   Never expose another participant's progress in outreach reports by
    default.

## 9. Database and migrations

-   The authoritative single source of truth for all database tables,
    columns, constraints, indexes, and relationships is `docs/database.md`.
    No migrations or model definitions may deviate from `docs/database.md`.
-   Add migrations with every schema change.
-   Add indexes for actual query/report patterns.
-   Use explicit constraints for event UUID and idempotency.
-   For data migrations, make them repeatable or safely resumable where
    possible.
-   Avoid irreversible data loss. If a destructive change is
    unavoidable, require written approval, backup verification, and
    rollback/recovery plan.
-   Tests must run against the supported database engine, not only
    SQLite if PostgreSQL behaviour matters.

## 10. Definition of done

A task is done only when: - acceptance criteria pass; - relevant
automated tests pass; - authorization and failure paths are covered; -
formatting/lint/type checks pass; - no unrelated changes remain; -
docs/contracts are updated; - known limitations are disclosed; - a human
approval is recorded where required.

## 11. Required completion report

Use this structure: - **Task:** ID and title - **Outcome:** completed /
blocked / partial - **Changes:** concise list of modules/files -
**Acceptance criteria:** each pass/fail with evidence - **Tests:**
commands/checks and actual results - **Security/offline/content
implications:** relevant findings - **Known limitations:** explicit -
**Decisions needed:** exact question and owner

Do not use "done" as a substitute for evidence.
