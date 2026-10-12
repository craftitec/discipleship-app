# PRODUCT.md --- Product Requirements

**Version:** 1.0 \| **Status:** Pilot baseline

## 1. Product statement

An accessible, education-level-agnostic digital discipleship platform
that helps people begin, continue, or resume a Christian formation
journey through three connected pathways---Formation, Restoration, and
Biblical Discernment---while preserving human ministry, theological
accountability, privacy, and offline continuity.

The platform also provides source attribution for outreach-based
enrolment so ministries and missioners can understand which outreaches,
events/sections, and pathways are generating enrolments and where
follow-up may be needed.

## 2. Outcomes

### Participant outcomes

-   Start or continue a journey at any time without being judged for
    inactivity, restarting, or changing pace.
-   Access approved lessons on a responsive PWA, including previously
    downloaded content when offline.
-   Understand learning objectives in plain language regardless of
    formal education level.
-   Complete short lessons, questions, reflection prompts, and practical
    activities.
-   See what has been saved locally and what has synchronized.
-   Request optional mentor or church support without being forced to
    disclose private reflections.

### Ministry outcomes

-   Create an outreach and add one or more events/sections under it.
-   Enrol participants into one or more pathways for a specific
    event/section.
-   Attribute enrolments to the originating outreach, event/section,
    missioner, and ministry when known.
-   Review aggregate enrolment and engagement reports with
    permission-based access.
-   Assign optional mentors and record consented follow-up status.
-   Publish only approved, versioned learning content.

### Governance outcomes

-   Establish shared core biblical principles and standards while
    transparently identifying interpretive differences.
-   Prevent unreviewed content and AI-generated material from becoming
    authoritative curriculum.
-   Preserve a clear audit trail for content approval, role changes, and
    sensitive administrative actions.

## 3. Non-goals for pilot

The pilot will not: - Replace a local church, pastoral care, human
discipleship, or emergency services. - Certify salvation, spiritual
maturity, faithfulness, restoration, or ministry readiness. - Rank
people, churches, missioners, or ministries by spiritual worth. -
Automatically label a person as backslidden or spiritually unhealthy
based on inactivity. - Provide public social networking, open discussion
forums, livestreaming, or private mentor chat unless separately
approved. - Automatically adjudicate every doctrinal disagreement or
claim that all traditions agree. - Let AI author or publish curriculum
without accountable human review. - Require continuous connectivity to
complete downloaded lessons and save progress. - Share participant-level
progress with a ministry, missioner, or mentor merely because they
created the outreach. - Introduce microservices, predictive
spiritual-risk scoring, or a complex recommendation model in the pilot.

## 4. Primary user groups and roles

### Participant

A person enrolled in one or more pathways. Can view assigned and
available approved content, complete activities, manage their consent
and notification choices, see their progress, and optionally request
human support.

### Outreach creator

An individual or ministry user permitted to create an outreach. Must be
authenticated. The creator may be a missioner acting independently or a
user affiliated with a ministry.

### Ministry administrator

Manages the ministry workspace, authorized users, outreach records,
event/section setup, and aggregate reports. Does not automatically
receive access to private reflections or sensitive pastoral notes.

### Missioner / outreach worker

May create or manage assigned outreaches/events and enrol participants.
Access is limited to records they are authorized to manage. The system
must preserve who created or imported each enrolment.

### Content editor

Drafts and maintains learning content, references, translations,
learning objectives, and metadata. Cannot publish content unless also
granted the reviewer/publisher permission.

### Theological reviewer

Reviews doctrinal claims, Scripture references, interpretive notes, and
content suitability. Approval must be recorded with reviewer identity,
timestamp, content version, and decision.

### Mentor

A person assigned to support a participant after the participant
consents. Can see only the participant information specifically
permitted by policy and the participant's consent. No default access to
private reflections or full activity history.

### Platform administrator

Operates the service and manages platform-level configuration. Access to
participant content must be limited, justified, logged, and technically
separated from ordinary support workflows.

## 5. Core domain concepts

-   **Ministry:** an organizational workspace that may manage users,
    outreaches, events, enrolments, and approved local content within
    network policy.
-   **Outreach:** a named mission/outreach initiative with an owner,
    status, dates, and attribution identity.
-   **Outreach event/section:** a child unit under an outreach, such as
    a crusade service, campus meeting, community visit, follow-up group,
    or event segment. Use one canonical entity with a `kind` field
    rather than separate tables for every possible event label.
