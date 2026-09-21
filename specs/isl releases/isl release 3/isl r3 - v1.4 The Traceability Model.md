# ALETHEIA Specification Language (ISL) v1.4
# The Traceability Model
**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0, ISL v1.1
**Supersedes:** ISL r2 v1.4 The Traceability Model
**Document Type:** Traceability Model Specification

---

## 1.0 Scope

This document defines the traceability model used by the ALETHEIA Specification Language and conforming ALETHEIA platform implementations. Traceability establishes the required relationships between specification entities, construction tasks, generated artifacts, validation results, repair actions, governance events, reusable assets, and lifecycle changes.

This document applies to all lifecycle phases beginning with canonical specification normalization and continuing through planning, execution, validation, repair, artifact consolidation, deployment preparation, and later specification evolution. Traceability is not limited to documentation or reporting. It is a required system property that governs whether generated artifacts can be accepted, audited, maintained, or deployed.

This document is the single authoritative catalog for traceability node types, edge types, and traceability object identifier prefixes. Other ISL r3 documents reference this catalog rather than redefining it. Identifier prefix conventions are defined in ISL v0.1 §4 and referenced here.

This document defines traceability principles, identifiers, graph structure, the authoritative node and edge type catalogs, artifact association, derivation relationships, bidirectional traceability, impact analysis, decision records, integrity checks, audit queries, lifecycle and versioning behavior, and conformance requirements.

---

## 2.0 Traceability Principles

A conforming implementation MUST enforce the following principles rather than treating traceability as optional metadata.

* **Traceability Is Structural** — Traceability MUST be implemented as part of the platform's persistent state. It MUST NOT be reconstructed solely from commit messages, file names, comments, or informal documentation. The traceability graph is a required structural representation used by planning, execution, governance, audit, and impact analysis.
* **Traceability Is Created at the Time of Action** — Traceability relationships MUST be recorded when the related action occurs: artifact relationships when artifacts are created, validation relationships when validation runs, and repair relationships when repair actions occur. The platform MUST NOT rely on retrospective reconstruction as the primary traceability mechanism.
* **Traceability Is Bidirectional** — The platform MUST support navigation from specification entity to derived tasks, artifacts, validations, decisions, and governance events, and from artifact, task, validation, or decision back to the originating specification entities.
* **Traceability Is Versioned** — Traceability MUST preserve historical relationships across specification versions. When a specification changes, prior traceability graphs MUST remain queryable. New traceability relationships MUST be added rather than overwriting history.
* **Traceability Is Integrity-Enforced** — An artifact, task, validation result, or governance event that lacks required traceability MUST be treated as a traceability integrity violation. A stable artifact repository MUST NOT accept untraced artifacts as valid construction outputs.
* **Traceability Supports Accountability** — Traceability records MUST preserve enough information to determine what action occurred, what caused it, who or what performed it, what artifacts were affected, and what evidence supports the result.

---

## 3.0 Traceability Identifiers

Stable identifiers are essential because traceability depends on persistent references across lifecycle events.

### 3.1 Identifier Format

All traceability identifiers MUST conform to the identifier format and entity type prefix conventions defined in ISL v0.1 §4. Canonical entity identifiers use the prefixes defined there (for example `REQ`, `SVC`, `VAL`).

### 3.2 Traceability Object Identifier Prefixes

The following prefixes SHALL be used for traceability objects that are not canonical specification entities. These prefixes are registered in ISL v0.1 §4 and are the authoritative set for traceability objects.

| Object Type | Prefix |
| ----------- | ------ |
| Construction Task | TSK |
| Artifact | ART |
| Validation Result | VRS |
| Repair Record | RPR |
| Decision Record | DEC |
| Tool Invocation | TIV |
| Model Invocation | MIV |
| Governance Event | GOV |
| Impact Analysis | IAN |
| Traceability Snapshot | TRS |
| Deployment Artifact | DPA |
| Execution Event | EXE |
| Reusable Asset | RAS |
| Reuse Decision | RSD |
| Reuse Fitness Assessment | RFA |
| Construction Boundary | BND |
| Connection Context | CNX |
| Boundary Continuation Record | BCR |

### 3.3 Traceability Object Identifier Format

