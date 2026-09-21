# ALETHEIA Specification Language (ISL) v1.5
# Construction Planning and Reuse Model
**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0, ISL v1.1
**Supersedes:** ISL r2 v1.5 The Construction Planning Model, ISL r2 v1.8 The Reusable Asset and Construction Reuse Model
**Document Type:** Construction Planning and Reuse Model Specification

---

## 1.0 Scope

This document defines the Construction Planning and Reuse Model used by the ALETHEIA platform to transform a validated canonical ISL specification into an executable construction task graph, and to make governed reuse of validated assets the default posture of autonomous construction.

The Construction Planning Model converts the canonical specification graph into a structured plan that the execution runtime can schedule and execute. A valid specification describes what the system must be; a construction plan defines the work required to produce it. The Reuse Model establishes reusable assets as first-class specification, planning, traceability, governance, and validation objects, and requires reuse discovery before generation.

This document is the single authoritative definition of **Construction Boundaries and Connection Contexts** in the ISL r3 corpus. Other r3 documents reference this document for the boundary and connection-context model rather than redefining it.

This document defines planning preconditions, inputs, and outputs; construction task schema and classification; task generation and dependency resolution; the Construction Task Graph; verification and artifact production planning; parallel planning and critical path; Construction Boundaries and Connection Contexts; repair task generation; the Reusable Asset Registry; reuse discovery, fitness assessment, and reuse decisions; asset promotion, deprecation, supersession, and retirement; duplication detection; governance constraints, plan validation, monitoring, and adaptation; plan versioning, execution handoff, and completion; and conformance requirements.

---

## 2.0 Planning and Reuse Principles

A conforming implementation MUST enforce the following principles.

* **Specification-Derived Planning** — Every construction task MUST be derived from one or more canonical specification entities, platform-required lifecycle obligations, or governance-required controls. The Planning Engine MUST NOT create arbitrary construction work without traceability to specification intent or a documented platform rule.
* **Explicit Task Decomposition** — Construction work MUST be decomposed into explicit tasks. Each task MUST have a type, source entity, assigned role, dependencies, expected outputs, and status. A plan that contains vague work items such as "build system," "implement backend," or "add security" MUST NOT be considered valid unless those items are decomposed into executable tasks.
* **Dependency Safety** — The Planning Engine MUST resolve task dependencies before execution begins. A task MUST NOT be scheduled before the tasks it depends on have completed or been waived under governance rules.
* **Verification by Design** — Verification MUST be planned as part of construction. It MUST NOT be treated as an optional post-processing step. Every task that produces executable, structural, contractual, infrastructural, or policy-relevant artifacts MUST have associated verification tasks.
* **Reuse Before Generation** — For implementation, schema, interface, contract, infrastructure, validation, and policy tasks, the Planning Engine MUST perform reuse discovery before full generation is authorized. Generation is permitted only when reuse discovery determines that no suitable approved asset exists, or when governance approves delta generation, wrapping, extension, or replacement.
* **Reuse Is Governed** — Reusable assets are not informal snippets. Promotion, dependency creation, deprecation, retirement, and cross-boundary or cross-project use MUST be governed according to asset risk, sensitivity, validation status, ownership, and enterprise policy.
* **Reuse Is Traceable** — Every artifact that reuses, extends, wraps, adapts, or composes a reusable asset MUST record traceability to that asset and to the reuse decision that authorized the dependency.
* **Reuse Must Not Increase Drift** — Reuse MUST preserve the active Construction Boundary. A reusable asset MUST NOT introduce behavior, dependencies, data exposure, assumptions, or architectural responsibilities outside the authorized boundary unless a valid Connection Context and governance decision authorize the crossing.
* **Reuse Fitness Is Explicit** — The platform MUST assess asset fitness using structured criteria. A semantic match MUST NOT be accepted solely because an asset name or textual description appears similar to the requirement.
* **Controlled Adaptability** — The construction plan MUST support adaptation when the specification changes or when execution introduces repair tasks. Adaptation MUST be controlled by traceability, impact analysis, and governance rules. The platform MUST NOT restart the entire plan when only affected tasks require re-execution.

---

## 3.0 Planning Preconditions

### 3.1 Formal Planning Preconditions

The Planning Engine MUST NOT generate an executable construction plan unless all of the following are true: the specification has reached Machine-Valid or higher readiness (readiness record, enforced by ISL v1.3); canonical normalization completed without blocking errors (canonical validation report, ISL v1.1); the canonical graph contains required entities and relationships (canonical model, ISL v1.1); the traceability graph is initialized (traceability state, ISL v1.4); planning governance constraints are available (governance state, ISL v1.3); required construction profiles are known (planning configuration, platform implementation); and tool capability needs can be identified (tool capability model, ISL v1.2).

### 3.2 Advisory Planning

For Draft or Reviewable specifications, the Planning Engine MAY produce advisory planning output. Advisory output MUST be clearly marked non-executable. Advisory planning MAY identify missing specification sections, likely task categories, missing validation coverage, dependency risks, tool capability needs, governance issues, and readiness blockers. Advisory planning MUST NOT be used by the execution runtime.

### 3.3 Precondition Failure

If formal planning preconditions fail, the Planning Engine MUST produce a Planning Precondition Failure Record. Each record MUST include: failure-id (unique failure identifier); specification-id (system identifier); specification-version (specification version); failed-precondition (precondition that failed); severity (severity from the common error model); evidence (missing or failed evidence, conditional); required-action (remediation required); and recorded-at (time the failure was recorded).

---

## 4.0 Planning Inputs and Outputs

### 4.1 Required Inputs

The required planning inputs are: Canonical Model (ISL v1.1 canonical specification graph, required); Specification Version (version being planned, required); Readiness Record (evidence that planning is permitted, required); Traceability State (existing traceability graph or initialized traceability state, required); Governance State (policies, risk tier, waivers, planning constraints, required); Tool Capability Catalog (required for verification task planning, conditional); Planning Configuration (planning rules, task granularity, supported artifact types, required); Prior Construction Plan (required for adaptation or replanning, conditional); and Impact Analysis Result (required when planning follows specification change, conditional).

### 4.2 Input Consistency Rules

All planning inputs MUST reference the same specification identifier and specification version unless the Planning Engine is performing version comparison or impact-based adaptation. If planning uses a prior construction plan, the Planning Engine MUST verify whether the prior plan was derived from the immediately preceding specification version or another explicitly declared baseline. A planning input version mismatch MUST produce a blocking planning validation error.

### 4.3 Required Outputs

The required planning outputs are: the Construction Task Graph (executable directed graph of construction tasks); the Planning Record (summary of the planning operation); Task Definitions (structured records for every construction task); Dependency Records (explicit dependency edges between tasks); the Critical Path (longest dependency chain); Parallel Groups (sets of tasks eligible for concurrent execution); the Verification Plan (verification tasks and tool capability requirements); the Artifact Production Plan (expected artifact outputs and traceability associations); the Planning Validation Report (result of validating the construction plan); Traceability Updates (links from entities to tasks and planning decisions); and the Execution Readiness Summary (whether the plan is eligible for runtime execution).

### 4.4 Planning Record Schema

Each planning record MUST include: planning-record-id (unique planning operation identifier); plan-id (construction plan identifier); specification-id (system identifier); specification-version (specification version planned); canonical-model-version (canonical model version); planning-mode (`advisory`, `formal`, `adaptation`); generated-at (time the plan was generated); generated-by (Planning Engine version or component); outcome (`generated`, `failed`, `partially-generated`); and validation-report-id (planning validation report reference, conditional).

---

## 5.0 Construction Task Definition

Every executable construction activity must be represented as a task. Tasks provide the runtime with enough information to assign work, schedule dependencies, invoke agents or tools, verify outputs, and monitor progress.

### 5.1 Base Task Schema

