# ALETHEIA Specification Language (ISL) v1.3
# Readiness and Governance Model

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0, ISL v1.1
**Supersedes:** ISL r2 v1.3 Specification Readiness Levels, ISL r2 v1.7 The Governance and Control Model
**Document Type:** Readiness and Governance Model Specification

---

## 1.0 Scope

This document consolidates the Specification Readiness Model and the Governance and Control Model for the ALETHEIA Specification Language. Readiness and governance are one integrated lifecycle: readiness defines the maturity and authorization state of an ISL specification, and governance defines the roles, policies, approvals, risk tiers, waivers, overrides, escalations, deployment authorization, and audit controls that regulate progression through that lifecycle.

The readiness model establishes whether a specification may remain in authoring, proceed to review, undergo machine validation, or authorize autonomous construction. The governance model establishes who may review, approve, authorize, override, administer, and audit every governance-controlled lifecycle point, from authoring through readiness progression, planning, execution, deterministic validation, repair, consolidation, deployment preparation, post-change reauthorization, and audit.

Shared conventions — the shared enums including Risk Tier (§6.2), Validation Outcome (§6.5), Governance Decision (§6.6), Readiness Level (§6.1), the common Error Record (§8), the common Telemetry Event (§9), the design principles (§7), and the conformance framework (§10) — are defined in ISL v0.1 and are referenced rather than redefined here. In particular, this document uses the shared Risk Tier enum (`low`, `standard`, `high`, `critical`) and the shared Governance Decision enum (`allow`, `warn`, `block`, `escalate`, `approval-required`, `waiver-required`, `override-required`).

Because risk tiers, waivers, overrides, approval gates, and reauthorization are shared between readiness and governance, they are defined once in this document and applied at every lifecycle point.

---

## 2.0 Lifecycle Principles

### 2.1 Readiness Is a Gate

Readiness levels MUST control platform behavior, not serve as descriptive labels. A platform MUST enforce readiness restrictions when authoring, validating, planning, executing, or deploying specification-derived systems.

### 2.2 Criteria Must Be Verifiable

Every readiness transition MUST be based on explicit criteria verifiable through automated checks, governance records, or documented human review. A criterion that cannot be verified MUST NOT be used as a mandatory gate unless converted into a structured review requirement.

### 2.3 Higher Requires Lower; Progression Is Ordered

A specification MUST satisfy all lower-level criteria before advancing and MUST NOT skip readiness levels. Standard forward progression is Draft → Reviewable → Machine-Valid → Autonomous-Ready.

### 2.4 Readiness Is Version-Specific

Readiness applies to a specific specification version. Every readiness record MUST identify the specification version to which it applies. A readiness decision for a prior version MUST remain auditable and MUST NOT be overwritten.

### 2.5 Governance Is Continuous and Enforceable

Governance MUST operate throughout the lifecycle, not only at readiness transitions or deployment approval. Governance rules MUST produce runtime behavior; a violation, missing approval, expired waiver, or separation-of-duties violation MUST cause a defined response. Governance requirements that do not produce enforceable platform behavior MUST be treated as incomplete.

### 2.6 Governance Decisions Are Traceable and Immutable

Every approval, rejection, waiver, override, escalation, policy evaluation, and deployment authorization MUST be traceable to the affected specification version, task, artifact, validation result, policy, or execution event. Governance records affecting readiness, execution, waiver, override, or deployment authorization MUST be immutable after creation; corrections MUST be appended as new records referencing the original.

### 2.7 Risk Determines Control Strength; Human Authority Remains Explicit

Higher-risk systems MUST require stronger governance controls. Risk tier MUST influence approval requirements, evidence retention, validation depth, tool restrictions, repair permissions, waiver authority, and deployment authorization. Autonomous construction MAY perform many engineering tasks, but where human approval is required the platform MUST pause until an authorized governance role acts.

---

## 3.0 Governance Roles and Separation of Duties

### 3.1 Standard Governance Roles

| Role | Responsibility | Approval Authority |
| --- | --- | --- |
| Reviewer | Reviews specification completeness and coherence | Reviewable → Machine-Valid |
| Security Reviewer | Reviews security policy coverage, data protection, security risk | Machine-Valid security sign-off |
| Architecture Reviewer | Reviews architecture, boundaries, interfaces, data model, operational feasibility | Machine-Valid architecture sign-off |
| Authorizing Official | Authorizes autonomous construction and deployment progression | Machine-Valid → Autonomous-Ready, deployment authorization |
| Governance Administrator | Maintains governance configuration, role mapping, policy profiles, risk tier rules | Governance configuration |
| Override Authority | Approves manual overrides of failed checks or blocked criteria | Override approval |
| Auditor | Reviews governance records, traceability, evidence, compliance history | Audit access, no construction authority |
| Operations Approver | Approves operational readiness or environment-specific deployment where required | Deployment or operational approval |
| Data Steward | Reviews data classification, retention, privacy, data handling rules | Data governance approval where required |

