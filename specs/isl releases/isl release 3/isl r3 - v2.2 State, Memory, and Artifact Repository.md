# ALETHEIA Specification Language (ISL) v2.2
# State, Memory, and Artifact Repository
**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x
**Supersedes:** ISL r2 v2.2 The State and Memory Model, ISL r2 v2.3 The Artifact Repository Model
**Document Type:** Platform Model Specification

---

## 1.0 Scope

This document defines how the ALETHEIA platform represents, persists, versions, transitions, validates, recovers, queries, and exposes its operational state and durable memory, and how it manages the artifacts produced, modified, repaired, packaged, imported, or retained during autonomous software construction.

State represents the current operational condition of the platform. Memory represents the durable historical record of how the platform reached that condition. State allows the platform to decide what should happen next; memory allows the platform to explain what happened before, recover after interruption, support audit, and evolve systems over time. The Artifact Repository is the controlled workspace and historical archive where generated implementation artifacts are preserved as first-class lifecycle objects.

This document applies across specification authoring, canonical normalization, readiness evaluation, construction planning, execution, agent collaboration, deterministic validation, repair cycles, artifact lifecycle management, governance enforcement, deployment preparation, observability, and audit.

Shared conventions — including normative language, field requirement levels, identifier formats, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and are incorporated by reference. Domain documents MUST NOT redefine them.

## 2.0 State and Memory Principles

1. **State Is Explicit.** All lifecycle-relevant state MUST be represented in structured platform records. The platform MUST NOT rely on hidden process memory, transient prompt context, implicit local variables, file names, or informal logs as the only source of truth for critical lifecycle decisions.
2. **Memory Is Durable.** Historical records that support traceability, audit, recovery, governance, validation, or artifact reconstruction MUST be persisted durably. A platform MUST NOT lose governance approvals, traceability links, validation results, repair records, artifact associations, or readiness transitions when a process restarts.
3. **State Transitions Are Controlled.** State values MUST change only through defined transitions. Invalid transitions MUST be rejected or escalated.
4. **State and Memory Are Versioned.** Specifications, canonical models, construction plans, execution graphs, artifacts, governance records, agent outputs, and traceability snapshots MUST preserve version context, and the platform MUST be able to distinguish the current state of the latest version from the historical state of prior versions.
5. **State Consistency Is Enforced.** Related state categories MUST remain consistent and MUST be checked at defined checkpoints.
6. **Memory Access Is Governed.** Agents and subsystems MUST access memory through controlled interfaces and receive only the state and memory relevant to their task, role, risk tier, authorization, and context package.
7. **Recovery Must Be Deterministic.** After interruption, restart, crash, or controlled halt, the platform MUST reconstruct state deterministically from durable records, snapshots, and event logs, and MUST NOT continue from uncertain or contradictory state without recovery validation.
8. **Artifacts Are First-Class Lifecycle Objects.** Artifacts MUST be represented as structured lifecycle objects with metadata, state, version, traceability, validation, and governance information. A file without artifact metadata MUST NOT be treated as a valid platform artifact.
9. **Validation and Traceability Precede Promotion.** An artifact MUST NOT be promoted to stable repository state unless required deterministic validation has passed (or a valid governance waiver exists) and traceability is complete. Reasoning-agent output alone MUST NOT be sufficient for artifact promotion.

## 3.0 State Categories

### 3.1 Required State Categories

| State Category | Description |
| ----- | ----- |
| Specification State | Current and historical authored specification and readiness metadata |
| Canonical Model State | Current and historical canonical semantic model state |
| Readiness State | Readiness level, transition records, evidence packages, and regression state |
| Planning State | Construction plans, task graphs, dependencies, critical path, and plan adaptation |
| Execution State | Execution graphs, phases, tasks, runtime activity, repair, and completion |
| Artifact State | Artifact metadata, versions, lifecycle state, repository location, validation, and supersession |
| Traceability State | Traceability graph, snapshots, integrity checks, and impact analysis |
| Governance State | Policies, approvals, waivers, overrides, escalations, risk tiers, and audit logs |
| Tool State | Tool records, trust profiles, invocations, findings, and version drift |
| Agent State | Agent invocations, context packages, outputs, handoffs, failures, and conflicts |
| Model State | Model provider records, model invocations, routing decisions, and output normalization |
| Reuse State | Reusable asset records, Reuse Fitness Assessments, Reuse Decisions, and dependency state |
| Observability State | Telemetry events, alerts, metrics, logs, traces, and monitoring summaries |
| Repository State | Artifact repository content, status, branches, conflicts, and integrity |
| Configuration State | Runtime, platform, governance, tool, model, and deployment configuration |

### 3.2 Category Rules

Each required state category MUST have a defined owner subsystem. Each category MUST define current state records and historical memory records where applicable. State categories MUST be linked through identifiers rather than unstructured textual references. State category updates MUST emit events when the update affects lifecycle behavior, governance, traceability, validation, or execution.

## 4.0 State and Memory Architecture

### 4.1 Logical Stores

State and memory MAY be implemented using relational, graph, document, object, event-log, filesystem-backed, embedded, or hybrid storage mechanisms, provided the required semantics are preserved.

| Store | Responsibility |
| ----- | ----- |
| Current State Store | Maintains latest state records for platform objects |
| Event Log | Records immutable state transition and lifecycle events |
| Graph Store | Maintains canonical, traceability, and dependency relationships |
| Artifact Metadata Store | Maintains artifact metadata, lifecycle state, versions, and repository links |
| Governance Record Store | Maintains approvals, waivers, overrides, policies, and audit records |
| Agent Memory Store | Maintains agent invocations, context packages, outputs, handoffs, failures, and conflicts |
| Evidence Store | Maintains validation reports, tool outputs, test reports, security findings, and deployment evidence |
| Snapshot Store | Maintains point-in-time state, graph, and lifecycle snapshots |
| Configuration State Store | Maintains versioned platform, runtime, tool, model, and governance configuration |

A conforming platform MAY combine logical stores into the same physical backend but MUST preserve the logical responsibilities of each store. State records that affect readiness, execution, artifact validity, governance, traceability, or deployment MUST be durable. Event logs and governance audit records MUST be append-only or integrity-protected.