Every construction task MUST conform to the following schema: task-id (unique task identifier within the construction plan); task-type (task type from §6.0); source-entity-ids (specification entities or platform rules originating the task); description (human-readable description of work); assigned-agent-role (required when the task requires reasoning or generation); required-tool-capabilities (tool capabilities required by the task); dependencies (task identifiers that MUST complete before the task begins); artifacts-produced (artifact types expected from the task); artifacts-consumed (artifact identifiers or artifact types consumed); verification-tasks (associated verification task identifiers); governance-constraints (policy or approval constraints applying to the task); priority (`critical`, `high`, `medium`, `low`); status (`pending`, `in-progress`, `completed`, `failed`, `escalated`, `skipped`); created-at (timestamp of task creation); and updated-at (timestamp of last task update, conditional).

### 5.2 Task Identifier Format

Task identifiers MUST use the traceability object identifier format defined in ISL v1.4 and the `TSK` prefix from ISL v0.1 §4. Recommended format: `TSK-{system-id}-{sequence}`. Example: `TSK-acme-order-00001`.

### 5.3 Task Description Rules

A task description MUST describe a single unit of work and MUST be specific enough for an execution runtime, agent, tool, or human reviewer to understand expected activity. Invalid task descriptions include "Build the app," "Add backend," "Do validation," and "Make it secure." Valid task descriptions include "Generate the Order data model schema from DAT-acme-order-00001" and "Validate generated Order API contract using OpenAPI validation."

### 5.4 Source Entity Rules

Every task MUST have at least one source-entity-id unless the task is a platform-required lifecycle task. Platform-required lifecycle tasks MUST use a source reference to the rule or phase that caused the task to exist. Examples include artifact consolidation, deployment preparation, deterministic validation, and traceability snapshot creation.

---

## 6.0 Task Classification

Task type determines agent role assignment, expected outputs, validation behavior, and runtime treatment. A conforming Planning Engine MUST use the task types defined here unless an approved extension defines additional task types.

### 6.1 Task Types

The supported task types and their primary agent roles are: `interpretation` (Specification Interpreter); `architecture` (Architecture Planner); `planning` (Construction Planner); `reuse-discovery` (Construction Planner); `implementation` (Implementation Generator); `schema` (Implementation Generator); `interface` (Implementation Generator); `infrastructure` (Deployment Preparer); `test` (Test Generator); `verification` (Review Agent); `security` (Security Validator); `repair` (Repair Analyst); `documentation` (Implementation Generator); `consolidation` (Execution Runtime); `deployment-preparation` (Deployment Preparer); `traceability` (Traceability Engine); and `governance` (Governance Engine).

### 6.2 Task Type Rules

A task MUST use exactly one primary task type. A task MAY include secondary classifications in metadata, but runtime scheduling MUST use the primary task type. A task type MUST be compatible with its assigned-agent-role and required-tool-capabilities.

A repair task MUST NOT be generated during initial planning unless it represents a planned repair review for a known existing artifact. Standard repair tasks are generated dynamically during execution.

### 6.3 Agent Role Mapping

A task requiring reasoning MUST include an assigned-agent-role. A task performed entirely by deterministic tools MAY omit assigned-agent-role if required-tool-capabilities are declared. A task performed by a platform subsystem MUST identify the responsible component. A governance-constrained task MUST reference the applicable policy or governance checkpoint.

---

## 7.0 Task Generation Rules

Task generation must be deterministic enough that a conforming Planning Engine can produce repeatable plans for the same specification, configuration, and ISL version.

### 7.1 General Task Generation Rule

For each canonical entity that requires construction, validation, policy enforcement, documentation, or deployment preparation, the Planning Engine MUST generate one or more tasks. The Planning Engine MUST NOT generate tasks for deprecated or superseded entities unless required for migration, cleanup, traceability, or governance review.

### 7.2 Entity-to-Task Mapping

The required task categories per canonical entity are: Project (interpretation, planning, documentation, consolidation, deployment-preparation); Context (architecture, verification, documentation); Stakeholder (governance, documentation); Actor (interface, security, test); Requirement (implementation, test, verification); Capability (architecture, implementation, test); Service (architecture, implementation, interface, verification, documentation); DataEntity (schema, implementation, verification, security); Workflow (implementation, test, verification, documentation); Interface (interface, implementation, verification, security); Policy (security, verification, governance); Infrastructure (infrastructure, verification, deployment-preparation); Validation (test, verification); and ReusableAsset (reuse-discovery, verification, governance).

### 7.3 Required Generation Rules

A must-have Requirement MUST produce or be linked to at least one implementation or verification path.

Any Requirement, Capability, Service, DataEntity, Interface, Policy, Infrastructure, or Validation entity that may produce implementation, schema, interface, contract, infrastructure, policy, or validation artifacts MUST produce or be linked to a reuse-discovery task before generation tasks are executable.

A DataEntity MUST produce schema-related tasks unless explicitly marked transient and non-structural. A Service MUST produce implementation and verification tasks. An Interface MUST produce contract generation or contract validation tasks. A Policy MUST produce policy evaluation or security validation tasks. An Infrastructure entity MUST produce infrastructure validation or deployment preparation tasks. A Validation entity MUST produce verification or test tasks consistent with its validation type.

### 7.4 Task Granularity

The Planning Engine SHOULD generate tasks at a granularity that supports independent validation, targeted repair, traceable artifact production, parallel execution, and impact-based replanning. Tasks that are too broad reduce repair precision and traceability; tasks that are too small may increase scheduling overhead. Planning configuration MAY control task granularity within the constraints of this model.

---

## 8.0 Dependency Resolution

A valid construction plan MUST resolve all dependencies before execution begins.

### 8.1 Required Dependency Rules

The Planning Engine MUST enforce the following dependency rules: interpretation tasks MUST complete before plan confirmation; architecture tasks MUST complete before implementation tasks dependent on structural decisions; schema tasks MUST complete before implementation tasks that consume schema artifacts; interface tasks MUST complete before implementations exposing those interfaces; policy interpretation tasks MUST complete before policy validation tasks; verification tasks MUST run after the task producing the artifact; test execution MUST occur after relevant implementation artifacts exist; repair tasks MUST be triggered by failed verification or test outcomes; repaired artifacts MUST be validated again before acceptance; consolidation MUST NOT begin until required artifacts are valid, waived, or escalated under governance; and deployment preparation MUST depend on consolidated project state.

### 8.2 Dependency Types

| Dependency Type | Meaning |
| --------------- | ------- |
| hard | Target task MUST complete successfully before source task begins |
| soft | Target task SHOULD complete first but MAY be bypassed with justification |
| governance | Target approval or policy decision MUST exist before source task begins |
| artifact | Source task requires artifact produced by target task |
| validation | Source task requires validation outcome from target task |
| repair | Source task is generated in response to failed target task |
| traceability | Source task requires traceability record or snapshot from target task |

### 8.3 Dependency Record Schema

Each dependency record MUST include: dependency-id (unique dependency identifier); source-task-id (task that depends on another task); target-task-id (task that must occur first); dependency-type (`hard`, `soft`, `governance`, `artifact`, `validation`, `repair`, `traceability`); rationale (reason the dependency exists); required (whether the dependency blocks execution); and created-at (time the dependency was created).

### 8.4 Circular Dependency Detection

The Planning Engine MUST detect circular dependencies. A circular dependency MUST cause planning validation failure unless the cycle is explicitly permitted by an approved extension and does not affect execution ordering. Circular dependency errors MUST identify all tasks participating in the cycle.

### 8.5 Dependency Failure

If a required dependency cannot be resolved, the affected task MUST be marked blocked in the Planning Validation Report. A construction plan with unresolved required dependencies MUST NOT be executable.

---

## 9.0 Construction Task Graph

The Construction Task Graph is the authoritative planning artifact consumed by the execution runtime.

### 9.1 Graph Structure

The Construction Task Graph SHALL be a directed graph.

| Graph Element | Description |
| ------------- | ----------- |
| Node | Construction task |
| Edge | Dependency relationship |
| Root | Planning or interpretation task initiating construction |
| Terminal Nodes | Consolidation, deployment-preparation, or completion tasks |

### 9.2 Graph Schema

