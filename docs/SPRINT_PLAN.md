# SPRINT_PLAN.md --- Agile Delivery Backlog

**Version:** 1.0 \| **Status:** Ordered implementation baseline

## 1. Planning assumptions

-   Sprint length: two weeks.
-   Team: one product owner, one theological/content owner, one
    safeguarding owner (may be part-time), one frontend engineer, one
    backend engineer, and shared QA/design capacity. Roles may be
    combined, but approvals remain accountable.
-   Target: controlled pilot, not general availability.
-   Stack: React/TypeScript/Vite PWA; IndexedDB/Dexie; Workbox;
    Django/DRF; PostgreSQL.
-   Sprint 0 is a planning and risk-retirement sprint. Sprint 1 onward
    produces working increments.
-   The sprint order is a dependency order, not a promise of calendar
    completion. Re-estimate after Sprint 0.
-   Do not start live participant onboarding until governance, privacy,
    consent, and safeguarding gates pass.

## 2. Task execution protocol

For every task: 1. Read the relevant product, architecture, security,
and framework requirements. 2. Inspect current repository and existing
patterns before editing. 3. Confirm dependencies and identify any
conflict with the specification. 4. Implement the smallest complete
vertical slice. 5. Add or update tests before marking done. 6. Run
required checks and record actual results. 7. Update API/schema/docs if
behaviour changes. 8. Report files changed, tests run, results, known
limitations, and any decision needed.

A task is `Done` only when every acceptance criterion passes, code is
reviewed, tests are committed, no unresolved high-severity issue
remains, and any required human review is recorded.

## Sprint 0 --- Scope, governance and risk retirement

### S0-01 --- Approve product and pathway boundaries

**Depends on:** none\
**Owner:** Product owner + theological owner\
**Tasks:** approve `PRODUCT.md`; confirm pilot roles and non-goals;
define what "harmonized core principles" means for this network; approve
a list of out-of-scope doctrinal topics.\
**Acceptance:** - Three pathways have approved purpose statements. - No
feature implies spiritual scoring or punitive inactivity treatment. -
Core biblical standard owner and approval process are named. - Open
questions are recorded in `DECISION_LOG.md`.

### S0-02 --- Approve consent, privacy and safeguarding baseline

**Depends on:** S0-01\
**Owner:** Safeguarding owner + data owner\
**Tasks:** define pilot participant age eligibility; approve consent
notice; set data retention; define disclosure/incident routes; approve
Zero-Knowledge client-side reflection encryption standard (`AES-256-GCM`
via Web Crypto API, per `BENCHMARK_REVIEW.md` Mediation 4); confirm that
private reflections are client-encrypted and never ingested into AI
prompts or server logs.\
**Acceptance:** - Written policies identify responsible owners and
response expectations. - Minor onboarding is explicitly allowed with
controls or excluded from pilot. - UI claims about privacy match actual
data handling. - Zero-Knowledge reflection encryption is ratified. - No
unstaffed reporting feature is in scope.

### S0-03 --- Finalize enrolment access method

**Depends on:** S0-01\
**Owner:** Product owner\
**Tasks:** choose authenticated worker enrolment, self-enrolment
link/code, or both; specify participant identity matching and duplicate
policy; decide whether phone/email is required or optional.\
**Acceptance:** - Every enrolment flow has exact required fields. -
Link/code expiry, revocation, rate limits, and recovery are specified if
used. - Duplicate participant matching does not expose whether a person
already exists to unauthorized users.

### S0-04 --- Approve data model and API contract

**Depends on:** S0-01, S0-03\
**Owner:** Tech lead\
**Tasks:** review entity relationships, attribution snapshots,
journey/version model, idempotency strategy, deletion/retention
behaviour, report definitions; approve `docs/database.md` as the canonical
Single Source of Truth (SOT) for all database tables, columns, constraints,
and Dexie.js offline schemas across Phase 1 and post-pilot Phases 2/3.\
**Acceptance:** - `docs/database.md` committed as sole SOT. - Unique
constraints and indexes are specified. - Every required report metric has
a precise definition. - API endpoint permissions and contracts map
strictly to `docs/database.md`.