### 4.2 Base State Record Schema

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| state-record-id | string | REQUIRED | Unique state record identifier |
| state-category | enum | REQUIRED | State category from §3.1 |
| object-id | string | REQUIRED | Identifier of object whose state is represented |
| object-type | string | REQUIRED | Type of object represented |
| specification-id | string | CONDITIONAL | Required when state is specification-scoped |
| specification-version | semver | CONDITIONAL | Required when state is specification-version scoped |
| current-state | string | REQUIRED | Current state value |
| prior-state | string | CONDITIONAL | Prior state value when transition occurred |
| state-version | semver | REQUIRED | Version of this state record |
| updated-at | ISO 8601 | REQUIRED | Time state was updated |
| updated-by | string | REQUIRED | Subsystem, role, agent, tool, or runtime updating state |
| correlation-id | string | CONDITIONAL | Related execution, task, event, or governance check |
| traceability-node-id | string | CONDITIONAL | Traceability node associated with object |
| integrity-hash | string | CONDITIONAL | Integrity hash when supported |

A state record MUST identify the object whose state it represents and its current-state value. A state update affecting lifecycle behavior MUST preserve prior-state or emit a state event containing prior-state. State records MUST NOT be silently overwritten when historical reconstruction is required.

## 5.0 State Event Model

State events are the durable memory of how state evolved. State records represent the current condition; state events represent the chronological history.

### 5.1 State Event Schema

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| state-event-id | string | REQUIRED | Unique state event identifier |
| event-type | string | REQUIRED | Type of state event |
| state-category | enum | REQUIRED | State category affected |
| object-id | string | REQUIRED | Object affected |
| object-type | string | REQUIRED | Type of object affected |
| prior-state | string | CONDITIONAL | State before event |
| new-state | string | CONDITIONAL | State after event |
| specification-id | string | CONDITIONAL | Related specification identifier |
| specification-version | semver | CONDITIONAL | Related specification version |
| caused-by | string | REQUIRED | Task, invocation, governance action, subsystem, or user causing event |
| correlation-id | string | CONDITIONAL | Related execution graph, task, invocation, or transaction |
| event-time | ISO 8601 | REQUIRED | Time event occurred |
| event-sequence | integer | REQUIRED | Monotonic sequence within scope |
| evidence | array | CONDITIONAL | Evidence supporting event |
| metadata | object | OPTIONAL | Supplemental metadata |

### 5.2 State Event Rules

State events that affect readiness, governance, artifact validity, execution completion, validation, repair, or traceability MUST be durable. State events MUST be append-only where used for audit or recovery, and MUST be ordered within their scope using event-sequence or equivalent ordering. A state event MUST NOT be removed from historical memory unless retention policy permits archival and audit requirements are preserved.

### 5.3 Required State Event Types

The platform MUST emit state events for:

* `specification-created`, `specification-versioned`
* `canonical-model-generated`
* `readiness-transitioned`, `readiness-regressed`
* `construction-plan-created`, `construction-plan-adapted`
* `reuse-discovery-started`, `reuse-decision-recorded`
* `reusable-asset-promoted`, `reusable-asset-deprecated`, `reusable-asset-retired`
* `execution-started`, `phase-state-changed`, `task-state-changed`
* `artifact-created`, `artifact-state-changed`
* `validation-recorded`
* `repair-started`, `repair-completed`
* `traceability-link-created`, `traceability-integrity-checked`
* `governance-event-recorded`
* `tool-invocation-recorded`, `agent-invocation-recorded`, `model-invocation-recorded`
* `deployment-preparation-recorded`
* `snapshot-created`, `recovery-started`, `recovery-completed`

The corresponding operational telemetry events are defined in, and referenced from, ISL v2.3 (Observability, Telemetry, and Deployment).

## 6.0 State Transition Transaction Model

State transitions often affect multiple state categories. The platform MUST prevent partial state updates from creating inconsistent conditions.

### 6.1 Transaction Requirements

A state transition affecting multiple state categories MUST be applied atomically or through a compensating transaction pattern that preserves consistency. At minimum, the platform MUST ensure that all required state updates complete successfully, or that incomplete updates are detectable and recoverable, and that recovery can determine the last consistent state.

### 6.2 Transaction Schema

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| transaction-id | string | REQUIRED | Unique transition transaction identifier |
| transaction-type | string | REQUIRED | Type of transition transaction |
| affected-state-records | array | REQUIRED | State records updated |
| affected-event-records | array | REQUIRED | Events emitted |
| initiated-by | string | REQUIRED | Subsystem, task, user, or governance action initiating transition |
| started-at | ISO 8601 | REQUIRED | Start time |
| completed-at | ISO 8601 | CONDITIONAL | Completion time |
| status | enum | REQUIRED | `pending`, `committed`, `failed`, `compensating`, `compensated` |
| recovery-action | string | CONDITIONAL | Required when transaction fails |

### 6.3 Transaction Rules

A committed transaction MUST have corresponding state events. A failed transaction MUST enter `failed` or `compensating` state. Compensating actions MUST be recorded as events. The platform MUST NOT continue execution from a failed transaction unless recovery has validated consistency.

## 7.0 Cross-State Consistency

### 7.1 Required Consistency Invariants

| Invariant | Rule |
| ----- | ----- |
| Specification-to-Canonical | Machine-Valid readiness requires a passed canonical model state |
| Readiness-to-Planning | Formal executable planning requires Machine-Valid or higher readiness |
| Readiness-to-Execution | Execution requires Autonomous-Ready readiness |
| Planning-to-Execution | Execution State MUST reference a valid executable plan |
| Task-to-Artifact | Completed artifact-producing tasks MUST reference produced artifacts |
| Artifact-to-Validation | Valid artifacts MUST reference passed or waived validation |
| Reuse-to-Planning | Applicable generation tasks MUST reference a Reuse Decision Record |
| Artifact-to-Traceability | Stable artifacts MUST have complete traceability |
| Validation-to-Repair | Failed validation triggering repair MUST reference Repair State |
| Repair-to-Artifact | Repaired artifact state MUST reference repair record |
| Governance-to-Action | Governed action MUST reference allow, approval, waiver, or override |
| Tool-to-Validation | Tool-produced validation state MUST reference tool invocation result |
| Agent-to-Output | Completed agent invocation MUST reference validated output |
| Model-to-Agent | Model invocation MUST reference agent invocation |
| Deployment-to-Artifact | Deployment preparation state MUST reference stable or approved artifacts |
| Completion-to-Evidence | Completed execution MUST reference completion report and evidence |