The Construction Task Graph MUST conform to the following structure: plan-id (unique construction plan identifier); specification-id (system identifier); specification-version (specification version); canonical-model-version (canonical model version); readiness-level (readiness level at time of planning); planning-mode (`advisory`, `formal`, `adaptation`); created-at (plan creation timestamp); updated-at (last update timestamp, conditional); status (`draft`, `valid`, `executable`, `in-progress`, `completed`, `failed`, `superseded`); tasks (task definitions); dependencies (dependency records); critical-path (ordered task identifiers on critical path); parallel-groups (groups of task identifiers eligible for parallel execution); verification-plan (verification task summary); artifact-production-plan (expected artifact outputs); governance-constraints (governance constraints affecting execution, conditional); and traceability-snapshot-id (traceability snapshot associated with the plan, conditional).

### 9.3 Graph Validity Rules

A Construction Task Graph MUST contain at least one task. Every task dependency MUST reference defined task identifiers. Every non-root task MUST be reachable through dependency or sequencing relationships. Every task producing artifacts MUST declare expected artifact types. Every implementation, schema, interface, infrastructure, and security task MUST have associated verification coverage. A formal Construction Task Graph MUST be acyclic for required execution dependencies.

### 9.4 Graph Status Rules

| Status | Meaning |
| ------ | ------- |
| draft | Plan is incomplete or advisory |
| valid | Plan satisfies planning validation but is not yet execution-authorized |
| executable | Plan may be consumed by execution runtime |
| in-progress | Plan is actively executing |
| completed | Plan execution completed successfully |
| failed | Plan cannot complete |
| superseded | Plan replaced by later plan version |

A plan MUST NOT be marked executable unless all planning validation checks pass and readiness permits execution preparation.

---

## 10.0 Verification and Artifact Production Planning

### 10.1 Verification Task Integration

Verification must be planned before execution so that artifact acceptance criteria are known at generation time. The Planning Engine MUST create or associate verification tasks for implementation, schema, interface, infrastructure, security, test generation, and deployment preparation tasks, and for documentation tasks when documentation is required for governance or operation.

A verification task MUST include the base task schema and the following fields: verification-targets (artifact types, artifact identifiers, or task identifiers to validate); validation-criteria (conditions that define successful verification); required-tool-capabilities (tool capabilities required for deterministic verification, conditional); expected-outcome (`passed`, `warning-acceptable`); failure-action (`repair`, `escalate`, `halt`, `warn`); and validation-entity-ids (Validation entities linked to the task, conditional).

Verification tasks MUST be scheduled after the tasks that produce their verification targets. Verification criteria MUST be traceable to Validation entities, policy rules, task outputs, or platform-defined quality rules. Verification tasks MUST specify failure-action. A verification task without validation criteria MUST fail planning validation.

### 10.2 Artifact Production Planning

Artifact production planning allows the runtime to detect missing outputs and allows traceability to be established at artifact creation time. Each task that produces artifacts MUST declare: expected-artifact-type (`source`, `config`, `schema`, `infrastructure`, `contract`, `test`, `documentation`); expected-count (expected number of artifacts when known, conditional); source-entity-ids (specification entities artifacts are expected to trace to); repository-location-pattern (expected repository location or path pattern, conditional); validation-required (whether deterministic validation is required); documentation-required (whether generated documentation is required, conditional); and traceability-required (whether traceability association must be recorded).

If a task completes without producing declared artifacts, the runtime MUST treat the task as failed. If an artifact is produced that was not declared, the runtime MUST classify it as unexpected and require traceability justification before acceptance. Expected artifacts MUST be mapped to source specification entities before execution. A task that produces deployment artifacts MUST trace those artifacts to Infrastructure or operational Requirement entities.

---

## 11.0 Parallel Planning and Critical Path

### 11.1 Parallel Construction Planning

Parallel execution improves performance but must not compromise dependency safety, artifact consistency, governance, or traceability. Parallel groups are planning recommendations consumed by the execution runtime.

Tasks MAY be placed in the same parallel group when no required dependency path exists between them, they do not write to the same artifact target, they do not require exclusive access to the same state resource, they do not require sequential governance review, they can produce traceability independently, and their required tools and agents can execute concurrently.

Each parallel group MUST include: parallel-group-id (unique parallel group identifier); task-ids (tasks eligible for concurrent execution); dependency-boundary (dependency condition that releases the group); shared-resources (shared resources considered by the planner, conditional); governance-constraints (constraints affecting concurrency, conditional); and rationale (why the tasks are safe to run in parallel).

A task MUST NOT be placed in a parallel group with another task if either task depends on the other. Tasks that modify the same artifact or repository location MUST NOT be parallel unless a coordination mechanism is declared. Governance constraints MAY force sequential execution even where technical dependencies permit parallelism. The runtime MAY reduce or serialize parallel groups but MUST NOT increase concurrency beyond dependency and governance limits without replanning or validation.

### 11.2 Critical Path Calculation

The critical path identifies the longest dependency chain in the Construction Task Graph and provides a planning estimate of the minimum execution sequence assuming unlimited parallel capacity.

The Planning Engine MUST compute and record the critical path for every formal Construction Task Graph. The critical path MUST include ordered task identifiers and MUST be recomputed when tasks are added or removed, dependencies change, repair tasks are inserted, affected tasks are reset due to specification change, or governance constraints alter execution order.

Each critical path record MUST include: critical-path-id (unique critical path record identifier); plan-id (construction plan identifier); task-ids (ordered tasks in the critical path); calculated-at (time calculated); calculation-method (algorithm or method used); estimated-duration (estimated duration when available, optional); and assumptions (assumptions affecting path calculation, conditional).

The critical path MUST contain only tasks present in the Construction Task Graph and MUST respect dependency direction. If task durations are unknown, the Planning Engine MAY calculate critical path by dependency length rather than time.

---

## 12.0 Construction Boundaries and Connection Contexts

This section is the authoritative definition of Construction Boundaries and Connection Contexts in the ISL r3 corpus. Other r3 documents reference this section rather than redefining it.

Construction boundaries define bounded planning, reasoning, validation, and execution scopes within a construction plan. A construction boundary is a first-class planning unit that groups related specification entities, construction tasks, artifacts, validation activities, governance checks, and continuation records into a coherent unit of autonomous work.

The Planning Engine MUST organize every Construction Task Graph into one or more construction boundaries. A construction plan with no declared construction boundaries MUST NOT be considered valid.

Construction boundaries exist to preserve architectural coherence, constrain reasoning scope, support deterministic execution, enable governance review, and maintain traceable continuity across staged construction activity. They apply equally to local-first, cloud-hosted, hybrid, and distributed enterprise workflows. Boundary-based construction is not an optimization for small models or local execution environments; it is a structural control mechanism required for reliable autonomous construction.

Construction Boundaries are the primary context-drift control primitive. A boundary defines the maximum semantic, architectural, execution, and model-context scope for a unit of construction. An agent, model, tool, or runtime worker MUST NOT introduce or rely on entities, assumptions, artifacts, decisions, or policies outside the active boundary unless a valid Connection Context authorizes that crossing.

### 12.1 Boundary Definition

A construction boundary MUST define a bounded scope of work within the Construction Task Graph. Each Boundary Definition MUST include the following fields.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| boundary-id | string | REQUIRED | Unique identifier for the construction boundary |
| boundary-name | string | REQUIRED | Human-readable name of the boundary |
| boundary-purpose | string | REQUIRED | Description of the architectural or construction purpose of the boundary |
| boundary-type | enum | REQUIRED | foundation, specification, semantic, planning, runtime, governance, security, tooling, agent, telemetry, deployment, experimental |
| source-entity-ids | array | REQUIRED | Specification entity identifiers included in the boundary scope |
| task-ids | array | REQUIRED | Construction task identifiers assigned to the boundary |
| expected-artifact-types | array | CONDITIONAL | Artifact types expected to be produced or modified within the boundary |
| dependency-boundaries | array | REQUIRED | Boundary identifiers that MUST complete before this boundary may execute |
| connection-contexts | array | REQUIRED | Connection Context identifiers governing permitted cross-boundary interactions |
| entry-criteria | array | REQUIRED | Conditions that MUST be satisfied before boundary execution begins |
| exit-criteria | array | REQUIRED | Conditions that MUST be satisfied before the boundary may be marked complete |
| continuation-record-id | string | CONDITIONAL | Identifier of the Boundary Continuation Record produced after completion |
| status | enum | REQUIRED | pending, ready, in-progress, completed, failed, blocked, escalated |
| created-at | ISO 8601 | REQUIRED | Timestamp at which the boundary was created |