### S0-05 --- Approve AI scope and provider constraints

**Depends on:** S0-01, S0-02\
**Owner:** Product owner + theological owner + data owner\
**Tasks:** decide provider, source retrieval approach, allowed
prompts/data, retention terms, budget cap, fallback.\
**Acceptance:** - AI may only use approved versioned content for
participant-facing explanations. - Private reflections and safeguarding
data are excluded by default. - Provider outage and ungrounded output
fallback are documented. - If not approved, AI remains feature-flagged
off and the rules-based system proceeds.

### S0-06 --- Repository and delivery plan

**Depends on:** S0-04\
**Owner:** Tech lead\
**Tasks:** select supported dependency versions; define module layout,
branching/PR policy, CI checks, environments, secret handling, coding
conventions, and migration policy.\
**Acceptance:** - Architecture and repository structure approved. - A
clean checkout can run the documented setup. - CI checks are named and
executable. - No secrets or real participant data are present in seed
fixtures.

## Sprint 1 --- Platform foundation

### S1-01 --- Create repository skeleton and CI

**Depends on:** S0-06\
**Tasks:** create frontend/backend layout; add formatting, linting, type
checking, unit-test commands, production build, dependency checks,
environment example files.\
**Acceptance:** - New developer can follow setup instructions from a
clean checkout. - CI runs all configured checks. - Production secrets
are absent from repository.

### S1-02 --- Implement backend foundation

**Depends on:** S1-01\
**Tasks:** configure Django/DRF, PostgreSQL, migrations, health
endpoint, API version namespace, structured safe error responses.\
**Acceptance:** - Health endpoint works without exposing secrets. -
Migrations apply cleanly to empty test database. - API errors follow
contract. - Database credentials are environment-configured.

### S1-03 --- Implement responsive PWA shell

**Depends on:** S1-01\
**Tasks:** React/TypeScript routing, accessible layout, PWA manifest,
service-worker registration, offline fallback page, build/version
indicator.\
**Acceptance:** - App installs in supported target browsers. - Core
shell opens after initial successful load without network. - 360 px
viewport has no horizontal overflow on core shell. - Service-worker
update behaviour is documented and tested.

### S1-04 --- Dual authentication and role primitives

**Depends on:** S1-02\
**Tasks:** implement dual authentication strategy: secure HttpOnly SameSite
session/cookie handling with CSRF protection for Web/PWA clients, and
stateless JWT Bearer token authentication (via `rest_framework_simplejwt`:
token obtain, refresh, and verify endpoints) for native mobile apps and
external API clients; implement login/logout; current-user endpoint; user
and ministry-membership role primitives; rate limiting.\
**Acceptance:** - Web/PWA clients authenticate securely via session
cookies. - Native mobile clients authenticate via Bearer JWT tokens. -
Anonymous requests cannot access private endpoints. - Logout invalidates
the server session and revokes/blacklists refresh tokens. - Login and token
refresh endpoints are rate-limited. - Role checks are server-side and
test-covered.

### S1-05 --- OpenAPI 3.1 specification and mobile readiness (drf-spectacular)

**Depends on:** S1-02, S1-04\
**Tasks:** configure `drf-spectacular` for automated OpenAPI 3.1 schema
generation; document security schemes (`SessionCookie` and `BearerAuth`);
expose versioned schema (`GET /api/v1/schema/`) and interactive
documentation endpoints (Swagger UI, Redoc); validate schema generation in
CI to support auto-generating typed client SDKs for native mobile (Swift,
Kotlin) and TypeScript clients.\
**Acceptance:** - Valid OpenAPI 3.1 schema exports in JSON and YAML. - Both
session cookie and bearer token security schemes are documented. - CI
schema generation check passes without warnings. - Schema validates
compatibility with native mobile client generator tooling.

## Sprint 2 --- Network, outreach and enrolment attribution

### S2-01 --- Ministry workspace and membership

