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

## 3. Core entities and validation

### Ministry

Fields: `id`, `name`, `slug`, `status`, `created_at`, `updated_at`.

Rules: - Name is required and trimmed. - Slug uniqueness is enforced
within the platform or tenancy model selected by architecture
decision. - Only authorized platform administrators can change
platform-level ministry status. - Membership roles are separate records;
do not store one role on the user record for all ministries.

### Outreach

Fields: `id`, `name`, `description`, `owner_user_id`,
`owner_ministry_id` nullable, `status`, `starts_at` nullable, `ends_at`
nullable, timestamps.

Rules: - Name required, max length configured centrally (suggest 160
characters). - End date cannot precede start date when both are
present. - An owner ministry must be one the creator is authorized to
act for. - Archiving prevents new enrolments by default but does not
delete historical attribution.

### Outreach event/section

Fields: `id`, `outreach_id`, `name`, `kind`, `status`, optional
dates/location/mode, timestamps.

Rules: - Parent outreach must be accessible to the actor. - Event must
not be created under an archived outreach. - Event date range must be
valid. - Do not require location for online or unspecified events.

### Enrolment

Fields: `id`, `participant_id`, `pathway_code`, optional `outreach_id`,
`event_id`, `missioner_id`, `ministry_id`, `source_type`,
`source_snapshot`, `created_by_id`, `consent_version`, `status`,
timestamps.

Rules: - Pathway must be one of the three supported codes for pilot. -
If `event_id` is supplied, its `outreach_id` must match the enrolment's
outreach. - Source fields must be server-validated against actor
permissions. - `source_snapshot` is generated server-side; clients
cannot write arbitrary attribution snapshots. - Duplicate policy: same
participant + pathway + event is idempotent for the same
request/idempotency key. If a genuine repeat enrolment is needed, create
a new event or an explicitly versioned enrolment attempt rather than
accidental duplicate records. - Enrolment must not overwrite an existing
journey's progress. - Store participant consent and notice version
applicable to the enrolment flow.

### Activity event

Fields: `event_uuid`, `journey_id`, `lesson_version_id`, `event_type`,
`payload_schema_version`, `payload`, `client_created_at`,
`server_received_at`.

Rules: - `event_uuid` unique globally or scoped by authenticated user
with a unique database constraint. - Event owner must match
authenticated participant or an explicitly authorized client session. -
`payload` must be validated against event-type-specific schema; do not
accept arbitrary unbounded JSON. - Enforce size limits and reject
executable content. - Client cannot set `server_received_at`,
authoritative score, or permissions. - Duplicate UUID with identical
canonical payload returns `already_accepted`; same UUID with a different
payload returns conflict and is audited safely.

## 4. Endpoint inventory

The following endpoints define the intended contract. Exact URL spelling
may be adjusted during Sprint 0, but semantics and permission rules must
not be weakened.

### Identity

-   `POST /auth/login`
-   `POST /auth/logout`
-   `GET /me`
-   `GET /me/consents`
-   `POST /me/consents`
-   `POST /me/data-request` (only if data access/deletion workflow is
    approved for pilot)

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
