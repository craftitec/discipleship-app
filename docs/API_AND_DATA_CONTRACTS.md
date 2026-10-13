# API_AND_DATA_CONTRACTS.md --- API and Data Contracts

**Version:** 1.0 \| **Status:** Contract baseline; finalize exact
serializers during Sprint 0

## 1. Conventions

-   Base path: `/api/v1`.
-   JSON request/response bodies, UTF-8.
-   ISO 8601 timestamps in UTC; client-created timestamps are metadata,
    not authoritative server time.
-   Stable opaque IDs (UUID recommended) for client-generated events and
    externally referenced resources.
-   Standard errors use a stable machine-readable `code`, safe
    human-readable `message`, optional field errors, and `request_id`.
-   Pagination is required for list endpoints.
-   Mutating endpoints must validate object-level permissions.
-   Do not return private reflections, pastoral notes, access tokens, or
    internal audit metadata in ordinary resource serializers.
-   Version API and payload schemas. Breaking changes require a
    versioning decision.

Example error:

``` json
{
  "error": {
    "code": "permission_denied",
    "message": "You do not have permission to perform this action.",
    "field_errors": {},
    "request_id": "server-generated-id"
  }
}
```

## 2. Canonical enums

`pathway_code`: `formation`, `restoration`, `discernment`

`outreach_status`: `draft`, `active`, `completed`, `archived`

`event_kind`: controlled but extensible labels such as `crusade`,
`church_service`, `campus_outreach`, `community_visit`, `follow_up`,
`other`. Store the kind code separately from its display label.

`content_status`: `draft`, `in_review`, `approved`, `published`,
`retired`

`journey_state`: `not_started`, `in_progress`, `completed`,
`paused_by_participant`

`sync_result`: `accepted`, `already_accepted`, `rejected`, `conflict`

Do not use `backslidden`, `unfaithful`, `spiritually_at_risk`, or
similar moral/spiritual labels as system status values.

## 3. Core entities and API validation rules

> [!IMPORTANT]
> The canonical database schema, column definitions, data types, constraints, and relationships for all entities below are defined exclusively in [`docs/database.md`](database.md). This section defines the API validation rules, serializer constraints, and business logic for data in transit.

### Person & User (Maps to `people` and `users`)

-   Biodata (`first_name`, `last_name`, `phone_number`, `avatar_url`, `language_preference`, `timezone`) resides exclusively in `people`.
-   Authentication credentials (`email`, `password_hash`, `is_active`, `email_verified_at`) reside exclusively in `users` with foreign key `person_id`.
-   A `person` record can exist independently of a `users` record (e.g. evangelism outreach respondents, unauthenticated seekers, or guest event facilitators).
-   When an unauthenticated participant registers for online access, a `users` account is created linking to their existing `person_id`.
-   API endpoints returning public or ministerial profiles expose `people` fields, never sensitive authentication or security columns.

### Roles & Ministry Memberships (Maps to `roles` and `ministry_memberships`)

-   System and organizational roles are managed via a dedicated `roles` catalog table (`platform_admin`, `ministry_admin`, `mentor`, `outreach_worker`, `curriculum_editor`, `participant`).
-   Memberships link `user_id`, `ministry_id`, and `role_id`. A user's role is never stored as a flat string or unverified attribute on the user record.
-   Access to ministry-scoped resources requires an active membership with an appropriate role.

### Ministry (Maps to `ministries`)

-   Name is required, non-empty, and trimmed.
-   Slug uniqueness is enforced platform-wide for tenant routing.
-   Only platform administrators can alter ministry status.
-   Ministry configuration includes branding, default language, and offline pack limits.

### Outreach (Maps to `outreaches`)

-   Name is required (max 160 characters).
-   `channel` is required and must be either `physical` or `virtual`.
-   Default `location` (text description/address) and optional `city_id` are maintained on the parent outreach.
-   End date (`ends_at`) cannot precede start date (`starts_at`).
-   An owner ministry (`owner_ministry_id`) must be one the authenticated creator is authorized to administer.
-   Archiving prevents new enrolments while preserving all historical attribution data.

### Outreach Event/Section (Maps to `outreach_events`)

-   Parent outreach (`outreach_id`) must exist, be active, and be accessible to the creator.
-   An outreach consists of one or more events/sessions (e.g., Day 1 Morning Session, Day 2 Evening Revival).
-   Event cannot be attached to an archived outreach.
-   Event date range must be logically valid.
-   Optional `location_override` for sessions hosted at an alternate site, and `stream_url` for virtual sessions.