**Depends on:** S1-04\
**Tasks:** ministry records, membership roles, invitations or controlled
onboarding, scoped list/detail APIs.\
**Acceptance:** - A user may belong to multiple ministries. - Ministry A
users cannot read Ministry B data without explicit authorization. -
Membership removal revokes access on the next request.

### S2-02 --- Outreach CRUD

**Depends on:** S2-01\
**Tasks:** create, edit, list, detail, archive outreach; support
independent individual creator and ministry-owned outreach.\
**Acceptance:** - Authorized individual can create an outreach without a
ministry. - Ministry user can create only within an authorized
ministry. - Unauthorized edits fail server-side. - Archiving preserves
historical records.

### S2-03 --- Events/sections

**Depends on:** S2-02\
**Tasks:** create/update/list event or section under an outreach;
controlled event kind; optional dates/location/mode.\
**Acceptance:** - One outreach supports multiple events/sections. -
Parent ownership is enforced. - Invalid date range is rejected. - Event
cannot be created under an inaccessible or archived outreach.

### S2-04 --- Participant enrolment and source attribution

**Depends on:** S2-03, S0-03\
**Tasks:** create enrolment per participant/pathway; capture
outreach/event/missioner/ministry and creator; create immutable source
snapshot; implement idempotency.\
**Acceptance:** - All three pathways can be selected. - One participant
can have multiple pathway enrolments without overwriting progress. -
Event/outreach mismatch is rejected. - Retry with same idempotency key
produces one enrolment. - Source attribution is preserved after a
rename.

### S2-05 --- Enrolment access/onboarding flow

**Depends on:** S2-04\
**Tasks:** implement approved access invitation/code or worker-assisted
account claim flow; privacy notice; consent version; participant
first-login route.\
**Acceptance:** - Participant can securely claim/access their
enrolment. - Link/code (if used) is scoped, revocable, rate-limited, and
contains no personal data. - Invalid/expired access gives safe error. -
Consent version and timestamp are retained.

### S2-06 --- Enrolment reports

**Depends on:** S2-04\
**Tasks:** aggregate enrolment counts by date, pathway, outreach, event,
missioner, ministry; attribution completeness report.\
**Acceptance:** - Record count and unique participant count are
separately labeled. - Cross-ministry filters cannot leak data. - Reports
exclude private reflections and lesson answers. - Historical source
attribution remains stable.

## Sprint 3 --- Content governance and first pathway

### S3-01 --- Content model and versioning

**Depends on:** S1-02, S0-04\
**Tasks:** pathway/module/lesson/content-version models; source/licence
metadata; publication states.\
**Acceptance:** - Published versions are immutable. - Draft edits create
a new version. - Missing required metadata blocks submission for
review. - Historical activity can reference exact lesson version.

### S3-02 --- Review and publish workflow

**Depends on:** S3-01\
**Tasks:** editor/reviewer permissions, review decision, publish/retire
workflow, audit events.\
**Acceptance:** - Unauthorized role cannot approve/publish. -
Author/reviewer separation follows policy. - Every decision records
actor, version, time, and outcome. - Draft/unapproved content is not
delivered as published material.

### S3-03 --- Participant lesson experience

**Depends on:** S3-01, S1-03\
**Tasks:** pathway catalogue, module/lesson screen, plain-language
lesson layout, references, progress state, accessible controls,
integration of Head-Heart-Hands pedagogical balance metadata
(`BENCHMARK_REVIEW.md` GAP-05).\
**Acceptance:** - Participant sees only published/eligible content. -
References and content version are visible in the agreed UI. - Critical
flow works by keyboard. - Empty/error/loading states are implemented. -
Lesson metadata reflects holistic formation dimension (`head`, `heart`,
`hands`).

### S3-04 --- Formation pilot content

**Depends on:** S3-02, S3-03, theological approval\
**Tasks:** import/adapt approved existing materials into a small
complete Formation journey structured across the Head, Heart, and Hands
framework.\
**Acceptance:** - At least one end-to-end Formation module is reviewed
and published. - Every lesson has objectives, sources/references, rights
metadata, review record, and designated formation dimension (`head`,
`heart`, `hands`). - No content is fabricated or published by the coding
agent without review.