Traceability object identifiers MUST conform to `{PREFIX}-{system-id}-{sequence}`, where PREFIX is the object prefix from §3.2, system-id is the normalized Project system identifier, and sequence is a zero-padded sequence unique within object type. Example: `ART-acme-order-00042`.

### 3.4 Identifier Rules

Traceability identifiers MUST be unique within their object type and system, MUST remain stable after assignment, and MUST NOT be reused after deletion, deprecation, supersession, or archival. A traceability object MUST NOT be accepted into persistent state without a valid identifier.

---

## 4.0 Traceability Graph

The traceability graph is the primary structure used to represent traceability. It connects specification entities to construction and lifecycle records through typed relationships. The traceability graph extends the canonical semantic graph: the canonical graph represents specification meaning, while the traceability graph represents lifecycle derivation and evidence.

### 4.1 Graph Definition

The traceability graph SHALL be a directed graph.

| Graph Element | Description |
| ------------- | ----------- |
| Node | Specification entity, task, artifact, validation result, decision, repair, invocation, governance event, deployment artifact, reusable asset, or construction boundary |
| Edge | Typed traceability relationship |
| Root | Project entity |
| Snapshot | Versioned view of the traceability graph at a point in lifecycle time |

### 4.2 Required Graph Properties

The traceability graph MUST support directed relationships, typed edges, bidirectional traversal, versioned snapshots, immutable historical records, and query by node identifier, relationship type, specification version, artifact identifier, governance event, and validation outcome.

### 4.3 Relationship to Canonical Graph

The traceability graph MUST include or reference canonical entity identifiers from ISL v1.1. The traceability graph MAY store canonical graph relationships directly or MAY reference a canonical model version. A traceability graph MUST identify the canonical model version from which its specification entity nodes originate.

---

## 5.0 Traceability Node Types

This section is the authoritative catalog of traceability node types. Node types allow the platform to distinguish specification concepts from construction activities, generated outputs, evidence, governance actions, and reuse objects.

### 5.1 Required Node Types

| Node Type | Description |
| --------- | ----------- |
| SpecificationEntity | Canonical entity from ISL v1.1 |
| ConstructionTask | Task from the Construction Task Graph |
| Artifact | Generated or managed construction output |
| ValidationResult | Result of deterministic validation or test execution |
| RepairRecord | Record of repair activity |
| DecisionRecord | Record explaining significant construction or repair decisions |
| ToolInvocation | Deterministic tool invocation record |
| ModelInvocation | Reasoning model invocation record |
| GovernanceEvent | Approval, waiver, escalation, or policy event |
| ImpactAnalysis | Impact analysis result |
| TraceabilitySnapshot | Versioned graph snapshot |
| DeploymentArtifact | Deployment-related artifact |
| ExecutionEvent | Runtime event from execution lifecycle |
| ReusableAsset | Asset registered for governed reuse |
| ReuseDecision | Decision authorizing reuse, partial reuse, wrapping, extension, composition, or generation |
| ReuseFitnessAssessment | Assessment of candidate reusable asset suitability |
| ConstructionBoundary | A bounded planning and execution scope containing tasks, artifacts, validation results, governance records, and continuation records |
| ConnectionContext | A permitted, required, and traceable interaction between construction boundaries |
| BoundaryContinuationRecord | Record preserving the information required by downstream boundaries to continue construction |

### 5.2 Node Base Schema

Every traceability node MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| node-id | string | REQUIRED | Unique identifier |
| node-type | enum | REQUIRED | Node type from §5.1 |
| specification-id | string | REQUIRED | System identifier |
| specification-version | semver | REQUIRED | Specification version associated with the node |
| created-at | ISO 8601 | REQUIRED | Time node was created |
| created-by | string | REQUIRED | Platform component, agent, tool, or authority creating node |
| status | enum | REQUIRED | active, deprecated, superseded, failed, archived |
| metadata | object | OPTIONAL | Supplemental metadata |

### 5.3 Node Rules

A node MUST NOT exist without a valid node-id and MUST identify the specification version to which it relates. A node representing an artifact MUST include artifact metadata as defined in ISL v1.2. A node representing a governance event MUST reference governance records defined in ISL v1.3. A node representing a tool invocation MUST reference tool records defined in ISL v1.2. A node representing a ConstructionBoundary MUST reference the boundary definition and its Connection Contexts and Boundary Continuation Records as defined in ISL v1.5.

