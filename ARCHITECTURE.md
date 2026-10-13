# ARCHITECTURE.md --- System Architecture

**Version:** 1.0 \| **Status:** Pilot baseline

## 1. Architecture decision

Build a modular monolith backend and an installable Progressive Web App
(PWA). Offline learning is a first-class requirement.

### Stack

-   Frontend: React, TypeScript, Vite.
-   PWA/service worker: Workbox via a maintained Vite PWA integration.
-   Local persistence: IndexedDB using Dexie.js.
-   Backend: Python, Django, Django REST Framework.
-   Database: PostgreSQL.
-   API: versioned JSON REST API under `/api/v1`; automated OpenAPI 3.1
    schema generation via `drf-spectacular` for typed mobile/web client SDKs.
-   Media: S3-compatible object storage or equivalent for published
    media; do not store large media blobs in PostgreSQL.
-   CI: lint, type check, unit/integration tests, dependency checks,
    migration checks, production build.
-   Hosting: one managed or appropriately secured application
    environment with HTTPS, PostgreSQL backups, and separate
    staging/production configuration.

Use supported stable versions at implementation time and lock exact
versions in the repository. Do not use prerelease dependencies without
approval.

## 2. System context

``` text
Participant PWA
  ├─ React UI
  ├─ Service worker / cached app shell
  ├─ IndexedDB (approved content, local progress, sync outbox)
  └─ Sync coordinator
          │ HTTPS /api/v1
          ▼
Django REST API (modular monolith)
  ├─ Identity & permissions
  ├─ Ministries & outreach attribution
  ├─ Enrolments & journeys
  ├─ Content governance
  ├─ Learning activities & adaptation
  ├─ Mentorship & consent
  ├─ Reporting
  └─ AI adapter (optional, isolated)
          │
          ▼
      PostgreSQL
          ├─ authoritative records
          ├─ immutable content versions
          ├─ sync idempotency receipts
          └─ audit events

Published media → object storage/CDN
Monitoring → errors, metrics, security/audit events (no private reflection bodies)
```

## 3. Backend module boundaries

Use Django apps or equivalent clearly separated modules: 1. `accounts`
--- users, sessions, identity lifecycle. 2. `organizations` ---
ministries, memberships, roles. 3. `outreach` --- outreaches,
events/sections, workers, attribution. 4. `enrolments` --- pathway
enrolments and access invitations. 5. `content` --- pathways, modules,
lesson metadata, immutable published versions, review workflow. 6.
`learning` --- journeys, attempts, activity events, progression state.
7. `sync` --- idempotency receipts, sync endpoints, conflict outcomes.
8. `mentorship` --- consent, mentor assignments, permitted follow-up
records. 9. `reporting` --- permission-filtered aggregate reports. 10.
`audit` --- security-sensitive administrative actions. 11.
`ai_assistance` --- optional provider adapter and source-grounding
enforcement.

Do not create a Django app for every model. Modules should represent
stable responsibilities. Cross-module writes must go through explicit
service functions or domain operations rather than importing another
module's private internals.

## 4. Data ownership

-   PostgreSQL is authoritative for server-confirmed users, roles,
    ministries, outreach/event records, enrolments, published content,
    mentor assignments, and synchronized learning events.
-   IndexedDB is authoritative only for unsynchronized local work and
    local cache state.
-   Published content versions are immutable. Editing creates a new
    version.
-   Client-supplied role, ministry membership, content approval state,
    attribution identity, or server timestamp is never trusted.
-   Client events include a stable UUID generated once on the device and
    retained across retries.
-   Server records include a server-generated ID and server timestamp.
-   Attribution snapshots preserve source details at enrolment time even
    if ownership or display names later change.

## 5. Offline strategy

### 5.1 Cached data

Cache: - app shell and versioned static assets; - published lesson packs
explicitly downloaded or selected for offline availability; - essential
pathway metadata; - current participant's minimum necessary journey
state; - locally created supported activity events awaiting sync.

Do not cache: - administrative reports; - other participants' records; -
private mentor notes; - broad API responses containing unnecessary
personal data; - AI outputs as authoritative content.