### 7.2 Consistency Checkpoints and Behavior

The platform MUST perform consistency checks at canonical validation completion, readiness transition, construction plan validation, execution start, task completion, artifact transition to valid or stable, validation failure, repair completion, governance approval or waiver application, artifact consolidation, deployment preparation, execution completion, recovery after interruption, and specification version change.

A consistency failure MUST produce a State Consistency Error Record. A blocking consistency failure MUST prevent the associated lifecycle action. Recovery MUST be initiated when a consistency failure is detected after interruption or crash.

## 8.0 Specification, Canonical, and Readiness State

### 8.1 Specification State

Every authored specification version MUST have a Specification State record. Specification State MUST preserve links to its canonical model versions. A specification version that reached Machine-Valid or Autonomous-Ready MUST NOT be deleted from memory while its generated artifacts remain active. A new specification version MUST NOT overwrite the prior version’s state. Key fields include `specification-id`, `specification-version`, `isl-version`, `owner`, `authored-source-reference`, `status` (from the shared Readiness Level enum plus `deprecated`, `superseded`), `current-canonical-model-version`, `supersedes-version`, `superseded-by-version`, `created-at`, and `updated-at`.

### 8.2 Canonical Model State

A canonical model with `validation-status` `failed` MUST NOT support Machine-Valid readiness. A canonical model version MUST reference exactly one specification version. A specification version MAY have multiple canonical model attempts, but only one current valid canonical model MAY be active for planning at a time. Canonical Model State MUST be preserved for historical versions used in planning or execution.

### 8.3 Readiness State

A specification version MUST have exactly one current Readiness State. Autonomous-Ready Readiness State MUST reference governance authorization. Regression MUST update Readiness State and emit a `readiness-regressed` event. Planning and execution subsystems MUST consult Readiness State before formal planning or execution. `readiness-level` uses the shared Readiness Level enum (`draft`, `reviewable`, `machine-valid`, `autonomous-ready`).

## 9.0 Planning and Execution State

### 9.1 Planning State

A plan MUST NOT be marked executable unless planning validation passed. A plan marked executable MUST reference Machine-Valid or Autonomous-Ready specification state. A plan used by active execution MUST NOT be overwritten by adaptation; adaptation MUST create a new plan version or explicitly versioned graph state. A superseded plan MUST remain queryable if it was used for execution, governance review, or audit.

### 9.2 Execution State

Execution State MUST be updated when phase, task, artifact, validation, repair, governance, tool, or agent state changes affect execution progress. Execution State MUST NOT be marked `completed` unless ISL v1.2 completion criteria are satisfied. Execution State MUST enter `recovering` during recovery validation after interruption and MUST NOT resume from `recovering` until consistency checks pass. `execution-status` uses the shared Execution Status enum from ISL v0.1 §6.12.

### 9.3 Phase State Values

| State | Meaning |
| ----- | ----- |
| `not-started` | Phase has not begun |
| `in-progress` | Phase is active |
| `completed` | Phase completed successfully |
| `skipped` | Phase was not applicable |
| `failed` | Phase failed |
| `escalated` | Phase requires governance or human action |

### 9.4 Task State

Task State MUST follow the permitted task status transitions defined in ISL v1.5. A task MUST NOT be marked `completed` if required artifacts are missing, or if required validation failed and no valid waiver exists. A failed task with generated artifacts MUST ensure those artifacts are marked `failed`, `pending`, or `non-stable`. A task in `escalated` state MUST reference at least one escalation, approval gate, waiver requirement, or governance event.

| From | To | Condition |
| ----- | ----- | ----- |
| `pending` | `in-progress` | Task assigned to agent, tool, or platform component |
| `pending` | `skipped` | Valid non-applicability or governance decision exists |
| `in-progress` | `completed` | Required outputs produced and required validation passed or waived |
| `in-progress` | `failed` | Task failed or produced invalid outputs |
| `in-progress` | `escalated` | Governance, repair, or runtime escalation required |
| `failed` | `in-progress` | Repair or retry authorized |
| `failed` | `escalated` | Repair not possible or threshold reached |
| `escalated` | `in-progress` | Escalation resolved and retry authorized |
| `escalated` | `failed` | Governance closes task as failed |
| `completed` | `pending` | Specification change or impact analysis invalidates task |
| `completed` | `skipped` | Governance or impact analysis determines task no longer applicable |

No other Task State transitions are permitted.

## 10.0 Artifact Lifecycle

This section defines the single artifact lifecycle state model. It uses the shared Artifact State enum from ISL v0.1 §6.7 and MUST NOT define a divergent artifact-state enum.

### 10.1 Artifact Lifecycle States

| State | Meaning |
| ----- | ----- |
| `candidate` | Artifact proposed by an agent, tool, repair, or import process but not yet admitted |
| `pending` | Artifact admitted to working repository state but not fully validated |
| `valid` | Artifact passed required validation |
| `failed` | Artifact failed validation or repository integrity checks |
| `repaired` | Artifact was modified through repair and requires or has completed revalidation |
| `escalated` | Artifact requires governance, human review, or manual intervention |
| `waived` | Artifact has an unresolved finding accepted by a valid governance waiver |
| `stable` | Artifact is valid or waived, traceable, and accepted into stable repository state |
| `packaged` | Artifact is included in a package or deployment bundle |
| `superseded` | Artifact has been replaced by a later version |
| `deprecated` | Artifact remains retained but SHOULD NOT be used for new construction |
| `non-deployable` | Artifact is retained for evidence, debug, review, or isolated risk handling |
| `archived` | Artifact is no longer active but retained according to retention policy |
| `deleted` | Artifact content removed according to approved retention and deletion policy |

### 10.2 Permitted State Transitions

