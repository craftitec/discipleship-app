# SECURITY_AND_SAFEGUARDING.md

**Version:** 1.0 \| **Status:** Mandatory policy baseline; legal and
ministry-owner review required before live pilot

## 1. Governing principles

-   Dignity: no shame-based design, public spiritual status, or punitive
    treatment of inactivity.
-   Minimum necessary data: collect only information needed for
    enrolment, learning, safety, and agreed follow-up.
-   Consent and transparency: explain what is collected, who can see it,
    and how it is used.
-   Least privilege: access is specific to role, ministry, assignment,
    and participant consent.
-   Human accountability: AI does not make doctrinal, pastoral,
    safeguarding, or high-impact decisions.
-   Data protection: apply applicable privacy law in each pilot
    jurisdiction; do not assume one jurisdiction's rules cover all
    participants.
-   Safe failure: loss of connectivity or AI availability must not
    destroy work or block core learning.

## 2. Data classification

### Public

Published public information about the platform and public approved
learning material.

### Internal

Draft content, configuration, non-sensitive operational metrics, and
internal workflow metadata.

### Personal

Account details, contact information, enrolment records, pathway
progress, and mentor relationships.

### Sensitive / restricted

Private reflections, disclosures about abuse or personal crises,
pastoral notes, safeguarding case data, and any data whose exposure
could reasonably cause harm.

The pilot should avoid collecting sensitive/restricted data unless a
documented, approved use case and handling process exists. Free-text
reflection prompts should state whether responses are private, shared,
or not submitted to the server. Prefer local/private reflection by
default unless the participant explicitly chooses to share.

## 3. Consent and notices

Before enrolment is finalized, show a plain-language notice covering: -
why the platform records enrolment and pathway progress; - which
ministry/outreach source is associated with enrolment; - which
people/roles can see enrolment metadata; - that private reflections are
not automatically shared; - that mentor support is optional; - how to
withdraw consent or request data access/correction/deletion where
applicable; - that the platform is not an emergency service; - how
notifications and AI-assisted features work, if enabled.

Record notice/consent version, timestamp, user/action source, and scope.
Do not bundle mentor consent, AI consent, marketing consent, and basic
platform terms into one ambiguous checkbox.

If minors may participate, the pilot must not onboard them until the
ministry owner approves age-appropriate consent, guardian involvement
where legally required, child safeguarding, mentor vetting,
communication rules, and jurisdiction-specific procedures. Do not infer
age or guardian consent.

## 4. Role and object-level access

-   Participants access only their own account, journeys, and consented
    relationships.
-   Outreach creators manage only their own outreach records or those
    delegated to them.
-   Ministry administrators access records in their ministry scope, not
    unrelated ministries.
-   Missioners access only assigned outreach/event and enrolment data
    needed for their role.
-   Mentors see only the minimum information explicitly permitted for
    their active relationship.
-   Content editors cannot approve their own content unless the
    governance policy explicitly permits a documented dual-control
    exception; default is separation of author and reviewer.
-   Theological reviewers cannot alter participant records through their
    review role.
-   Platform support does not gain routine access to private
    reflections.
-   Administrative impersonation, if ever added, is excluded from the
    pilot unless independently approved and fully audited.

Every access decision must be enforced on the server. Hiding a button is
not authorization.

## 5. Outreach attribution privacy

Attribution is for operational learning and responsible follow-up, not
surveillance or spiritual ranking.

-   A ministry or missioner can see the enrolment records they are
    authorized to manage.
-   Aggregate reports are the default.
-   Do not expose lesson answers, private reflections, inferred
    spiritual state, or full journey history in outreach dashboards.
-   Use small-cell suppression for sensitive aggregate reports if counts
    could identify a person; threshold is a policy decision to approve
    before pilot.
-   Do not publish rankings based on return rates, completion, or
    pathway progress.
-   Retain historical attribution snapshots, but support correction of
    demonstrably inaccurate attribution through an audited correction
    process.

## 6. Mentor safety

Before a mentor can access a participant relationship: 1. Participant
explicitly requests or accepts the relationship. 2. The system records
what information is shared. 3. Mentor eligibility and any required
training/vetting are confirmed by the ministry's accountable owner. 4.
Participant sees mentor identity and how to end the relationship. 5. The
platform applies the minimum permissions defined for that relationship.

The pilot should not include unrestricted direct messaging unless a
separate safety design is approved. Mentors must not be able to export
lists of participants or bulk download personal records by default.

## 7. Restoration and sensitive disclosures