A construction task MUST belong to at least one construction boundary. A task MAY belong to more than one boundary only when it performs an explicitly declared cross-boundary coordination function.

A boundary MUST declare excluded scope when the risk of context drift, architectural overlap, data exposure, or duplicated construction is material. Excluded scope MAY be empty only when the boundary purpose and included entities are sufficient to prevent ambiguity. A boundary MUST NOT be considered valid when its included scope, excluded scope, entry criteria, exit criteria, and Connection Contexts conflict.

### 12.2 Boundary Types

Boundary type identifies the architectural role of the boundary within the construction plan. The supported boundary types are: `foundation` (shared primitives, contracts, base models, and reusable abstractions); `specification` (specification intake, parsing, readiness evaluation, and authored representation processing); `semantic` (canonical entities, semantic graph structure, relationships, and normalization outputs); `planning` (task graph generation, dependency resolution, critical path, and parallel groups); `runtime` (task execution, scheduling, state transitions, repair orchestration, and execution lifecycle); `governance` (approval gates, policy evaluation, waiver handling, and audit controls); `security` (Zero Trust controls, access validation, dependency risk, and policy enforcement); `tooling` (deterministic tool invocation and validation plugins); `agent` (reasoning agent orchestration, model interaction, generation, review, and repair analysis); `telemetry` (execution telemetry, observability, monitoring, and diagnostics); `deployment` (packaging, infrastructure preparation, environment configuration, and deployment readiness); and `experimental` (reference construction or domain-specific validation workflows not yet part of the stable core platform).

Boundary types MAY be extended in future ISL versions. Custom boundary types MUST NOT be used in normative construction plans unless registered as an ISL extension.

### 12.3 Boundary Dependency Resolution

The Planning Engine MUST resolve dependencies between boundaries before the Construction Task Graph is considered valid. Boundary dependencies MUST be derived from task dependencies declared between tasks assigned to different boundaries, specification relationships between entities in different boundaries, artifacts produced in one boundary and consumed in another, governance requirements that require one boundary to complete before another, and tooling requirements of downstream boundaries.

A boundary MUST NOT be marked ready while any dependency boundary remains pending, in-progress, failed, blocked, or escalated. Circular boundary dependencies MUST be detected during planning; a detected boundary cycle MUST cause planning to halt and MUST produce a recorded planning error identifying the boundary identifiers involved.

Boundary dependency resolution MUST NOT replace task dependency resolution. Boundary dependencies constrain the execution order of groups of tasks, while task dependencies constrain the execution order of individual construction tasks.

### 12.4 Connection Contexts

A Connection Context defines the permitted, required, and traceable interaction between construction boundaries. Connection Contexts are required because autonomous construction must not rely on implicit assumptions between boundaries. Cross-boundary interaction MUST be explicit, bounded, validated, and traceable.

Each Connection Context MUST include the following fields.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| connection-context-id | string | REQUIRED | Unique identifier for the connection context |
| from-boundary-id | string | REQUIRED | Boundary providing information, artifacts, or constraints |
| to-boundary-id | string | REQUIRED | Boundary receiving information, artifacts, or constraints |
| context-purpose | string | REQUIRED | Description of why the connection exists |
| context-type | enum | REQUIRED | contract, artifact, semantic, governance, telemetry, validation, model-context, continuation, reusable-asset |
| provided-elements | array | REQUIRED | Identifiers of artifacts, tasks, specification entities, decisions, or records made available |
| required-elements | array | REQUIRED | Identifiers or categories of elements the receiving boundary requires |
| validation-rule | string | REQUIRED | Verifiable rule used to confirm the connection context is valid |
| trust-policy | string | REQUIRED | Policy governing how the receiving boundary may rely on the provided elements |
| sensitivity-classification | enum | REQUIRED | Sensitivity classification from ISL v0.1 §6.3 |
| context-minimization-rule | string | REQUIRED | Rule limiting the context to the minimum elements needed |
| drift-detection-rule | string | REQUIRED | Rule used to detect unauthorized concepts or dependencies crossing the boundary |
| expires-at | ISO 8601 | CONDITIONAL | Expiration time when the connection is temporary or approval-bound |
| supersedes-context-id | string | CONDITIONAL | Prior Connection Context superseded by this record |
| created-at | ISO 8601 | REQUIRED | Timestamp at which the connection context was created |

A boundary MUST NOT consume artifacts, decisions, model outputs, task results, governance records, or telemetry from another boundary unless a Connection Context authorizes that consumption.

A Connection Context MUST be validated before the receiving boundary begins execution. Failure to validate a required Connection Context MUST block the receiving boundary and MUST be recorded as a planning or governance event.

A Connection Context MUST NOT transfer the full prior boundary state when a minimized subset is sufficient. If generated output introduces a dependency, entity, policy assumption, data exposure, reusable asset, or architectural responsibility not authorized by the active boundary or its Connection Contexts, the boundary MUST record context drift and MUST repair, replan, or escalate before downstream execution continues.

Connection Context validation MUST prove that the transferred context is necessary, current, compatible with the receiving boundary, and no broader than the receiving task requires. For resource-constrained local model profiles, the Planning Engine MUST produce context packages that prefer references, summaries, contracts, schemas, interface excerpts, test evidence, and Reuse Decision Records before full artifact content.

### 12.5 Boundary Entry Criteria

Boundary Entry Criteria define the conditions required before execution of a boundary may begin. At minimum, each boundary MUST verify the following before execution: all dependency boundaries have completed successfully; all required Connection Contexts have passed validation; all artifacts consumed by the boundary are present and traceable; all approval gates required before boundary execution have been satisfied; all deterministic tools needed by tasks in the boundary have registered Tool Records; all model, agent, specification, task, and artifact context required by the boundary is available; and model and agent context is limited to active boundary scope and authorized Connection Contexts.

The Runtime MUST NOT execute a task assigned to a boundary unless the boundary entry criteria have been satisfied.

### 12.6 Boundary Exit Criteria

Boundary Exit Criteria define the conditions required before a boundary may be marked complete. At minimum, a boundary MUST NOT be marked completed unless all of the following are true: every task assigned to the boundary has completed successfully or has been explicitly waived through governance; all required deterministic verification tasks associated with the boundary have passed or produced warning-acceptable outcomes; all artifacts declared by the boundary have been produced and recorded; all produced artifacts are linked to source specification entities and construction tasks; all policies applicable to the boundary have been evaluated; a Boundary Continuation Record has been produced when downstream boundaries depend on this boundary; no unresolved repair, validation, governance, or security escalation remains open within the boundary; and no unauthorized concept, dependency, artifact, policy, reusable asset, or assumption remains unresolved outside boundary scope.

If any exit criterion fails, the boundary MUST remain in failed, blocked, or escalated status until the condition is resolved.

### 12.7 Boundary Continuation Records

A Boundary Continuation Record preserves the information required by downstream boundaries to continue construction without relying on conversational memory, implicit assumptions, or unstructured reasoning context.

Each Boundary Continuation Record MUST include: continuation-record-id (unique identifier); boundary-id (boundary that produced the record); completed-at (timestamp at which the boundary was completed or suspended); completed-task-ids (tasks completed within the boundary); produced-artifact-ids (artifacts produced or modified within the boundary); decision-record-ids (Decision Records produced within the boundary); validation-result-ids (tool invocation or verification results associated with the boundary); governance-record-ids (approval, waiver, policy evaluation, or escalation records, conditional); reusable-asset-ids (reusable assets selected, reused, extended, wrapped, or composed within the boundary, conditional); connection-context-ids (Connection Contexts consumed or produced by the boundary); drift-findings (context drift findings detected during boundary execution, conditional); open-issues (issues deferred to later boundaries or human review); downstream-context-summary (human-readable summary of what downstream boundaries must know); and next-boundary-recommendations (recommended downstream boundary identifiers or actions, conditional).

