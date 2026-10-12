# DISCIPLESHIP_FRAMEWORK.md --- Formation, Restoration and Discernment

**Version:** 1.0 \| **Status:** Framework baseline requiring
theological-owner approval

## 1. Purpose and theological posture

The platform supports Christian formation through approved
Scripture-grounded resources and human community. It is not a new
church, denomination, doctrinal authority, spiritual diagnostic system,
or replacement for pastoral ministry.

The network's objective is to establish shared core biblical principles
and standards while treating secondary, disputed, or tradition-specific
interpretations transparently. "Harmonization" means clarifying shared
foundations, identifying genuine agreement, describing differences
fairly, and setting clear publishing boundaries. It does not mean
erasing meaningful disagreements or presenting one interpretation as
universally accepted without authority to do so.

## 2. Required content governance before publication

The network must appoint: - a theological governance owner or council; -
a content editor/curator; - at least one qualified reviewer for each
published content item; - a safeguarding owner; - a content-rights owner
or responsible approver.

The governance owner must publish a versioned **Core Biblical Principles
and Content Standard** before the first real pilot participant is
enrolled. The application must not invent this standard.

That standard must specify: 1. core doctrines/principles the network
affirms; 2. doctrinal questions that are outside the pilot's scope; 3.
questions where multiple Christian interpretations may be represented;
4. how Scripture references and quotations are verified; 5. how
translations are selected and licensed; 6. which source materials may be
adapted and under what rights; 7. review, approval, correction,
withdrawal, and appeal processes; 8. how a local ministry may add
contextual resources without overriding network-level standards.

If reviewers disagree, content remains in review until the designated
governance process resolves the publication decision. Coding agents must
not resolve theological disputes.

## 3. Pathway definitions

### A. Formation

**Purpose:** provide a dependable foundation for people beginning or
strengthening their Christian walk.

Suggested curriculum modules, subject to review: 1. The gospel and
response to Christ. 2. Assurance, grace, repentance, and faith, framed
according to the approved standard. 3. Reading and understanding the
Bible. 4. Prayer and developing sustainable spiritual practices. 5.
Christian identity, character, and everyday obedience. 6. Life in
Christian community and the role of the local church. 7. Worship,
service, gifts, and serving others. 8. Handling questions, temptation,
disappointment, and uncertainty. 9. Sharing faith and caring for others.
10. Continuing growth and helping another person grow.

Each module must define: - one primary learning outcome in plain
language; - a small number of measurable learning objectives; - approved
Scripture references; - a short explanation or media resource; - one
comprehension activity; - one practical application or reflection
prompt; - a next step and optional human-support route; - estimated
duration and offline availability; - content version and review status.

Do not imply that completing the list establishes spiritual maturity or
guarantees a particular spiritual outcome.

### B. Restoration

**Purpose:** offer a dignified route back into learning, community, and
appropriate human support after disengagement, doubt, hurt, failure, or
a difficult season.

Principles: - entry is voluntary; inactivity alone does not
automatically assign this pathway; - users are not labeled "backslidden"
by the system; - no shaming, threats, guilt-based notifications, or
public indicators; - the user chooses what they wish to work on where
feasible; - the path begins with welcome, listening, and manageable next
steps rather than forced confession; - sensitive disclosures are not
treated as ordinary learning data; - pastoral or safeguarding escalation
follows a human-reviewed policy, not an AI diagnosis; - participants can
leave, pause, or return later.

Suggested stages: 1. Welcome back and choose a starting point. 2.
Reflect on what would help now, without requiring disclosure. 3. Revisit
relevant foundational teaching. 4. Choose a small, realistic practice or
next step. 5. Explore reconnection with a trusted person, mentor, or
church if desired. 6. Review progress and choose the next step.

Restoration content must distinguish pastoral support from mental-health
treatment and emergency response. The platform must not claim that
completing a sequence resolves abuse, trauma, addiction, mental illness,
or other complex circumstances.

### C. Biblical Discernment

**Purpose:** teach users to examine claims, traditions, cultural
practices, and doctrinal assertions responsibly in light of Scripture
and transparent interpretive reasoning.

Each discernment unit should separate: 1. **Question or claim:** what is
being examined, stated neutrally. 2. **Biblical text:** verified
references and properly licensed quotations or summaries. 3.
**Context:** author, audience, literary form, surrounding passage, and
relevant historical context where established. 4. **Interpretation:**
how the source or reviewer understands the text. 5. **Shared core:**
what the network's approved standard considers foundational. 6.
**Interpretive differences:** credible Christian views, with sources and
fair summaries where appropriate. 7. **Application:** questions that
help users reason and act responsibly. 8. **Limitations:** what the
material does not establish. 9. **Human discussion:** optional route to
a mentor or church leader.

The interface must distinguish a biblical quotation from paraphrase, a
source's claim from platform explanation, and a core standard from a
contested interpretation. No fabricated references or citations are
allowed.

## 4. Education-level-agnostic design

The platform must not infer a person's intelligence, literacy, spiritual
maturity, or worth from spelling, response speed, education history, or
assessment performance.