### Event Facilitator (Maps to `event_facilitators`)

-   Facilitators are linked at the event level via `event_id` and `person_id`.
-   Outreach-level facilitators are derived dynamically as the union of facilitators across all constituent events.
-   Using `person_id` rather than `user_id` allows tracking external guest ministers and visiting workers who do not hold platform user credentials.
-   Facilitator role code (`lead`, `speaker`, `counselor`, `usher`, `logistics`) is validated against allowed values.

### Enrolment (Maps to `enrolments`)

-   Learner is identified by `person_id` (foreign key to `people`).
-   Enrolment references `pathway_id` (foreign key to `pathways`), ensuring structural resilience against human-readable pathway code alterations.
-   Enrolment occurs at the event level via `event_id` (foreign key to `outreach_events`), which directly rolls up to the parent outreach.
-   `enrolled_by_user_id` identifies the authenticated actor who performed the enrolment (equals the participant's own `user_id` if self-enrolled, or an outreach worker's `user_id` if registered on-site).
-   Source attribution snapshot (`source_snapshot`) is generated exclusively server-side as an immutable JSONB blob; clients cannot write arbitrary attribution snapshots.
-   Duplicate policy: repeated enrolment for the same person + pathway + event returns the existing record idempotently.
-   Enrolment must not reset or overwrite an existing journey's progress.
-   Consent version affirmed during onboarding is permanently recorded in `accounts_consent_record`.

### Media Assets & Lesson Media (Maps to `media_assets` and `lesson_media_assets`)

-   Centralized digital asset management provides deduplicated media storage (`image`, `audio`, `video`, `document`).
-   Media records track `file_size_bytes`, `mime_type`, `sha256_checksum`, and `storage_path` for offline package bundling.
-   `lesson_media_assets` maps assets to lessons with specific roles (`illustration`, `audio_recitation`, `video_lecture`, `infographic`) and display sort order.
-   All media URLs returned in public curriculum APIs are validated against authorized content versions.

### Activity Event (Maps to `learning_activity_event`)

-   `event_uuid` must be a valid client-generated UUIDv4 and unique.
-   Event owner must match the authenticated participant session.
-   `payload` must strictly validate against event-type-specific schemas; arbitrary unbounded JSON is rejected.
-   Client cannot set `server_received_at`, authoritative score, or permissions.
-   Duplicate UUID with identical canonical payload returns `already_accepted`; duplicate UUID with differing payload returns `conflict` and triggers a safe security audit.
-   Free-form reflection payloads must be submitted as client-side encrypted ciphertext blobs per `docs/database.md` (`learning_encrypted_journal_entry`).

## 4. Endpoint inventory

The following endpoints define the intended contract. Exact URL spelling
may be adjusted during Sprint 0, but semantics and permission rules must
not be weakened.

### Identity and authentication

-   `POST /auth/login` (cookie-based session authentication for Web/PWA)
-   `POST /auth/logout`
-   `POST /auth/token/obtain` (JWT Bearer token obtain for native mobile/API clients)
-   `POST /auth/token/refresh` (JWT refresh token rotation)
-   `POST /auth/token/verify` (JWT token verification)
-   `GET /me`
-   `GET /me/consents`
-   `POST /me/consents`
-   `POST /me/data-request` (only if data access/deletion workflow is
    approved for pilot)

### Schema and API documentation

-   `GET /schema/` (OpenAPI 3.1 YAML/JSON specification via `drf-spectacular`)
-   `GET /docs/` (Interactive Swagger UI)
-   `GET /redoc/` (Interactive Redoc documentation)

### Ministries and memberships

-   `GET /ministries`
-   `POST /ministries` (platform-approved onboarding flow only)
-   `GET /ministries/{id}`
-   `GET /ministries/{id}/members`
-   `POST /ministries/{id}/invitations`
-   `PATCH /ministries/{id}/members/{membership_id}`

### Outreach

-   `GET /outreaches`
-   `POST /outreaches`
-   `GET /outreaches/{id}`
-   `PATCH /outreaches/{id}`
-   `POST /outreaches/{id}/archive`
-   `GET /outreaches/{id}/events`
-   `POST /outreaches/{id}/events`
-   `PATCH /outreaches/{id}/events/{event_id}`

### Enrolment and access

-   `POST /enrolments`
-   `GET /enrolments` (scoped; participants see their own, outreach
    users see only permitted records)
