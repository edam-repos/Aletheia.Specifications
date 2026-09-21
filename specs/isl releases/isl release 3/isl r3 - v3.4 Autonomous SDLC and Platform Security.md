# ALETHEIA Specification Language (ISL) v3.4

# Autonomous SDLC and Platform Security

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x, ISL v2.x, ISL v3.3
**Supersedes:** ISL r2 v3.7 The Autonomous Software Development Lifecycle Model, ISL r2 v3.10 The Platform Security Architecture
**Document Type:** Execution Layer Specification

---

## 1.0 Scope

This document defines the Autonomous Software Development Lifecycle (SDLC) Model and the Platform Security Architecture for the ALETHEIA platform. It specifies how autonomous construction activity is organized into a governed enterprise SDLC — lifecycle phases, phase gates, roles, evidence, quality controls, metrics, and audit — and how the platform is secured across trust zones, identity, authorization, isolation, secret management, context protection, injection defense, model/agent/tool security, supply chain, and incident response.

This document does not redefine the Autonomous Development Loop. ISL v3.3 defines the loop-level control cycle for generation, validation, repair, convergence, packaging, deployment preparation, and re-entry. This document defines the broader lifecycle framework that governs when loops begin, what phase they support, what evidence must be produced, who may approve transitions, how releases are controlled, how operations feed back into specifications, and how the platform's autonomous activity maps to enterprise SDLC obligations. Security content is consolidated here and presented once.

Shared conventions — normative language, field requirement levels, identifiers, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and are referenced rather than redefined.

---

## 2.0 Relationship to the Autonomous Development Loop

The Autonomous Development Loop (ISL v3.3) is the mechanism by which the platform performs construction. The Autonomous SDLC is the framework that determines when the loop may run, what lifecycle phase the loop supports, what evidence is required, what approvals are necessary, how outputs are accepted, and how operational feedback becomes future specification work.

| Concern | ISL v3.3 Autonomous Development Loop | ISL v3.4 Autonomous SDLC |
| ------- | ------------------------------------ | ------------------------ |
| Primary question | How does the platform iterate from specification to validated artifacts? | How does the enterprise govern autonomous development end to end? |
| Unit of control | Loop run | Lifecycle phase and phase gate |
| Main state object | loop-run-id | lifecycle-run-id |
| Core activities | interpret, plan, generate, validate, repair, converge | authorize, govern, evidence, review, release, operate, evolve |
| Primary controls | loop states, repair limits, convergence criteria | phase gates, role accountability, evidence packages, release criteria |
| Completion concept | loop success or failure | lifecycle phase transition or lifecycle closure |
| Human involvement | escalation and governed intervention | phase ownership, approval, review, audit, release accountability |

### 2.1 SDLC Rules

* An Autonomous SDLC phase MAY contain one or more Autonomous Development Loop runs.
* An Autonomous Development Loop run MUST be associated with an SDLC phase when executed inside a governed lifecycle.
* A loop run that produces release-impacting artifacts MUST produce evidence usable by the SDLC phase gate.
* A lifecycle phase MUST NOT transition solely because a loop run succeeded. The transition MUST also satisfy lifecycle evidence, governance, security, traceability, and release criteria.

---

## 3.0 Autonomous SDLC Design Principles

1. **Lifecycle Governance Is Mandatory** — Every enterprise use of autonomous construction MUST operate within a declared lifecycle model. The platform MUST know whether it is supporting discovery, specification, design, construction, verification, release preparation, deployment authorization, operations, maintenance, or retirement.
2. **Specifications Are Lifecycle Assets** — Specifications are not temporary prompts. They MUST be treated as lifecycle assets with ownership, versioning, readiness state, governance history, traceability links, and change control.
3. **Phase Gates Are Evidence-Based** — A lifecycle phase MUST NOT close unless required evidence exists. Evidence MAY include specifications, canonical models, readiness reports, construction plans, execution graphs, artifact metadata, validation results, security results, governance decisions, traceability snapshots, release records, operational telemetry, and audit records.
4. **Autonomy Is Risk-Tiered** — The degree of permitted autonomy MUST depend on risk tier, governance profile, organizational policy, environment, and system criticality. Standard systems MAY allow more automated progression. High and Critical systems MUST require stronger evidence, review, approval, and retention controls.
5. **Human Accountability Remains Explicit** — Autonomous construction MAY automate development activity, but it MUST NOT erase accountability. Lifecycle owners, reviewers, authorizing officials, governance administrators, security reviewers, and operations owners MUST remain explicit where required by policy.
6. **Operations Feed Back Into Specification** — Operational telemetry, incidents, defects, security findings, performance findings, and governance findings MUST be capable of creating controlled change triggers that re-enter the lifecycle through specification or maintenance phases.
7. **Release Readiness Is Not Deployment Authorization** — A system may be release-ready without being authorized for deployment. Release readiness confirms that artifacts, evidence, packaging, and validation are complete. Deployment authorization is a governance decision allowing deployment to a target environment.
8. **Reuse and Context Boundaries Are Lifecycle Controls** — Enterprise autonomous construction MUST treat reuse decisions, reusable asset provenance, Construction Boundaries, and Connection Contexts as lifecycle evidence. These controls reduce duplicated construction, constrain model context, and limit context drift across phases.

---

## 4.0 Lifecycle Phases

A conforming implementation MAY add phases, but it MUST preserve these standard phase meanings or define a documented equivalent.

| Phase | Name | Purpose |
| ----: | ---- | ------- |
| 0 | Lifecycle Intake | Register initiative, scope, ownership, risk tier, and lifecycle profile |
| 1 | Specification Development | Author and structure the ISL specification |
| 2 | Specification Readiness | Validate specification and approve readiness progression |
| 3 | Architecture and Planning | Normalize canonical model, discover reuse, and generate construction plan |
| 4 | Autonomous Construction | Reuse, generate, admit, validate, repair, and stabilize artifacts |
| 5 | Verification and Validation | Confirm functional, structural, security, policy, and quality evidence |
| 6 | Release Preparation | Package stable artifacts and prepare release evidence |
| 7 | Deployment Authorization | Approve or reject deployment to target environment |
| 8 | Operations and Monitoring | Observe deployed system and collect operational feedback |
| 9 | Maintenance and Evolution | Process changes, defects, enhancements, and re-entry |
| 10 | Retirement and Archival | Retire systems, preserve records, archive artifacts, and close lifecycle |

### 4.1 Phase Rules

* Each lifecycle phase MUST have an owner or responsible role.
* Each lifecycle phase MUST have entry criteria and exit criteria.
* Each lifecycle phase MUST produce or reference evidence.
* A phase MAY be skipped only when the lifecycle profile explicitly permits skipping and records the rationale.
* High and Critical risk systems MUST NOT skip Specification Readiness, Verification and Validation, Deployment Authorization, or Retirement and Archival.

---

## 5.0 Lifecycle Profile

A Lifecycle Profile defines which SDLC phases, gates, evidence, roles, and automation limits apply to a system.

### 5.1 Lifecycle Profile Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| lifecycle-profile-id | string | REQUIRED | Unique lifecycle profile identifier |
| profile-name | string | REQUIRED | Human-readable profile name |
| profile-version | semver | REQUIRED | Profile version |
| risk-tier | enum | REQUIRED | `low`, `standard`, `high`, `critical` |
| applicable-environments | array | REQUIRED | local, dev, test, staging, prod, regulated-prod |
| required-phases | array | REQUIRED | Lifecycle phases required |
| optional-phases | array | CONDITIONAL | Lifecycle phases optional |
| prohibited-skips | array | CONDITIONAL | Phases that may not be skipped |
| required-gates | array | REQUIRED | Phase gates required |
| required-roles | array | REQUIRED | Roles required by lifecycle |
| evidence-requirements | array | REQUIRED | Evidence requirements by phase |
| reuse-policy-id | string | CONDITIONAL | Reuse policy governing reuse discovery and exception handling |
| context-boundary-policy-id | string | CONDITIONAL | Policy governing Construction Boundaries and Connection Contexts |
| automation-level | enum | REQUIRED | assisted, supervised-autonomous, autonomous-with-gates, restricted |
| human-review-required | boolean | REQUIRED | Whether human review is mandatory |
| retention-profile-id | string | REQUIRED | Retention profile |
| governance-profile-id | string | REQUIRED | Governance profile |
| status | enum | REQUIRED | draft, active, superseded, revoked |
| approved-at | ISO 8601 | CONDITIONAL | Approval time |
| approved-by | string | CONDITIONAL | Approving authority |

### 5.2 Lifecycle Profile Rules

* Every autonomous SDLC run MUST reference a lifecycle-profile-id.
* A revoked lifecycle profile MUST NOT be used for new lifecycle runs.
* A lifecycle profile for High or Critical systems MUST require human review and deployment authorization.
* Lifecycle profile changes affecting active systems MUST trigger impact analysis.