-   **Missioner:** an authenticated user or named accountable outreach
    worker associated with an outreach/enrolment source. Do not create
    duplicate user identities just to record attribution.
-   **Enrolment:** a traceable record linking a participant to a pathway
    and optionally to an outreach event, missioner, and ministry. One
    person may have multiple enrolments across different pathways or
    events; duplicate enrolments for the same participant/pathway/event
    must be handled by an explicit idempotent rule.
-   **Pathway:** Formation, Restoration, or Biblical Discernment.
-   **Journey:** a participant's progress through a versioned pathway.
-   **Lesson/activity attempt:** a record of work done against a
    specific content version.
-   **Content version:** an immutable published version of a lesson or
    learning resource.
-   **Mentor relationship:** a consent-based, time-bounded association
    between participant and mentor.
-   **Attribution:** the source fields retained for reporting,
    regardless of later changes to outreach ownership.

## 6. Primary workflows

### 6.1 Create outreach and events

1.  Authorized creator signs in.
2.  Creator creates an outreach with name, description (optional), owner
    ministry (optional for independent creators), planned/actual dates
    (optional), and status.
3.  Creator adds one or more events/sections with name, kind, date/time
    (optional), location/mode (optional), and status.
4.  Creator may invite authorized enrolment workers if supported by the
    assigned permissions.
5.  Outreach and event identifiers are stable and unique.

**Acceptance:** an unauthorized user cannot create or modify another
creator's or ministry's outreach. A child event cannot be attached to a
non-existent or inaccessible outreach.

### 6.2 Enrol a participant

1.  Enroller chooses outreach and event/section, if applicable.
2.  Enroller records only the minimum required participant details.
3.  Enroller selects one or more pathways.
4.  The participant receives a non-judgmental invitation/access method
    and a clear privacy explanation.
5.  The system records the enrolment source, enrolment actor,
    timestamps, consent state, and pathway.
6.  The participant can sign in and continue at any later time.

Pilot enrolment may support direct entry by an authorized worker and a
participant self-enrolment link/code. The exact access-link mechanism
must be selected before implementation; see `DECISION_LOG.md`. Do not
place sensitive personal information in URLs or QR codes.

**Acceptance:** every outreach-attributed enrolment has a valid
outreach/event reference when those fields are selected, and preserves
creator/source attribution. A person can be enrolled into multiple
pathways without losing existing progress.

### 6.3 Begin or resume a pathway

1.  Participant signs in or follows the approved access flow.
2.  The system shows their current journeys and any available assigned
    journeys.
3.  Participant selects a pathway and continues the next eligible
    activity, or resumes a previous one.
4.  No inactivity threshold blocks access, downgrades the participant,
    or labels their spiritual state.
5.  A participant may request to restart or review earlier lessons.
    Restarting must not silently delete historical attempts.

### 6.4 Offline learning

1.  Participant downloads an eligible lesson pack while online.
2.  Participant opens the PWA offline.
3.  Participant reads lessons and completes supported activities.
4.  Each change is saved locally before the UI reports "Saved on this
    device."
5.  On reconnect, the client syncs queued changes and reports server
    confirmation.
6.  If synchronization fails, local data remains and the UI explains the
    status in plain language.

### 6.5 Optional mentor connection

1.  Participant requests or consents to mentor support.
2.  Authorized coordinator reviews the request and assigns an eligible
    mentor.
3.  Participant is informed who can see what.
4.  Mentor sees only approved fields and may record permitted follow-up
    metadata.
5.  Either party may end the relationship; access is revoked according
    to retention policy.

### 6.6 Content governance

1.  Editor creates a draft with learning objective, content, Scripture
    references, source/license metadata, audience/language metadata, and
    pathway placement.
2.  Reviewer assesses the material.
3.  Approved version is published immutably.
4.  Changes create a new draft/version; published content is never
    silently overwritten.
5.  Participants' historical attempts retain the content version they
    used.

## 7. Functional requirements

### Identity and access

-   FR-001: Users must authenticate before accessing private journeys or
    ministry workspaces.
-   FR-002: Enforce server-side role and object-level authorization for
    every protected operation.
-   FR-003: A participant may belong to multiple ministries or be
    enrolled by different outreach sources without duplicate identity
    creation.
-   FR-004: Public enrolment links/codes, if enabled, must be revocable,
    rate-limited, scoped, and contain no personal data.
-   FR-005: User-facing copy must use respectful, non-condemnatory
    language.