### S3-05 --- Assessment and activity events

**Depends on:** S3-03\
**Tasks:** supported question types, answer submission, activity events,
deterministic feedback.\
**Acceptance:** - Attempts reference exact lesson version. - Client
cannot set authoritative result fields. - Feedback matches reviewed
answer/explanation. - Event UUID is stable across retries.

## Sprint 4 --- Offline persistence and synchronization

### S4-01 --- IndexedDB schema and migrations

**Depends on:** S1-03, S3-03\
**Tasks:** Dexie schema as specified in `docs/database.md` Section 4 for
content packs, journey snapshots, outbox, sync metadata; schema version
upgrades.\
**Acceptance:** - Local schema can upgrade from previous version in
tests. - Queued events survive app restart and schema upgrade. - No
credentials or unnecessary sensitive data are stored.

### S4-02 --- Download and cache approved content

**Depends on:** S3-01, S4-01\
**Tasks:** download approved lesson packs; checksum/version metadata;
size/status display; content update state.\
**Acceptance:** - Only published content can be downloaded. - Downloaded
content opens offline. - Version identity is retained. - Failed/partial
download is not marked complete.

### S4-03 --- Durable local activity capture

**Depends on:** S4-01, S3-05\
**Tasks:** validate activity locally; write in transaction; show
saved-on-device state; preserve unsynced queue.\
**Acceptance:** - Saved confirmation appears only after IndexedDB
commit. - Activity survives close/reopen in offline test. - Repeated
taps do not create duplicate local event UUIDs. - Storage failure is
shown clearly and does not falsely claim success.

### S4-04 --- Sync API and idempotency

**Depends on:** S2-04, S3-05, S4-03\
**Tasks:** event batch endpoint, event UUID uniqueness, per-event
result, payload validation, sync receipts.\
**Acceptance:** - Every submitted event receives a result. - Repeated
identical event returns already accepted. - Same UUID/different payload
returns conflict. - Unauthorized journey event is rejected. - One
invalid event does not silently drop unrelated valid events.

### S4-05 --- Client sync coordinator

**Depends on:** S4-04\
**Tasks:** sync on launch/reconnect/manual retry; transient retry; queue
status; auth-expiry recovery; no dependency on Background Sync.\
**Acceptance:** - Online transition triggers a sync attempt. - Timeout
leaves local event queued. - Server acknowledgement updates state to
synced. - Permanent rejection does not loop forever and gives a recovery
action. - No local data is erased on failed sync.

### S4-06 --- Offline resilience test pass

**Depends on:** S4-02 through S4-05\
**Tasks:** automate offline/restart/reconnect/retry/stale-version
scenarios.\
**Acceptance:** - Required offline matrix in `TEST_STRATEGY.md` passes
on supported browsers. - Known limitations are documented. - No P0/P1
data integrity defect remains.

## Sprint 5 --- Adaptation and all three pathways

### S5-01 --- Deterministic adaptation service

**Depends on:** S3-05\
**Tasks:** implement configurable rules R1--R5 in
`DISCIPLESHIP_FRAMEWORK.md`; persist reason codes for recommendations.\
**Acceptance:** - Wrong answer triggers reviewed
feedback/reinforcement. - Repeated misses offer alternative explanation
at configured threshold. - Correct objective can unlock next eligible
lesson. - Recommendation includes plain-language reason. - No
spiritual-status score or inference is produced.

### S5-02 --- Restoration pathway

**Depends on:** S3-02, S5-01, safeguarding approval\
**Tasks:** implement voluntary entry, user-selected starting point,
small-step lessons, neutral return experience, optional support
request.\
**Acceptance:** - Inactivity never auto-enrols a participant in
Restoration. - Participant can pause and resume without penalty. - No
shaming or public status labels. - Sensitive disclosure is not required
to proceed.