---

## 6.0 Traceability Edge Types

This section is the authoritative catalog of traceability edge types. Edges are semantically meaningful. An implementation MUST NOT collapse all traceability relationships into generic links.

### 6.1 Edge Types

| Edge Type | Source | Target | Meaning |
| --------- | ------ | ------ | ------- |
| originates | SpecificationEntity | ConstructionTask | Specification entity caused task creation |
| produces | ConstructionTask | Artifact | Task produced artifact |
| implements | Artifact | SpecificationEntity | Artifact implements specification entity |
| validates | ValidationResult | Artifact or SpecificationEntity | Validation result verifies target |
| evaluates | ValidationResult | Policy or Validation | Result evaluates rule or criterion |
| invokes | ConstructionTask or ExecutionEvent | ToolInvocation or ModelInvocation | Task or event invoked tool/model |
| generated-by | Artifact | ModelInvocation or ToolInvocation | Artifact was generated by invocation |
| reviewed-by | Artifact or DecisionRecord | GovernanceEvent | Governance event reviewed target |
| governed-by | Artifact, Task, SpecificationEntity, or ConstructionBoundary | Policy | Target is constrained by policy |
| repairs | RepairRecord | Artifact | Repair record modifies or attempts to modify artifact |
| supersedes | Artifact | Artifact | New artifact replaces prior artifact |
| depends-on | Artifact or Task | Artifact or Task | Source requires target |
| derived-from | Artifact, Task, or DecisionRecord | SpecificationEntity or Artifact | Source was derived from target |
| reuses | Artifact, Task, ConstructionBoundary, or ReuseDecision | ReusableAsset | Source directly uses approved reusable asset |
| extends | Artifact, Task, ConstructionBoundary, or ReuseDecision | ReusableAsset | Source extends approved reusable asset |
| wraps | Artifact, Task, ConstructionBoundary, or ReuseDecision | ReusableAsset | Source adapts approved reusable asset through wrapper |
| composes | Artifact, Task, ConstructionBoundary, or ReuseDecision | ReusableAsset | Source composes one or more reusable assets |
| assesses | ReuseFitnessAssessment | ReusableAsset or ConstructionTask | Assessment evaluates fit of candidate asset |
| authorizes-reuse | ReuseDecision | Artifact, Task, or ConstructionBoundary | Reuse decision authorizes downstream construction |
| affects | ImpactAnalysis | Artifact, Task, or SpecificationEntity | Impact analysis identified affected target |
| unaffected | ImpactAnalysis | Artifact, Task, or SpecificationEntity | Impact analysis identified target as unaffected |
| escalates-to | Task, Artifact, or RepairRecord | GovernanceEvent | Target was escalated to governance |
| authorizes | GovernanceEvent | Readiness, Task, Deployment, or ExecutionEvent | Governance event authorizes target |
| blocks | GovernanceEvent or Policy | Task, Artifact, or ExecutionEvent | Governance or policy blocks target |
| waives | GovernanceEvent | PolicyEvaluation or ValidationResult | Governance event waives finding |
| contains-task | ConstructionBoundary | ConstructionTask | Boundary contains the task |
| contains-artifact | ConstructionBoundary | Artifact | Boundary produced or modified the artifact |
| depends-on-boundary | ConstructionBoundary | ConstructionBoundary | Boundary requires another boundary to complete first |
| provides-context | ConstructionBoundary | ConnectionContext | Boundary provides context to another boundary |
| consumes-context | ConstructionBoundary | ConnectionContext | Boundary consumes context from another boundary |
| produces-continuation | ConstructionBoundary | BoundaryContinuationRecord | Boundary produced continuation state |

### 6.2 Edge Base Schema

