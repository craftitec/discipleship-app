# DECISION_LOG.md --- Decisions, Assumptions and Open Questions

**Version:** 1.0 \| **Status:** Living document

## 1. Confirmed by product brief

  -----------------------------------------------------------------------
  ID                      Decision                Consequence
  ----------------------- ----------------------- -----------------------
  D-001                   Primary audience is a   Support individual
                          network of individuals  outreach creators and
                          and ministries.         ministry workspaces
                                                  with scoped
                                                  permissions.

  D-002                   Governance is           Establish shared
                          network-level.          content standards and
                                                  accountable review
                                                  before publication.

  D-003                   Offline requirement is  Downloaded lessons and
                          intermittent            activity capture must
                          connectivity.           work offline and sync
                                                  later.

  D-004                   AI scope includes       Keep AI optional and
                          adaptive assistance.    bounded; deterministic
                                                  rules remain
                                                  authoritative for core
                                                  progression.

  D-005                   Delivery platform is a  No native app-store
                          PWA.                    release is required for
                                                  pilot.

  D-006                   Target is a controlled  Prioritize safety,
                          pilot.                  complete workflows, and
                                                  validation over broad
                                                  feature breadth.

  D-007                   Theological approach is Harmonize shared core
                          broad Christian         principles; identify
                          foundations.            genuine differences
                                                  transparently.

  D-008                   Mentorship is optional. Participant can
                                                  progress without a
                                                  mentor; consent gates
                                                  mentor access.

  D-009                   Content comes primarily Source attribution and
                          from existing           rights/permission
                          materials.              metadata are required.

  D-010                   Users may begin or      No punitive inactivity
                          resume any pathway at   rules; no automatic
                          any time without        Restoration assignment.
                          judgment.               

  D-011                   Individuals or          Outreach/event
                          ministries can create   hierarchy and
                          outreaches with         permissions are core
                          events/sections.        MVP capabilities.

  D-012                   Participants can be     Enrolment and
                          enrolled into any of    attribution must
                          three pathways per      support multiple
                          event.                  pathways and sources.

  D-013                   Enrolments must be      Store stable
                          reportable by outreach, attribution relations
                          missioner, and          and immutable source
                          ministry.               snapshots.

  D-014                   Platform should be      Use plain language,
                          educational-level       accessible design,
                          agnostic.               short lessons, and
                                                  varied assessment
                                                  formats.
  -----------------------------------------------------------------------

## 2. Provisional technical decisions

These are recommended defaults for implementation; the technical owner
must approve them in Sprint 0.

  -----------------------------------------------------------------------
  ID                      Proposed default        Reason
  ----------------------- ----------------------- -----------------------
  A-001                   React + TypeScript +    Responsive browser
                          Vite PWA                delivery and strong
                                                  ecosystem for
                                                  service-worker tooling.

  A-002                   IndexedDB via Dexie.js  Durable structured
                                                  local data and explicit
                                                  schema migrations.

  A-003                   Workbox-managed service Maintainable app-shell
                          worker                  and content caching.

  A-004                   Django + Django REST    Fast API delivery,
                          Framework               strong admin tooling,
                                                  mature authorization
                                                  and data modelling.

  A-005                   PostgreSQL              Relational integrity,
                                                  constraints, and
                                                  reporting queries.

  A-006                   Modular monolith        Lower operational
                                                  complexity during
                                                  pilot.

  A-007                   Two-week sprints        Planning baseline;
                                                  re-estimate after
                                                  Sprint 0.

  A-008                   AI feature flag         Avoid unapproved
                          defaults off until      processing and ensure
                          provider/data review    core flows work
                          passes                  independently.

  A-009                   Published content       Preserve source/version
                          versions immutable      traceability for
                                                  learning and review.

  A-010                   Event UUID + server     Safe at-least-once
                          idempotency receipt for delivery with
                          sync                    exactly-once effect per
                                                  event identifier.
  -----------------------------------------------------------------------