Boundary Continuation Records MUST be immutable once produced. Corrections MUST be recorded as new continuation records referencing the superseded record.

### 12.8 Boundary Traceability

Construction boundaries MUST participate in the traceability graph. The traceability graph MUST support boundary-level navigation in addition to specification, task, and artifact navigation. The traceability node and edge types for boundaries are defined in the authoritative catalog in ISL v1.4 §5 and §6.

Boundary traceability MUST support bidirectional navigation. Given a boundary, the platform MUST be able to return all tasks, artifacts, decision records, validation results, and governance records associated with it. Given an artifact, task, decision record, or validation result, the platform MUST be able to return the boundary or boundaries that produced or governed it.

Boundary traceability MUST be preserved across specification version changes. If a boundary is superseded by a later construction cycle, the new boundary MUST be connected to the prior boundary using a supersedes relationship.

### 12.9 Boundary Governance

Construction boundaries MUST be subject to governance controls when required by policy, risk tier, or boundary type. The Governance Engine MUST be able to evaluate policies at boundary entry, boundary execution, and boundary exit. A governance policy MAY apply to a single task, a single artifact, an entire boundary, or a connection between boundaries.

The following boundary events MUST be recordable as governance events: boundary-created, boundary-ready, boundary-started, boundary-blocked, boundary-completed, boundary-failed, boundary-escalated, connection-context-validated, and continuation-record-produced.

A boundary that contains governance, security, deployment, or high-risk policy enforcement tasks MUST NOT be marked complete until applicable governance evaluations have been recorded.

### 12.10 Boundary Execution Rules

The Runtime MUST execute construction tasks according to both task dependencies and boundary dependencies. The Runtime MUST NOT execute a task outside the active boundary unless one of the following is true: the task is declared as a cross-boundary coordination task; the task is required to validate or produce a Connection Context; a governance authority has approved a documented override; or a repair task must access prior boundary outputs to correct a failed artifact.

When execution crosses a boundary, the Runtime MUST record the reason for the cross-boundary interaction and MUST link the interaction to a valid Connection Context or governance record. The Runtime MUST preserve boundary state so construction can resume after interruption.

### 12.11 Local-First, Cloud, and Hybrid Applicability

Construction boundaries apply to all supported execution environments. A conforming implementation MUST NOT treat boundary planning as optional because an execution environment has access to larger models, larger context windows, distributed compute, or cloud orchestration.

In local-first environments, boundaries constrain context, memory, tooling scope, and workstation resources. In cloud-hosted environments, boundaries constrain architectural scope, reasoning drift, governance checkpoints, and auditability. In hybrid environments, boundaries define what context, artifacts, and model interactions may cross local/cloud execution boundaries. In distributed enterprise environments, boundaries support partitioned execution, project isolation, operational monitoring, and staged governance.

The size and composition of a boundary MAY vary by execution environment. However, every construction plan MUST define boundaries, boundary dependencies, connection contexts, entry criteria, exit criteria, and continuation records.

### 12.12 Boundary Adaptation

When a specification changes after a Construction Task Graph has been generated, the Planning Engine MUST evaluate boundary impact in addition to task and artifact impact. Boundary impact analysis MUST identify affected-boundaries (boundaries requiring re-execution or review), unaffected-boundaries (boundaries confirmed as unaffected), affected-connection-contexts (Connection Contexts requiring validation or regeneration), affected-continuation-records (Boundary Continuation Records superseded by the change), and boundary-regression-level (`none`, `entry-check`, `partial-reexecution`, `full-boundary-reexecution`, or `governance-review`).

The platform MUST NOT re-execute unaffected boundaries unless explicitly instructed by a governance authority. This is consistent with the traceability rule that unaffected artifacts identified by impact analysis MUST NOT be regenerated unless explicitly instructed by governance.

### 12.13 Reuse Boundary Rules

Reuse MUST operate inside the active Construction Boundary. A reused asset that introduces external behavior, shared state, cross-boundary assumptions, external services, sensitive data access, or downstream dependencies MUST be authorized through a Connection Context.

The Connection Context MUST identify asset identifiers, source and target boundaries, allowed artifacts and interfaces, assumptions transferred, validation evidence transferred, sensitivity classification, trust level, and expiration or supersession conditions.

If a reusable asset cannot be summarized or bounded within the active Construction Boundary and authorized Connection Contexts, the Reuse Decision MUST escalate. For local or resource-constrained model profiles, the platform MUST prefer asset summaries, contracts, metadata, test evidence, and selected excerpts before transferring full asset content into model context.

A Connection Context MUST NOT authorize broad transfer such as "all prior code," "entire project history," or "full repository context" unless governance records why no smaller context package can satisfy the task.

---

## 13.0 Repair Task Generation

Repair tasks are typically generated dynamically during execution after validation or testing failure. Repair planning must respect execution repair limits defined in ISL v1.2.

### 13.1 Repair Task Trigger

A repair task MAY be generated only when deterministic validation fails, test execution fails, policy validation identifies correctable non-compliance, artifact production produces invalid structure, or review identifies a defect requiring correction.

### 13.2 Repair Task Schema Extensions

A repair task MUST include the base task schema and the following fields: repair-task-id (unique repair task identifier); failed-task-id (task whose output failed); failed-validation-result-id (validation or test result triggering repair); failed-artifact-id (artifact being repaired); iteration (repair attempt number for the artifact); termination-policy (repair limits from ISL v1.2); repair-scope (`artifact`, `task`, `service`, `interface`, `schema`, `policy`); and expected-correction (correction expected or proposed, conditional).

### 13.3 Repair Task Insertion Rules

A repair task MUST be inserted after the failed verification or test task, MUST inherit relevant dependencies from the failed task, MUST be followed by revalidation, and MUST be traceable to the failed artifact, failed validation result, and source specification entities. The Planning Engine MUST refuse to generate a repair task that would exceed repair limits defined in ISL v1.2.

### 13.4 Repair Dependency Rules

Repair task dependencies MUST ensure that failed artifact state is available, validation findings are available, affected source entities remain valid, governance permits repair, and revalidation follows repair.

---

## 14.0 Reusable Asset Registry

The platform MUST provide a Reusable Asset Registry or an implementation-equivalent controlled interface. The registry MUST support asset registration, semantic search, capability tagging, version lookup, provenance lookup, validation status lookup, sensitivity and trust classification, dependency and consumer lookup, and promotion, deprecation, retirement, and supersession workflows.

The registry MAY be local, embedded, cloud-hosted, federated, or backed by an enterprise catalog. Regardless of implementation form, the registry MUST expose the same governed lookup, provenance, validation, and decision behavior required by this document.

### 14.1 Asset Types

The registry MUST support at least the following asset types: library, module, component, utility, service-contract, schema, template, infrastructure-module, validation-harness, policy-module, and agent-pattern.

### 14.2 Asset Record Schema

Each reusable asset record MUST include: asset-id (unique reusable asset identifier); asset-type (asset type from the registry catalog); name (human-readable asset name); description (semantic description of asset capability); version (semver); capability-tags (capability tags derived from canonical entities and validated behavior); satisfied-entity-types (canonical entity types the asset can satisfy); provenance-reference (specification, artifact, import, or governance source); validation-status (`unvalidated`, `validated`, `warning`, `failed`, `expired`, `superseded`); validation-evidence (required when validation-status is validated, warning, failed, or expired); traceability-snapshot-id (traceability snapshot proving origin and validation); sensitivity-classification (from ISL v0.1 §6.3); trust-level (`approved`, `restricted`, `experimental`, `prohibited`); owner (owning role, team, project, or governance authority); reuse-scope (`project`, `workspace`, `tenant`, `enterprise`, `public`); deprecation-state (`active`, `deprecated`, `superseded`, `retired`); compatibility (supported platforms, languages, contracts, runtimes, or ISL versions, conditional); immutable-identity-hash (stable integrity marker when artifact content is available, conditional); consumer-count (number of known active consumers when dependency tracking is available, conditional); and registered-at (registration timestamp).