| From | To | Condition |
| ----- | ----- | ----- |
| `candidate` | `pending` | Repository admission accepted |
| `pending` | `valid` | Required validation passed |
| `pending` | `failed` | Required validation failed |
| `pending` | `escalated` | Governance or human review required |
| `failed` | `repaired` | Repair task modifies artifact |
| `repaired` | `valid` | Revalidation passed |
| `repaired` | `failed` | Revalidation failed |
| `failed` | `escalated` | Repair limit, policy, or unresolved failure |
| `valid` | `stable` | Traceability complete and governance conditions satisfied |
| `waived` | `stable` | Waiver valid and traceability complete |
| `stable` | `packaged` | Artifact included in package |
| `stable` | `superseded` | Later artifact replaces it |
| `stable` | `deprecated` | Governance or specification evolution marks obsolete |
| `superseded` | `archived` | Retention policy archives artifact |
| `deprecated` | `archived` | Retention policy archives artifact |
| `archived` | `deleted` | Deletion permitted by retention policy |
| `escalated` | `pending` | Escalation resolved and retry authorized |
| `escalated` | `failed` | Escalation closed as failure |
| `escalated` | `waived` | Waiver granted |

A `rejected` outcome MAY be implemented as a terminal admission outcome; rejected artifacts MUST NOT be promoted. A convenience view such as `promoted-for-reuse` MUST NOT replace the artifact lifecycle state model. Promotion of an artifact into the Reusable Asset Registry MUST be recorded as both an artifact event and a reusable asset lifecycle event, and MUST satisfy ISL v1.5 rules. Reusable asset deprecation, supersession, and retirement are governed by ISL v1.5 and MUST preserve artifact metadata, validation evidence, governance records, traceability links, and dependent asset impact data.

### 10.3 Lifecycle Rules

An artifact MUST NOT enter `stable` unless `validation-status` is `passed` or `waived` and `traceability-status` is `complete`. An artifact MUST NOT enter `packaged` unless it is `stable` or explicitly approved by governance for non-deployable packaging. An artifact in `failed` state MUST NOT be packaged for deployment. An artifact in `superseded`, `deprecated`, `archived`, or `deleted` state MUST preserve metadata required for historical traceability.

## 11.0 Artifact Categories, Identifiers, and Metadata

### 11.1 Standard Artifact Types

| Artifact Type | Description |
| ----- | ----- |
| `source` | Source code or implementation logic |
| `config` | Application, runtime, tool, or environment configuration |
| `schema` | Data schema, validation schema, or persistence model |
| `infrastructure` | Infrastructure-as-code, environment definition, or resource declaration |
| `contract` | Interface, API, event, message, or integration contract |
| `test` | Unit, integration, acceptance, contract, security, or operational test |
| `documentation` | Generated system, component, API, operational, or user documentation |
| `script` | Operational, build, migration, deployment, or maintenance script |
| `package` | Compiled, bundled, containerized, or distributable output |
| `deployment` | Deployment manifest, release descriptor, environment package, or deployment bundle |
| `evidence` | Validation report, scan report, test report, coverage report, or audit evidence |
| `metadata` | Repository, artifact, package, manifest, or traceability metadata |

Every artifact MUST declare exactly one primary artifact type. An artifact MAY declare secondary classifications in metadata. Artifact type MUST determine default repository location, validation expectations, packaging eligibility, and lifecycle rules. Artifact types MAY be extended by approved extensions, but extensions MUST NOT redefine standard artifact type meanings.

### 11.2 Artifact Identifier and Naming

Artifact identifiers MUST follow the traceability object identifier pattern `ART-{system-id}-{sequence}`, using the `ART` prefix from ISL v0.1 §4.2. Artifact identity MUST be stable even when repository paths change. Repository file names SHOULD follow `{semantic-name}.{artifact-role}.{extension}`; repository paths SHOULD follow `/{domain}/{module-or-service}/{artifact-category}/{artifact-name}` unless ecosystem conventions require otherwise.

Artifact identifiers MUST NOT depend on file path. A path rename MUST preserve artifact identifier and version history. Two active artifacts MUST NOT share the same repository path in the same workspace unless the path is intentionally versioned or environment-scoped. Generated artifact names MUST be deterministic for equivalent specification input, planning configuration, and artifact naming policy.

### 11.3 Artifact Metadata Model

Every artifact MUST have an Artifact Metadata Record. Artifact metadata is the primary mechanism connecting artifact content to specification intent, construction tasks, validation evidence, repository state, and governance decisions.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| artifact-id | string | REQUIRED | Unique artifact identifier |
| artifact-name | string | REQUIRED | Human-readable or file-oriented artifact name |
| artifact-type | enum | REQUIRED | Artifact type from §11.1 |
| artifact-version | semver | REQUIRED | Artifact version |
| specification-id | string | REQUIRED | System identifier |
| specification-version | semver | REQUIRED | Specification version that produced or governs artifact |
| canonical-model-version | semver | CONDITIONAL | Canonical model version used |
| task-id | string | REQUIRED | Construction task that produced or modified artifact |
| source-entity-ids | array | REQUIRED | Specification entities or approved source references |
| derivation-type | enum | REQUIRED | From the shared Artifact Derivation Type enum (ISL v0.1 §6.8), plus `imported`, `packaged` |
| reuse-decision-id | string | CONDITIONAL | Reuse Decision Record authorizing reuse-based derivation |
| reusable-asset-ids | array | CONDITIONAL | Reusable Asset identifiers used by the artifact |
| reusable-asset-versions | array | CONDITIONAL | Asset versions selected for reuse |
| reuse-fitness-assessment-id | string | CONDITIONAL | Reuse Fitness Assessment supporting the decision |
| reuse-scope | enum | CONDITIONAL | `same-project`, `portfolio`, `enterprise`, `external`, `third-party` |
| reuse-provenance-status | enum | CONDITIONAL | `complete`, `incomplete`, `inconsistent`, `waived` |
| repository-location | string | REQUIRED | Logical or physical repository location |
| branch-id | string | CONDITIONAL | Branch or workspace containing artifact |
| content-hash | string | CONDITIONAL | Integrity hash when supported |
| created-at | ISO 8601 | REQUIRED | Artifact creation time |
| created-by | string | REQUIRED | Agent, tool, subsystem, or import process creating artifact |
| updated-at | ISO 8601 | REQUIRED | Last update time |
| current-state | enum | REQUIRED | Lifecycle state from §10.1 (shared ISL v0.1 §6.7 enum) |
| validation-status | enum | REQUIRED | `not-evaluated`, `passed`, `failed`, `warning`, `waived` |
| traceability-status | enum | REQUIRED | `complete`, `incomplete`, `inconsistent` |
| governance-status | enum | REQUIRED | `not-required`, `pending`, `approved`, `waived`, `blocked` |
| supersedes-artifact-id | string | CONDITIONAL | Prior artifact replaced |
| superseded-by-artifact-id | string | CONDITIONAL | Later artifact replacing this artifact |
| retention-class | enum | REQUIRED | `transient`, `operational`, `audit`, `archival` |