-   Restoration is a pathway option, not an automated label.
-   The app must not prompt users to confess private matters in a way
    that implies a human will read them if that is not true.
-   The app must clearly state whether a response stays on device, is
    stored privately, or is shared.
-   Do not present AI as a pastor, therapist, crisis counselor, or
    substitute for professional care.
-   If the platform permits a participant to report abuse, imminent
    danger, or a safeguarding concern, the ministry must define the
    responsible human team, response hours, escalation route,
    confidentiality limits, and emergency instructions before the
    feature is enabled.
-   If no such staffed workflow exists, do not create an in-app "report
    danger" feature that appears monitored. Display clear local
    emergency guidance approved for the target jurisdiction instead.
-   No AI-only safeguarding triage or automated notification to a
    mentor/family/church.

## 8. AI data and output safeguards

-   Use only an approved provider with reviewed data processing and
    retention terms.
-   Do not send personal identifiers, private reflections, mentor notes,
    or safeguarding records to AI by default.
-   Use retrieval from approved, versioned content.
-   Require citations and verify source IDs against the retrieved set.
-   Label AI-assisted responses and state that they may be incomplete.
-   Provide approved-source fallback when retrieval is insufficient.
-   Rate-limit requests and set usage/cost caps.
-   Treat user input and retrieved documents as untrusted; protect
    against prompt injection.
-   Do not use participant interactions to train models unless
    separately approved with explicit informed consent and a lawful
    basis.
-   Log source IDs, model/provider version, request category, and
    outcome; avoid retaining full prompts where they contain personal
    data.
-   AI must never modify access permissions, content approval, consent,
    enrolment attribution, mentor assignments, or safeguarding
    decisions.

## 9. Technical security requirements

-   HTTPS only; secure cookies or approved token storage strategy.
-   CSRF protection for cookie-authenticated state changes.
-   Password hashing via framework-supported modern defaults; no custom
    cryptography.
-   Rate limits for authentication, invite/code resolution, sync, and
    AI.
-   Strict CORS allowlist.
-   Input validation and output encoding.
-   Content Security Policy and security headers appropriate to the PWA.
-   File upload restrictions: allowlist types, size limits, malware
    scanning where applicable, and private storage for non-public
    assets.
-   Secrets only in secret manager/environment configuration; never
    commit secrets.
-   Audit role changes, exports, content
    approval/publication/retirement, consent changes, and access to
    restricted records.
-   Logs must exclude tokens, passwords, private reflections, and
    safeguarding narrative.
-   Automated dependency and vulnerability checks in CI.
-   Database backups encrypted and access restricted.
-   Define incident response owner and breach notification obligations
    for pilot jurisdictions.

## 10. Offline-device risks

-   Explain that downloaded lessons and unsynced work may remain on a
    device.
-   Provide logout and local-data clearing controls.
-   On shared devices, warn users before enabling offline private
    progress.
-   Do not store credentials in IndexedDB.
-   Minimize sensitive data in local snapshots.
-   Apply local expiry/cleanup to cached personal data where practical,
    without deleting unsynced work silently.
-   If local data is cleared, warn that unsynced work may be lost and
    provide a safe export/recovery path where feasible.
-   Avoid sensitive notification previews on lock screens.

## 11. Retention and deletion

Exact retention periods are not established by this baseline. Before
pilot, the data owner must approve a retention schedule for: - inactive
accounts; - enrolment and attribution records; - activity events and
assessment attempts; - private reflections; - mentor relationships and
follow-up records; - security/audit events; - consent evidence; -
backups and deleted data.

Deletion requests must follow applicable law and documented ministry
obligations. Where records must be retained for a defined lawful
purpose, explain the limitation to the user. Do not promise deletion
from backups or third-party services unless the implementation can
deliver it.

## 12. Incident response

For suspected privacy, security, or safeguarding incident: 1. Preserve
relevant technical evidence without unnecessary copying of sensitive
content. 2. Notify the designated incident owner through the approved
channel. 3. Restrict affected access or disable the feature if risk is
ongoing. 4. Assess scope and applicable legal/ministry reporting duties.
5. Communicate with affected people when required and safe. 6. Document
cause, remediation, and prevention. 7. Do not let a coding agent close
an incident solely because a patch passes tests.

## 13. Release blockers

Pilot must not launch with: - cross-ministry data leakage; -
unauthorized mentor access; - loss or duplication of offline activity
due to retry; - unreviewed content published as approved; - unapproved
AI processing of personal/sensitive data; - misleading promises about
confidentiality or monitored support; - no approved data-retention and
incident owner; - minors onboarded without approved safeguarding
arrangements.