Every traceability edge MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| edge-id | string | REQUIRED | Unique edge identifier |
| edge-type | enum | REQUIRED | Edge type from §6.1 |
| source-node-id | string | REQUIRED | Source node identifier |
| target-node-id | string | REQUIRED | Target node identifier |
| specification-id | string | REQUIRED | System identifier |
| specification-version | semver | REQUIRED | Specification version |
| created-at | ISO 8601 | REQUIRED | Time edge was created |
| created-by | string | REQUIRED | Platform component, tool, agent, or authority creating edge |
| rationale | string | CONDITIONAL | Required when edge is inferred |
| confidence | enum | CONDITIONAL | explicit, inferred, imported, repaired |

### 6.3 Edge Rules

Every edge source and target MUST resolve to existing traceability nodes. An edge MUST use a defined edge type. Inferred edges MUST include a rationale. Edges MUST NOT be deleted when historical traceability must be preserved; corrections MUST be represented through supersession, deprecation, or corrective records.

---

## 7.0 Artifact Association

Artifact association is the minimum traceability requirement for construction outputs. An artifact without a specification association cannot be trusted as a product of the ISL construction process.

### 7.1 Artifact Association Record Schema

Each artifact association MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| association-id | string | REQUIRED | Unique association identifier |
| artifact-id | string | REQUIRED | Artifact identifier |
| artifact-type | enum | REQUIRED | source, config, schema, infrastructure, contract, test, documentation |
| source-entity-ids | array | REQUIRED | One or more originating specification entity identifiers |
| task-id | string | REQUIRED | Construction task that produced artifact |
| derivation-type | enum | REQUIRED | Derivation type from ISL v0.1 §6.8 |
| created-at | ISO 8601 | REQUIRED | Time association was created |
| specification-version | semver | REQUIRED | Specification version at artifact creation |
| rationale | string | CONDITIONAL | Required when derivation-type is inferred |
| validation-status | enum | REQUIRED | Artifact state from ISL v0.1 §6.7 |

### 7.2 Derivation Types

The derivation type uses the shared enum from ISL v0.1 §6.8. The primary derivation types for artifact association are:

| Derivation Type | Meaning |
| --------------- | ------- |
| direct | Artifact directly implements one or more specification entities |
| derived | Artifact is produced as a consequence of implementing specification entities |
| inferred | Artifact was created to satisfy an implicit structural need |
| repair | Artifact replaces or modifies another artifact due to repair |
| reused | Artifact is produced by reusing an approved reusable asset |
| reuse-delta | Artifact is the delta, adapter, extension, or composition logic over a reused asset |

### 7.3 Artifact Association Rules

Every artifact produced by the platform MUST have at least one Artifact Association Record. An artifact with derivation-type `inferred` MUST include a rationale. An artifact with derivation-type `repair` MUST reference the prior artifact and Repair Record. An artifact with derivation-type `reused` or `reuse-delta` MUST reference the Reuse Decision Record and the reused Reusable Asset. An artifact MUST NOT be marked valid unless its traceability association exists. An unassociated artifact MUST be treated as an untraced artifact and MUST NOT enter the stable artifact repository.

---

## 8.0 Traceability During Planning and Execution

### 8.1 Planning Traceability

Planning creates the bridge between specification entities and executable construction tasks. The Planning Engine MUST record why each task exists. For every generated construction task, the platform MUST record: SpecificationEntity originates ConstructionTask; ConstructionTask depends-on ConstructionTask when dependencies exist; ConstructionTask governed-by Policy when policies apply; and ConstructionTask derived-from Construction Plan.

Every construction task MUST trace to at least one source specification entity unless it is a platform-required task such as consolidation, validation, or deployment preparation. Platform-required tasks MUST include a rationale and reference the phase or rule that caused them to be generated. A task without traceability MUST NOT be eligible for execution.

### 8.2 Execution Traceability

The runtime MUST record traceability links for task start and completion, artifact generation, deterministic validation, test execution, tool invocation, model invocation, repair cycle initiation, repair modifications, security and policy evaluation, escalation events, waivers and overrides, artifact consolidation, and deployment preparation.

Traceability links MUST be created at the time of the execution event. If traceability recording fails, the runtime MUST mark the affected task or artifact as failed or escalated. A traceability failure during execution MUST produce an error in the `traceability` error class using the common error model from ISL v0.1 §8. Execution MUST NOT mark a construction plan completed while required execution traceability links are missing.

---

## 9.0 Validation and Repair Traceability

### 9.1 Validation Traceability