Content requirements: - use clear, concrete language and explain
necessary theological terms; - offer audio or other accessible formats
when rights and pilot scope permit; - use short sections with one main
idea at a time; - use examples that do not assume a particular country,
denomination, income, or schooling experience; - make controls readable
and usable on low-cost phones; - avoid color-only indicators and
unexplained icons; - allow participants to review material without
penalty; - provide captions/transcripts for audio/video where
applicable; - do not require long-form writing as the only evidence of
understanding.

Assessment should test the stated learning objective, not academic
sophistication. Allow multiple response formats where practical.

## 5. Progression model

Use explicit journey states: - `not_started` - `in_progress` -
`awaiting_sync` (client-side status only) - `completed` -
`paused_by_participant` - `archived` (administrative/content lifecycle
only; never a punitive state)

A pathway can contain ordered modules, optional modules, lessons, and
activities. A lesson may be: - `available` - `in_progress` -
`completed` - `needs_review` where a learning rule recommends
reinforcement.

`needs_review` describes the learning activity, not the participant.

A journey's progress percentage, if shown, must be defined as a simple
completion measure and labeled as such. It must never be called a
spiritual maturity score. Do not block the entire journey after a wrong
answer unless a specific reviewed pedagogical reason requires it;
default behaviour is feedback plus reinforcement.

## 6. Adaptation rules for pilot

Adaptation must be explainable, deterministic where safety or
progression is concerned, and logged in a way that supports debugging
without exposing unnecessary sensitive information.

Initial rule set: - R1: If an answer is incorrect, show reviewed
feedback and offer a retry or a short reinforcement resource. - R2: If
the same objective is missed repeatedly, recommend an alternative
explanation or optional mentor discussion; do not label the user. - R3:
If an objective is demonstrated, offer the next eligible lesson while
retaining access to prior material. - R4: If a participant returns after
inactivity, resume their last valid state and optionally show a neutral
"Welcome back" message. - R5: If the participant explicitly chooses "I
want to start again," create a new journey attempt or reset the active
path while preserving prior history according to policy. - R6: If AI is
used to tailor a supplemental explanation, retrieve only from approved
content, cite the retrieved sources, label it as AI-assisted, and
provide a non-AI alternative. - R7: AI output cannot change a content
approval state, pathway eligibility, consent, mentor access,
safeguarding status, or doctrinal classification. - R8: If AI
confidence/source grounding is insufficient or the service is
unavailable, return the approved content and deterministic
recommendation instead.

The exact repeated-miss threshold, progression prerequisites, and
model/provider configuration are product decisions that must be recorded
before implementation. Until approved, use a configurable default of two
unsuccessful attempts before offering an alternative explanation,
without blocking progress.

## 7. AI boundaries

Permitted for pilot only if approved: - retrieving and summarizing
approved content with citations; - generating an optional alternative
explanation from approved source passages; - recommending reviewed
content based on explicit learning events and user-selected needs; -
helping content editors find potentially relevant approved resources,
without publishing content.

Prohibited: - generating authoritative doctrine without human review; -
presenting uncited output as Scripture; - inventing verse references or
claiming a verse says something it does not; - inferring salvation, sin,
spiritual maturity, mental health, abuse, or backsliding from usage
data; - assigning risk scores to participants; - contacting a mentor,
ministry, family member, or church on the model's own initiative; -
using private reflections to train external models unless separately
approved with explicit, informed consent and a documented lawful
basis; - allowing prompt injection or lesson text to override system
policies or disclose private records.

## 8. Content schema minimum

Every content item must include: - stable content ID; - pathway and
module placement; - title and plain-language summary; - learning
objectives; - body or media reference; - Scripture references and
translation identifier when quoted; - source attribution and
licence/permission status; - language and estimated duration; -
accessibility metadata; - reviewer and approval timestamps; - version
number and publication status; - optional tags for audience/context; -
adaptation rules, if any, approved independently of the lesson body.

## 9. Required review checklist

Before publication, reviewer confirms: - references exist and accurately
support the stated point; - quotation rights and translation/licence
requirements are met; - claims and interpretations are distinguished; -
disagreements are represented fairly where relevant; - language is
understandable and non-condemnatory; - examples are appropriate for the
intended audience; - safeguarding concerns are not mishandled; -
learning objectives and assessment align; - AI-generated contributions,
if any, have been checked against sources; - the content has a named
owner and correction path.

## 10. Framework acceptance criteria

-   Three pathways have distinct purpose statements and reviewed pilot
    lesson sets.
-   Every published lesson has objectives, references, rights metadata,
    review identity, and immutable version.
-   The UI never describes a completion percentage as spiritual
    maturity.
-   Inactivity never automatically assigns restoration or labels a
    participant.
-   Contested views can be represented with attribution without being
    mislabeled as shared core.
-   AI cannot publish content or alter protected states.
-   A participant can pause and resume any pathway without penalty.
-   The theological governance owner signs off the Core Biblical
    Principles and Content Standard before live pilot launch.