---

## 6.0 Lifecycle Run Record

Each governed lifecycle execution MUST produce an Autonomous SDLC Run Record.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| lifecycle-run-id | string | REQUIRED | Unique lifecycle run identifier |
| lifecycle-profile-id | string | REQUIRED | Lifecycle profile used |
| specification-id | string | REQUIRED | Specification identifier |
| specification-version | semver | REQUIRED | Specification version |
| system-id | string | REQUIRED | System under lifecycle control |
| lifecycle-run-type | enum | REQUIRED | initial, change, maintenance, release, deployment, retirement |
| current-phase | enum | REQUIRED | Current lifecycle phase |
| current-phase-state | enum | REQUIRED | not-started, active, blocked, completed, skipped, escalated, failed |
| risk-tier | enum | REQUIRED | Risk tier |
| owner-role | string | REQUIRED | Primary lifecycle owner role |
| active-loop-run-ids | array | CONDITIONAL | Development loop runs associated |
| active-gate-ids | array | CONDITIONAL | Open phase gates |
| evidence-package-id | string | CONDITIONAL | Current lifecycle evidence package |
| started-at | ISO 8601 | REQUIRED | Lifecycle run start |
| completed-at | ISO 8601 | CONDITIONAL | Lifecycle run completion |
| final-outcome | enum | CONDITIONAL | succeeded, failed, cancelled, retired, superseded |

---

## 7.0 Phase Gate Model

Phase gates control movement between lifecycle phases.

### 7.1 Phase Gate Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| phase-gate-id | string | REQUIRED | Unique phase gate identifier |
| lifecycle-run-id | string | REQUIRED | Lifecycle run |
| from-phase | enum | REQUIRED | Phase being exited |
| to-phase | enum | REQUIRED | Phase being entered |
| gate-type | enum | REQUIRED | readiness, architecture, construction, validation, security, release, deployment, operations, retirement |
| required-evidence | array | REQUIRED | Evidence required |
| required-approver-role | string | CONDITIONAL | Approver role when approval required |
| gate-decision | enum | REQUIRED | pending, passed, failed, waived, escalated, blocked |
| decision-record-id | string | CONDITIONAL | Governance decision record |
| findings | array | CONDITIONAL | Gate findings |
| opened-at | ISO 8601 | REQUIRED | Gate opened |
| closed-at | ISO 8601 | CONDITIONAL | Gate closed |

### 7.2 Phase Gate Rules

* A lifecycle phase MUST NOT close until its required gate passes, is waived, or is explicitly skipped according to lifecycle profile.
* A failed phase gate MUST prevent transition to the next phase.
* A waived phase gate MUST reference a valid governance waiver.
* A deployment phase gate MUST require Authorizing Official approval for production and regulated production environments.
* A phase gate decision MUST be auditable.

---

## 8.0 Lifecycle Roles and Responsibilities

The Autonomous SDLC distinguishes platform roles from human accountability roles.

### 8.1 Human Lifecycle Roles

| Role | Responsibility |
| ---- | -------------- |
| Lifecycle Owner | Owns lifecycle execution and final business accountability |
| Specification Owner | Owns specification correctness and version control |
| Domain Expert | Validates domain meaning and business intent |
| Architecture Reviewer | Reviews architecture and planning evidence |
| Security Reviewer | Reviews security, privacy, and policy evidence |
| Quality Reviewer | Reviews validation and quality evidence |
| Operations Owner | Owns operational readiness and monitoring acceptance |
| Authorizing Official | Approves Autonomous-Ready readiness and deployment authorization |
| Governance Administrator | Manages lifecycle policies and governance configuration |
| Auditor | Reviews lifecycle evidence and audit records |

### 8.2 Platform Lifecycle Roles

| Platform Role | Responsibility |
| ------------- | -------------- |
| Specification Engine | Parses and validates authored specification |
| Semantic Model Engine | Produces canonical model |
| Planning Engine | Produces construction plan |
| Runtime Orchestrator | Executes construction tasks |
| Agent Orchestrator | Coordinates reasoning agents |
| Model Integration Layer | Routes model invocations |
| Tool Integration Layer | Performs deterministic validation |
| Artifact Repository | Stores and controls artifacts |
| Traceability Engine | Maintains lifecycle traceability |
| Governance Engine | Enforces policies and gates |
| Observability Layer | Emits telemetry and operational signals |
| State Manager | Maintains lifecycle and execution state |

### 8.3 Role Rules

* A lifecycle run MUST identify a Lifecycle Owner.
* A specification MUST identify a Specification Owner.
* High and Critical risk systems MUST require Security Reviewer and Authorizing Official roles.
* Separation-of-duties rules defined in governance policy MUST be enforced.
* Platform automation MUST NOT impersonate a human approval role.

---

## 9.0 Lifecycle Phase Requirements

### 9.1 Phase 0 — Lifecycle Intake

Registers the initiative and establishes the control context. Entry requires a new system, change, maintenance, release, or retirement request; a responsible owner; preliminary scope; and governance profile selection.

Required activities: create lifecycle-run-id; identify system-id; assign Lifecycle Owner and Specification Owner; select lifecycle profile; assign initial risk tier; identify target environments and governance profile; create initial lifecycle evidence package.

Exit requires: lifecycle run record exists; lifecycle profile is active; owner roles assigned; risk tier assigned; governance profile attached; initial phase gate passes or is not required by profile.

### 9.2 Phase 1 — Specification Development

Produces or updates the formal ISL specification. The output is a structured, versioned specification that can be parsed, normalized, validated, governed, and traced.

Required activities: author or update specification; assign specification-id and specification-version; define required canonical entities; define requirements, capabilities, services, data, interfaces, policies, validations, and infrastructure where applicable; classify non-functional requirements where applicable; record assumptions and constraints; run structural validation where supported; preserve specification version history.

Exit requires: specification source exists; required metadata and entity identifiers exist; specification version recorded; known gaps recorded; Specification Owner submits specification for readiness evaluation.

### 9.3 Phase 2 — Specification Readiness

Determines whether the specification may advance through readiness levels and eventually authorize autonomous construction. It uses the readiness levels defined in ISL v0.1 (`draft`, `reviewable`, `machine-valid`, `autonomous-ready`) and governance gates.

Required activities: parse specification; normalize into canonical model; validate canonical entities and relationships; evaluate readiness exit criteria; identify readiness findings; open required readiness gates; record approvals, rejections, waivers, or regressions.

Exit requires: readiness state recorded; canonical validation evidence exists; readiness transition record exists; required approval gates closed; Autonomous-Ready state exists if autonomous construction is requested.

Readiness rules: Autonomous construction MUST NOT begin unless the specification is Autonomous-Ready. A specification change after Autonomous-Ready MUST trigger readiness regression evaluation. A failed readiness gate MUST block construction admission.

### 9.4 Phase 3 — Architecture and Planning

Transforms the canonical model into a governed construction plan. Entry requires the specification at Machine-Valid or higher for planning, a canonical model, available planning agents or deterministic planners, and governance permission.

Required activities: analyze canonical entities and relationships; identify architectural components; generate construction task graph; identify task dependencies; perform Reuse Discovery for applicable artifact-producing scope; create Reuse Decision Records authorizing reuse, delta, wrapper, extension, composition, or new generation; define Construction Boundaries and required Connection Contexts; identify required agents and tools; define expected artifacts; define validation tasks; define security and policy validation tasks where required; seed traceability links; validate construction plan.

Exit requires: construction plan exists; task graph valid; required dependencies resolved; applicable Reuse Discovery complete; required Reuse Decision Records exist; Construction Boundaries and Connection Contexts valid for planned execution; required tool and agent capabilities available or exceptions recorded; planning validation passes; planning gate passes where required.

### 9.5 Phase 4 — Autonomous Construction

Executes the construction plan and produces controlled artifacts. Entry requires the specification Autonomous-Ready, an executable construction plan, passing execution admission, active runtime configuration, available artifact repository and reusable asset registry when reuse applies, applicable Reuse Decision Records, valid Construction Boundaries and Connection Contexts, passing governance checks, and available agents, models, and tools.

Required activities: initialize execution graph; schedule and execute construction tasks; invoke agents through Agent Orchestrator; route model calls through Model Integration Layer; reuse approved assets or generate only the authorized delta, wrapper, extension, composition, or new artifact candidates; admit artifacts into repository working state; invoke deterministic tools; record validation results; perform bounded repair cycles; update task, artifact, validation, repair, and execution state; maintain traceability links; emit telemetry.

Exit requires: required construction tasks completed, skipped, waived, or escalated according to policy; required artifacts exist in repository state with metadata; reuse-derived artifacts have reusable asset provenance metadata; no unresolved context drift findings remain for completed construction scope; validation failures repaired, waived, escalated, or terminally failed; execution graph complete for construction scope; construction evidence exists.