Validation traceability provides evidence that generated artifacts satisfy the requirements, policies, interfaces, workflows, or infrastructure expectations that justified their creation. Validation without traceability is not sufficient evidence for readiness, completion, or governance.

Every validation result MUST link to the artifact evaluated (when artifact-level validation), the Validation entity (when derived from specification validation), the tool invocation (when a deterministic tool is used), the Requirement or Policy validated (when applicable), and the task that triggered validation.

A validation result MUST NOT be accepted without a target. A passed validation result MUST reference the artifact or entity it proves. A failed validation result MUST be linked to any Repair Record it triggers. A waived validation result MUST be linked to the governance event authorizing the waiver.

### 9.2 Repair Traceability

Repair actions are especially important to trace because they modify previously generated artifacts in response to validation failures.

Every Repair Record MUST link to the failed validation result, the artifact being repaired, the construction task, the modified artifact version (when produced), the repair decision record (conditional), and the subsequent validation result.

A repaired artifact MUST preserve linkage to the original source specification entities and MUST supersede the artifact version it replaces. A repair that modifies an artifact MUST produce a `repairs` edge and a `supersedes` edge when a new artifact version is created. A repair that fails MUST remain traceable and MUST NOT be deleted.

---

## 10.0 Decision Records

Decision Records capture significant reasoning, construction, repair, validation, or governance decisions that affect the generated system. Enterprise auditability requires that important choices be explainable.

### 10.1 Decision Record Schema

A Decision Record MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| decision-id | string | REQUIRED | Unique decision identifier |
| decision-type | enum | REQUIRED | interpretation, planning, generation, modification, repair, validation, escalation, governance |
| agent-role | string | CONDITIONAL | Agent role making or proposing decision |
| task-id | string | CONDITIONAL | Related construction task |
| artifact-id | string | CONDITIONAL | Related artifact |
| source-entity-ids | array | CONDITIONAL | Specification entities influencing decision |
| rationale | string | REQUIRED | Explanation of why decision was made |
| alternatives-considered | array | CONDITIONAL | Alternatives considered |
| model-invocation-id | string | CONDITIONAL | Model invocation that produced reasoning |
| tool-invocation-id | string | CONDITIONAL | Tool invocation influencing decision |
| governance-event-id | string | CONDITIONAL | Governance event influencing decision |
| recorded-at | ISO 8601 | REQUIRED | Time decision was recorded |
| recorded-by | string | REQUIRED | Agent, tool, runtime, or authority recording decision |

### 10.2 Decision Record Rules

Decision Records MUST be immutable after creation. Corrections MUST be recorded as new Decision Records referencing the superseded record.

A Decision Record MUST be created for inferred artifacts, repair proposals, escalations, manual overrides, governance waivers, and construction decisions that materially affect architecture, security, policy, or deployment. A Decision Record SHOULD be created for significant implementation choices when multiple valid alternatives exist.

---

## 11.0 Bidirectional Traceability

Traceability must support both forward and reverse questions. Forward traceability answers "What did this specification entity produce?" Reverse traceability answers "Why does this artifact exist?"

### 11.1 Forward Traceability

Given a specification entity, the platform MUST be able to return construction tasks derived from the entity, artifacts implementing or derived from the entity, validation results proving the entity, policies governing the entity, decisions involving the entity, repairs affecting artifacts derived from the entity, and deployment artifacts derived from the entity.

### 11.2 Reverse Traceability

Given an artifact, validation result, repair record, decision record, or governance event, the platform MUST be able to return originating specification entities, construction tasks involved, model or tool invocations involved, validation records affecting status, repair records affecting current version, policies governing the object, and governance events authorizing or blocking actions.

### 11.3 Navigation Rules

Traceability queries MUST return results scoped to a specification version unless the query explicitly requests cross-version history. A platform MUST support both direct and transitive traversal. A platform MUST indicate whether returned relationships are direct, derived, inferred, repaired, superseded, or governed.

---

## 12.0 Impact Analysis

Impact analysis determines what tasks, artifacts, validations, policies, and deployment materials are affected when a specification changes. It allows the platform to avoid rebuilding everything when only part of the specification changes, while preventing unsafe partial updates.

### 12.1 Impact Analysis Trigger