### 3.2 Role Assignment

Every role assignment MUST be recorded (assignment identifier, role, subject identifier, scope of `global`, `project`, `specification`, `environment`, or `policy`, specification identifier when scoped, effective dates, assignor, and status). A role MUST NOT be exercised without an active assignment. Expired or revoked assignments MUST NOT authorize governance actions. Assignments affecting readiness, authorization, override, or deployment MUST be auditable.

### 3.3 Separation of Duties

For the same specification version: the Authorizing Official MUST be distinct from the Reviewer; MUST be distinct from the Security Reviewer; and SHOULD be distinct from the Architecture Reviewer. The Override Authority MUST be distinct from the requester. The Governance Administrator SHOULD NOT approve their own configuration change. The Auditor MUST NOT approve readiness transitions or deployment authorization.

The Governance Engine MUST validate separation-of-duties rules before a transition to Autonomous-Ready, before an override is applied, and before deployment authorization. A blocking separation violation MUST prevent the associated governance action and MUST NOT be bypassed except by an approved policy that explicitly permits the exception for the applicable risk tier. Each violation MUST be recorded with the conflicting subject and roles, the affected action, severity, and required remediation.

---

## 4.0 Readiness Levels

### 4.1 Overview

| Level | Name | Meaning | Autonomous Construction Permitted |
| --- | --- | --- | --- |
| 0 | Draft | Incomplete or actively authored | NO |
| 1 | Reviewable | Structurally coherent, ready for human review | NO |
| 2 | Machine-Valid | Structurally and semantically valid | NO |
| 3 | Autonomous-Ready | Validated, approved, and authorized | YES |

The readiness level MUST be recorded in the Project entity's `readiness-level` field. A specification version MUST have exactly one current readiness level; Machine-Valid MUST be preserved as a satisfied historical transition when the current state is Autonomous-Ready.

### 4.2 State Rules

Standard forward progression MUST follow Draft → Reviewable → Machine-Valid → Autonomous-Ready; no other forward path is permitted. Regression MAY move a specification backward by one or more levels and MUST be recorded as a readiness transition. The platform MUST NOT initiate construction planning or execution unless the required readiness level is reached, and autonomous artifact generation MUST NOT begin unless the specification is Autonomous-Ready.

### 4.3 Level 0 — Draft

At Draft readiness the platform MAY provide authoring assistance, detect missing sections, identify ambiguous language, recommend identifiers, run advisory structural checks, and generate preliminary quality warnings. The platform MUST NOT initiate construction planning, generate implementation artifacts, mark canonical entities construction-ready, authorize autonomous execution, or treat provisional identifiers as stable traceability identifiers. A document that lacks enough structure to identify the system MUST NOT be treated as an ISL Draft specification.

A specification advances from Draft to Reviewable only when it satisfies all Draft exit criteria: the System Identity and Context section is present with all required fields populated; the specification version is valid semantic version; the ISL version is supported; at least one measurable business objective, one explicit scope exclusion, and one actor or stakeholder are defined; at least one functional requirement is defined; and a Change History section exists.

### 4.4 Level 1 — Reviewable

Reviewable is the enterprise control point where human judgment is applied before deeper semantic validation. The platform MAY perform structural validation, preliminary canonical mapping, generate review reports, and route the specification for human review. The platform MUST NOT initiate autonomous construction, execute artifact generation, treat preliminary canonical output as authoritative, or bypass required reviewer sign-off.

A specification advances from Reviewable to Machine-Valid only when all required ISL v1.0 sections and fields are present; functional requirements carry identifiers, priority, source, and validation refs and use active voice without prohibited ambiguous terms; non-functional requirements have measurable targets where measurable and are classified by quality characteristic; actors are typed and define permissions; stakeholders with approval authority map to governance roles; data entities and relationships define attributes, types, nullability, constraints, and cardinality; services define single-responsibility statements; interfaces define contract and authentication; workflows define steps and exception paths where required; policies are verifiable rules; acceptance criteria link to specification identifiers; change history is complete; and Reviewer sign-off is recorded.

At least one designated Reviewer MUST approve the Reviewable → Machine-Valid transition, confirming coherence, understandable objectives, reviewable requirements, explicit scope, no obvious domain contradictions, and readiness for formal machine validation. Reviewer sign-off does not replace automated validation. Blocking review findings MUST be resolved or formally waived before Machine-Valid.

### 4.5 Level 2 — Machine-Valid