### 9.6 Phase 5 — Verification and Validation

Determines whether the constructed system satisfies specification, quality, security, policy, and repository integrity requirements. This is distinct from validation tasks executed during construction; lifecycle Verification and Validation is the phase gate that determines whether the system may move toward release preparation.

Required activities: run required build, test, static analysis, security, policy, repository, and traceability checks according to validation profile; normalize validation outcomes; classify findings; determine blocking and non-blocking findings; route failed findings to repair, waiver, escalation, or failure; produce verification summary and validation evidence package.

Exit requires: required validation checks passed or have valid waivers; no blocking validation finding remains unresolved; traceability integrity passes; repository integrity passes; security findings resolved, waived, or escalated according to governance; verification gate passes.

Verification rules: A system MUST NOT proceed to Release Preparation with unresolved blocking validation findings. A failed security validation MUST block release preparation unless a valid governance waiver exists. A missing traceability integrity report MUST block release preparation.

### 9.7 Phase 6 — Release Preparation

Packages stable artifacts and creates release evidence. It does not authorize deployment; it prepares release candidates that may later be submitted for deployment authorization.

Required activities: select stable artifacts for release candidate; generate package manifest; package artifacts; record exact artifact versions; run package validation; create release evidence package; identify waivers included in release; create release readiness record.

Exit requires: release candidate package exists; package manifest references exact artifact versions; package validation passes or valid waiver exists; release evidence package exists; release readiness gate passes.

Release rules: A release candidate MUST NOT include failed artifacts. A release candidate MUST NOT include untraced artifacts. A release candidate that includes waived findings MUST disclose those waivers in the release evidence package.

### 9.8 Phase 7 — Deployment Authorization

Determines whether a release candidate may be deployed to a target environment. It is a governance phase, intentionally separate from Release Preparation because a package may be technically ready but not authorized for a specific environment.

Required activities: evaluate release evidence; evaluate target environment profile; evaluate risk tier; evaluate security and policy findings; evaluate operational readiness; identify required approvals; record deployment authorization decision.

Exit requires: deployment authorization decision recorded; required approvals recorded; unresolved blocking findings absent or waived; authorization scope defined; target environment defined; authorization expiration defined where policy requires it.

Deployment authorization rules: Production deployment MUST require Authorizing Official approval. Regulated production deployment MUST require the lifecycle profile's required security, quality, operations, and governance approvals. Deployment authorization MUST be scoped to release candidate, environment, and time period. Expired deployment authorization MUST NOT permit deployment.

### 9.9 Phase 8 — Operations and Monitoring

Observes deployed systems and collects feedback. ALETHEIA uses observability, governance, and traceability to connect operational behavior back to specification and lifecycle state.

Required activities (SHOULD): collect operational telemetry; correlate telemetry with release, package, artifact, and specification version; monitor service health and quality indicators; record incidents, defects, or operational findings; detect policy or security events; create change triggers when required; preserve operational evidence.

Operations rules: Operational findings that affect specification intent MUST create or link to a change trigger. Security incidents MUST follow governance and security escalation rules. Operational telemetry MUST NOT replace audit records.

### 9.10 Phase 9 — Maintenance and Evolution

Processes change requests, defects, enhancements, reauthorization needs, and operational feedback. It is the SDLC frame for the change-driven re-entry behavior defined in ISL v3.3.

Required activities: classify change type; perform impact analysis; identify affected specifications, canonical entities, tasks, artifacts, packages, and deployments; update specification or create change proposal; determine whether readiness regression is required; determine whether reauthorization is required; initiate change-driven loop run when required.

Exit requires: change rejected, deferred, or accepted; accepted change has traceable lifecycle records; affected artifacts repaired, regenerated, revalidated, repackaged, or retired; required reauthorization completed; lifecycle evidence updated.

### 9.11 Phase 10 — Retirement and Archival

Closes the lifecycle of a system, release, package, or artifact set. Retirement must preserve auditability, traceability, retention obligations, and operational closure.

Required activities: mark affected system, release, package, and artifacts as retired, deprecated, archived, or superseded; preserve required artifact metadata; preserve traceability snapshots; preserve governance and audit records; archive validation and release evidence; close operational monitoring obligations where applicable; record retirement decision.

Exit requires: retirement decision recorded; artifact and package states updated; required evidence archived; retention policy attached; traceability remains queryable; governance closure record exists.

---

## 10.0 Lifecycle Evidence Package

The lifecycle evidence package aggregates phase-level evidence.

### 10.1 Lifecycle Evidence Package Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| lifecycle-evidence-package-id | string | REQUIRED | Unique evidence package identifier |
| lifecycle-run-id | string | REQUIRED | Lifecycle run |
| specification-id | string | REQUIRED | Specification identifier |
| specification-version | semver | REQUIRED | Specification version |
| lifecycle-profile-id | string | REQUIRED | Lifecycle profile |
| included-phases | array | REQUIRED | Phases represented |
| specification-evidence | array | CONDITIONAL | Specification evidence |
| readiness-evidence | array | CONDITIONAL | Readiness evidence |
| planning-evidence | array | CONDITIONAL | Planning evidence |
| reuse-evidence | array | CONDITIONAL | Reuse discovery, decision, and reusable asset evidence |
| boundary-context-evidence | array | CONDITIONAL | Construction Boundary, Connection Context, and drift evidence |
| construction-evidence | array | CONDITIONAL | Construction evidence |
| verification-evidence | array | CONDITIONAL | Verification evidence |
| release-evidence | array | CONDITIONAL | Release evidence |
| deployment-evidence | array | CONDITIONAL | Deployment authorization evidence |
| operations-evidence | array | CONDITIONAL | Operations evidence |
| maintenance-evidence | array | CONDITIONAL | Maintenance evidence |
| retirement-evidence | array | CONDITIONAL | Retirement evidence |
| traceability-snapshot-ids | array | REQUIRED | Traceability snapshots included |
| governance-record-ids | array | CONDITIONAL | Governance records included |
| created-at | ISO 8601 | REQUIRED | Package creation time |
| retention-class | enum | REQUIRED | operational, audit, archival |

### 10.2 Evidence Package Rules

* A lifecycle phase gate MUST reference the evidence package or phase-specific evidence records.
* A successful lifecycle run MUST have a lifecycle evidence package.
* High and Critical risk lifecycle evidence packages MUST be retained as audit or archival records.
* Evidence packages MUST remain queryable by specification-id, lifecycle-run-id, release candidate, and deployment authorization record.

---

## 11.0 SDLC Quality Model

The Autonomous SDLC MUST support quality evaluation across lifecycle phases.

### 11.1 Quality Categories

The platform SHOULD classify quality evidence using categories aligned with ISO/IEC 25010 concepts:

| Quality Category | Example Evidence |
| ---------------- | ---------------- |
| functional suitability | acceptance tests, behavior validation |
| performance efficiency | performance tests, resource telemetry |
| compatibility | integration tests, environment compatibility reports |
| usability | review findings, documentation review |
| reliability | resilience tests, recovery tests, failure analysis |
| security | vulnerability scans, policy checks, threat findings |
| maintainability | static analysis, modularity metrics, traceability completeness |
| portability | package validation, deployment environment compatibility |

### 11.2 Quality Evidence Rules

* A lifecycle profile SHOULD define required quality categories.
* High and Critical systems MUST define required security, reliability, and maintainability evidence.
* Quality findings MUST be classified as blocking, high, medium, low, or informational.
* Blocking quality findings MUST prevent release readiness unless waived.

---

## 12.0 SDLC Metrics

Lifecycle metrics allow organizations to assess autonomous development performance and control effectiveness.

### 12.1 Recommended Metrics

The platform SHOULD collect: lifecycle phase duration; phase gate pass/fail rate; readiness regression count; construction loop count per lifecycle run; validation failure rate; repair iteration count; repair convergence rate; waiver count by phase; governance approval wait time; release candidate rejection rate; deployment authorization rejection rate; operational defect rate by release; change-driven re-entry frequency; traceability completeness rate; evidence package completeness rate.

### 12.2 Metrics Rules

* SDLC metrics MUST preserve correlation to lifecycle-run-id.
* Metrics MUST NOT expose restricted artifacts, prompts, secrets, or personal data without authorization.
* Metrics used for governance decisions MUST be traceable to source records.

---

## 13.0 SDLC Traceability Requirements

The Autonomous SDLC MUST maintain traceability across lifecycle phases.

### 13.1 Required Lifecycle Traceability Links

The platform MUST record links between: lifecycle run and specification version; specification version and canonical model; canonical model and construction plan; construction plan and execution graph; execution graph and generated artifacts; artifacts and validation results; validation failures and repair records; stable artifacts and release candidate; release candidate and deployment authorization; deployed release and operational telemetry summary; operational finding and change trigger; change trigger and maintenance lifecycle run; retired artifact and archival record.

### 13.2 Traceability Rules