Artifact metadata MUST be created before or at the time artifact content is admitted to the repository and MUST be updated when artifact state changes. Artifact metadata MUST NOT be silently overwritten. A metadata change affecting validation, traceability, governance, repository location, version, or state MUST emit a state event.

Artifacts with derivation-type `reused`, `reuse-delta`, `wrapper`, `extension`, or `composition` MUST include reuse metadata sufficient to reconstruct the selected asset, decision authority, fitness rationale, and generated delta. Missing reuse metadata MUST block stable promotion unless a governance waiver explicitly authorizes the exception and marks the artifact non-deployable or waived according to ISL v1.2. Reusable asset provenance MUST remain queryable after supersession, deprecation, archival, or retirement.

## 12.0 Repository Structure and Modularity

A conforming implementation SHOULD organize repository content using the standard top-level layout `/specification`, `/canonical`, `/services`, `/data`, `/interfaces`, `/workflows`, `/tests`, `/infrastructure`, `/deployment`, `/docs`, `/scripts`, `/evidence`, and `/metadata`. Service artifacts SHOULD be organized as `/services/{service-name}/{source|config|tests|contracts|docs|metadata}`.

Repository layout MUST be deterministic based on artifact type, service/module ownership, and repository policy. A repository MAY use ecosystem-native layout conventions when required, but MUST preserve artifact metadata and traceability. Artifacts belonging to distinct services or modules SHOULD be isolated to support parallel construction and impact-based regeneration. Repository layout MUST support bidirectional navigation between artifact metadata and file location.

## 13.0 Admission and Promotion

### 13.1 Admission

Admission is the controlled process of accepting a candidate artifact into working repository state. Admission does not mean the artifact is valid; it means the repository recognizes and controls the artifact.

Artifacts MAY be admitted from Implementation Generator output, Test Generator output, Deployment Preparer output, tool-generated output, repair task output, imported external artifact, human-authored artifact registered through governance, or platform-generated metadata or evidence. The repository MUST check artifact identifier validity, artifact type validity, metadata completeness, source task or source reference validity, repository location availability, path collision risk, traceability source presence, content availability, governance constraints, and (where required by policy) malware or unsafe content.

A candidate artifact MUST NOT become `pending` unless admission checks pass. An artifact with missing metadata MUST be rejected or escalated. An artifact with no source reference MUST be rejected unless imported under governance-controlled registration. An artifact with a path collision MUST enter conflict handling.

### 13.2 Promotion

Promotion moves artifacts from working state into `stable`, package-ready, or release-ready state. An artifact MAY be promoted to `stable` only when metadata is complete; state is `valid` or `waived`; `validation-status` is `passed` or `waived`; `traceability-status` is `complete`; `governance-status` is `approved`, `waived`, or `not-required`; reuse-provenance-status is complete or not applicable; repository integrity checks pass; no unresolved merge conflict exists; and content hash or an equivalent integrity marker is recorded where supported.

Promotion MUST emit an `artifact-state-changed` event, MUST update artifact metadata, and MUST be traceable to validation and governance evidence. Promotion to `stable` MUST be blocked when required evidence is missing. Promotion of a reuse-derived artifact MUST be blocked when the selected asset has been retired, deprecated without approval, superseded without impact analysis, or classified outside the authorized reuse scope.

## 14.0 Versioning, Supersession, and Version Control

### 14.1 Versioning and Supersession

Each artifact MUST have an `artifact-version`, using semantic versioning unless an ecosystem-specific scheme is explicitly mapped. A semantic change to artifact behavior SHOULD increment minor or major version; a non-behavioral correction MAY increment patch version. A repair that changes artifact content MUST create a new artifact version or preserve prior content hash and repair history.

When one artifact replaces another, the new artifact MUST reference `supersedes-artifact-id` and the replaced artifact MUST reference `superseded-by-artifact-id`. Supersession MUST create a traceability edge. Superseded artifacts MUST remain queryable and MUST NOT be used for new packaging unless a rollback or governance-approved reconstruction requires it.

### 14.2 Version Control Integration

Version control MAY be Git or another system with equivalent history, branching, merging, and commit identity, but MUST preserve the required artifact semantics. Version control commits MUST NOT substitute for artifact metadata; artifact metadata remains authoritative for lifecycle state. Version control history SHOULD be linked to artifact version records. Each commit SHOULD include specification-id, specification-version, plan-id, task-id, artifact-ids, agent-or-tool-id, validation-status, traceability-snapshot-id, and governance-record-id where applicable. A commit that modifies artifacts without corresponding artifact metadata updates MUST produce a repository integrity finding.

## 15.0 Branching, Merge, and Conflict Handling

### 15.1 Branching Strategy

Branching isolates generated changes, repairs, reviews, release preparation, and deployment packaging for parallel construction.

| Branch Type | Purpose |
| ----- | ----- |
| `main` | Stable accepted repository state |
| `construction` | Active artifact generation for a specification version |
| `task` | Isolated work for a construction task |
| `repair` | Isolated repair attempt for a failed artifact |
| `review` | Human or agent review workspace |
| `release` | Release or package preparation |
| `hotfix` | Urgent correction to stable or released artifact |
| `experiment` | Non-production exploratory generation |