### 14.3 Registry Rules

The registry MUST NOT list an asset as approved unless validation evidence and traceability evidence are available.

An asset with validation-status `failed`, `expired`, or deprecation-state `retired` MUST NOT be selected for new construction unless governance grants an explicit exception. An asset with trust-level `experimental` MAY be selected only when runtime policy permits experimental dependencies. Restricted assets MUST NOT be exposed across project, tenant, or environment boundaries without governed authorization.

The registry MUST distinguish an artifact lifecycle state from a reusable asset lifecycle state. Promotion to the registry creates or updates an asset record; it MUST NOT erase the original artifact metadata, artifact validation evidence, artifact version history, or traceability links.

Enterprise registries MUST support deterministic export of asset records, dependency relationships, governance decisions, validation evidence references, and consumer impact data.

---

## 15.0 Reuse Discovery

Reuse Discovery is a mandatory planning activity for tasks that may produce or select implementation artifacts.

### 15.1 Reuse Discovery Applicability

Reuse Discovery MUST run for task types including implementation, schema, interface, contract, infrastructure, policy, validation, documentation-template, and agent-pattern. A platform MAY skip Reuse Discovery only when the task type is explicitly declared non-reusable by policy or when governance authorizes emergency generation.

### 15.2 Reuse Discovery Inputs

Reuse Discovery MUST use source-entity-ids, the active construction-boundary-id, connection-context-ids, task-capability-tags, required-artifact-types, target-platform-profile, governance-profile-id, and sensitivity-classification.

### 15.3 Reuse Discovery Outcomes

Reuse Discovery MUST produce one of the outcomes from the shared Reuse Outcome enum in ISL v0.1 §6.10: `reuse-full` (existing asset fully satisfies the task intent); `reuse-partial` (existing asset partially satisfies the task intent and delta is needed); `wrap` (existing asset can be used through an adapter or wrapper); `extend` (existing asset can be extended under governed compatibility rules); `compose` (multiple assets can jointly satisfy the task intent); `promote-candidate` (a candidate asset is promoted for governed reuse); or `reject-reuse` (no suitable approved asset exists, or a candidate must not be used due to policy or mismatch).

When a candidate asset requires governance or human review, the Reuse Decision MUST escalate to governance rather than recording a discovery outcome. The Runtime MUST NOT dispatch a generation task until a Reuse Discovery outcome has been recorded for applicable tasks.

---

## 16.0 Reuse Fitness Assessment

Each candidate asset considered during Reuse Discovery MUST produce a Reuse Fitness Assessment.

### 16.1 Assessment Record Schema

Each Reuse Fitness Assessment MUST include: reuse-fitness-assessment-id (unique assessment identifier); task-id (construction task being assessed); source-entity-ids (canonical entities the task must satisfy); candidate-asset-id (candidate reusable asset); coverage-level (`full`, `partial`, `none`, `conflicting`, `uncertain`); coverage-rationale (explanation of the coverage judgment); interface-compatibility (`compatible`, `adapter-required`, `incompatible`, `unknown`); version-compatibility (`compatible`, `upgrade-required`, `downgrade-required`, `incompatible`, `unknown`); security-validation-status (`passed`, `warning`, `failed`, `expired`, `not-evaluated`); boundary-compatibility (`within-boundary`, `connection-context-required`, `incompatible`, `unknown`); sensitivity-compatibility (`compatible`, `restricted`, `incompatible`, `unknown`); recommended-action (`reuse-full`, `reuse-partial`, `wrap`, `extend`, `compose`, `reject`, `escalate`); assessed-by (planner, agent, tool, or governance component); and assessed-at (assessment timestamp).

### 16.2 Assessment Rules

A candidate asset MUST NOT receive coverage-level `full` unless it satisfies all must-have requirements linked to the task. An asset requiring a Connection Context MUST NOT be selected unless the Connection Context exists and has passed validation. An asset with failed or expired security validation MUST produce recommended-action `reject` or `escalate`. An uncertain assessment MUST NOT authorize full reuse.

---

## 17.0 Reuse Decision Record

Reuse Discovery MUST produce a Reuse Decision Record for each applicable task. Each Reuse Decision Record MUST include: reuse-decision-id (unique identifier); task-id (task governed by the decision); construction-boundary-id (active Construction Boundary); selected-asset-ids (assets selected for reuse, composition, extension, or wrapping, conditional); outcome (Reuse Outcome from ISL v0.1 §6.10); decision-rationale (reason for the selected outcome); delta-generation-required (whether generation remains required); delta-generation-rationale (required when delta-generation-required is true); governance-decision-id (required when governance approval was needed); traceability-link-ids (traceability links created or required by the decision); decided-by (planner, runtime, agent, tool, or governance component); and decided-at (decision timestamp).

Generation MUST NOT proceed unless the Reuse Decision Record authorizes `reuse-partial`, `wrap`, `extend`, or `compose` with delta generation, or `reject-reuse` with a rationale proving that no suitable approved asset exists.

A Reuse Decision Record that authorizes generation MUST include a rationale proving that applicable registry discovery was performed and that candidate assets were absent, rejected, incompatible, prohibited, or insufficient. An implementation MUST NOT record `reject-reuse` merely because generation is easier, faster, cheaper, or preferred by a model response.

---

## 18.0 Generation Constraints, Promotion, Deprecation, and Duplication

### 18.1 Generation Constraints After Reuse Discovery

Implementation agents, generators, and templates MUST respect the Reuse Decision Record. An agent MUST NOT generate a full replacement implementation when outcome is `reuse-full`. When outcome is `reuse-partial`, `wrap`, `extend`, or `compose`, generation MUST be limited to the recorded delta, adapter, extension, or composition logic. Generated deltas MUST preserve traceability to both the source specification entities and the reused assets.

### 18.2 Asset Promotion

After a new artifact passes validation and traceability closure, the platform SHOULD evaluate whether it is eligible for promotion to the Reusable Asset Registry.

An artifact SHOULD be considered for promotion when it is stable, passed required validation, has complete traceability, provides a generalizable capability, is not tightly coupled to a single specification's private data or policy context, has passed security validation or accepted governed warnings, has declared ownership and reuse scope, and has governance approval where required.

Promotion MUST create or update a reusable asset record, identify the owner responsible for future maintenance, identify the allowed reuse scope, and preserve provenance to the specification and artifacts that produced the asset. The promoting owner MUST acknowledge that other systems may take a dependency on the asset when reuse-scope exceeds the originating project.

### 18.3 Asset Deprecation, Supersession, and Retirement

Deprecating, superseding, or retiring a reusable asset is a governed lifecycle action. Deprecation MUST trigger impact analysis for known consumers. Supersession MUST identify the replacement asset where one exists. Retirement MUST NOT occur while active consumers depend on the asset unless governance approves a migration, isolation, or exception plan. An asset marked retired MUST NOT be selected for new construction.

### 18.4 Duplication Detection

Enterprise and Regulated profiles MUST require duplication detection as part of static analysis, semantic analysis, repository validation, or an implementation-equivalent validation mechanism.

A significant duplication finding between a new artifact and an approved reusable asset MUST route the task back through Reuse Discovery unless governance accepts the duplication with a documented rationale. Duplication findings MUST reference candidate asset identifiers when known. Enterprise and Regulated profiles MUST define duplication thresholds, matching methods, and exception handling in governance policy.

A duplicated artifact that bypassed required Reuse Discovery MUST NOT be promoted to stable state until Reuse Discovery is completed or governance records a waiver that explains why duplication is accepted.

---

## 19.0 Governance Constraints in Planning