The platform MUST perform impact analysis when a canonical entity changes; a requirement is added, removed, or modified; a policy rule changes; an interface contract changes; a data entity attribute changes; an infrastructure expectation changes; a reusable asset changes, is deprecated, is superseded, or is retired; a Reuse Decision changes; readiness regression occurs; or governance requires change-impact review.

### 12.2 Impact Analysis Record Schema

An Impact Analysis Record MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| analysis-id | string | REQUIRED | Unique impact analysis identifier |
| triggered-by | string | REQUIRED | Entity, change event, or governance event triggering analysis |
| specification-id | string | REQUIRED | System identifier |
| previous-specification-version | semver | CONDITIONAL | Prior version |
| new-specification-version | semver | REQUIRED | New version |
| changed-entities | array | REQUIRED | Entities changed |
| affected-artifacts | array | REQUIRED | Artifacts requiring regeneration or review |
| affected-tasks | array | REQUIRED | Tasks requiring re-execution or review |
| affected-validations | array | CONDITIONAL | Validations requiring re-run or update |
| affected-policies | array | CONDITIONAL | Policies requiring re-evaluation |
| affected-deployment-artifacts | array | CONDITIONAL | Deployment artifacts requiring update |
| affected-reusable-assets | array | CONDITIONAL | Reusable assets requiring review, replacement, or revalidation |
| affected-reuse-decisions | array | CONDITIONAL | Reuse decisions requiring reevaluation |
| unaffected-artifacts | array | REQUIRED | Artifacts confirmed unaffected |
| analysis-method | string | REQUIRED | Traversal or comparison method used |
| analysis-timestamp | ISO 8601 | REQUIRED | Time analysis completed |
| confidence | enum | REQUIRED | complete, partial, uncertain |

### 12.3 Impact Analysis Rules

Impact analysis MUST traverse the traceability graph from changed entities through dependent tasks, artifacts, validations, repairs, reusable assets, reuse decisions, and deployment artifacts. Impact analysis MUST traverse `reuses`, `extends`, `wraps`, and `composes` edges so that shared asset changes identify all dependent specifications, artifacts, tasks, Construction Boundaries, and Connection Contexts.

Artifacts listed as unaffected MUST NOT be regenerated unless explicitly instructed by governance or runtime configuration. If impact analysis confidence is `partial` or `uncertain`, the platform MUST escalate or require human review before proceeding with selective reconstruction. Impact analysis results MUST be recorded as traceability nodes and edges.

---

## 13.0 Traceability Snapshots

A snapshot captures the state of the traceability graph at a specific lifecycle point. Snapshots support audit, rollback analysis, comparison between specification versions, and reconstruction of historical construction state.

### 13.1 Snapshot Trigger Points

A Traceability Snapshot MUST be created after canonical normalization reaches Machine-Valid, before Autonomous-Ready authorization, at execution start, after artifact consolidation, after deployment preparation, after specification version change, after impact analysis, and when governance requires audit preservation.

### 13.2 Snapshot Schema

A Traceability Snapshot MUST include:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| snapshot-id | string | REQUIRED | Unique snapshot identifier |
| specification-id | string | REQUIRED | System identifier |
| specification-version | semver | REQUIRED | Specification version |
| canonical-model-version | semver | REQUIRED | Canonical model version |
| created-at | ISO 8601 | REQUIRED | Time snapshot created |
| created-by | string | REQUIRED | Platform component creating snapshot |
| node-count | integer | REQUIRED | Number of nodes included |
| edge-count | integer | REQUIRED | Number of edges included |
| snapshot-purpose | enum | REQUIRED | readiness, execution-start, consolidation, deployment, audit, change-impact |
| storage-location | string | REQUIRED | Where snapshot is stored |
| integrity-hash | string | CONDITIONAL | Hash or integrity marker when supported |

### 13.3 Snapshot Rules

Snapshots MUST be immutable once created. A later snapshot MUST NOT overwrite an earlier snapshot. Snapshots SHOULD support graph comparison when implementations provide version-diff capability.

---

## 14.0 Traceability Integrity Checks

Integrity checks identify missing links, invalid references, untraced artifacts, orphan nodes, and inconsistent graph state. A platform cannot claim reliable autonomous construction if traceability integrity is not enforced.