Branches SHOULD follow `{branch-type}/{specification-id}/{specification-version}/{task-or-purpose}`. Stable artifacts MUST reside in `main` or a governed stable-equivalent branch. Construction work MUST occur outside `main` until promotion. A branch MUST reference specification-id and specification-version. A branch merge into `main` MUST require promotion checks. High and Critical risk systems SHOULD require governance approval before merging release branches into `main`.

### 15.2 Merge and Conflict Handling

Merge conflicts MUST be treated as controlled lifecycle events. Conflict types include `path-conflict`, `content-conflict`, `metadata-conflict`, `version-conflict`, `traceability-conflict`, `validation-conflict`, `governance-conflict`, `package-conflict`, and `deletion-conflict`.

A blocking conflict MUST prevent merge. A metadata conflict MUST be resolved before artifact promotion. A traceability conflict MUST be resolved before `stable` state. A validation conflict MUST be resolved in favor of `failed` or `warning` unless governance approves a waiver or evidence proves the passing result applies to the current artifact version. A governance conflict MUST be routed to the Governance Engine. A content conflict MAY be resolved by agent-assisted merge only if deterministic validation is required afterward. Artifacts affected by merge conflict resolution MUST be revalidated unless governance explicitly records why validation is not applicable. A merge into stable repository state MUST NOT complete while affected artifacts are `pending`, `failed`, or traceability-incomplete.

### 15.3 Repository State and Locking

Repository state MUST match artifact metadata records. An artifact file present without metadata MUST produce an `unmanaged-artifact` finding; metadata for missing artifact content MUST produce a `missing-artifact` finding. The stable branch MUST contain only `stable`, `packaged`, `deprecated`, `superseded`, or `archived` artifacts unless governance permits another state. Pending, failed, or escalated artifacts MUST NOT appear as stable outputs.

The repository MAY lock branches, paths, artifacts, or packages during high-risk operations including promotion to stable, merge into main, release packaging, deployment package creation, repository recovery, destructive cleanup, and archive transition. A lock MUST record owner, scope, start time, and expiry or release condition.

## 16.0 Validation, Repair, and Evidence

### 16.1 Validation State

A failed validation linked to a must-have requirement MUST block task completion unless a valid waiver exists. A timeout MUST NOT be converted into `passed` state. A waived validation MUST reference an active waiver record. A failed validation that triggers repair MUST reference the associated Repair Record.

### 16.2 Repair State

Repair State MUST enforce the repair iteration limits defined by ISL v1.2. A repair MUST NOT be marked `resolved` without successful revalidation or a valid waiver. A repair reaching the escalation threshold MUST enter `escalated` state. Repair history MUST be preserved even when repair fails or is abandoned.

### 16.3 Validation Evidence

A stable artifact MUST have evidence supporting `validation-status` `passed` or `waived`. Evidence records MUST reference the artifact version evaluated. Evidence for one artifact version MUST NOT be reused for another version unless impact analysis proves applicability and governance permits reuse. Security evidence for High or Critical systems MUST be retained according to governance retention rules.

## 17.0 Memory: Working and Persistent

### 17.1 Working Memory

Working memory MAY include task-scoped context packages, active agent prompt context, temporary validation working files, runtime scheduling queues, in-progress tool outputs, and transient repair analysis context. Working memory MUST NOT be the only storage location for records required for traceability, audit, governance, recovery, or artifact reconstruction.

### 17.2 Persistent Memory

Persistent memory MUST include specification versions; canonical model versions; readiness records; construction plans; execution graphs; task state history; artifact metadata and versions; validation results; repair records; governance records; traceability graphs and snapshots; agent invocation and output records; tool invocation and evidence records; model invocation metadata; configuration history; and audit logs.

### 17.3 Memory Promotion

When working memory produces lifecycle-relevant output, the platform MUST promote that output into persistent memory before it affects downstream state. For example, an agent output used for implementation MUST be persisted, a validation result used for artifact acceptance MUST be persisted, a governance decision used for continuation MUST be persisted, and a repair proposal used to modify an artifact MUST be persisted.

## 18.0 Snapshots and Checkpoints

### 18.1 Snapshot Types

| Snapshot Type | Description |
| ----- | ----- |
| `specification-snapshot` | Captures authored specification state |
| `canonical-snapshot` | Captures canonical model state |
| `planning-snapshot` | Captures construction plan and task graph state |
| `execution-snapshot` | Captures execution graph and runtime state |
| `artifact-snapshot` | Captures artifact metadata and status |
| `traceability-snapshot` | Captures traceability graph state |
| `governance-snapshot` | Captures governance state |
| `repository-snapshot` | Captures artifact repository content and metadata |
| `full-platform-snapshot` | Captures coordinated state across all required categories |

### 18.2 Required Snapshot Triggers

The platform MUST create snapshots at Machine-Valid readiness, Autonomous-Ready authorization, execution start, before artifact consolidation, after artifact consolidation, before deployment authorization, execution completion, before major plan adaptation, after specification version change, before destructive or superseding operations, and at governance-required audit points. Repository snapshots SHOULD additionally be created before a merge into stable branch, before artifact promotion, before package creation, before deployment preparation, before destructive cleanup, and before and after major repair waves.

A snapshot record MUST include `snapshot-id`, `snapshot-type`, `included-state-categories`, `created-at`, `created-by`, `storage-location`, `integrity-hash` (when supported), `recovery-eligible`, and `retention-class` (`transient`, `operational`, `audit`, `archival`).

### 18.3 Checkpoint Rules

Execution checkpoints MUST be created before and after high-impact operations including task graph adaptation, artifact consolidation, deployment preparation, high-risk repair, governance override application, repository branch merge or promotion, and execution halt or suspension. A recovery-eligible checkpoint MUST include enough information to resume or safely roll back affected execution state.

## 19.0 Recovery and Resumption

Recovery must not guess; it must reconstruct and validate platform state before continuing.

### 19.1 Recovery Preconditions

The platform MAY attempt recovery only if durable state records, event logs, and latest relevant snapshots are accessible or reconstructible; governance, traceability, and artifact repository state are accessible; configuration state is known; and the recovery actor or subsystem has authority to perform recovery.

### 19.2 Recovery Process