### S5-03 --- Biblical Discernment pathway

**Depends on:** S3-02, theological approval\
**Tasks:** implement
claim/text/context/interpretation/shared-core/difference/application
structure.\
**Acceptance:** - UI distinguishes text, interpretation, and contested
views. - References link to approved metadata. - Content cannot be
published without review and source metadata. - No fabricated citations.

### S5-04 --- Journey history, pause and restart

**Depends on:** S5-01, S4-04\
**Tasks:** journey state, pause/resume, review previous content, restart
as new attempt/history marker.\
**Acceptance:** - A participant can resume after any period of
inactivity. - Restart does not erase prior attempts. - Separate pathways
retain independent progress. - Offline state reconciles according to
documented rules.

### S5-05 --- Optional mentor request and consent

**Depends on:** S0-02, S1-04\
**Tasks:** participant request, coordinator assignment, explicit
consent, scoped mentor view, end relationship.\
**Acceptance:** - No mentor access before consent. - Mentor view
contains only approved fields. - Consent withdrawal revokes access. - No
unrestricted chat/export is added.

## Sprint 6 --- AI-assisted adaptation (feature-flagged)

### S6-01 --- AI provider and data-processing approval

**Depends on:** S0-05\
**Tasks:** finalize provider, model, data terms, retention, budget,
source retrieval and fallback.\
**Acceptance:** - Written approval covers allowed data, retention, and
cost. - If approval is absent, feature remains disabled. - Core platform
works with AI disabled.

### S6-02 --- Approved-content retrieval

**Depends on:** S3-01, S6-01\
**Tasks:** retrieve only approved versioned resources; return source IDs
and reference metadata.\
**Acceptance:** - Retrieval excludes drafts/retired content unless
specifically permitted for an editor-only workflow. - Each returned
source has a stable ID/version. - Retrieval failure returns approved
fallback.

### S6-03 --- AI alternative explanation endpoint

**Depends on:** S6-02\
**Tasks:** generate optional supplemental explanation from retrieved
content; validate citations; label response; rate limit.\
**Acceptance:** - Invalid/nonexistent citations are rejected or replaced
by fallback. - Output is labeled AI-assisted. - No private reflection or
pastoral note is sent by default. - AI cannot change content state,
progression, consent, or permissions.

### S6-04 --- AI safety and regression tests

**Depends on:** S6-03\
**Tasks:** prompt injection, unsupported claim, citation hallucination,
provider outage, cost limit, and data minimization tests.\
**Acceptance:** - Adversarial lesson/user text cannot override system
boundaries. - Provider outage does not block learning. - AI cannot
publish content. - Safety owner signs off before feature is enabled.

## Sprint 7 --- Pilot hardening and release

### S7-01 --- Full authorization review

**Depends on:** S2--S6\
**Tasks:** test every endpoint and role boundary; review exports, logs,
reports, and admin actions.\
**Acceptance:** - Cross-user and cross-ministry tests pass. - No P0/P1
authorization issue remains. - Privileged actions are audited.

### S7-02 --- Accessibility and comprehension validation

**Depends on:** S3--S5\
**Tasks:** keyboard/screen-reader checks; low-end phone review;
plain-language comprehension tests with representative users.\
**Acceptance:** - Critical flows meet agreed WCAG 2.2 AA target or have
an approved, non-critical exception. - Participants can explain
saved/synced status and mentor privacy in usability tests. - Blocking
accessibility defects are fixed.

### S7-03 --- Observability, backups and restore

**Depends on:** S1-02\
**Tasks:** health/metrics/error monitoring, safe logs, database backups,
restore rehearsal, incident procedure.\
**Acceptance:** - Restore rehearsal recovers required records in a clean
environment. - Logs contain no tokens or private reflection bodies. -
Incident owner and escalation contact are documented. - Alert thresholds
and operational owner are defined.

### S7-04 --- Pilot content and operational readiness