### Outreach and attribution

-   FR-010: Authorized users can create, update, archive, and view their
    permitted outreaches.
-   FR-011: Each outreach can contain multiple events/sections.
-   FR-012: An enrolment can reference one pathway and an optional
    outreach, event/section, ministry, and missioner.
-   FR-013: Enrolment source and creation actor are retained as
    attribution snapshots; reassignment must not rewrite historical
    attribution.
-   FR-014: Reports can group enrolments by date range, outreach,
    event/section, missioner, ministry, and pathway, subject to
    permissions.
-   FR-015: Reports distinguish total enrolment records from unique
    participants and must label the metric unambiguously.
-   FR-016: Repeated form submission or network retry must not create
    duplicate enrolments when the same idempotency key is reused.

### Learning and adaptation

-   FR-020: The platform provides three named pathways: Formation,
    Restoration, Biblical Discernment.
-   FR-021: Each published lesson has explicit learning objectives and
    approved source references.
-   FR-022: The participant can complete supported activities without
    connectivity when content is downloaded.
-   FR-023: Progress is based on defined activity events and transparent
    rules, not opaque spiritual scoring.
-   FR-024: Adaptive recommendations explain the learning reason in
    simple language.
-   FR-025: A participant can resume after any length of inactivity
    without penalty.
-   FR-026: A participant can review prior material and request a
    restart without erasing history.
-   FR-027: AI-assisted output is optional, bounded by approved sources,
    labeled where applicable, and never automatically published as
    curriculum.
-   FR-028: If AI is unavailable, core learning and deterministic
    adaptation remain functional.

### Content and governance

-   FR-030: Content has draft, in-review, approved, published, retired
    states.
-   FR-031: Only authorized reviewers can approve content; only
    authorized publishers can publish approved versions.
-   FR-032: Published content versions are immutable.
-   FR-033: The system records source/license information and review
    metadata.
-   FR-034: Contested interpretations are presented as interpretations
    with attribution, not falsely stated as universal agreement.
-   FR-035: The network defines and approves its core biblical
    principles and doctrinal boundaries before pilot content is
    published.

### Mentorship and privacy

-   FR-040: Mentorship is optional by default.
-   FR-041: Consent is recorded before mentor access begins.
-   FR-042: Mentors cannot access private reflections unless the
    participant separately and explicitly shares them.
-   FR-043: A ministry's enrolment attribution does not grant automatic
    access to participant progress.
-   FR-044: Participants can withdraw mentor consent and request
    account/data actions according to the published policy.

## 8. Non-functional requirements

-   NFR-001: At a 360 CSS-pixel viewport, core participant flows have no
    horizontal page overflow.
-   NFR-002: Previously downloaded lesson content and supported
    activities remain available offline after app restart.
-   NFR-003: Local activity writes are durable before the interface
    displays a saved confirmation.
-   NFR-004: Synchronization is idempotent and retryable.
-   NFR-005: All private API endpoints require authentication and
    object-level authorization unless explicitly designated public.
-   NFR-006: Sensitive fields are excluded from aggregate reporting by
    default.
-   NFR-007: The interface targets WCAG 2.2 AA for critical participant
    and administration flows.
-   NFR-008: The backend provides automated tests for authorization,
    validation, idempotency, content versioning, and sync.
-   NFR-009: The application displays an understandable offline/sync
    status.
-   NFR-010: No sensitive personal information or private reflections
    are written to ordinary application logs.

## 9. Pilot metrics

Track operational/product indicators, not judgments about spiritual
worth: - enrolment records and unique participants by source
dimensions; - percentage of enrolments with complete attribution; -
first lesson started/completed; - return/resume rates at defined
intervals, reported only in aggregate; - offline activities successfully
synchronized; - duplicate/rejected/conflicted sync operations; -
optional mentor requests and accepted connections; -
participant-reported clarity, accessibility, and perceived safety; -
content review turnaround and content correction incidents.

Do not set performance targets until the pilot owner approves a baseline
and sample size. Do not publish rankings of missioners/ministry based on
participant engagement or spiritual outcomes.

## 10. Definition of pilot-ready

All release gates in `TEST_STRATEGY.md` pass; all P0/P1 security and
safeguarding issues are closed; three pathways contain reviewed sample
content; source attribution works end to end; offline learning survives
app restart; synchronization is proven idempotent; accessibility and
participant comprehension have been tested; backup restoration has been
demonstrated; and an accountable ministry owner signs off the pilot.