* Lifecycle phase gates MUST NOT pass if required lifecycle traceability is missing.
* Release preparation MUST include traceability from release candidate to specification version.
* Maintenance and Evolution MUST use traceability for impact analysis.
* Retirement MUST preserve traceability for audit and historical reconstruction.

---

## 14.0 SDLC Governance Requirements

### 14.1 Required Governance Controls

The Autonomous SDLC MUST support: lifecycle profile approval; risk-tier assignment; readiness approval; architecture or planning review where required; security review where required; waiver and override management; release readiness review where required; deployment authorization; post-change reauthorization; retirement approval where required.

### 14.2 Governance Rules

* Governance decisions MUST be recorded as audit records.
* Governance audit records MUST be immutable or correction-only.
* A blocked governance decision MUST prevent affected lifecycle progression.
* A waiver MUST be scoped to phase, finding, artifact, release, or deployment as applicable.
* A waiver MUST have expiration or review conditions when required by policy.

---

## 15.0 SDLC State Model

### 15.1 Lifecycle Phase State Values

| State | Meaning |
| ----- | ------- |
| not-started | Phase has not begun |
| active | Phase is in progress |
| blocked | Phase cannot proceed due to unresolved condition |
| completed | Phase exit criteria satisfied |
| skipped | Phase intentionally skipped under profile rules |
| escalated | Phase requires governance or human resolution |
| failed | Phase failed and cannot proceed without new action |

### 15.2 SDLC State Rules

* Each phase MUST maintain phase state.
* Phase state transitions MUST emit state events.
* A lifecycle run MUST NOT enter a later phase until prior required phase is completed, skipped, or waived according to profile.
* A failed phase MUST prevent lifecycle completion.

---

## 16.0 SDLC Error Classes

This section defines lifecycle-specific error classes under the `governance`, `validation`, `traceability`, and `runtime` categories of the common error model (ISL v0.1 §8).

| Error Class | Description | Default Handling |
| ----------- | ----------- | ---------------- |
| sdlc-lifecycle-profile-missing | No lifecycle profile assigned | block intake |
| sdlc-lifecycle-profile-invalid | Lifecycle profile invalid or revoked | block lifecycle run |
| sdlc-owner-missing | Required lifecycle owner missing | block phase |
| sdlc-risk-tier-missing | Risk tier not assigned | block intake |
| sdlc-phase-entry-failed | Phase entry criteria not satisfied | block phase |
| sdlc-phase-exit-failed | Phase exit criteria not satisfied | block transition |
| sdlc-phase-gate-failed | Phase gate failed | block transition |
| sdlc-evidence-missing | Required evidence missing | block gate |
| sdlc-readiness-not-authorized | Required readiness state absent | block construction |
| sdlc-planning-not-approved | Planning gate failed or missing | block construction |
| sdlc-validation-blocking-finding | Blocking validation finding unresolved | block release |
| sdlc-security-blocking-finding | Blocking security finding unresolved | block release or deployment |
| sdlc-release-readiness-failed | Release readiness criteria not satisfied | block deployment authorization |
| sdlc-deployment-authorization-missing | Deployment authorization absent | block deployment |
| sdlc-operational-feedback-untracked | Operational feedback not linked to change trigger | warn or escalate |
| sdlc-impact-analysis-missing | Required impact analysis missing | block maintenance progression |
| sdlc-retention-policy-missing | Retention policy missing for retirement | block retirement |
| sdlc-traceability-incomplete | Required lifecycle traceability missing | block gate |
| sdlc-governance-audit-write-failed | Governance audit record could not be written | halt governed action |

### 16.1 SDLC Error Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| sdlc-error-id | string | REQUIRED | Unique lifecycle error identifier |
| error-class | enum | REQUIRED | Error class |
| severity | enum | REQUIRED | blocking, high, medium, low |
| lifecycle-run-id | string | REQUIRED | Lifecycle run affected |
| phase | enum | CONDITIONAL | Phase affected |
| phase-gate-id | string | CONDITIONAL | Gate affected |
| specification-id | string | CONDITIONAL | Specification affected |
| release-candidate-id | string | CONDITIONAL | Release affected |
| deployment-authorization-id | string | CONDITIONAL | Deployment authorization affected |
| message | string | REQUIRED | Human-readable explanation |
| required-action | string | REQUIRED | Required remediation |
| detected-at | ISO 8601 | REQUIRED | Detection time |
| detected-by | string | REQUIRED | Detecting component or role |

---

## 17.0 SDLC Audit Requirements

The Autonomous SDLC MUST preserve audit evidence for lifecycle decisions.

### 17.1 Required Audit Records

The platform MUST preserve audit records for: lifecycle profile approval; lifecycle owner assignment; risk-tier assignment; readiness transition; phase gate decisions; approvals; waivers; overrides; security review decisions; release readiness decisions; deployment authorization decisions; post-change reauthorization; retirement decisions.

### 17.2 Audit Rules

* Audit records MUST include actor, role, timestamp, decision, scope, and rationale.
* Automated platform decisions MUST identify the platform component making the decision.
* Human approvals MUST identify authenticated human or authorized role identity.
* Audit records MUST be retained according to lifecycle profile and governance policy.

---

## 18.0 SDLC Testing Requirements

The lifecycle model itself MUST be testable.

### 18.1 Required Test Categories

| Test Category | Purpose |
| ------------- | ------- |
| lifecycle-profile | Validate lifecycle profile schema and rules |
| phase-entry | Validate phase entry criteria |
| phase-exit | Validate phase exit criteria |
| phase-gate | Validate gate pass, fail, waiver, escalation behavior |
| role-assignment | Validate required role assignment |
| evidence-package | Validate evidence completeness |
| readiness-control | Validate construction cannot begin before Autonomous-Ready |
| release-control | Validate release cannot proceed with failed evidence |
| deployment-authorization | Validate deployment cannot proceed without authorization |
| operations-feedback | Validate telemetry and incidents create change triggers |
| maintenance-reentry | Validate change triggers start governed re-entry |
| retirement | Validate archival and retention requirements |
| governance-audit | Validate audit records are created and immutable |

### 18.2 Testing Rules

* Lifecycle tests MUST verify that missing evidence blocks phase gates.
* Lifecycle tests MUST verify that governance blocks cannot be bypassed.
* Lifecycle tests MUST verify that release readiness does not imply deployment authorization.
* Lifecycle tests MUST verify that operational feedback can create maintenance re-entry.

---

## 19.0 SDLC Conformance Requirements

### 19.1 Lifecycle Profile Conformance

A lifecycle profile conforms to ISL v3.4 if it: declares required phases, gates, roles, risk tier, evidence requirements, reuse and context-boundary requirements when applicable, automation level, retention profile, and governance profile; and is versioned and approved.

### 19.2 Lifecycle Run Conformance

A lifecycle run conforms to ISL v3.4 if it: references a valid lifecycle profile; identifies system, specification, version, owner, and risk tier; tracks phase state; enforces phase gates; links loop runs to lifecycle phases; records reuse evidence when artifact-producing work is in scope; records Construction Boundary and Connection Context evidence when boundary-scoped work is in scope; records evidence packages; preserves lifecycle traceability; records governance decisions; produces lifecycle completion or transition records.

### 19.3 Phase Gate Conformance

A phase gate conforms to ISL v3.4 if it: identifies from-phase and to-phase; declares required evidence; declares reuse and boundary/context evidence where applicable; declares required approver role where applicable; records pass, fail, waiver, block, or escalation decision; references governance records where applicable; blocks transition when failed or pending.

### 19.4 Release Readiness Conformance

Release readiness conforms to ISL v3.4 if it: references stable artifact versions; references reusable asset provenance for reuse-derived artifacts; references validation evidence; references traceability snapshot; references package manifest; records unresolved findings and waivers; confirms release candidate completeness; does not imply deployment authorization.

### 19.5 Deployment Authorization Conformance

Deployment authorization conforms to ISL v3.4 if it: references release candidate; references target environment; references operational readiness evidence; records required approvals; records validity scope and expiration where applicable; is issued by required authority; blocks deployment when absent, expired, or rejected.

### 19.6 Platform Conformance

A platform conforms to ISL v3.4 if it: supports lifecycle profiles; supports lifecycle run records; supports lifecycle phases and gates; associates autonomous development loop runs with lifecycle phases; enforces reuse-first lifecycle controls where applicable; enforces Construction Boundary and Connection Context lifecycle controls; enforces readiness, validation, release, deployment, operations, maintenance, and retirement controls; preserves lifecycle evidence packages, traceability, and audit records; supports lifecycle metrics; prevents phase transitions without required evidence; prevents deployment without deployment authorization.

---

## 20.0 Security Design Principles