Machine-Valid means the specification passed structural and semantic validation and is normalized into the canonical semantic model. It does not authorize autonomous construction. The platform MAY generate or update a construction plan, perform impact analysis, evaluate traceability coverage, prepare governance review and construction authorization materials, and validate tool capability requirements. The platform MUST NOT begin artifact generation, initiate autonomous execution, treat construction authorization as implied, or bypass final approval gates.

A specification MUST NOT be marked Machine-Valid unless Reviewable exit criteria are satisfied, canonical normalization completed with no blocking errors, reviewer sign-off is recorded, and structural validation produced no blocking errors. Exit to Autonomous-Ready requires that: all entity identifiers conform to the identifier format; all relationship references resolve; every non-Project entity is reachable from Project; all must-have functional requirements have linked Validation entities and trace to a Service or Capability; non-functional requirements have validation or reviewable compliance methods; DataEntity attributes define type and nullability; sensitive or regulated data entities are governed by Policy entities; workflows define exception paths and terminal states; interfaces define contract, authentication, and versioning strategy; infrastructure entities define runtime platform, deployment model, resource requirements, and scaling model; policies define verifiable rules and enforcement points; Security and Architecture Reviewer sign-offs are recorded; a risk tier is assigned; required deterministic tool capabilities are identified; and no open blocking review findings remain.

Machine-Valid status MUST be supported by the structural, canonical validation, reference resolution, validation coverage, policy coverage, and traceability coverage reports plus reviewer sign-off records.

### 4.6 Level 3 — Autonomous-Ready

Autonomous-Ready is the only readiness level at which full autonomous construction may begin. It means the specification satisfies all required authoring, structural, semantic, validation, traceability, policy, risk, and governance criteria and the organization has accepted the risk of autonomous construction. The platform MAY initiate or confirm planning, begin execution, generate artifacts, run deterministic validation, enter bounded repair cycles, perform policy validation, and prepare deployment artifacts, while MUST enforcing execution preconditions, traceability, governance checkpoints, repair termination rules, and execution recording.

A specification MUST NOT be marked Autonomous-Ready unless all Machine-Valid exit criteria are satisfied and: a construction authorization is recorded by the Authorizing Official; the risk tier is approved for autonomous construction; required Tool Records exist for planned deterministic validation capabilities; a construction plan exists or can be generated; Reuse policy and registry access are configured when reuse applies; Construction Boundary and Connection Context controls are configured when boundary-scoped construction applies; no unresolved interpretation ambiguities are recorded; all blocking policies have validation or enforcement mechanisms; required waivers, if any, are approved and unexpired; separation-of-duties rules are satisfied; execution runtime configuration is compatible with the risk tier; an artifact repository target is configured; and the traceability graph is initialized.

Autonomous construction is permitted only for the exact specification version marked Autonomous-Ready. A later modification MUST NOT inherit Autonomous-Ready status unless the change is classified non-impacting and governance rules allow preservation.

---

## 5.0 Policy Governance

### 5.1 Policy Sources and Targets

Governance policies MAY originate from ISL Policy entities, organizational policy libraries, security control frameworks, architecture standards, platform configuration, environment-specific deployment policies, regulatory control mappings, and risk-tier profiles. Policies MAY apply to specification entities, canonical relationships, construction tasks, generated artifacts, interfaces, data entities, infrastructure definitions, tool and model invocations, repair cycles, deployment artifacts, readiness transitions, and runtime actions.

### 5.2 Violation Responses

Policy violation responses MUST be one of: `block` (prevent progression until resolved or waived), `escalate` (suspend automated progression and request governance action), `warn` (record and allow unless risk tier escalates), or `audit-only` (record without affecting progression).

### 5.3 Policy Evaluation

Every policy evaluation MUST produce a Policy Evaluation Record including an evaluation identifier, policy identifier and source, target identifier and type, an outcome (`compliant`, `non-compliant`, `not-applicable`, `waived`), the violation response, findings for non-compliant outcomes, the evaluator, timestamp, evidence, and waiver reference when waived. A `block` policy MUST prevent progression when non-compliant unless a valid waiver exists; an `escalate` policy MUST pause affected activity and emit an escalation event; a `warn` policy MUST produce an audit record and MAY permit progression unless the risk tier promotes warnings; an `audit-only` policy MUST produce an audit record.

---

## 6.0 Risk Tiers

Risk tier MUST be assigned before Autonomous-Ready readiness. A risk tier assignment MUST be recorded (assignment identifier, specification identifier and version, risk tier, assignment rationale, assignor, timestamp, evidence, and optional reassessment date). Risk determines required reviewers, validation depth, evidence retention, waiver strictness, runtime approval points, and deployment authorization.

### 6.1 Risk Tier Definitions