Governance constraints must be visible in the plan before execution, not discovered only after a task attempts to run.

### 19.1 Governance Constraint Types

The governance constraint types are: `approval-required` (task requires approval before execution); `policy-check-required` (task requires policy evaluation); `tool-restricted` (task may use only approved tools); `model-restricted` (task may use only approved model providers); `human-review-required` (task output requires human review); `waiver-required` (task cannot proceed unless a waiver exists); `risk-tier-control` (task behavior constrained by risk tier); and `separation-of-duties` (approval or review must be assigned to a distinct role).

### 19.2 Governance Constraint Record Schema

Each governance constraint record MUST include: constraint-id (unique constraint identifier); task-id (task affected); policy-id (policy causing the constraint, conditional); constraint-type (constraint type from §19.1); enforcement-point (`planning`, `execution`, `validation`, `deployment`); blocking (whether the constraint blocks execution); and required-action (action required to satisfy the constraint).

### 19.3 Governance Planning Rules

The Planning Engine MUST identify governance constraints applicable to tasks. A task with unresolved blocking governance constraints MUST NOT be considered execution-ready. Governance constraints MUST be included in the Construction Task Graph or associated planning records.

Governance MUST also control registry write access, asset promotion, cross-project reuse, cross-tenant reuse, restricted asset reuse, deprecation and retirement, exceptions to reuse-before-generation, and reuse of assets with expired, warning, or experimental status. Governance decisions affecting reusable assets MUST be auditable.

---

## 20.0 Planning Validation

A plan that fails validation MUST NOT be consumed by the execution runtime as executable.

### 20.1 Required Validation Checks

The Planning Engine MUST validate that the plan has required metadata, references the current specification version, all tasks have required fields, all task identifiers are unique, all dependencies resolve, no required dependency cycles exist, all source entity references resolve, all required task types are generated, every artifact-producing task declares expected artifacts, every artifact-producing task has verification coverage, every must-have requirement has an implementation and validation path, every policy has a planned evaluation or enforcement task, every interface has a contract generation or validation task, every DataEntity has a schema or validation task, every Infrastructure entity has a deployment or validation task, governance constraints are represented, traceability links from source entities to tasks exist or can be created, critical path is calculated, parallel groups are dependency-safe, and every applicable task has a Reuse Discovery outcome and Reuse Decision Record.

### 20.2 Planning Validation Report Schema

Each planning validation report MUST include: validation-report-id (unique report identifier); plan-id (construction plan evaluated); specification-id (system identifier); specification-version (specification version); outcome (`passed`, `failed`, `warning`); findings (validation findings, conditional); validated-at (time validation completed); validated-by (Planning Engine component); and executable (whether the plan is executable).

### 20.3 Planning Validation Finding Schema

Each planning validation finding MUST include: finding-id (unique finding identifier); finding-class (finding class); severity (severity from the common error model); task-id (related task, conditional); entity-id (related specification entity, conditional); message (finding description); and required-action (remediation required).

### 20.4 Failed Validation

If planning validation fails with blocking findings, the plan status MUST be `draft` or `failed`. The plan MUST NOT be marked executable.

---

## 21.0 Planning Error Classes

Planning errors use the common error model from ISL v0.1 §8. The following extension error classes are registered under the `validation` and `reuse` error categories: `planning-precondition-failed` (planning precondition not satisfied, critical); `planning-input-version-mismatch` (planning inputs reference inconsistent versions, critical); `planning-missing-task` (required task not generated, critical); `planning-invalid-task-schema` (task missing required fields, critical); `planning-duplicate-task-id` (task identifier duplicated, critical); `planning-unresolved-dependency` (dependency references missing task, critical); `planning-circular-dependency` (required dependency cycle exists, critical); `planning-missing-source-trace` (task lacks source entity or rule trace, critical); `planning-missing-verification` (artifact-producing task lacks verification coverage, critical); `planning-missing-artifact-declaration` (artifact-producing task lacks expected artifact declaration, critical); `planning-invalid-parallel-group` (parallel group violates dependency or resource constraints, error); `planning-missing-governance-constraint` (applicable governance constraint not represented, error); `planning-repair-limit-exceeded` (repair task would exceed termination policy, critical); `planning-critical-path-unavailable` (critical path could not be calculated, error); `planning-impact-analysis-required` (change requires impact analysis before adaptation, critical); `reuse-discovery-missing` (applicable task lacks a Reuse Discovery outcome, critical); `reuse-decision-missing` (applicable task lacks a Reuse Decision Record, critical); and `reuse-generation-without-decision` (generation proceeded without a valid reuse decision, critical).

Each error record MUST conform to the common error record structure from ISL v0.1 §8.1.

---

## 22.0 Construction Monitoring

Although execution monitoring is performed by the runtime, the construction plan must provide the status model and expected task transitions the runtime will use.

### 22.1 Task Status Values

The task status values are: `pending` (task has not started); `in-progress` (task has been assigned or is executing); `completed` (task completed and required verification passed or was waived); `failed` (task failed due to invalid output, failed validation, or execution error); `escalated` (task requires governance or human intervention); and `skipped` (task was not executed due to validated non-applicability or governance decision).

### 22.2 Permitted Status Transitions

The only permitted status transitions are: `pending` to `in-progress` (task assigned to agent, tool, or component); `pending` to `skipped` (task determined not applicable by valid rule or governance decision); `in-progress` to `completed` (outputs produced and required verification passed or waived); `in-progress` to `failed` (task failed or produced invalid outputs); `in-progress` to `escalated` (escalation threshold or governance condition reached); `failed` to `in-progress` (repair task generated and assigned); `failed` to `escalated` (repair not possible or repair threshold reached); `escalated` to `in-progress` (escalation resolved and retry authorized); `escalated` to `failed` (governance closes task as failed); `completed` to `pending` (specification change invalidates completed task); and `completed` to `skipped` (governance or impact analysis determines task no longer applicable).

No other transitions are permitted.

### 22.3 Monitoring Rules

The runtime MUST update task status as execution progresses. Invalid status transitions MUST be recorded as execution integrity violations. The Planning Engine MUST preserve status history when adapting an in-progress plan.

---

## 23.0 Plan Adaptation to Specification Changes

Because ISL specifications are living artifacts, construction plans must evolve without requiring unnecessary full reconstruction. Plan adaptation must be driven by traceability and impact analysis.

### 23.1 Adaptation Trigger

Plan adaptation MUST occur when a specification change affects planned or completed tasks, readiness regression affects execution eligibility, impact analysis identifies affected artifacts or tasks, governance requires replanning, an interface, policy, data entity, infrastructure, or must-have requirement changes, or repair or execution feedback reveals a planning defect.

### 23.2 Required Adaptation Inputs

Plan adaptation MUST use the prior construction plan, previous specification version, new specification version, impact analysis result, traceability graph, governance constraints, and execution state when adaptation occurs during execution.

### 23.3 Adaptation Process

When adapting a plan, the Planning Engine MUST: receive or generate an impact analysis result; identify affected specification entities, tasks, and artifacts; reset affected incomplete or completed tasks when required; preserve unaffected completed tasks; generate tasks for newly added entities; remove or deprecate tasks for removed entities where safe; insert migration, cleanup, or supersession tasks when required; recompute dependencies, critical path, and parallel groups; revalidate the adapted plan; record a Plan Adaptation Record; and update traceability.

### 23.4 Plan Adaptation Record Schema

Each plan adaptation record MUST include: adaptation-id (unique adaptation identifier); prior-plan-id (plan being adapted); new-plan-id (adapted plan identifier); previous-specification-version (prior specification version); new-specification-version (new specification version); impact-analysis-id (impact analysis used); affected-task-ids (tasks affected); preserved-task-ids (tasks preserved); added-task-ids (new tasks added, conditional); removed-task-ids (tasks removed or deprecated, conditional); reset-task-ids (tasks reset to pending, conditional); governance-events (governance events associated with adaptation, conditional); adapted-at (time adaptation completed); and outcome (`adapted`, `failed`, `escalated`).