### 5.2 Local schema

IndexedDB stores: - `contentPacks`: content version, checksum, size,
download status; - `journeySnapshots`: minimal server-confirmed journey
state and last sync version; - `activityOutbox`: event UUID, entity
reference, content version, payload, client-created timestamp, sync
status, retry count; - `syncMetadata`: last successful sync, schema
version, last server cursor; - `localPreferences`: non-sensitive
preferences.

Do not store passwords, long-lived bearer tokens, or unnecessary
sensitive disclosures in IndexedDB. Use the safest supported
authentication mechanism approved for the deployment. If offline
re-entry is supported, local access must be bound to the previously
authenticated device/session with a clear shared-device warning and a
secure local-lock strategy.

### 5.3 Write-before-confirm

When a user submits an offline-capable activity: 1. Validate the payload
locally. 2. Commit it to IndexedDB in a transaction. 3. Only after the
transaction completes, show "Saved on this device." 4. If online,
attempt synchronization. 5. Change the UI to "Synced" only after server
acknowledgement. 6. On failure, keep the event queued and show a
non-alarming retry status.

### 5.4 Synchronization semantics

-   At-least-once delivery from client to server.
-   Exactly-once effect for a given event UUID, enforced by a unique
    server constraint/idempotency receipt.
-   Append-only learning events where possible; derive mutable journey
    state from accepted events.
-   Use explicit event types and payload schema versions.
-   Reject malformed/unauthorized events without deleting local payload.
-   Server response returns accepted, already-accepted, rejected, or
    conflict status per event.
-   Retry transient errors with bounded exponential backoff plus jitter.
-   Do not retry permanent validation/permission failures indefinitely.
-   Sync on app launch, reconnect, explicit user action, and after
    eligible activity completion. Background Sync is an enhancement
    only.
-   Never resolve a conflict by silently overwriting the newest server
    state with a stale client snapshot.

### 5.5 Content version updates

A downloaded content pack retains the exact published version. If newer
content exists, show an update state. Never replace a version while the
user is completing an activity. If a version is retired for a
safety/copyright reason, the server may mark it unavailable for new
downloads and communicate an explicit retirement response; define
whether previously cached content is removed based on the reason and
policy.

## 6. Authentication and authorization

-   Use HTTPS everywhere.
-   Dual authentication architecture: secure, HttpOnly, SameSite session
    cookies with CSRF protection for Web/PWA clients, and stateless JWT
    Bearer token authentication (`rest_framework_simplejwt`: short-lived
    access credentials + refresh rotation) for native mobile applications
    and external API integrations.
-   CSRF protection is mandatory for cookie-authenticated state-changing
    requests.
-   Every protected endpoint performs server-side object-level
    permission checks.
-   Scope ministry data by active membership and explicit role; never
    trust a client-supplied ministry ID.
-   Outreach access is granted by creator ownership, authorized ministry
    membership, or explicit assignment.
-   Participant progress is private by default.
-   Mentor access is limited to consented relationship and allowed
    fields.
-   Use rate limits for login, invitations, public enrolment codes, AI
    requests, and sync endpoints.
-   Record privileged changes in an audit log.

## 7. Domain model and database schema single source of truth

The authoritative **Single Source of Truth (SOT)** for the entire application database schema, table names, fields, data types, constraints, foreign key relationships, indexes, and client-side Dexie.js stores is [`docs/database.md`](docs/database.md).

To prevent schema drift and uncoordinated table definitions, **no table schemas or field lists are defined in this architecture document**. All models and migrations must implement the canonical tables specified in `docs/database.md`:

-   **Identity & Access:** `people`, `users`, `roles`, `accounts_consent_record`
-   **Ministries & Tenancy:** `ministries`, `ministry_memberships`
-   **Outreach & Attribution:** `outreaches`, `outreach_events`, `event_facilitators`
-   **Enrolments:** `enrolments`, `enrolments_access_invitation`
-   **Content Governance:** `pathways`, `pathway_versions`, `pathway_topics`, `pathway_lessons`, `lesson_versions`, `content_review`
-   **Media & Digital Assets:** `media_assets`, `lesson_media_assets`
-   **Learning & Progression:** `learning_journey`, `learning_activity_event`, `learning_assessment_attempt`, `learning_encrypted_journal_entry`
-   **Contemplative Formation:** `learning_rule_of_life_template`, `learning_participant_rhythm`, `learning_scripture_memory_verse`, `learning_balance_reflection`
-   **Mentorship & Lineage:** `mentorship_relationship`, `mentorship_affinity_profile`, `mentorship_generational_link`
-   **Synchronization & Idempotency:** `sync_receipt`
-   **Audit & Security:** `audit_event`
-   **Ecosystem Integrations:** `integration_webhook_subscription`, `integration_webhook_event_log`
-   **Client Offline Stores:** `contentPacks`, `journeySnapshots`, `activityOutbox`, `syncMetadata`, `localPreferences`, `personalRhythms`, `encryptedJournals`, `scriptureMemoryDeck`, `cachedMediaAssets`

Refer to [`docs/database.md`](docs/database.md) for full column specifications, integrity rules, and migration guidelines.

## 8. Attribution and reporting

Each enrolment is a separate record for a participant/pathway/source
combination. Reporting must distinguish: - enrolment count: number of
enrolment records; - unique participant count: distinct participant
identities in the selected scope; - pathway enrolments: enrolments
grouped by pathway; - outreach/event/missioner/ministry attribution
coverage.

Store immutable attribution snapshots for labels/source identifiers at
the time of enrolment. Foreign keys remain the primary relation;
snapshots support historical reporting and survive renames/archiving. Do
not allow a creator to claim a ministry's identity without authorized
membership.

Default report output is aggregate. Participant-level export requires a
specific permission, stated purpose, and audit entry. No ranking of
missioners or ministries by spiritual performance.

## 9. AI integration boundary

AI is optional and must be isolated behind `ai_assistance`. Core
pathways, progression, and offline learning must not depend on an AI
provider.

For each AI request: - pass only the minimum data needed; - retrieve
from approved, versioned content; - require citations to content
IDs/version IDs and Scripture references stored in reviewed material; -
validate that citations exist in retrieved context; - label generated
content as AI-assisted; - provide a fallback to approved material; - do
not send private reflections, pastoral notes, or identifiable
participant data to the model by default; - log provider/model version,
source IDs, policy version, and outcome, but not unnecessary private
prompt text; - use output moderation and rate limits; - treat lesson
text and retrieved content as untrusted input that cannot override
system instructions; - never let AI change permissions, enrolments,
content approval, consent, safeguarding, or progression state.

The exact provider, data processing terms, retention settings, model,
and costs are not selected in this baseline and must be approved before
enabling live AI.

## 10. Performance and reliability targets

Pilot targets (provisional, validate against hosting): - common
authenticated API reads: p95 under 500 ms under pilot load, excluding
external AI/media latency; - lesson shell usable after initial install
without network; - local activity save: UI confirmation only after
IndexedDB transaction success; - sync retries do not create duplicate
activity or enrolments; - backups automated daily at minimum, with
restoration rehearsal before pilot; - no P0/P1 security/safeguarding
defects at release.

## 11. Deployment environments

-   Local development: documented one-command startup, seed data, no
    production secrets.
-   CI: automated lint/type/test/build/migration checks.
-   Staging: synthetic test data, production-like HTTPS and
    service-worker behaviour.
-   Production pilot: restricted administrator accounts, backups,
    monitoring, secure secrets, dependency update policy, and rollback
    plan.

Use separate credentials and data between environments. Never copy live
participant records into local development.

## 12. Architecture acceptance criteria

-   Domain boundaries and authorization checks are test-covered.
-   Offline-capable activity survives app restart and sync retry.
-   Duplicate event UUID is idempotent.
-   Published lesson versions cannot be mutated.
-   Reports are scoped to permitted ministry/outreach data.
-   AI provider failure does not block core learning.
-   A restored backup can boot the application and recover required
    data.