Using the shared Risk Tier enum (ISL v0.1 §6.2):

| Tier | Governing Criteria | Required Controls |
| --- | --- | --- |
| `low` | Internal use, no regulated data, no safety-critical behavior, limited operational impact | Default readiness, validation, traceability, and approval requirements |
| `standard` | Handles personal data, integrates with regulated systems, or affects important business workflows | Security review consideration, policy coverage evidence, enhanced evidence retention |
| `high` | Operates in regulated domains (finance, healthcare, public-sector) or supports critical infrastructure | Security and Architecture Reviewer approval, stricter tool/model restrictions, waiver review, deployment authorization |
| `critical` | Safety-critical, legally mandated, public-impacting, high-availability, or mission-critical | Independent authorization, strictest evidence retention, restricted tool/model profiles, mandatory deployment approval, enhanced audit review |

### 6.2 Risk Tier Change

A risk tier increase MUST trigger readiness reevaluation. A risk tier reduction MUST require governance approval. Any risk tier change MUST be recorded as a governance event. If risk tier changes during execution, the runtime MUST evaluate whether active tasks must pause, halt, or re-enter approval gates.

---

## 7.0 Approval Gates

Approval gates are lifecycle checkpoints requiring authorized governance approval before progression. They make governance executable: the runtime, readiness evaluator, or planning engine MUST pause or block when a required gate is not satisfied.

### 7.1 Standard Gate Types

| Gate Type | Required Approver |
| --- | --- |
| reviewable-transition | Reviewer |
| machine-valid-security | Security Reviewer |
| machine-valid-architecture | Architecture Reviewer |
| autonomous-ready | Authorizing Official |
| deployment-authorization | Authorizing Official |
| override-approval | Override Authority |
| waiver-approval | Waiver Authority or Authorizing Official |
| post-change-reauthorization | Authorizing Official |
| tool-use-approval | Governance Administrator or designated Tool Authority |
| model-use-approval | Governance Administrator or designated Model Authority |
| high-risk-repair-approval | Authorizing Official or Security Reviewer |
| production-deployment-approval | Authorizing Official and Operations Approver when required |

### 7.2 Gate Record

An Approval Gate Record MUST include a gate identifier, gate type, specification identifier and version, target identifier (conditional), required role, requester, request timestamp, status (`pending`, `approved`, `rejected`, `expired`, `cancelled`), decision-by and decision-at when decided, decision rationale for rejection, override, waiver, or high-risk approval, and expiry for pending gates.

### 7.3 Gate Rules

A required approval gate MUST block progression until approved; a rejected gate MUST prevent the associated transition or action; an expired gate MUST NOT authorize progression; approval MUST reference the exact specification version and target action; and approval for one version MUST NOT authorize another unless governance explicitly permits reuse.

### 7.4 Runtime Gate Enforcement

The runtime MUST create or evaluate the required approval gate at defined enforcement points (readiness transition to Machine-Valid; readiness transition to Autonomous-Ready; execution start; high-risk task or repair continuation; override and waiver application; restricted tool or model invocation; deployment preparation completion; post-change continuation). Construction boundaries MAY also require approval gates, policy evaluation, waiver handling, escalation review, or audit recording at boundary entry, execution, escalation, and exit; the Governance Engine MUST be capable of blocking when Boundary Entry or Exit Criteria fail governance validation.

* **Pending:** the runtime pauses the affected task/phase/action, records the pause in the Execution Graph, creates or updates the gate record, emits an audit event, exposes the gate to the approver, and prevents dependent tasks from proceeding when dependency rules require the blocked action.
* **Approved:** the runtime verifies the approver's active role, separation-of-duties rules, and that the approval applies to the current version and target; records the decision; updates the Execution Graph; and resumes only what the approval permits.
* **Rejected:** the runtime marks the affected action blocked, failed, halted, or escalated per policy, records the rejection rationale, prevents dependent tasks, emits an audit event, and requires replanning, repair, waiver, override, or termination before continuation.
* **Expired:** the runtime marks the gate expired, suspends or halts the action per policy, records an audit event, exposes the expired state, and requires a new request before continuation.

---

## 8.0 Waivers

A waiver permits a known policy violation, validation finding, or governance exception to proceed under explicit constraints, for a limited time or scope. It is not permanent permission to ignore controls.

### 8.1 Conditions and Prohibitions

A waiver MAY be requested when a policy violation or validation finding is known and documented, compensating controls are defined, risk is accepted by an authorized role, the waiver has a defined expiry, and affected targets are explicitly identified. A waiver MUST NOT be used to bypass missing system identity, missing construction authorization, separation-of-duties (unless a specific policy permits it), unbounded repair cycles, absent traceability for stable artifacts, unknown artifact provenance, prohibited tool or model use (unless an Override Authority approves an explicit override), or critical safety or legal constraints where waiver is forbidden.