1. **Zero Trust for Inputs** — All externally supplied input MUST be treated as untrusted until validated. This includes specifications, uploaded files, prior artifacts, model responses, tool outputs, external API responses, repository content, telemetry payloads, and user instructions.
2. **Least Privilege** — Users, agents, services, tools, plugins, workers, and model providers MUST receive only the minimum access required to perform their authorized function.
3. **Role-Bounded Autonomy** — Autonomous agents MUST operate only within their declared role contracts. No agent MAY approve governance gates, mark validation as passed, promote artifacts, access secrets, or authorize deployment unless a separate specification explicitly grants that authority and governance permits it.
4. **Defense in Depth** — Security MUST be enforced at multiple layers: identity, authorization, context control, model protocol, tool sandboxing, repository controls, runtime gates, governance decisions, audit records, and telemetry monitoring.
5. **Deterministic Security Validation** — Where security can be checked deterministically, the platform MUST use deterministic tools or policy checks rather than relying only on model reasoning.
6. **Fail Closed** — Security control failure MUST default to blocking the affected action unless a governance-approved degraded-mode policy explicitly permits continuation.
7. **Auditability** — Security-relevant decisions MUST be recorded. The platform MUST preserve enough evidence to reconstruct who acted, what was evaluated, what was permitted, what was blocked, and why.

### 20.1 Architecture Rules

* Security controls MUST be enforced before actions occur, not merely reported afterward.
* Security checks that affect lifecycle progression MUST create structured records.
* Security findings MUST be linked to affected tasks, artifacts, agents, tools, models, contexts, or governance records.

---

## 21.0 Trust Zones

The platform MUST define trust zones to separate responsibilities and exposure.

| Trust Zone | Description |
| ---------- | ----------- |
| user-access-zone | User interfaces, APIs, CLIs, dashboards |
| control-plane-zone | Governance, scheduling, policy, configuration, orchestration |
| reasoning-zone | Agents, context packages, model requests, model responses |
| tool-execution-zone | Tool plugins, compilers, scanners, tests, generated-code execution |
| artifact-zone | Artifact content, metadata, packages, repository branches |
| state-zone | Runtime state, memory, checkpoints, recovery records |
| audit-zone | Governance audit records and immutable evidence |
| telemetry-zone | Logs, traces, metrics, alerts, dashboards |
| external-integration-zone | External model providers, tool services, repositories, identity providers |
| sandbox-zone | Isolated execution for generated code and untrusted tool activity |

### 21.1 Trust Zone Rules

* Data moving between trust zones MUST pass through an approved interface.
* External integrations MUST terminate in gateway components or approved adapters.
* Sandbox-zone outputs MUST be validated before entering artifact-zone or state-zone.
* Audit-zone records MUST be immutable or correction-only.

---

## 22.0 Identity Architecture

### 22.1 Identity Types

| Identity Type | Description |
| ------------- | ----------- |
| human-user | Human interacting with the platform |
| service-identity | Platform service identity |
| worker-identity | Runtime worker identity |
| agent-identity | Logical identity for an agent implementation |
| tool-plugin-identity | Logical identity for a tool plugin |
| automation-identity | CI/CD, deployment, or scheduled automation |
| external-provider-identity | External system, model provider, repository, or tool service |

### 22.2 Identity Rules

* Every security-relevant action MUST be attributable to an identity.
* Enterprise deployments MUST authenticate users through an approved identity provider.
* Service-to-service calls in enterprise and distributed deployments MUST use service identities.
* Disabled or revoked identities MUST NOT authorize platform actions.
* Human approval records MUST identify authenticated human or authorized role identity.
* Platform automation MUST NOT impersonate human approval roles.

---

## 23.0 Authorization Model

### 23.1 Authorization Dimensions

Authorization decisions MUST consider: identity; role; project scope; tenant scope; environment; risk tier; action type; target object; lifecycle phase; governance policy; sensitivity classification; approval or waiver state.

### 23.2 Standard Authorization Actions

| Action | Description |
| ------ | ----------- |
| specification-read | Read specification content |
| specification-write | Modify specification content |
| readiness-transition | Change readiness level |
| construction-start | Start autonomous construction |
| task-dispatch | Dispatch runtime task |
| agent-invoke | Invoke agent |
| model-invoke | Invoke model |
| tool-invoke | Invoke tool plugin |
| artifact-admit | Admit artifact candidate |
| artifact-promote | Promote artifact to stable |
| governance-approve | Approve governance gate |
| waiver-grant | Grant waiver |
| override-apply | Apply override |
| deployment-authorize | Authorize deployment |
| audit-read | Read audit records |
| configuration-change | Modify platform configuration |
| secret-access | Access secret reference |

### 23.3 Authorization Rules

* Authorization MUST be evaluated before governed actions.
* Authorization denial MUST block the action.
* High and Critical risk systems MUST enforce separation of duties for approval roles.
* A user or service MUST NOT gain access to another project or tenant unless cross-scope access is explicitly governed.

---

## 24.0 Project and Tenant Isolation

Shared deployments MUST prevent cross-project and cross-tenant contamination.

### 24.1 Isolation Scope

The platform MUST isolate: specifications; canonical models; construction plans; execution graphs; task queues; artifacts; traceability graphs; governance records; model context packages; tool workspaces; secrets; telemetry; evidence packages; deployment preparation outputs.

### 24.2 Isolation Rules

* A task from one project MUST NOT read or write another project's artifacts unless governed cross-project access is explicitly authorized.
* A tenant's context package MUST NOT include another tenant's data unless cross-tenant access is explicitly authorized.
* Telemetry dashboards MUST enforce project and tenant access controls.
* Shared workers MUST clean workspaces between tasks.
* Shared model and tool providers MUST enforce context and artifact isolation.

---

## 25.0 Data Classification

The platform MUST classify data to determine allowed handling, using the shared sensitivity classification enum from ISL v0.1 (`public`, `internal`, `confidential`, `restricted`).

| Classification | Description |
| -------------- | ----------- |
| public | Approved for public disclosure |
| internal | Internal platform or organization information |
| confidential | Sensitive business, architecture, artifact, or operational information |
| restricted | Highly sensitive information including secrets, regulated data, tenant-isolated data, or security-critical information |

### 25.1 Classification Rules

* Every context package MUST declare sensitivity classification.
* Every artifact MUST declare sensitivity classification.
* Every telemetry event MUST declare redaction status and retention class.
* Restricted data MUST NOT be sent to external model or tool providers unless governance explicitly permits it.
* Classification downgrade MUST require governance approval.

---

## 26.0 Secret Management

### 26.1 Secret Types

Secrets MAY include: model provider credentials; repository credentials; tool service credentials; database credentials; identity provider credentials; signing keys; encryption keys; deployment credentials; access tokens; private keys.

### 26.2 Secret Rules

* Secrets MUST NOT be embedded in specifications.
* Secrets MUST NOT be included in agent prompts or model context unless explicitly authorized by governance.
* Secrets MUST NOT be written to logs, telemetry, generated artifacts, test fixtures, or evidence bundles.
* Secrets MUST be referenced by secret identifiers rather than secret values.
* Secret access MUST be scoped by project, tenant, environment, and service identity.
* Secret exposure MUST create a critical security finding and halt or isolate affected execution.

---

## 27.0 Context Protection

Context packages are security-critical because they control what agents and models can see.

### 27.1 Context Control Checks

Before an agent or model receives context, the platform MUST check: context purpose; task relevance; source object identifiers; sensitivity classification; tenant and project scope; secret presence; untrusted content markers; allowed model or provider trust profile; allowed agent role; active Construction Boundary; valid Connection Contexts; reusable asset sensitivity and provenance; governance restrictions; context size and minimization; redaction requirements.

### 27.2 Context Protection Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| context-protection-record-id | string | REQUIRED | Unique context protection record |
| context-package-id | string | REQUIRED | Context package evaluated |
| agent-invocation-id | string | CONDITIONAL | Agent invocation |
| model-interaction-request-id | string | CONDITIONAL | Model request |
| task-id | string | REQUIRED | Task context |
| construction-boundary-id | string | CONDITIONAL | Active Construction Boundary checked |
| connection-context-ids | array | CONDITIONAL | Connection Contexts checked |
| reusable-asset-ids | array | CONDITIONAL | Reusable assets included in context |
| sensitivity-classification | enum | REQUIRED | public, internal, confidential, restricted |
| secret-scan-outcome | enum | REQUIRED | passed, failed, warning |
| scope-check-outcome | enum | REQUIRED | passed, failed, warning |
| injection-risk-outcome | enum | REQUIRED | passed, failed, warning |
| redactions-applied | array | CONDITIONAL | Redactions applied |
| protection-outcome | enum | REQUIRED | passed, blocked, redacted, escalated |
| evaluated-at | ISO 8601 | REQUIRED | Evaluation time |
| evaluated-by | string | REQUIRED | Context controller |

### 27.3 Context Protection Rules