## 3. Decisions required before affected implementation

  -----------------------------------------------------------------------------------
  ID                Question                    Owner               Required by
  ----------------- --------------------------- ------------------- -----------------
  O-001             Will the pilot include      Safeguarding        Before live
                    minors? If yes, what        owner + legal/data  enrolment
                    consent, guardian, vetting, owner               
                    and safeguarding process                        
                    applies?                                        

  O-002             What identity fields are    Product owner +     Sprint 0
                    mandatory: phone, email,    data owner          
                    name, or optional                               
                    alternatives?                                   

  O-003             Will enrolment be           Product owner       Sprint 0
                    worker-assisted,                                
                    self-enrolment link/code,                       
                    or both? What delivery                          
                    channel is available?                           

  O-004             How are duplicate people    Product/technical   Sprint 0
                    identified and merged       owner               
                    without disclosing existing                     
                    account status?                                 

  O-005             What exact core biblical    Theological         Before content
                    principles and standards    governance owner    publication
                    are affirmed, and who                           
                    resolves review                                 
                    disagreements?                                  

  O-006             Which Bible translations    Content owner       Before content
                    and existing content                            import
                    licences/permissions are                        
                    available?                                      

  O-007             Which AI provider/model is  Product +           Before AI
                    permitted, under what       theological + data  enablement
                    retention/data-processing   owner               
                    terms and budget cap?                           

  O-008             Are reflections local-only, Product +           Before reflection
                    private server-stored, or   safeguarding + data feature
                    explicitly shareable?       owner               

  O-009             What data                   Data owner/legal    Before live
                    retention/deletion schedule reviewer            enrolment
                    and jurisdiction-specific                       
                    privacy notices apply?                          

  O-010             What staffed                Safeguarding owner  Before reporting
                    safeguarding/reporting                          feature
                    process exists, and during                      
                    what hours?                                     

  O-011             What exact pilot cohort,    Product/technical   Sprint 0
                    target devices/browsers,    owner               
                    hosting budget, and launch                      
                    date apply?                                     

  O-012             What is the approved        Data owner          Before reports
                    threshold for suppressing                       are exposed to
                    small aggregate report                          ministries
                    cells?                                          

  O-013             Can local ministries add    Theological         Sprint 0
                    supplemental content, and   governance owner    
                    what review path is                             
                    mandatory?                                      

  O-014             Is direct messaging         Product +           Sprint 0
                    required? Default is        safeguarding owner  
                    excluded from pilot.                            

  O-015             What language/localization  Product/content     Sprint 0
                    and audio requirements are  owner               
                    in pilot?                                       

  O-016             Generational Lineage Tree:  Theological         Before Sprint 9
                    confirm consented links     governance +        
                    only, with zero progress    product owner       
                    scores or multiplier                            
                    rankings (Mediation 1)?                         

  O-017             Rule of Life Builder:       Discipleship        Before Sprint 8
                    ratify un-scored            framework owner     
                    contemplative rhythms                           
                    and reject numerical                            
                    quotas (Mediation 2)?                           

  O-018             Zero-Knowledge Encryption:  Security +          Before Sprint 8
                    approve client-side         safeguarding owner  
                    AES-256-GCM standard for                        
                    reflections (Mediation 4)?                      

  O-019             ChMS Webhooks: approve      Tech lead + data    Before Sprint 9
                    outgoing webhook filters    owner               
                    excluding reflection                            
                    payloads (GAP-06)?                              

  O-020             HIP Affinity Matching:      Safeguarding owner  Before Sprint 9
                    ratify double-blind                             
                    matching and strict minor                       
                    protections (Mediation 3)?                      
  -----------------------------------------------------------------------------------

## 4. Assumptions that must not be mistaken for facts

-   The initial pilot can be limited to adults until a
    minor-safeguarding model is approved.
-   The platform has one network-wide theological standard, with
    carefully governed local contextual additions.
-   Existing materials can be adapted legally; rights must be verified
    item by item.
-   A participant may be represented by one canonical account while
    holding multiple enrolments.
-   The pilot does not need a native mobile app, direct messaging,
    public forums, or live video.
-   AI personalization can be limited to approved-content retrieval and
    optional alternative explanations.
-   The pilot can start with a modest cohort and scale later based on
    measured usage.
-   Hosting/provider choices and exact dependency versions are not yet
    final.

## 5. Change control

For each change, record: - decision ID; - date and owner; - context and
alternatives considered; - decision and rationale; - documents/tasks
affected; - migration/backward-compatibility implications; - security,
safeguarding, theological, offline, and reporting implications; -
approval status.

A coding agent may propose a decision but may not approve changes to
theological standards, consent, safeguarding, privacy, data retention,
or product scope.