### 8.2 Waiver Record

A Waiver Record MUST include a waiver identifier and type (`policy`, `validation`, `security`, `operational`, `deployment`, `traceability`), specification identifier and version, target identifier, justification, compensating controls, risk tier, requester, approver and approval timestamp, expiry, status (`active`, `expired`, `revoked`, `closed`), review date, and evidence.

### 8.3 Waiver Rules

A waiver MUST have an expiry, identify the target, and include compensating controls. An expired waiver MUST NOT authorize progression. A waiver MUST be traceable to the affected policy, validation result, artifact, task, or deployment action. A waiver affecting high or critical risk systems MUST require Authorizing Official approval unless governance defines a stricter role.

---

## 9.0 Overrides

An override permits progression despite a failed automated check or blocked state. Overrides are stronger and riskier than waivers and require stricter governance; they exist for exceptional circumstances where the normal control path cannot resolve a blocked condition and governance accepts responsibility for continuation.

### 9.1 Conditions and Prohibitions

An override MAY be requested when an automated check is known incorrect, a governance authority accepts documented risk, an external condition requires continuation, compensating controls are defined, and it is time-bound, scoped, and approved by the Override Authority. An override MUST NOT be used to authorize construction without a specification, canonical model, or traceability initialization; bypass missing Autonomous-Ready authorization; bypass audit logging; delete or alter historical governance records; suppress known critical security findings without explicit risk acceptance; or bypass legal or safety constraints where override is prohibited.

### 9.2 Override Record

An Override Record MUST include an override identifier and type (`readiness`, `validation`, `policy`, `runtime`, `tool`, `model`, `deployment`), specification identifier and version, target identifier, the failed control, justification, compensating controls, requester, Override Authority approver and timestamp, expiry, status, and evidence.

### 9.3 Override Rules

An override MUST have an expiry, be approved by an active Override Authority, be recorded before the affected action resumes, and be traceable to the affected control and target. The runtime MUST verify an override is active before using it, and MUST NOT reuse it across specification versions unless explicitly authorized. An expired override MUST NOT support progression; a readiness transition relying on an override MUST reference the override record.

---

## 10.0 Escalation Model

Escalation occurs when the platform cannot safely proceed autonomously and requires governance or human authority. It is a controlled pause, not failure by itself.

### 10.1 Escalation Triggers

The runtime or governance engine MUST escalate when repair limits are reached; a policy response is `escalate`; an approval gate is required and unsatisfied; a waiver or override is required; a tool conflict cannot be resolved automatically; restricted tool or model use requires approval; traceability integrity fails; a security finding requires human review; deployment authorization is missing; execution state integrity is inconsistent; or risk tier changes during active execution.

### 10.2 Escalation Record and Behavior

An Escalation Record MUST include an escalation identifier and type (`repair`, `policy`, `approval`, `waiver`, `override`, `tool`, `model`, `traceability`, `deployment`, `state-integrity`), specification identifier and version, target identifier, triggering event, required role, severity, status (`open`, `resolved`, `rejected`, `expired`, `cancelled`), opened timestamp, resolution fields, and next action (`resume`, `repair`, `replan`, `waive`, `override`, `halt`, `fail`).

When an escalation is opened, the runtime MUST mark the affected task/artifact/phase/action escalated, pause dependent actions when required, emit an audit event, preserve current execution state, expose or notify the required role, and prevent unauthorized continuation. Escalation may resolve by approval, rejection, waiver, override, repair authorization, replanning, execution halt, task failure, or deployment rejection. The runtime MUST apply only the next action authorized by the resolution.

---

## 11.0 Readiness Evaluation and Records

### 11.1 Readiness Transition Records

Every readiness transition MUST produce a readiness transition record (transition identifier, specification identifier and version, from-level and to-level, transition type of `progression`, `regression`, `override`, or `correction`, criteria evaluated/passed/failed, evidence records, authorized-by when approval is required, automated-checks-passed, override identifier when used, timestamp, notes). A transition record MUST NOT be overwritten; a transition using an override MUST reference an approved override record; a transition to Autonomous-Ready MUST reference governance authorization.

### 11.2 Readiness Evaluation Process

Evaluation MAY be triggered manually, automatically, or by a governance workflow, and SHOULD proceed through: identify current level, select target level, validate the transition path, evaluate criteria, collect evidence, determine outcome, produce a Readiness Report, and record the transition if approved. An evaluator MUST distinguish automated, manual review, and governance approval checks; MUST NOT mark a transition passed if any blocking criterion fails unless an approved override exists; and MUST produce a Readiness Report whether evaluation passes or fails.