**Depends on:** S0-02, S3-04, S5-02, S5-03, S5-05\
**Tasks:** approve sample lessons across all pathways, participant
notice, mentor guidance, support process, retention policy, pilot cohort
plan.\
**Acceptance:** - Each pathway has a complete reviewed pilot journey. -
Consent and privacy notice match implemented behaviour. - Support and
safeguarding ownership is explicit. - Pilot cohort and feedback
collection are approved.

### S7-05 --- Release candidate and rollback

**Depends on:** all prior release blockers\
**Tasks:** run full test suite, smoke tests, migration/rollback plan,
staging rehearsal, release checklist.\
**Acceptance:** - All release gates in `TEST_STRATEGY.md` pass. - P0/P1
defects are closed. - Rollback procedure is tested. - Product,
theological, safeguarding, and technical owners sign off.

## Sprint 8 --- Contemplative formation and zero-knowledge reflection architecture (Phase 2)

### S8-01 --- Rule of Life / spiritual liturgy builder (GAP-01 / Mediation 2)

**Depends on:** S5-01, S3-03\
**Owner:** Frontend engineer + Backend engineer + theological owner\
**Tasks:** implement `learning_rule_of_life_template` catalog and `learning_participant_rhythm` API endpoints (`/api/v1/rhythms/templates`, `/api/v1/me/rhythms`) per `docs/database.md`; build drag-and-drop interactive UI for daily, weekly, monthly, and seasonal spiritual habits; strictly un-scored and non-quota (no numeric minutes/chapters targets, no tactile mentor validation sign-offs, no streak counters).\
**Acceptance:** - Participants can assemble personal liturgies across Head, Heart, and Hands. - Missing a day displays zero warning bars, zero red counters, and zero guilt triggers. - Offline caching works in Dexie `personalRhythms` store. - Endpoints enforce participant ownership.

### S8-02 --- Head-Heart-Hands self-reflective balance map (GAP-05 / Mediation 5)

**Depends on:** S3-03, S5-01\
**Owner:** Frontend engineer + theological owner\
**Tasks:** implement `learning_balance_reflection` model and endpoints (`/api/v1/me/balance-reflections`) per `docs/database.md`; create self-reflective Likert prompts evaluating Head (Theological Understanding), Heart (Interior Affection & Prayer), and Hands (Service & Mission); build dynamic Balance Triangle visualization; generate deterministic recommendations for under-practiced dimensions.\
**Acceptance:** - Visualizes an unranked balance map (triangle) without maturity grades, pass/fail status, or numerical scores. - Provides deterministic recommendations to Formation lessons. - Participant results remain private and cannot be viewed by ministry admins or mentors without consent.

### S8-03 --- Zero-Knowledge client-side reflection encryption (Mediation 4)

**Depends on:** S4-01, S1-04\
**Owner:** Frontend engineer + security lead\
**Tasks:** implement Web Crypto API (`AES-256-GCM`) client-side encryption for `learning_encrypted_journal_entry` per `docs/database.md`; derive local master key via PBKDF2/Argon2 from user credentials; encrypt reflection bodies before IndexedDB commit and sync; implement optional asymmetric re-encryption for consented mentor sharing; verify server stores only ciphertext blobs, IVs, and key fingerprints.\
**Acceptance:** - Server cannot decrypt reflection contents. - Database administrators and loggers have zero-knowledge of private reflections. - Reflection bodies are mathematically isolated from AI provider prompts. - Unit and integration tests prove server cannot read plaintext reflections.

### S8-04 --- Offline Scripture memory engine (TMS) (GAP-04)

**Depends on:** S3-01, S4-01\
**Owner:** Frontend engineer + content owner\
**Tasks:** implement `learning_scripture_memory_verse` endpoints and Dexie `scriptureMemoryDeck` store per `docs/database.md`; deliver tactile spaced-repetition flashcards for approved Scripture verses based on The Navigators' Topical Memory System (TMS); build offline flip-card interface with hide/reveal prompts; strictly zero gamified leaderboard ranking.\
**Acceptance:** - Scripture cards download and function fully offline. - Verified Scripture text from licensed translations. - Touch-friendly review controls with accessible keyboard fallbacks. - Progress stored locally and synced idempotently.