Recovery MUST proceed through: enter `recovering` state; load latest durable state records; load latest relevant snapshots; replay or inspect state events after the snapshot; validate state transition transactions; check cross-state consistency invariants; compare artifact repository state with artifact metadata; compare traceability graph with artifact and task state; compare governance state with pending or blocked actions; determine a safe resume point, rollback action, or escalation; record a Recovery Event; then resume, halt, or escalate according to the outcome.

### 19.3 Recovery Outcomes

| Outcome | Meaning |
| ----- | ----- |
| `resume-safe` | State is consistent and execution may resume |
| `rollback-required` | State must return to a prior checkpoint |
| `compensation-required` | Compensating state updates are required |
| `manual-review-required` | Human or governance review is required |
| `halt-required` | Execution cannot continue safely |
| `state-corrupt` | State is inconsistent or unrecoverable |

### 19.4 Recovery Rules

The platform MUST NOT resume execution unless the recovery-outcome is `resume-safe`. If `rollback-required`, rollback MUST use a recovery-eligible checkpoint. If `compensation-required`, compensating actions MUST be recorded as state events. If `manual-review-required`, the platform MUST open an escalation. If state is corrupt, the platform MUST halt affected execution and notify governance or operations.

## 20.0 Memory Access for Agents and Subsystems

Memory access MUST be controlled, contextual, and traceable. Agents MUST access memory through context packages or approved memory access interfaces and MUST NOT query unrestricted platform memory directly. Memory provided to agents MUST be scoped to task, role, governance constraints, and sensitivity classification. Memory access MUST be recorded when it includes restricted, confidential, governance, security, or artifact content.

A Memory Access Request records `requester-id`, `requester-type` (`agent`, `subsystem`, `user`, `governance-role`, `tool`), `task-id`, `specification-id`, `requested-state-categories`, `sensitivity-maximum`, and `access-purpose`. The Memory Access Response records the `decision` (`allow`, `deny`, `partial`, `approval-required`, `redact`), provided/redacted/denied records, `rationale`, and `decided-at`. Repository access for platform components additionally enforces role and branch constraints; agents MUST NOT write directly to stable repository state, and tools MUST write outputs only to approved workspace or evidence locations.

## 21.0 Retention and Archival

### 21.1 Minimum Retention

The platform MUST retain, for the life of any generated system unless governance defines longer retention: specification versions that reached Machine-Valid or Autonomous-Ready; canonical model versions used for planning or execution; readiness transition records; construction plans used for execution; execution graphs and completion reports; artifact metadata and artifact version history; validation results supporting artifact acceptance; repair records; traceability snapshots; governance approvals, waivers, overrides, escalations, and audit logs; deployment authorization records; and configuration versions active during execution. Artifacts that reached `stable` MUST be retained while the generated system remains active; artifacts included in deployment packages MUST be retained with package manifest and evidence.

### 21.2 Archival and Deletion

Archived records MUST remain discoverable by identifier and MUST preserve traceability and audit relationships. Archived records MUST NOT be required for active execution unless restored or rehydrated. Archival MUST NOT break readiness, governance, traceability, or artifact audit queries for retained systems.

Deletion of state, memory, or artifact records MUST be governed by retention policy. Records required for audit, traceability, legal compliance, or active artifact reconstruction MUST NOT be deleted. Deletion events MUST themselves be auditable when deletion is permitted. Deletion MUST produce a deletion record, and deleted artifact metadata MUST remain discoverable when required for audit.

## 22.0 Integrity Checks

### 22.1 Required Checks

A conforming platform MUST check: state record schema validity; event sequence continuity; allowed state transitions; cross-state consistency invariants; snapshot integrity; traceability link presence; governance audit record presence; artifact metadata and repository consistency; validation-to-artifact consistency; repair-to-validation consistency; configuration version consistency; and active execution recoverability.

Repository integrity checks MUST additionally verify artifact files have metadata; metadata references existing artifact content; artifact identifiers are valid; repository paths are unique within branch scope; artifact states are valid; stable artifacts have complete traceability and validation evidence; package manifests reference existing artifact versions; branch merge records exist for stable merges; supersession relationships are bidirectional; archived artifacts remain discoverable by metadata; and governance-controlled operations have governance records.

### 22.2 Integrity Failure Rules

A failed integrity check affecting active execution MUST pause or halt affected execution until recovery or governance resolution. A failed integrity check affecting governance audit records MUST block governance-controlled actions. A failed integrity check affecting stable artifacts MUST prevent deployment authorization. An unmanaged artifact in the stable branch, a missing artifact referenced by metadata, or a traceability-incomplete stable artifact MUST be treated as blocking.

## 23.0 Artifact Impact Analysis

Artifact impact analysis determines which artifacts must be regenerated, revalidated, repaired, repackaged, or retired when specifications, plans, dependencies, tools, policies, or infrastructure expectations change.

Impact analysis MUST occur when a source specification entity changes, an interface contract changes, a data schema changes, a policy rule changes, an infrastructure expectation changes, a tool version or validation profile changes materially, a dependency changes, a package composition changes, a branch merge modifies artifact content, or a repair supersedes an artifact.

An Artifact Impact Result records `trigger-id`, `affected-artifacts`, `unaffected-artifacts`, `required-actions` (`regenerate`, `revalidate`, `repair`, `repackage`, `retire`, `no-action`), and `confidence` (`complete`, `partial`, `uncertain`). Artifacts identified as affected MUST NOT remain stable without required action. Artifacts identified as unaffected MUST NOT be regenerated unless governance or runtime configuration requires full reconstruction. Partial or uncertain impact analysis MUST trigger review or escalation before selective reconstruction.

## 24.0 Artifact Security and Sensitivity

Artifact security and sensitivity follow the shared Sensitivity Classification enum from ISL v0.1 §6.3 (`public`, `internal`, `confidential`, `restricted`). Artifacts MUST NOT contain secrets unless explicitly permitted by specification and governance policy. Configuration artifacts MUST be scanned for secrets before stable promotion when secret detection tools are available. Restricted artifacts MUST require controlled access. Security findings affecting artifacts MUST link to artifact metadata and validation evidence. Artifact access MUST obey governance and security policies.