-   `GET /enrolments/{id}`
-   `POST /enrolments/{id}/invitation` (if invitation delivery is in
    pilot)
-   `POST /public/enrolment-codes/resolve` (only if code-based enrolment
    is approved; strict rate limits and no personal data in code)

### Content and learning

-   `GET /pathways`
-   `GET /pathways/{code}`
-   `GET /content/packs`
-   `GET /content/lessons/{id}/versions/{version}`
-   `GET /me/journeys`
-   `GET /me/journeys/{id}`
-   `POST /me/journeys/{id}/events:sync`
-   `POST /me/journeys/{id}/restart` (creates a new attempt/history
    marker; does not erase historical data)
-   `POST /me/journeys/{id}/pause`

### Mentorship

-   `POST /me/mentor-requests`
-   `GET /me/mentor-relationships`
-   `POST /mentor-relationships/{id}/accept` (authorized
    participant/mentor flow)
-   `POST /mentor-relationships/{id}/end`

### Reporting

-   `GET /reports/enrolments`
-   `GET /reports/attribution-coverage`
-   `GET /reports/engagement` (aggregate only by default)

### Content governance

-   `POST /admin/content`
-   `PATCH /admin/content/{id}`
-   `POST /admin/content/{id}/submit-review`
-   `POST /admin/content-versions/{id}/review-decision`
-   `POST /admin/content-versions/{id}/publish`
-   `POST /admin/content-versions/{id}/retire`

## 5. Sync request/response contract

Request example:

``` json
{
  "schema_version": 1,
  "events": [
    {
      "event_uuid": "client-generated-uuid",
      "journey_id": "journey-uuid",
      "lesson_version_id": "lesson-version-uuid",
      "event_type": "assessment_answer_submitted",
      "payload_schema_version": 1,
      "payload": {
        "question_id": "question-uuid",
        "answer_id": "answer-uuid"
      },
      "client_created_at": "2026-10-11T10:30:00Z"
    }
  ]
}
```

Response example:

``` json
{
  "results": [
    {
      "event_uuid": "client-generated-uuid",
      "result": "accepted",
      "server_received_at": "2026-10-11T10:30:03Z"
    }
  ],
  "next_sync_cursor": "opaque-cursor"
}
```

Required semantics: - Process each event independently where safe; one
invalid event must not silently discard unrelated valid events. - Return
a result for every submitted event. - Duplicate event UUID with same
payload returns `already_accepted`. - Duplicate event UUID with
different payload returns `conflict`. - Invalid schema or forbidden
ownership returns `rejected` with a safe code. - The client retains
rejected/conflicted payloads until the user can recover, export, or
safely discard them under a defined UI flow. - Server must not accept an
event against a lesson version the participant was never allowed to
access, except a documented offline grace policy for a version
previously downloaded. - Use a documented maximum batch size and payload
limit; initial suggested limit: 100 events per request and 256 KB per
event payload, subject to security review.

## 6. Reporting semantics

Every report response must identify: - date range and timezone
applied; - metric definition; - filters used; - permission scope; -
whether counts represent enrolment records or unique participants.

Default metrics: - `enrolment_records` - `unique_participants` -
`formation_enrolments` - `restoration_enrolments` -
`discernment_enrolments` - `attribution_complete_records` -
`attribution_missing_records`

Do not expose private reflection text, individual assessment answers,
inferred spiritual state, or mentor notes in aggregate reporting.

## 7. Idempotency and transactions

-   Use an idempotency key for create/enrolment operations that may be
    retried by a client.
-   Store key, actor, endpoint/resource scope, request hash, result
    reference, and expiry policy.
-   Same key + same request returns the original outcome.
-   Same key + different request returns conflict.
-   Enforce uniqueness at the database layer; application-level checks
    alone are insufficient.
-   Create enrolment and its source snapshot in one transaction.
-   Create content version and review metadata atomically where
    appropriate.
-   Every authorization failure must avoid revealing whether an
    inaccessible record exists.

## 8. API acceptance criteria

-   API schema is documented and validated in CI.
-   Every endpoint has permission tests for allowed, denied, and
    cross-ministry access.
-   All write endpoints validate input server-side.
-   Duplicate enrolment and activity sync tests pass under retry
    conditions.
-   Pagination and stable error format are consistent.
-   Attribution reports distinguish record counts from unique people.
-   No endpoint leaks private reflection or pastoral note fields.