### S8-05 --- Phase 2 offline schema upgrade and client store integration

**Depends on:** S4-01, S8-01, S8-03, S8-04\
**Owner:** Frontend engineer\
**Tasks:** upgrade Dexie.js database schema from v1 to v2 as specified in `docs/database.md` Section 4; add `personalRhythms`, `encryptedJournals`, and `scriptureMemoryDeck` stores; write migration tests ensuring v1 data is preserved during v2 upgrade.\
**Acceptance:** - Existing v1 user data survives schema upgrade. - Offline writes to new stores succeed and survive app restart. - Offline activity outbox continues uninterrupted.

## Sprint 9 --- Generational lineage, affinity pairing and ecosystem interoperability (Phase 3)

### S9-01 --- Consented spiritual genealogy tree (GAP-03 / Mediation 1)

**Depends on:** S5-05, S2-04\
**Owner:** Backend engineer + Frontend engineer + theological owner\
**Tasks:** implement `mentorship_generational_link` model and endpoints (`/api/v1/mentorship/lineage`) per `docs/database.md`; build multi-generational lineage visualization from Gen 0 (pioneer) down to Gen 4 ($2\text{ Tim }2:2$); enforce strictly mutual-consent requirement before link creation; implement detection and prevention of circular discipleship loops; completely unranked (no multipliers leaderboard, no individual quiz/progress surveillance).\
**Acceptance:** - Lineage tree displays relational multiplication depth without showing private quiz scores or lesson completion bars. - Participant may opt out, rendering their branch as an anonymized counter. - Circular loops are blocked and audited.

### S9-02 --- Double-blind relational affinity (HIP) matching engine (GAP-02 / Mediation 3)

**Depends on:** S5-05, S0-02\
**Owner:** Backend engineer + Frontend engineer + safeguarding owner\
**Tasks:** implement `mentorship_affinity_profile` and pairing coordinator dashboard (`/api/v1/mentorship/affinity-matching`) per `docs/database.md`; curate 73+ non-sensitive affinity categories (vocational sector, life stage, arts, sports, ministry passions); build double-blind recommendation algorithm where identity is masked until coordinator facilitates introduction; implement strict minor protection guardrail: minors (`is_minor_flag = True`) are hard-blocked from peer matching and can only be assigned vetted adult mentors under guardian consent.\
**Acceptance:** - Double-blind matching operates without leaking personal contact details before mutual consent. - Automated matching for minors is hard-blocked and test-verified. - Coordinators can view compatibility scores across affinity categories without seeing restricted personal data.

### S9-03 --- ChMS webhook interoperability (GAP-06)

**Depends on:** S2-04, S1-04\
**Owner:** Backend engineer\
**Tasks:** implement `integration_webhook_subscription` and `integration_webhook_event_log` models and management API (`/api/v1/integrations/webhooks`) per `docs/database.md`; support standardized HMAC-SHA256 signed outgoing webhooks on `enrolment.created` and `mentor.assigned` for external Church Management Systems (Planning Center Online, Pushpay CCB); strictly exclude reflections, lesson answers, and private notes from webhook payloads.\
**Acceptance:** - Validated payload delivery to external endpoints with exponential backoff retry. - HMAC signatures prevent payload tampering. - Reflections and private fields are strictly stripped from all outbound webhook payloads.

### S9-04 --- Multi-tiered hierarchical dashboards (GAP-07)

**Depends on:** S2-06, S9-01\
**Owner:** Backend engineer + Frontend engineer + data owner\
**Tasks:** implement multi-tiered aggregate reporting hierarchy modeled on We Are Making Disciples: Participant (Personal Private) $\rightarrow$ Local Ministry Workspace (Aggregate) $\rightarrow$ Regional / Diocesan / Network Dashboard (Macro Trends); implement small-cell privacy suppression (cell counts $< 5$ suppressed) to prevent deanonymization.\
**Acceptance:** - Regional leaders can view multi-church trends without inspecting individual parish members. - Small-cell suppression protects individuals in small church cohorts. - No spiritual ranking of churches, missioners, or disciples.