* A context package containing unauthorized secrets MUST be blocked.
* A context package containing cross-tenant data without authorization MUST be blocked.
* A context package containing untrusted content MUST mark that content explicitly.
* Redaction that removes required task context MUST trigger rejection or escalation.
* A context package crossing a Construction Boundary without a valid Connection Context MUST be blocked.
* Reusable asset context MUST be checked for sensitivity, tenant scope, provenance, license, embedded secrets, and untrusted instruction content before inclusion in model or agent context.

---

## 28.0 Prompt and Context Injection Defense

Prompt injection and context injection occur when untrusted content attempts to override platform instructions, role boundaries, output contracts, governance controls, validation rules, or security policy.

### 28.1 Injection Classes

| Injection Class | Description |
| --------------- | ----------- |
| instruction-override | Attempts to override platform, role, or task instructions |
| role-escalation | Attempts to change agent role or authority |
| contract-bypass | Attempts to avoid output contract requirements |
| governance-bypass | Attempts to ignore approval, waiver, or policy requirements |
| validation-bypass | Attempts to mark work valid without deterministic validation |
| secret-exfiltration | Attempts to reveal or infer secrets |
| tool-abuse | Attempts to invoke unauthorized tools or commands |
| repository-abuse | Attempts to write outside approved artifact paths |
| data-exfiltration | Attempts to reveal restricted context or tenant data |
| persistence-abuse | Attempts to store malicious instructions in artifacts, memory, or metadata |

### 28.2 Sanitization Definition

For ISL, sanitization means applying deterministic transformations or markings to untrusted content before it is used in agent, model, tool, repository, or runtime contexts. Sanitization MAY include: separating untrusted content from platform instructions; escaping or quoting untrusted text; removing executable control sequences; redacting secrets; replacing restricted values with references; stripping unauthorized tool commands; removing unsupported file links or external references; marking content as untrusted; summarizing content when raw inclusion is unnecessary; blocking content that cannot be safely represented.

### 28.3 Injection Validation Rules

The platform MUST validate that: untrusted content is not placed in instruction sections; untrusted content cannot redefine agent role; untrusted content cannot change output contract; untrusted content cannot alter governance decision; untrusted content cannot declare validation success; untrusted content cannot request unauthorized tools; untrusted content cannot access secrets; untrusted content cannot write to stable artifact state; untrusted content cannot modify traceability or audit records; untrusted content cannot override system or platform constraints.

### 28.4 Injection Finding Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| injection-finding-id | string | REQUIRED | Unique injection finding |
| injection-class | enum | REQUIRED | Class from §28.1 |
| affected-object-id | string | REQUIRED | Context, artifact, specification, model request, or tool invocation affected |
| affected-object-type | enum | REQUIRED | specification, context-package, artifact, model-request, model-response, tool-output, telemetry, memory |
| severity | enum | REQUIRED | critical, high, medium, low |
| detection-method | enum | REQUIRED | pattern, schema, policy, model-assisted, human-review, deterministic-tool |
| evidence-reference | string | CONDITIONAL | Supporting evidence |
| handling | enum | REQUIRED | block, sanitize, redact, quarantine, escalate, warn |
| detected-at | ISO 8601 | REQUIRED | Detection time |
| detected-by | string | REQUIRED | Detector component |

### 28.5 Injection Handling Rules

* A critical injection finding MUST block the affected action.
* A secret-exfiltration finding MUST block the action and create a security incident record.
* A governance-bypass or validation-bypass finding MUST block downstream use.
* A sanitized context MUST retain a record of sanitization actions.
* An injection finding that cannot be classified MUST escalate.

---

## 29.0 Model Security

### 29.1 Model Security Rules

* Models MUST be invoked only through the Model Integration Layer.
* Model requests MUST reference approved context packages.
* Model providers MUST have trust profiles.
* External model providers MUST be governed before receiving confidential or restricted context.
* Raw model responses MUST be normalized and validated before use.
* Model outputs MUST NOT directly approve governance gates, pass validation, promote artifacts, or authorize deployment.
* Model output attempting to bypass controls MUST be rejected or escalated.

### 29.2 Model Trust Profile Requirements

A model trust profile MUST define: allowed risk tiers; allowed sensitivity levels; allowed environments; data retention constraints; provider location constraints; human review requirements; approval requirements; prohibited use cases.

---

## 30.0 Agent Security

### 30.1 Agent Security Rules

* Agents MUST be invoked only through the Agent Orchestrator or approved runtime path.
* Agents MUST operate under a role manifest.
* Agents MUST receive context only through controlled context packages.
* Agents MUST NOT directly access secrets.
* Agents MUST NOT directly mutate stable artifacts.
* Agents MUST NOT directly write governance audit records.
* Agents MUST NOT directly authorize deployment.
* Agent outputs MUST be validated before downstream use.
* Agent role violation MUST create a security finding.

---

## 31.0 Tool and Plugin Security

### 31.1 Tool Security Rules

* Tools MUST be registered before invocation.
* Tool plugins MUST declare capabilities, trust profiles, isolation profiles, and required permissions.
* Tool execution MUST use the least privileged isolation profile sufficient for the task.
* Tools executing generated code MUST run in sandboxed-generated-code or equivalent isolation.
* Tool outputs MUST be normalized before runtime decisions.
* Tool findings affecting validation, security, or release MUST be retained as evidence.
* A tool security boundary violation MUST halt or isolate affected execution.

---

## 32.0 Generated-Code Execution Security

Generated code is untrusted until validated.

### 32.1 Generated-Code Rules

* Generated code MUST NOT execute in the control-plane-zone.
* Generated code SHOULD execute only in sandbox-zone or tool-execution-zone.
* Generated code MUST NOT receive platform secrets by default.
* Generated code MUST NOT receive unrestricted network access by default.
* Generated code MUST NOT write to stable artifact repositories directly.
* Generated code execution MUST have time, filesystem, process, memory, and network limits.
* Generated-code execution failure MUST NOT compromise runtime state.

---

## 33.0 Artifact Repository Security

### 33.1 Artifact Integrity Rules

* Artifacts MUST have identifiers and metadata.
* Artifact content MUST be associated with exact artifact versions.
* Stable artifact promotion MUST require validation evidence, traceability, and governance eligibility.
* Reusable asset promotion MUST require security validation, provenance review, sensitivity classification, and governance eligibility before the asset may be reused by other construction scopes.
* Failed artifacts MUST NOT be promoted.
* Quarantined artifacts MUST NOT be consumed by downstream lifecycle stages.
* Artifact modification MUST require appropriate locks and authorization.
* Artifact integrity failure MUST block release preparation.

### 33.2 Artifact Security Metadata

Each artifact SHOULD include:

| Field | Required | Description |
| ----- | -------- | ----------- |
| artifact-id | YES | Unique artifact identifier |
| artifact-version | YES | Exact version |
| sensitivity-classification | YES | Data classification |
| provenance-reference | YES | Producing task or agent |
| validation-status | YES | Validation state |
| security-validation-status | CONDITIONAL | Security validation state |
| traceability-status | YES | Traceability completeness |
| integrity-hash | SHOULD | Integrity marker |
| quarantine-status | YES | Whether artifact is quarantined |
| waiver-references | CONDITIONAL | Waivers used |
| reusable-asset-id | CONDITIONAL | Reusable Asset identifier when promoted or reused |
| reuse-decision-id | CONDITIONAL | Reuse Decision authorizing asset use |
| asset-provenance-status | CONDITIONAL | complete, incomplete, inconsistent, waived |

---

## 34.0 Repository Path and Workspace Controls

### 34.1 Path Control Rules

* Artifact writes MUST occur only in approved repository workspaces.
* Path traversal patterns MUST be rejected.
* Generated artifacts MUST NOT overwrite platform source code unless operating in a governed platform-development context.
* Tool outputs MUST NOT write outside declared workspace.
* Package creation MUST use explicit artifact version references.
* Repository merge operations MUST be governed when they affect stable branches.

---

## 35.0 State, Memory, and Persistence Security

### 35.1 State Security Rules

* State transitions MUST occur through authorized state interfaces.
* Lifecycle-critical state MUST be durable.
* State records MUST include actor or component identity.
* State rollback MUST be governed and auditable.
* Memory used by agents MUST be scoped, versioned, and traceable.
* Hidden persistent memory that affects agent behavior MUST NOT be used.
* State inconsistency affecting security MUST trigger recovery or halt.

---

## 36.0 Governance Security

### 36.1 Governance Security Rules

* Governance policies MUST be versioned.
* Governance decisions MUST be attributable.
* Governance approvals MUST enforce separation of duties.
* Waivers MUST be scoped and time-bound where policy requires it.
* Overrides MUST require override authority.
* Expired waivers, approvals, and overrides MUST NOT authorize actions.
* Governance audit write failure MUST halt governed action.

---

## 37.0 Audit Security

### 37.1 Audit Record Requirements