### 14.1 Required Integrity Checkpoints

The platform MUST perform traceability integrity checks at canonical normalization (entities have valid identifiers), construction planning (tasks trace to specification entities), artifact creation (artifacts trace to tasks and source entities), validation completion (validation results trace to artifacts and criteria), repair completion (repairs trace to failures and modified artifacts), policy evaluation (evaluation records trace to policies and targets), artifact consolidation (consolidated artifacts are traceable and valid), deployment preparation (deployment artifacts trace to infrastructure and operational expectations), readiness transition (required traceability evidence exists), and execution completion (traceability graph is complete and consistent).

### 14.2 Integrity Check Rules

A traceability integrity check MUST verify that all node identifiers are valid, all edge source and target identifiers resolve, all artifacts have source associations, all tasks trace to source entities or justified platform rules, all validation results trace to targets, all repairs trace to failures, all superseded artifacts retain historical links, no stable artifact is untraced, and no required traceability edge is missing.

### 14.3 Integrity Check Record Schema

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| integrity-check-id | string | REQUIRED | Unique check identifier |
| specification-id | string | REQUIRED | System identifier |
| specification-version | semver | REQUIRED | Specification version |
| checkpoint | string | REQUIRED | Lifecycle checkpoint |
| outcome | enum | REQUIRED | passed, failed, warning |
| findings | array | CONDITIONAL | Integrity findings |
| checked-at | ISO 8601 | REQUIRED | Time check occurred |
| checked-by | string | REQUIRED | Platform component performing check |

### 14.4 Integrity Failure Rules

A failed traceability integrity check MUST block the lifecycle action associated with the checkpoint unless governance explicitly waives the failure. An untraced artifact finding MUST be treated as blocking for artifact consolidation and execution completion.

---

## 15.0 Traceability Error Classes

Traceability errors use the common error model from ISL v0.1 §8. The following extension error classes are registered under the `traceability` error category.

| Error Class | Description | Default Severity |
| ----------- | ----------- | ---------------- |
| traceability-invalid-identifier | Identifier does not conform to required format | critical |
| traceability-missing-node | Required node is missing | critical |
| traceability-missing-edge | Required edge is missing | critical |
| traceability-unresolved-reference | Edge source or target cannot be resolved | critical |
| traceability-untraced-artifact | Artifact lacks required source association | critical |
| traceability-orphan-node | Node is disconnected from required graph context | error |
| traceability-invalid-edge-type | Edge type is not permitted | critical |
| traceability-missing-rationale | Inferred relationship lacks rationale | error |
| traceability-snapshot-missing | Required snapshot was not produced | error |
| traceability-history-overwrite | Historical traceability record was overwritten | critical |
| traceability-impact-analysis-incomplete | Impact analysis could not determine affected scope | error |
| traceability-integrity-failed | Integrity check failed | critical |

Each error record MUST conform to the common error record structure from ISL v0.1 §8.1, including error-id, error-class, severity, message, required-action, recorded-at, and conditional target-id and evidence fields.

---

## 16.0 Traceability Audit Queries

The following are the minimum audit queries that a conforming platform MUST support. These queries provide evidence for governance, compliance, debugging, and lifecycle management. A platform SHOULD expose them through an API, query interface, report generator, or governance dashboard.

### 16.1 Required Audit Queries

| Query | Description |
| ----- | ----------- |
| requirements-coverage | For each requirement, list implementing artifacts and validations |
| must-have-coverage | For each must-have requirement, list implementation and validation evidence |
| policy-enforcement | For each policy, list governed targets and evaluation records |
| untraced-artifacts | List artifacts lacking required source associations |
| artifact-origin | Given an artifact, return originating entities, tasks, invocations, and decisions |
| change-impact | Given a specification change, list affected and unaffected artifacts |
| decision-history | Given an artifact or task, list related Decision Records |
| repair-history | Given an artifact, list repair attempts and outcomes |
| validation-history | Given an artifact or requirement, list validation results |
| governance-history | Given a specification or artifact, list approvals, waivers, escalations, and overrides |
| supersession-history | Given an artifact, list versions it superseded and versions superseding it |
| deployment-trace | Given a deployment artifact, list source infrastructure and operational expectations |