## 25.0 Error Model

State, memory, recovery, and repository errors use the common Error Record structure and error-class taxonomy from ISL v0.1 §8. The following extension error classes are registered under the shared taxonomy categories and MUST NOT replace the shared base model.

### 25.1 State, Memory, and Recovery Errors (registered under `runtime`)

| Error Class | Description | Default Handling |
| ----- | ----- | ----- |
| `state-record-missing` | Required state record is missing | halt or recover |
| `state-record-invalid` | State record fails schema validation | halt affected action |
| `state-transition-invalid` | Attempted transition is not permitted | reject transition |
| `state-event-missing` | Required state event was not recorded | recover or escalate |
| `state-event-sequence-gap` | Event sequence has gap or inconsistency | recover or escalate |
| `state-transaction-failed` | Multi-state transition transaction failed | recover or compensate |
| `state-consistency-failed` | Cross-state invariant failed | pause or halt |
| `state-snapshot-missing` | Required snapshot is missing | warning, escalate, or halt |
| `state-snapshot-corrupt` | Snapshot integrity check failed | recover from prior snapshot or halt |
| `state-recovery-failed` | Recovery could not produce safe state | halt |
| `memory-access-denied` | Memory access request denied | block request |
| `memory-access-violation` | Unauthorized memory access attempted | block and escalate |
| `memory-retention-violation` | Required memory record missing or deleted early | escalate |
| `governance-state-unavailable` | Governance state unavailable for governed action | halt governed action |
| `traceability-state-inconsistent` | Traceability state conflicts with artifact/task state | pause or halt |

### 25.2 Repository Errors (registered under `repository`)

| Error Class | Description | Default Handling |
| ----- | ----- | ----- |
| `repository-artifact-metadata-missing` | Artifact content lacks metadata | block promotion |
| `repository-artifact-content-missing` | Metadata references missing content | block promotion or recover |
| `repository-artifact-untraced` | Artifact lacks required traceability | block stable state |
| `repository-artifact-validation-missing` | Artifact lacks required validation evidence | block promotion |
| `repository-artifact-validation-failed` | Artifact validation failed | repair or escalate |
| `repository-artifact-state-mismatch` | Artifact metadata differs from repository state | recover or halt |
| `repository-path-conflict` | Multiple artifacts conflict on path | conflict handling |
| `repository-merge-conflict` | Branch merge conflict detected | block merge |
| `repository-metadata-conflict` | Metadata differs incompatibly | block merge or promote |
| `repository-version-conflict` | Artifact version conflict detected | block promotion |
| `repository-branch-policy-violation` | Branch rule violated | block operation |
| `repository-stable-state-violation` | Invalid artifact present in stable state | halt or recover |
| `repository-package-invalid` | Package manifest invalid or incomplete | block package |
| `repository-evidence-missing` | Required evidence absent | block promotion or deployment |
| `repository-retention-violation` | Required artifact or evidence removed early | escalate |
| `repository-access-denied` | Access request denied | block request |
| `repository-integrity-failed` | Integrity check failed | halt affected action |
| `repository-recovery-failed` | Recovery could not restore consistent state | halt and escalate |
| `repository-reuse-metadata-missing` | Reuse-derived artifact lacks reuse metadata | block promotion |
| `repository-reuse-scope-violation` | Artifact uses asset outside authorized reuse scope | block promotion or package |
| `repository-retired-asset-used` | Artifact depends on retired reusable asset | block reuse and trigger impact analysis |

### 25.3 Error Record

All errors use the common Error Record from ISL v0.1 §8.1, with domain-specific identifier fields (`state-category`, `repository-id`, `branch-id`, `artifact-id`, `task-id`) carried as applicable evidence and target identifiers.

## 26.0 Conformance

### 26.1 State and Memory Conformance

A state record conforms if it includes required base fields, uses a defined state category, identifies the object represented, preserves specification-version context where applicable, uses valid current-state values for its category, preserves update timestamp and actor, and supports correlation with events or traceability where required. A memory store conforms if it persists required records, preserves historical and versioned records, supports event-log storage, supports snapshots, supports identifier-based retrieval and lifecycle audit queries, enforces access control, and preserves integrity for governance, traceability, and audit records. A state manager conforms if it applies allowed transitions, rejects invalid transitions, emits state events, manages transition transactions, enforces cross-state consistency, creates checkpoints and snapshots, performs integrity checks, initiates recovery when required, preserves state history, and exposes state query interfaces. A recovery mechanism conforms if it enters `recovering`, loads durable records and snapshots, replays or inspects state events, validates transition transactions, checks consistency, compares repository and metadata state, determines safe resume/rollback/compensation/escalation/halt outcomes, records recovery events, and prevents unsafe resumption.

### 26.2 Artifact Repository Conformance

An Artifact Repository conforms if it stores artifacts and metadata; enforces admission checks; enforces lifecycle state transitions using the shared Artifact State enum; preserves artifact versions; supports repository structure conventions; supports branch isolation; detects and records merge conflicts; preserves validation evidence and traceability associations; preserves reuse decision, reusable asset, and reuse fitness associations; supports packaging; enforces retention and archival rules; performs integrity checks; and supports required repository queries. A repository manager conforms if it admits candidates, rejects invalid artifacts, promotes to stable only when eligible, manages versioning and supersession, manages branch and merge operations, blocks invalid merges, associates artifacts with validation evidence and governance records, blocks reuse-derived promotion when reuse provenance is incomplete, creates repository snapshots, recovers repository state, emits telemetry, and produces repository error records.

### 26.3 Platform Conformance

A platform conforms to this document if it maintains all required state categories; distinguishes working from persistent memory; persists lifecycle-critical memory; enforces state transition and cross-state consistency rules; supports recovery after interruption; controls memory access; preserves governance, traceability, validation, and artifact memory; supports snapshots and checkpoints; supports retention and archival rules; exposes required audit queries; routes all generated artifacts through the Artifact Repository; prevents agents from directly writing stable artifacts; prevents untraced or unvalidated artifacts from entering stable state; integrates repository state with the State and Memory Model, Traceability Engine, Governance Engine, and Reusable Asset Registry; and preserves repository auditability and lifecycle history.