### 11.3 Readiness Report

A Readiness Report (report identifier, specification identifier and version, current and target level, evaluation outcome, criterion results, blocking failures, warnings, evidence records, evaluated timestamp and evaluator) MUST include all criteria applicable to the target transition, identify blocking failures separately from warnings, and include remediation for blocking failures where determinable. Criterion results use the shared Validation Outcome semantics (`passed`, `failed`, `warning`, `not-applicable`); each records the verification method and whether failure blocks.

### 11.4 Automated Evaluation

A conforming evaluator MUST support automated checks for required section and field presence, semantic version and ISL version validity, identifier format and duplicate detection, enum validity, unresolved references, canonical normalization outcome, required relationships, validation and policy coverage, interface/workflow/data-model completeness, and readiness regression triggers. Business-objective quality, domain correctness, architecture suitability, security risk acceptability, policy adequacy, and construction authorization MAY require human or governance evaluation. For the same specification, ISL, governance profile, and evaluator version, automated evaluation SHOULD produce the same result; divergences MUST record the version or configuration difference.

---

## 12.0 Readiness Regression

A specification MUST regress when a change invalidates criteria required for its current readiness level. Regression applies to the modified version, MUST be recorded as a readiness transition, and MUST trigger impact analysis when traceability exists.

### 12.1 Mandatory Regression Rules

| Change Type | Regress To |
| --- | --- |
| Change to System Identity required fields | Reviewable |
| Addition, removal, or change to a must-have functional requirement statement | Reviewable |
| Change to a non-functional requirement target | Reviewable |
| Change to a security policy rule or violation response | Machine-Valid |
| Change to DataEntity attribute type/nullability or sensitive data classification | Machine-Valid |
| Change to Interface contract reference or authentication/authorization rule | Machine-Valid |
| Change to Infrastructure deployment model | Machine-Valid |
| Change invalidating canonical normalization | Draft or Reviewable |
| Change after Autonomous-Ready affecting construction scope | Machine-Valid |
| Removal of validation for a must-have requirement | Machine-Valid |

### 12.2 Regression Severity and Record

Regression severity SHOULD be classified as `minor` (no regression, review note), `moderate` (regress to Reviewable or Machine-Valid), `major` (Autonomous-Ready authorization invalidated), or `critical` (return to Draft). A regression record MUST include the changed elements, prior and new readiness level, reason, impact analysis reference when available, and a governance event reference.

---

## 13.0 Post-Change Reauthorization

Reauthorization restores a readiness level invalidated by regression. A specification that regressed MUST satisfy all criteria for the target level again; prior approvals MAY be cited as historical evidence but MUST NOT substitute for required approvals when the change affects approved content.

### 13.1 Reauthorization Rules

A specification that regresses from Autonomous-Ready MUST NOT resume execution until impact analysis is complete, canonical validation passes, affected validation coverage is restored, affected governance approvals are renewed, and construction authorization is reissued when required. Governance review MUST occur when a must-have requirement, security policy, data classification, interface contract, infrastructure model, risk tier, or validation coverage changes, or when deployment artifacts change after authorization.

### 13.2 Evidence Reuse

Evidence from prior versions MAY be reused only when the relevant elements are unchanged, the evidence applies to the same semantic content, governance policy permits reuse, and the Readiness Report identifies reused evidence explicitly.

### 13.3 Reauthorization Record

Post-change reauthorization MUST produce a readiness transition record and a governance approval record when returning to Autonomous-Ready, and MAY be captured in a Reauthorization Record (reauthorization identifier, prior and new specification version, impact analysis identifier, affected approvals/waivers/deployments, required actions, authorized-by, and status).

---

## 14.0 Readiness, Planning, and Execution

Planning is not execution. Advisory gap analysis is permitted at Draft; preliminary (non-executable) planning MAY occur at Reviewable; formal planning MAY occur at Machine-Valid; executable planning and execution MAY proceed at Autonomous-Ready. A construction plan generated before Autonomous-Ready MUST NOT be executed until the specification reaches Autonomous-Ready and execution preconditions are satisfied. If a specification changes after planning, readiness regression and impact analysis MUST determine whether the plan remains valid.

Autonomous execution MUST NOT begin unless the specification version is Autonomous-Ready, construction authorization is recorded, execution preconditions are satisfied, and no blocking regression has occurred. If a specification changes while execution is active, the runtime MUST determine whether the change affects executing tasks or artifacts; a change that invalidates Autonomous-Ready status MUST cause affected execution activities to pause, halt, or re-plan.

---

## 15.0 Deployment Authorization

