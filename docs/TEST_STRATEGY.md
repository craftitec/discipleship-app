# TEST_STRATEGY.md --- Verification and Release Gates

**Version:** 1.0 \| **Status:** Mandatory

## 1. Testing principle

Acceptance criteria must be verified by automated tests wherever
practical. Manual checks are allowed for usability, theological review,
accessibility, and device/browser behaviour, but must produce a recorded
result, tester, date, environment, and evidence reference.

A green unit-test suite alone does not establish pilot readiness.

## 2. Required CI checks

Every pull request must pass: 1. formatting/lint; 2. frontend TypeScript
type checking; 3. frontend unit tests; 4. backend unit and API tests; 5.
migration consistency check; 6. authorization and tenant/ministry-scope
tests; 7. API schema/contract validation; 8. production frontend build;
9. dependency vulnerability check; 10. secret scanning where available.

Do not merge with failing checks unless an accountable maintainer
records a narrowly scoped, time-bounded exception and the exception does
not involve security, safeguarding, data integrity, or authorization.

## 3. Test layers

### Unit

Test domain services, validation, progression rules, attribution
snapshot creation, content versioning, and adaptive recommendation
rules.

### Integration

Test database constraints, API serializers, permissions, transactions,
idempotency receipts, content publishing, and sync handling.

### End-to-end

Test full flows in a real browser against a test backend, including
outreach creation through to enrolment and participant learning.

### Offline/resilience

Test airplane mode, app restart, reconnect, server timeout, repeated
requests, expired authentication, stale content version, partial batch
failure, and local schema upgrade.

### Human review

Test plain-language comprehension, respectful restoration copy,
theological correctness, accessibility, mentor privacy explanations, and
safeguarding readiness.

## 4. Minimum acceptance test matrix

### Identity and authorization

-   Anonymous user cannot access private journeys.
-   Participant A cannot read or mutate Participant B's journey or
    enrolment.
-   Ministry A administrator cannot access Ministry B outreach,
    enrolment, or reports without an explicit authorized relationship.
-   Outreach worker cannot modify an outreach they do not own or manage.
-   Mentor cannot view a participant's private reflection by default.
-   Changing a client-supplied ministry ID does not expand permissions.
-   Role removal takes effect on the next protected server request.

### Outreach and enrolment

-   Authorized individual creates an outreach without a ministry.
-   Authorized ministry member creates an outreach for that ministry.
-   Outreach creator adds multiple events/sections.
-   Event cannot be attached to a different outreach through tampered
    request data.
-   Enroller enrolls a participant in Formation, Restoration, and/or
    Discernment.
-   Attribution report correctly groups by outreach, event, missioner,
    ministry, and pathway.
-   Report distinguishes enrolment records from unique participants.
-   Duplicate submission with the same idempotency key creates one
    enrolment.
-   Archived outreach cannot receive a new enrolment unless a specific
    approved override exists.
-   Historical attribution remains stable after outreach or ministry
    rename.

### Content governance

-   Draft content is not visible as published learning content.
-   Unapproved content cannot be published by an editor without
    publishing permission.
-   Published content version cannot be mutated.
-   Editing creates a new version.
-   Historical activity retains its lesson version.
-   Missing or invalid Scripture/source metadata blocks publication.
-   Contested interpretation is rendered with its classification and
    attribution.

### Learning and adaptation

-   Wrong answer returns reviewed feedback and a reinforcement option.
-   Repeated unsuccessful attempts offer alternative explanation at the
    configured threshold.
-   Correct answer unlocks the next eligible lesson according to the
    published rules.
-   Previous lessons remain accessible.
-   Inactivity never changes the person's spiritual status or blocks
    access.
-   Restart creates a new attempt/history marker without silently
    deleting prior work.
-   AI unavailable: approved lessons and deterministic adaptation still
    work.
-   AI response without valid citations is rejected or replaced by
    fallback content.

### Offline and sync