Security-relevant audit records MUST include: actor identity; actor role; action; target object; decision; policy reference; timestamp; correlation-id; rationale or finding reference; approval, waiver, or override reference where applicable.

### 37.2 Audit Rules

* Audit records MUST be immutable or correction-only.
* Audit records MUST NOT be deleted by platform components.
* Audit records MUST be protected from unauthorized reading and modification.
* Audit records for High and Critical risk systems MUST be retained according to lifecycle retention profile.

---

## 38.0 Telemetry and Log Security

### 38.1 Telemetry Security Rules

* Telemetry MUST NOT contain secrets.
* Telemetry containing restricted information MUST be redacted, summarized, or access-controlled.
* Security events MUST include correlation identifiers.
* Critical security findings MUST generate alerts.
* Telemetry failure MUST NOT hide security failures.
* Telemetry export MUST obey classification and governance policy.

---

## 39.0 Network Security

### 39.1 Network Rules

* External model providers MUST be accessed only through the Model Integration Layer.
* External tool services MUST be accessed only through the Tool Integration Layer.
* Repository services MUST require authenticated access.
* Service-to-service calls SHOULD use encrypted channels in enterprise deployments.
* Restricted environments SHOULD use network segmentation.
* Generated code MUST NOT receive unrestricted network access unless explicitly governed.

---

## 40.0 Supply Chain Security

### 40.1 Supply Chain Controls

The platform SHOULD support: dependency scanning; plugin certification; model provider trust profiles; tool version recording; artifact integrity hashes; package manifests; SBOM generation where configured; template version recording; reusable asset provenance recording; reusable asset vulnerability and license scanning; provenance records; vulnerability findings; license findings.

### 40.2 Supply Chain Rules

* Tool and model versions used for construction MUST be recorded.
* Plugins used in lifecycle-critical validation SHOULD have certification evidence.
* Package manifests MUST reference exact artifact versions.
* Critical supply-chain findings MUST block release readiness unless waived.
* Reusable assets imported from external, third-party, or cross-tenant sources MUST be treated as supply-chain inputs and MUST be governed before use in lifecycle-critical construction.

---

## 41.0 Security Validation

### 41.1 Security Validation Categories

| Category | Description |
| -------- | ----------- |
| specification-security | Security requirements, policies, authentication, authorization definitions |
| context-security | Secret detection, injection detection, sensitivity validation |
| reuse-security | Reusable asset provenance, scope, license, vulnerability, and sensitivity validation |
| artifact-security | Static analysis, secret scanning, dependency audit, vulnerability scan |
| model-security | Provider trust, data retention, restricted context checks |
| tool-security | Plugin trust, isolation, permission review |
| repository-security | Path control, branch control, artifact integrity |
| deployment-security | Manifest validation, environment compatibility, secrets policy |
| governance-security | Approval, waiver, override, audit integrity |

### 41.2 Security Validation Rules

* Security validation MUST run before release readiness for High and Critical systems.
* A critical security finding MUST block artifact promotion, release readiness, or deployment authorization unless a valid waiver exists.
* Security validation evidence MUST be retained.
* Security validation failure MUST produce structured findings.

---

## 42.0 Security Finding Model

### 42.1 Security Finding Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| security-finding-id | string | REQUIRED | Unique finding identifier |
| finding-category | enum | REQUIRED | specification, context, prompt-injection, model, agent, tool, artifact, repository, deployment, governance, telemetry, network, supply-chain |
| severity | enum | REQUIRED | critical, high, medium, low, informational |
| affected-object-id | string | REQUIRED | Affected object |
| affected-object-type | enum | REQUIRED | specification, context-package, model-request, model-response, agent-output, tool-invocation, artifact, repository, package, deployment, governance-record |
| policy-reference | string | CONDITIONAL | Policy or control reference |
| evidence-reference | string | CONDITIONAL | Evidence |
| recommended-action | enum | REQUIRED | block, repair, redact, quarantine, revalidate, waive, escalate, monitor |
| blocks-progression | boolean | REQUIRED | Whether finding blocks lifecycle progression |
| detected-at | ISO 8601 | REQUIRED | Detection time |
| detected-by | string | REQUIRED | Detector |

### 42.2 Finding Rules

* Critical findings MUST block affected progression unless waived.
* Findings used for waiver decisions MUST be linked to waiver records.
* Findings triggering repair MUST be linked to repair records.
* Findings affecting release or deployment MUST be included in lifecycle evidence packages.

---

## 43.0 Quarantine Model

### 43.1 Quarantine Conditions

The platform MUST quarantine artifacts, contexts, outputs, or workspaces when: provenance is unclear; traceability is missing; security validation fails critically; prompt injection is detected; secret exposure is detected; generated code performs unauthorized behavior; repository integrity is uncertain; interrupted execution produced ambiguous side effects.

### 43.2 Quarantine Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| quarantine-record-id | string | REQUIRED | Unique quarantine record |
| quarantined-object-id | string | REQUIRED | Object quarantined |
| quarantined-object-type | enum | REQUIRED | artifact, context-package, model-response, tool-output, workspace, package |
| reason | string | REQUIRED | Quarantine reason |
| triggering-finding-id | string | CONDITIONAL | Finding that triggered quarantine |
| allowed-actions | array | REQUIRED | inspect, delete, repair, revalidate, release, archive |
| release-requires | enum | REQUIRED | governance-approval, security-review, revalidation, forbidden |
| quarantined-at | ISO 8601 | REQUIRED | Quarantine time |
| quarantined-by | string | REQUIRED | Component or actor |

### 43.3 Quarantine Rules

* Quarantined artifacts MUST NOT be promoted.
* Quarantined context packages MUST NOT be sent to models.
* Quarantined outputs MUST NOT be consumed downstream.
* Release from quarantine MUST be recorded and governed.

---

## 44.0 Incident Response

### 44.1 Security Incident Classes

| Incident Class | Description |
| -------------- | ----------- |
| secret-exposure | Secret appeared in context, logs, artifact, model output, or telemetry |
| unauthorized-access | Identity accessed unauthorized object |
| cross-tenant-exposure | Tenant boundary violation |
| prompt-injection-critical | Injection attempt affected or could affect control behavior |
| tool-sandbox-escape | Tool or generated code violated sandbox boundary |
| artifact-integrity-compromise | Artifact content or metadata integrity failed |
| governance-bypass-attempt | Attempt to bypass approval, waiver, or override |
| audit-integrity-failure | Audit write, retention, or immutability failure |
| supply-chain-critical | Critical dependency, plugin, or package compromise |
| deployment-security-block | Deployment preparation or authorization blocked by security condition |

### 44.2 Incident Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| security-incident-id | string | REQUIRED | Unique incident identifier |
| incident-class | enum | REQUIRED | Incident class |
| severity | enum | REQUIRED | critical, high, medium, low |
| affected-project-id | string | CONDITIONAL | Project affected |
| affected-tenant-id | string | CONDITIONAL | Tenant affected |
| affected-object-ids | array | REQUIRED | Objects affected |
| triggering-finding-ids | array | CONDITIONAL | Findings that triggered incident |
| containment-action | enum | REQUIRED | halt, isolate, quarantine, revoke, rotate-secrets, block, monitor |
| status | enum | REQUIRED | opened, contained, investigating, resolved, closed |
| opened-at | ISO 8601 | REQUIRED | Open time |
| resolved-at | ISO 8601 | CONDITIONAL | Resolution time |
| owner-role | string | REQUIRED | Responsible role |

### 44.3 Incident Response Rules

* Critical incidents MUST halt or isolate affected execution.
* Secret exposure incidents MUST trigger secret rotation or governance-approved compensating action.
* Cross-tenant exposure MUST escalate.
* Audit integrity failure MUST halt governed actions.
* Incident closure MUST include resolution evidence.

---

## 45.0 Security Monitoring and Alerting

### 45.1 Required Security Alerts

The platform SHOULD alert on: critical security finding; secret exposure; prompt injection critical finding; governance bypass attempt; unauthorized access attempt; tool sandbox violation; generated-code network violation; cross-tenant access attempt; artifact integrity failure; audit write failure; repeated model output contract failures; repeated tool security failures; deployment authorization security block.

### 45.2 Alert Rules

* Critical alerts MUST include affected object identifiers.
* Security alerts MUST include correlation identifiers.
* Security alert suppression MUST NOT hide unresolved critical incidents.
* Alerts MUST be routed according to governance and operations policy.

---

## 46.0 Vulnerability Management

### 46.1 Vulnerability Sources

Vulnerabilities MAY be detected in: platform source code; generated artifacts; dependencies; tool plugins; model providers; deployment manifests; infrastructure definitions; container images; configuration templates; secrets management; telemetry exports.