Deployment-ready does not equal authorized for deployment. Deployment authorization MUST verify that execution completed or the required scope is complete; required validation passed or was waived; blocking policy violations are resolved or waived; traceability integrity checks passed; deployment artifacts are produced and validated; risk-tier-specific approvals are satisfied; operational readiness evidence exists; and environment-specific constraints are satisfied.

A Deployment Authorization Record MUST include an authorization identifier, specification identifier and version, deployment target, deployment artifacts covered, risk tier, validation, policy, and traceability evidence, the Authorizing Official or required role, timestamp, optional expiry, and status (`authorized`, `rejected`, `expired`, `revoked`). Production deployment MUST NOT occur without deployment authorization. Authorization MUST apply to a specific version and target, MUST NOT be reused after material change unless governance revalidates, and a rejected authorization MUST block deployment.

---

## 16.0 Governance State and Runtime Control Contract

### 16.1 Governance State

Governance state records the current and historical posture of a specification, plan, execution, artifact, deployment, or configuration, including Role, Policy, Approval, Waiver, Override, Escalation, Risk, Audit, and Deployment Authorization state. Governance state MUST be versioned, queryable by authorized roles, updated when governance events occur, consulted by readiness evaluators, planning engines, execution runtimes, tool selectors, and deployment preparation components, and MUST NOT be silently modified or deleted.

### 16.2 Runtime Check Contract

Runtime components MUST consult the Governance Engine at defined control points and MUST obey the returned decision. A Governance Check Request includes a check identifier and type (`readiness`, `planning`, `execution`, `policy`, `tool`, `model`, `repair`, `deployment`, `waiver`, `override`), specification identifier and version, target identifier and type, requested action, risk tier, context, and requester. A check response returns a decision from the shared Governance Decision enum plus applicable policies, required gate or role, findings, rationale, optional expiry, and decision metadata.

The runtime MUST NOT proceed when governance returns `block`, `escalate`, `approval-required`, `waiver-required`, or `override-required`; MUST record check responses; MUST keep decisions traceable to affected actions; and MUST NOT allow expired decisions to authorize later actions.

---

## 17.0 Evidence and Evidence Package

### 17.1 Readiness Evidence Package

A readiness evidence package MUST identify the specification version, be immutable after the readiness decision is recorded (corrections appended, not overwritten), and be accessible to governance and audit roles. It SHOULD include the authored specification version; structural validation report (Reviewable and above); canonical validation, reference resolution, validation coverage, policy coverage, and traceability coverage reports; review findings and dispositions (Reviewable and above); governance approval records (Machine-Valid and Autonomous-Ready); risk tier record, construction authorization, and construction plan or planning readiness evidence (Autonomous-Ready); reuse and Construction Boundary/Connection Context readiness evidence where applicable; override/waiver records when applicable; and readiness transition records for all transitions.

### 17.2 Governance Evidence

Governance evidence may include readiness reports, canonical validation reports, construction plans, planning validation reports, traceability snapshots, validation and test results, security scan results, policy evaluations, risk tier assignments, review findings, approval/waiver/override records, deployment preparation records, and audit log references. A governance decision authorizing progression MUST include sufficient evidence; evidence MUST reference the applicable specification version and be preserved for audit; evidence for high or critical risk systems SHOULD be under stricter integrity controls; a decision lacking required evidence MUST be treated as incomplete and MUST NOT authorize progression.

---

## 18.0 Audit Logging

Audit logs provide the immutable history needed for accountability, compliance, and system integrity, and are mandatory for governance-relevant events. The Audit Log Entry uses the shared Telemetry Event structure (ISL v0.1 §9) for correlation and MUST include an audit event identifier, event type, specification identifier and version when scoped, target, actor and actor role, event time, outcome, rationale for approvals/rejections/waivers/overrides/deployments, evidence, prior and new state, and correlation identifier.

The platform MUST support audit event types covering role assignment/revocation, approval requested/recorded/rejected, policy evaluation and violation, waiver requested/granted/expired/revoked, override requested/applied/expired, escalation received/resolved, risk tier assigned/changed, deployment authorized/rejected, reusable-asset lifecycle events, reuse approvals, governance check performed, and governance configuration changed.

Audit log entries MUST be immutable; no component MAY modify or delete an entry after it is written; corrections MUST be new events referencing the original; and records MUST be retained according to governance retention requirements. A governance audit write failure MUST halt or escalate governance-controlled actions because the action cannot be made auditable.

---

## 19.0 Governance Configuration, Telemetry, and Standards

### 19.1 Configuration

Governance configuration determines roles, policies, approval rules, risk tiers, waiver/override rules, and runtime enforcement behavior, and itself MUST be controlled. A configuration record MUST include a configuration identifier and version, effective dates, role mappings, approval rules, risk tier rules, waiver/override rules, policy profiles, deployment rules, and status (`draft`, `active`, `superseded`, `revoked`). Configuration changes MUST be versioned and audited. An active configuration MUST exist before progression to Machine-Valid or Autonomous-Ready, MUST NOT be silently changed during active execution, and a change during active execution MUST trigger evaluation of affected tasks.