### 16.2 Query Output Rules

Audit query results MUST include identifiers, relationship types, specification versions, and timestamps. Audit query results SHOULD indicate whether relationships are direct, transitive, inferred, repaired, superseded, or waived. Audit query results MUST be exportable in a machine-readable format when required by governance or audit.

---

## 17.0 Traceability, Readiness, and Governance

### 17.1 Readiness Traceability

Readiness levels depend on evidence, and traceability provides the relationships needed to prove that evidence applies to the correct specification version and entities.

Before Machine-Valid readiness, the platform MUST verify that canonical entities have valid identifiers, requirements link to validation criteria, policies link to governed targets, interfaces link to services, data entities link to owning or managing services where required, and workflows link to participating actors or services.

Before Autonomous-Ready readiness, the platform MUST verify that must-have requirements have implementation targets or planned construction tasks, validation coverage is traceable, governance approvals reference the correct specification version, risk tier records reference the Project entity, and a traceability snapshot exists.

A readiness transition MUST fail if required traceability evidence is missing. Readiness overrides involving traceability gaps MUST be approved by governance and MUST define compensating controls.

### 17.2 Governance Traceability

Governance decisions must be traceable to the specification, artifacts, tasks, policies, or lifecycle events they affect. Governance events MUST link to affected objects, including specification versions, readiness transitions, policies, construction tasks, artifacts, validation results, repair records, and deployment artifacts.

A waiver MUST link to the policy or criterion waived, the affected specification entity or artifact, the justification, compensating controls, the approving authority, and the expiry date. An escalation MUST link to the task, artifact, validation result, or repair record causing escalation, the governance event receiving escalation, the resolution decision, and the resumed or halted execution state.

---

## 18.0 Traceability Storage and Lifecycle

### 18.1 Storage Requirements

Traceability records must be durable, queryable, and protected from unauthorized modification. The storage implementation is not prescribed; a platform may use graph databases, relational databases, document stores, event logs, or hybrid mechanisms if the required semantics are preserved.

Traceability storage MUST support durable persistence, lookup by identifier, graph traversal, versioned snapshots, immutable historical records, query by specification version, artifact, requirement, and policy, and export for audit.

Traceability records SHOULD include integrity mechanisms such as hashes, append-only event logs, signed records, or immutable storage where required by governance. Historical traceability records MUST NOT be silently modified or deleted. Traceability retention MUST satisfy governance and audit requirements; at minimum, records for a specification version that reached Autonomous-Ready MUST be retained for the life of the generated system unless governance defines a longer period.

### 18.2 Lifecycle and Evolution

Software systems evolve over time, and traceability must preserve both current and historical truth.

When a specification version changes, the platform MUST preserve traceability for the prior version, produce an additive traceability layer or new snapshot, and keep prior-version traceability queryable.

When an artifact changes, the platform MUST preserve the prior artifact version or metadata sufficient to identify it, link new versions to prior versions using `supersedes` relationships, and trace a repaired artifact to the validation failure and repair record that caused the change.

Deprecated entities or artifacts MUST remain traceable. Deprecation MUST NOT delete historical traceability records. A deprecated artifact MUST identify its replacement when superseded.

---

## 19.0 Conformance Requirements

### 19.1 Traceability Graph Conformance

A traceability graph conforms to ISL v1.4 if it contains required node and edge types, uses valid identifiers, resolves all required references, supports bidirectional traversal, preserves specification version context, records artifact associations, records validation, repair, and governance relationships, and passes required integrity checks.

### 19.2 Traceability Engine Conformance

A traceability engine conforms to ISL v1.4 if it can create traceability nodes and edges at lifecycle event time, enforce required artifact associations, enforce identifier rules, perform bidirectional traceability queries, perform impact analysis, create traceability snapshots, perform integrity checks, report traceability errors, preserve historical traceability, and support required audit queries.

### 19.3 Platform Conformance

A platform conforms to ISL v1.4 if it integrates traceability with readiness evaluation, construction planning, execution runtime, deterministic validation, and governance events; blocks stable artifact acceptance for untraced artifacts; prevents execution completion when required traceability is incomplete; and preserves traceability across specification versions.