### 46.2 Vulnerability Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| vulnerability-record-id | string | REQUIRED | Unique vulnerability record |
| affected-object-id | string | REQUIRED | Object affected |
| affected-object-type | enum | REQUIRED | platform, artifact, dependency, plugin, model-provider, container, deployment, configuration |
| severity | enum | REQUIRED | critical, high, medium, low |
| source | string | REQUIRED | Scanner, tool, advisory, reviewer, incident |
| finding-reference | string | CONDITIONAL | Finding reference |
| remediation-status | enum | REQUIRED | open, accepted, repaired, waived, false-positive, closed |
| required-action | string | REQUIRED | Remediation |
| detected-at | ISO 8601 | REQUIRED | Detection time |
| due-at | ISO 8601 | CONDITIONAL | Remediation deadline |

### 46.3 Vulnerability Rules

* Critical vulnerabilities affecting release artifacts MUST block release readiness unless waived.
* Critical vulnerabilities affecting platform control plane MUST trigger incident response.
* Waived vulnerabilities MUST reference governance waiver records.
* Remediation MUST trigger revalidation where artifacts or platform behavior changed.

---

## 47.0 Security Configuration

### 47.1 Security Configuration Domains

| Domain | Examples |
| ------ | -------- |
| identity | identity providers, MFA, service identities |
| authorization | roles, permissions, policy bindings |
| context-protection | redaction rules, injection detection rules |
| model-security | trust profiles, provider allow lists |
| tool-security | plugin trust, isolation profiles |
| repository-security | branch protection, path restrictions |
| secret-management | secret stores, rotation rules |
| telemetry-security | redaction, export restrictions |
| incident-response | alert routing, containment policies |

### 47.2 Configuration Rules

* Security configuration MUST be versioned.
* Security configuration changes MUST be auditable.
* Security configuration changes affecting active execution MUST trigger impact evaluation.
* Revoked security configuration MUST NOT be used for new execution.

---

## 48.0 Security Error Classes

This section defines platform-security-specific error classes under the `security` category of the common error model (ISL v0.1 §8).

| Error Class | Description | Default Handling |
| ----------- | ----------- | ---------------- |
| security-authentication-failed | Identity could not authenticate | block |
| security-authorization-denied | Identity lacks permission | block |
| security-sod-violation | Separation of duties violation | block |
| security-context-secret-detected | Secret detected in context | block and incident |
| security-context-scope-violation | Context crosses project or tenant scope | block |
| security-connection-context-invalid | Connection Context is absent, expired, superseded, or incompatible | block |
| security-construction-boundary-drift | Task, model output, or context exceeds active Construction Boundary | block and escalate |
| security-prompt-injection-detected | Prompt or context injection detected | block or sanitize |
| security-model-trust-violation | Model trust profile disallows use | block |
| security-tool-trust-violation | Tool trust profile disallows use | block |
| security-tool-sandbox-violation | Tool violated sandbox controls | halt and incident |
| security-generated-code-violation | Generated code violated execution policy | halt or quarantine |
| security-artifact-integrity-failed | Artifact integrity check failed | quarantine |
| security-reusable-asset-untrusted | Reusable asset lacks required trust, provenance, or validation | block reuse |
| security-cross-scope-reuse-denied | Reuse across project, tenant, environment, or classification scope is unauthorized | block |
| security-repository-path-violation | Unauthorized path access or traversal | block |
| security-secret-exposure | Secret exposed in output, artifact, log, or telemetry | halt and incident |
| security-audit-write-failed | Security or governance audit write failed | halt governed action |
| security-telemetry-redaction-failed | Sensitive telemetry not redacted | block export |
| security-cross-tenant-exposure | Tenant boundary violated | halt and incident |
| security-governance-bypass-attempt | Attempt to bypass governance | block and escalate |
| security-deployment-blocked | Deployment blocked by security condition | block deployment |
| security-vulnerability-critical | Critical vulnerability detected | block affected progression |
| security-quarantine-release-denied | Quarantine release not authorized | block |

### 48.1 Security Error Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| security-error-id | string | REQUIRED | Unique security error identifier |
| error-class | enum | REQUIRED | Error class |
| severity | enum | REQUIRED | critical, high, medium, low |
| affected-object-id | string | CONDITIONAL | Object affected |
| affected-object-type | enum | CONDITIONAL | context, artifact, model, tool, repository, governance, telemetry, deployment |
| task-id | string | CONDITIONAL | Task affected |
| execution-graph-id | string | CONDITIONAL | Execution affected |
| lifecycle-run-id | string | CONDITIONAL | Lifecycle affected |
| message | string | REQUIRED | Human-readable explanation |
| default-handling | enum | REQUIRED | block, halt, quarantine, sanitize, redact, escalate, incident |
| required-action | string | REQUIRED | Required remediation |
| detected-at | ISO 8601 | REQUIRED | Detection time |
| detected-by | string | REQUIRED | Detector component |

---

## 49.0 Security Testing

### 49.1 Required Test Categories

| Test Category | Purpose |
| ------------- | ------- |
| authentication | Verify identity controls |
| authorization | Verify role, scope, and permission checks |
| separation-of-duties | Verify approval role separation |
| context-protection | Verify context minimization, redaction, scope checks |
| prompt-injection | Verify injection detection and handling |
| secret-detection | Verify secrets are blocked from prompts, logs, artifacts, telemetry |
| model-security | Verify model trust profile enforcement |
| tool-security | Verify plugin trust and isolation |
| sandbox | Verify generated-code and tool execution boundaries |
| artifact-integrity | Verify artifact hashes, metadata, promotion rules |
| repository-path | Verify path traversal and workspace restrictions |
| telemetry-redaction | Verify telemetry redaction and export controls |
| audit-integrity | Verify immutable or correction-only audit records |
| incident-response | Verify containment, escalation, and closure records |
| vulnerability-management | Verify findings block or route correctly |

### 49.2 Testing Rules

* Prompt injection tests MUST include attempts to override role, output contract, validation, governance, and secret-handling instructions.
* Secret detection tests MUST verify prompts, context packages, model responses, tool outputs, logs, telemetry, artifacts, and evidence bundles.
* Sandbox tests MUST verify filesystem, process, network, memory, and timeout restrictions.
* High and Critical deployments MUST run security tests before production authorization.

---

## 50.0 Security Conformance Requirements

### 50.1 Identity and Authorization Conformance

A platform conforms to identity and authorization requirements if it: authenticates users and service identities; enforces role, project, tenant, environment, action, and risk-tier authorization; records actor identity for security-relevant actions; enforces separation of duties where required; blocks disabled or revoked identities.

### 50.2 Context and Prompt Security Conformance

A platform conforms to context and prompt security requirements if it: classifies context packages; scans for secrets; enforces project and tenant scope; enforces Construction Boundary and Connection Context scope; checks reusable asset context before inclusion; marks untrusted content; separates untrusted content from platform instructions; detects prompt and context injection classes; blocks or sanitizes unsafe context; records context protection and injection findings.

### 50.3 Model Security Conformance

A platform conforms to model security requirements if it: invokes models only through the Model Integration Layer; enforces model trust profiles; blocks unauthorized restricted context transmission; normalizes and validates model outputs; prevents model outputs from bypassing governance, validation, traceability, artifact promotion, or deployment authorization.

### 50.4 Tool Security Conformance

A platform conforms to tool security requirements if it: invokes only registered tools; enforces tool trust and isolation profiles; sandboxes generated-code execution; records tool security findings; blocks tool sandbox violations; prevents tool outputs from bypassing normalization and validation.

### 50.5 Artifact and Repository Security Conformance

A platform conforms to artifact and repository security requirements if it: controls artifact admission; enforces metadata and versioning; prevents failed or quarantined artifacts from promotion; enforces path controls; records integrity evidence; links artifacts to validation and traceability records; blocks untrusted, unvalidated, or unauthorized reusable assets from promotion or reuse.

### 50.6 Governance and Audit Security Conformance

A platform conforms to governance and audit security requirements if it: records governance decisions; preserves immutable or correction-only audit records; blocks governed actions on audit write failure; enforces waiver and override rules; records security-relevant approvals, waivers, overrides, and incidents.

### 50.7 Platform Security Conformance

A platform conforms to ISL v3.4 if it: defines trust zones; enforces identity and authorization; protects secrets; protects context packages; protects reusable assets and cross-boundary Connection Contexts; defends against prompt and context injection; secures model interactions; secures tool and plugin execution; sandboxes generated code; protects artifacts and repositories; enforces governance security; preserves audit evidence; protects telemetry; supports vulnerability management; supports incident response; tests security controls; fails closed when security controls cannot be enforced.

---

## 51.0 Conformance to This Document

A conforming implementation MUST satisfy the SDLC conformance requirements (§19) and the security conformance requirements (§50), and MUST enforce the cross-cutting invariants defined in ISL v0.1 §11. Autonomous construction may be fast and generative, but it MUST remain identity-bound, role-bounded, context-controlled, least-privileged, sandboxed, traceable, governed, observable, auditable, and fail-closed.