## Sprint 10 --- Enterprise scalability, final safeguarding verification and release readiness

### S10-01 --- Full end-to-end cryptographic and safeguarding verification pass

**Depends on:** S8-03, S9-02, S7-01\
**Owner:** Security lead + safeguarding owner\
**Tasks:** perform automated and manual penetration testing of zero-knowledge encryption, double-blind HIP pairing, minor isolation boundaries, and webhook payload scrubbing; verify ordinary logs and error reporting contain no cleartext reflections or sensitive disclosures.\
**Acceptance:** - Zero cryptographic leaks found. - Minor protection verified by automated adversarial tests. - Audit trail accurately captures all privileged actions.

### S10-02 --- Performance, load and high-concurrency offline rehearsal

**Depends on:** S8-05, S9-03, S7-03\
**Owner:** Tech lead + QA\
**Tasks:** perform high-concurrency load testing of sync endpoints with thousands of simultaneous offline outbox flushes; benchmark PostgreSQL query plans for generational lineage recursive queries and multi-tenant reporting.\
**Acceptance:** - p95 API response times stay $< 500\text{ ms}$. - Recursive lineage queries execute in $< 50\text{ ms}$. - Zero deadlocks on sync receipts or webhook event logs.

### S10-03 --- Ecosystem documentation, governance council sign-off and release readiness

**Depends on:** S7-05, S8-01 through S10-02\
**Owner:** Product owner + theological owner + safeguarding owner + tech lead\
**Tasks:** verify all documents (`PRODUCT.md`, `DISCIPLESHIP_FRAMEWORK.md`, `ARCHITECTURE.md`, `API_AND_DATA_CONTRACTS.md`, `SECURITY_AND_SAFEGUARDING.md`, `TEST_STRATEGY.md`, `SPRINT_PLAN.md`, `DECISION_LOG.md`, `BENCHMARK_REVIEW.md`, and `database.md`) are aligned; obtain written sign-off from theological governance and safeguarding council for Phase 2 and Phase 3 capabilities.\
**Acceptance:** - Full document pack consistency verified. - All release gates pass. - Release candidate tagged.

## 3. Cross-sprint backlog rules

-   **Database SOT Discipline:** `docs/database.md` is the sole Single
    Source of Truth for all database schemas, table names, fields,
    constraints, indexes, and Dexie stores. No other document or code
    file may introduce diverging schema definitions.
-   **Anti-Scoring & Zero-Shame Rule:** Do not introduce authoritative
    spiritual maturity scores, stage ratings, or salvation certification
    algorithms (AssessME Trap).
-   **No Quantitative Quotas:** Do not introduce quantifiable habit quotas
    with tactile mentor sign-offs (DiscipleTracker Trap). Habits must be
    un-scored rhythms.
-   **No AI Reflection Ingestion:** AI must never ingest or process private
    reflections, pastoral notes, or identifiable disclosures
    (DiscipleTracker AI Trap). Zero-knowledge client-side encryption is
    mandatory.
-   **Offline-First PWA Standard:** Do not abandon the PWA architecture
    for native app-store walled gardens (The Navigators Trap).
-   **No Punitive Inactivity Treatment:** Do not use inactivity as an
    automated Restoration trigger or apply "backsliding" status labels.
-   If a task discovers an unresolved product or theological decision,
    stop the affected part and create a decision-log entry.
-   Do not use mock AI output in production paths without a visible
    development-only flag.
-   Do not replace real authorization with frontend-only hiding.
-   Do not store participant progress only in React state or local
    storage.
-   Do not mutate published content versions.
-   Do not delete local outbox events on sync failure.
-   Do not add features merely because a library makes them easy.
-   All migrations must be reversible where practical; destructive
    migrations require explicit data migration and rollback planning.
-   A sprint may be declared complete only when its exit gate passes,
    not when all tickets are merged.