### 19.2 Telemetry and Alerts

Governance telemetry follows the common Telemetry Event model (ISL v0.1 §9) and does not replace immutable audit logging. The platform SHOULD emit telemetry for governance checks, approval gates opened/resolved, escalations, waivers granted/expired, overrides applied, policy violations, deployment authorizations, separation violations, risk tier events, and audit write failures. The platform SHOULD alert on repeated waiver use, override frequency spikes, approval timeouts, critical policy violations, audit write failures, separation violations, blocked deployments, and risk tier mismatches.

### 19.3 Standards Alignment

Enterprise implementations SHOULD map governance policies to applicable control references (e.g., NIST SP 800-53 controls to Policy control-reference, ISO/IEC 25010 to non-functional requirement review, W3C PROV-DM to provenance activity records, OpenTelemetry to observability, TOGAF/ArchiMate to architecture governance). A security or compliance Policy SHOULD include an external control reference where applicable; a governance profile used in regulated environments MUST define its external control framework or internal baseline; and divergence from an adopted framework MUST be documented.

---

## 20.0 Audit Queries and Retention

### 20.1 Required Audit Queries

A conforming platform MUST support queries for: approval-history; readiness-governance-history; policy-violation-history; waiver-history; override-history; escalation-history; role-assignment-history; separation-violations; deployment-authorization-history; governance-check-history; risk-tier-history; and audit-integrity-check. Results MUST include identifiers, timestamps, actors, roles, target references, outcomes, and evidence references where applicable; MUST respect authorization rules; and SHOULD be exportable in machine-readable form.

### 20.2 Retention

The platform MUST retain role assignment, approval gate, policy evaluation, waiver, override, escalation, risk tier, deployment authorization, governance check, audit log, and governance configuration records. Records associated with a specification that reached Autonomous-Ready MUST be retained for the life of the generated system unless governance requires longer. Records for high or critical risk systems SHOULD be under enhanced integrity controls. Expired waivers and overrides MUST remain retained as historical records. All records MUST remain queryable for audit.

---

## 21.0 Errors

Readiness and governance errors extend the common Error Record model (ISL v0.1 §8). Registered extension classes include: readiness-missing-required-section/field, readiness-invalid-version, readiness-unsupported-isl-version, readiness-review-not-approved, readiness-canonical-validation-failed, readiness-reference-resolution-failed, readiness-validation-coverage-incomplete, readiness-policy-coverage-incomplete, readiness-risk-tier-missing, readiness-authorization-missing, readiness-separation-of-duties-violation, readiness-regression-required, readiness-override-expired, and readiness-evidence-incomplete; and governance-role-missing/expired, governance-separation-violation, governance-approval-missing/expired/rejected, governance-policy-noncompliant, governance-waiver-missing/expired, governance-override-missing/expired, governance-risk-tier-missing, governance-audit-write-failed, governance-evidence-incomplete, governance-runtime-check-failed, and governance-deployment-authorization-missing.

Blocking errors MUST prevent the affected action; governance audit write failures MUST halt or escalate; and governance errors MUST be traceable to affected targets where applicable.

---

## 22.0 Conformance

A specification conforms to this document if it records readiness level in the Project entity, satisfies all criteria for its claimed readiness level, includes required evidence, regresses when criteria are invalidated, and preserves readiness history by version.

A readiness evaluator conforms if it can evaluate all readiness levels, enforce permitted transition paths, check automated criteria, distinguish blocking failures from warnings, produce Readiness Reports and transition records, identify regression triggers, detect override requirements, and validate readiness evidence packages.

A Governance Engine conforms if it can evaluate policies and enforce violation responses, manage approval gates, validate role assignments, enforce separation of duties, assign or validate risk tiers, manage waivers, manage overrides, open and resolve escalations, produce governance check responses, record immutable audit events, expose audit queries, and integrate with readiness, planning, execution, tool selection, and deployment preparation.

An execution runtime conforms to the governance requirements if it can request governance checks at defined enforcement points, pause when approval is required, block or escalate per the returned decision, resume only after a valid approval, waiver, or override, record governance effects in execution state, and prevent deployment without deployment authorization.

A platform conforms if it treats governance as continuous control; enforces readiness and runtime approval gates; enforces policy violation responses, risk-tier controls, and separation of duties; preserves immutable governance records; supports the required audit queries; prevents autonomous construction and deployment without authorization; and integrates governance with traceability, planning, execution, tools, and artifact lifecycle.