-   Downloaded lesson opens with network disabled.
-   Activity is saved locally before saved confirmation appears.
-   Activity remains after closing and reopening the PWA.
-   Reconnection synchronizes activity and updates status to synced.
-   Replaying the same event UUID does not create a duplicate.
-   Reusing an event UUID with a different payload returns conflict.
-   One invalid event in a batch does not silently discard valid
    independent events.
-   Server timeout leaves local activity queued.
-   Expired session prompts safe re-authentication without losing local
    activity.
-   Client cannot sync activity to another user's journey.
-   A schema upgrade preserves queued events or stops with an actionable
    recovery message.
-   Stale content version follows documented offline grace/retirement
    policy.

### Mentorship and safeguarding

-   Mentor assignment cannot activate without participant consent.
-   Withdrawal of consent revokes mentor access.
-   Mentor can see only allowed fields.
-   Private reflection sharing is explicit and independently revocable
    where technically possible.
-   The UI accurately states whether a support request is monitored and
    when.
-   No AI-generated safeguarding decision triggers an external
    notification.

### Accessibility and usability

-   Keyboard navigation works for critical flows.
-   Inputs have programmatic labels and accessible errors.
-   Focus visibility and logical order are maintained.
-   Contrast and non-color status indicators meet WCAG 2.2 AA target.
-   360 px viewport has no horizontal overflow in critical flows.
-   Lesson content uses clear language and avoids unexplained jargon.
-   Offline and sync status is understandable without technical
    knowledge.
-   Screen-reader review is performed for sign-in, enrolment, lesson
    completion, and sync recovery.

## 5. Sample end-to-end acceptance scenarios

### Scenario A --- Outreach to synchronized learning

Given an authorized missioner and an active outreach with an event, when
the missioner enrols a participant into Formation, then the enrolment
records the correct outreach/event/missioner attribution. When the
participant opens the PWA, downloads a lesson, completes an activity
offline, restarts the app, and reconnects, the activity synchronizes
exactly once and appears in the participant's journey.

### Scenario B --- Multiple pathways and resumption

Given a participant enrolled into Formation and Discernment through
different events, when they complete one lesson in Formation and leave
for an extended period, then they can later resume Formation at the
correct point and access Discernment independently. Neither pathway
overwrites the other, and inactivity creates no negative status.

### Scenario C --- Ministry data isolation

Given two ministries with separate users and outreaches, when a user
from Ministry A requests Ministry B's report or enrolment by changing an
ID, the server returns a safe authorization failure and records the
security-relevant event without revealing protected data.

### Scenario D --- Transparent doctrinal disagreement

Given a reviewed discernment lesson with a shared-core statement and two
attributed interpretations, when a participant views it, the interface
distinguishes Scripture text, reviewer interpretation, and contested
views. The application does not label one view as universal consensus
unless the approved governance standard says so.

## 6. Performance and load checks

Before pilot, test a representative dataset with: - at least 1,000
synthetic participants; - at least 100 outreach records and 500
event/section records; - concurrent enrolment/reporting activity
representative of the pilot estimate; - sync batches at configured
maximum size; - database indexes for common report filters.

These are test dataset minimums, not a claim that the system is proven
at production scale. Record actual latency and error rates and compare
with targets in `ARCHITECTURE.md`.

## 7. Release gates

Release only if: - all required CI checks pass; - all P0/P1 defects are
closed; - no known cross-scope authorization defect exists; - offline
save/reopen/sync/retry passes on supported browsers; - backup
restoration is demonstrated; - three pathways contain approved pilot
content; - consent, retention, incident response, and safeguarding
owners are documented; - accessibility/usability review is recorded; -
rollback procedure is tested; - pilot owner signs the release checklist.

## 8. Defect severity

-   **P0:** active severe data exposure, data corruption/loss, unsafe
    safeguarding behaviour, or service-wide critical outage. Stop
    release; disable affected capability if live.
-   **P1:** major journey broken, unauthorized access, repeatable sync
    loss, or core pathway unusable. Blocks release.
-   **P2:** significant non-core defect with a safe workaround. Release
    only with owner approval and tracked remediation.
-   **P3:** minor visual/copy issue. May be scheduled after pilot if no
    accessibility or safety impact.