### 23.5 Adaptation Rules

The platform MUST NOT restart the entire construction plan when impact analysis identifies a bounded affected scope. Only affected tasks MUST be re-executed unless governance or traceability integrity requires broader reconstruction. Unchanged completed tasks MAY be preserved if their artifacts remain valid and traceability confirms they are unaffected. A task associated with a removed specification entity MUST be removed, deprecated, or converted into cleanup/migration work depending on artifact impact.

---

## 24.0 Plan Versioning and Supersession

Construction plans are derived artifacts and must be preserved across specification changes, replanning, repair insertion, and execution history.

### 24.1 Plan Version Fields

Every construction plan MUST include: plan-version (version of the construction plan); supersedes-plan-id (prior plan replaced by this plan, conditional); superseded-by-plan-id (later plan replacing this plan, conditional); version-reason (`initial`, `adaptation`, `repair-insertion`, `governance-change`, `correction`); and created-at (version creation time).

### 24.2 Versioning Rules

A new plan version MUST be created when the specification version changes, impact analysis modifies task scope, task dependencies are materially changed, repair tasks are inserted into the persistent graph, governance constraints alter execution order, critical path changes materially, or the execution runtime requires replanning.

Prior plan versions MUST remain queryable. A superseded plan MUST NOT be deleted if it was used for execution or governance review.

---

## 25.0 Readiness, Execution Handoff, and Completion

### 25.1 Planning and Readiness Integration

Readiness determines which planning actions are permitted: Draft permits advisory planning only; Reviewable permits advisory and preliminary planning; Machine-Valid permits formal construction planning; and Autonomous-Ready permits executable planning and execution handoff.

A formal construction plan MAY be generated at Machine-Valid readiness. A construction plan MUST NOT be executed until Autonomous-Ready readiness and execution preconditions are satisfied. If readiness regresses, the Planning Engine MUST determine whether the plan remains valid, must be adapted, or must be superseded.

### 25.2 Execution Handoff

The execution runtime depends on a validated, executable Construction Task Graph. A plan MUST satisfy the following before handoff to execution: plan validation passed; plan status is executable; specification version is Autonomous-Ready; governance constraints are represented; required tool capabilities and agent roles are known; artifact production plan exists; required Reuse Discovery tasks and Reuse Decision Records exist where applicable; verification plan exists; traceability links from entities to tasks exist; and critical path and parallel groups are calculated.

Each execution handoff record MUST include: handoff-id (unique handoff identifier); plan-id (plan handed to execution); specification-id (system identifier); specification-version (specification version); readiness-level (readiness level at handoff); validation-report-id (planning validation report); traceability-snapshot-id (traceability snapshot at handoff); handed-off-at (time handoff occurred); accepted-by-runtime (whether the runtime accepted the handoff); and rejection-reason (required when accepted-by-runtime is false).

The Execution Runtime MUST reject a handoff when plan status is not executable, the specification is not Autonomous-Ready, required dependencies are unresolved, required tools or agent roles are unavailable, governance constraints are unresolved, the traceability snapshot is missing, or planning validation failed.

### 25.3 Completion of the Construction Plan

Planning completion is not the same as execution completion. A plan may be complete as a planning artifact before execution begins; it may also later be completed as an executed plan.

A construction plan may be marked planning-complete when all required tasks have been generated, all required dependencies have been resolved, all required verification tasks exist, the artifact production plan is complete, governance constraints are represented, critical path is calculated, parallel groups are calculated, planning validation passes, and traceability links from source entities to tasks exist.

A construction plan may be marked execution-completed only when the execution runtime reports completion under ISL v1.2. Execution completion requires all required tasks completed, skipped, or waived; all required artifacts produced and validated or waived; repair cycles resolved or escalated and closed; tests completed or waived; policy validation completed or waived; artifacts consolidated; deployment preparation completed; traceability integrity checks passed; and a Completion Report produced.

A construction plan MUST be marked failed when blocking planning validation errors remain unresolved, required dependencies cannot be resolved, required tasks cannot be generated, repair limits prevent completion, traceability integrity cannot be established, or governance blocks required planning or execution activity.

---

## 26.0 Planning Records and Auditability

Construction planning decisions affect architecture, execution, artifacts, and governance, so they must be auditable.

### 26.1 Required Planning Records

The platform MUST preserve the Planning Record, Construction Task Graph, Task Definitions, Dependency Records, Planning Validation Report, Critical Path Record, Parallel Group Records, Artifact Production Plan, Verification Plan, Governance Constraint Records, Plan Adaptation Records, Execution Handoff Records, Reuse Discovery outcomes, Reuse Fitness Assessments, Reuse Decision Records, and Planning Error Records.

### 26.2 Audit Query Requirements

The platform MUST support queries for all tasks derived from a specification entity, all tasks producing a given artifact type, all dependencies for a task, all verification tasks for an implementation task, all tasks affected by a specification change, all tasks blocked by governance constraints, all repair tasks inserted during execution, critical path for a plan, parallel groups for a plan, planning validation findings, all reuse decisions for a task, and all consumers of a reusable asset.

### 26.3 Record Immutability

Planning records used for execution, governance review, or audit MUST NOT be overwritten. Corrections MUST be recorded by creating a new plan version or corrective record.

---

## 27.0 Conformance Requirements

### 27.1 Construction Plan Conformance

A Construction Plan conforms to ISL v1.5 if it includes required plan metadata, contains valid task definitions and dependency records, resolves all required dependencies, contains no invalid required dependency cycles, includes verification coverage, artifact production declarations, governance constraints, critical path, and parallel groups, passes planning validation, preserves traceability to source entities, and records reuse decisions for applicable tasks.

### 27.2 Planning Engine Conformance

A Planning Engine conforms to ISL v1.5 if it can validate planning preconditions, consume canonical ISL models, generate construction tasks from canonical entities, assign task types and responsible roles, resolve dependencies, detect circular dependencies, generate verification tasks, generate artifact production plans, identify parallel groups, calculate critical path, represent governance constraints, validate construction plans, adapt plans based on impact analysis, produce required planning records, support execution handoff, perform Reuse Discovery for applicable tasks, produce Reuse Fitness Assessments and Reuse Decision Records, and block generation without a valid reuse decision.

### 27.3 Reusable Asset Registry Conformance

A Reusable Asset Registry conforms to ISL v1.5 if it records required asset metadata, preserves provenance and traceability, exposes semantic asset lookup, records validation and trust state, supports dependency and consumer lookup, and enforces governance-controlled promotion and deprecation.

### 27.4 Runtime Conformance

A Runtime conforms to ISL v1.5 if it refuses to dispatch applicable generation tasks without Reuse Discovery, limits generation to authorized deltas when reuse is partial, records reuse traceability before stable promotion, routes duplication findings back through Reuse Discovery, enforces governance decisions for asset reuse, and enforces Construction Boundary and Connection Context controls.

### 27.5 Platform Conformance

A platform conforms to ISL v1.5 if it integrates planning with readiness state, traceability, governance, and tool capability discovery; prevents execution of non-executable plans; preserves planning records; supports plan adaptation and planning audit queries; governs registry curation; performs reuse-aware impact analysis; prevents unmanaged cross-project or cross-tenant reuse; validates reusable assets before approval; monitors asset deprecation, supersession, and retirement; defines duplication thresholds and routes significant duplication findings through Reuse Discovery; exports reusable asset, reuse decision, and consumer impact evidence for audit; and enforces minimized context transfer for reusable assets in local, cloud, and hybrid model profiles.

### 27.6 Regulated Conformance

A regulated implementation conforms to ISL v1.5 if it satisfies Platform Conformance and additionally requires explicit governance approval for restricted, cross-tenant, retired, deprecated, warning-status, or experimental asset use; preserves immutable evidence references for every promoted asset and every reuse-derived deployable artifact; prevents deployment-capable packaging when reuse provenance is incomplete unless a valid governance waiver marks the package risk and approver; and supports audit export of asset lineage from source specification to reusable asset to consuming artifact.
