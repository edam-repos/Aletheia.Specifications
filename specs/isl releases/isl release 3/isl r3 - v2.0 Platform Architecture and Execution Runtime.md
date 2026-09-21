# ALETHEIA Specification Language (ISL) v2.0
# Platform Architecture and Execution Runtime
**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x
**Supersedes:** ISL r2 v2.0 The Platform Architecture Model, ISL r2 v2.4 The Execution Runtime Model
**Document Type:** Platform Model Specification

---

## 1.0 Scope

This document defines the architecture of the ALETHEIA platform and the Execution Runtime that turns construction plans into controlled runtime activity. The platform is the system responsible for interpreting ISL specifications, normalizing them into canonical semantic models, generating construction plans, orchestrating agents, invoking deterministic tools, managing artifacts, preserving traceability, enforcing governance, and supporting autonomous software construction.

This document applies to conforming implementations of the ALETHEIA platform. It defines required platform subsystems, subsystem responsibilities, architectural boundaries, data flow rules, control points, integration contracts, runtime coordination responsibilities, and conformance expectations. It does not prescribe a specific programming language, cloud provider, database, model provider, operating system, or deployment topology. Those are implementation choices, provided the resulting platform satisfies this architecture.

This document defines:

* platform architecture principles
* core platform subsystems and responsibility boundaries
* platform layers and control boundaries
* platform data flows
* governance, traceability, observability, and artifact control integration
* local-first and enterprise scalability requirements
* the Execution Runtime lifecycle, admission, scheduling, queues, workers, leases, locks, checkpoints, recovery, and completion
* runtime error handling, isolation, security, and audit requirements
* conformance requirements

Shared conventions — normative language, field requirement levels, identifiers, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and are referenced here rather than redefined. Construction Boundaries and Connection Contexts are authoritatively defined in ISL v1.5 and are referenced here rather than redefined.

---

## 2.0 Platform Architecture

### 2.1 Architecture Principles

The platform MUST be organized as a layered architecture with clear responsibility boundaries. Each subsystem MUST perform a defined architectural role and MUST NOT bypass other subsystems' control responsibilities.

The following principles govern the platform architecture:

1. **Specification-Driven Operation** — The platform MUST operate from ISL specifications and their canonical semantic models. Construction activity MUST NOT be initiated from informal prompts, unnormalized prose, or ungoverned instructions. The specification is the source of truth.
2. **Local-First Capability** — The platform MUST be capable of operating in a local-first mode using local infrastructure, local artifact repositories, local tools, and locally hosted or locally available model providers where configured.
3. **Abstraction of External Dependencies** — External model providers, deterministic tools, artifact repositories, identity systems, observability systems, and deployment environments MUST be integrated through abstraction layers. The platform MUST NOT hardcode dependency-specific behavior into core orchestration or construction logic where an abstraction boundary is required.
4. **Governed Autonomy** — Governance MUST be an active control layer. Governance decisions MUST influence readiness, planning, execution, tool usage, repair, deployment preparation, and artifact acceptance. A subsystem MUST NOT continue a governed action when the Governance Engine returns `block`, `escalate`, `approval-required`, `waiver-required`, or `override-required`.
5. **Traceability by Construction** — Traceability MUST be recorded as actions occur, not as an optional report produced after execution. Every construction task, generated artifact, validation result, repair action, governance event, and deployment preparation output MUST be traceable according to ISL v1.4.
6. **Deterministic Validation Before Trust** — Generated artifacts MUST NOT be accepted as valid until required deterministic validation has passed or a governance-approved waiver exists. Reasoning outputs MUST pass through validation and governance boundaries before acceptance.
7. **Observability and Auditability** — The platform MUST expose sufficient observability and audit records to explain what the system did, why it did it, what evidence supports the result, and which governance decisions were applied.

### 2.2 Core Platform Subsystems

The platform SHALL include the following core subsystems:

| Subsystem | Primary Responsibility |
| --------- | ---------------------- |
| Specification Engine | Intake, parse, validate, and manage authored specifications |
| Semantic Model Engine | Normalize and manage canonical semantic models |
| Planning Engine | Generate and validate construction task graphs |
| Agent Orchestrator | Coordinate reasoning agents and model-backed reasoning tasks |
| Execution Runtime | Execute construction tasks and manage generation, validation, repair, and completion |
| Tool Integration Layer | Register, select, invoke, and normalize deterministic tools |
| Traceability Engine | Maintain lifecycle traceability graph |
| Governance Engine | Enforce policies, approval gates, waivers, overrides, and runtime decisions |
| Observability | Observe execution, collect telemetry, expose operational state |
| Artifact Repository | Store generated artifacts, metadata, versions, and evidence |
| Reusable Asset Registry | Catalog approved reusable assets, provenance, validation state, and dependency impact |
| State Manager | Maintain durable platform state, memory, checkpoints, snapshots, transactions, and recovery state |

The State Manager is the subsystem responsible for durable state and memory, including checkpoints, snapshots, transactions, and recovery state, as defined in ISL v2.2. The Observability subsystem is the platform's telemetry and monitoring capability, defined in ISL v2.3.

A conforming platform MUST preserve these logical responsibilities even if implementation packaging combines multiple subsystems into a single process or service.

### 2.3 Platform Layers

| Layer | Subsystems |
| ----- | ---------- |
| Specification Layer | Specification Engine, Semantic Model Engine |
| Planning Layer | Planning Engine, Traceability Engine |
| Execution Layer | Execution Runtime, Agent Orchestrator, Tool Integration Layer |
| Control Layer | Governance Engine, Observability |
| Artifact Layer | Artifact Repository, evidence stores, versioned outputs |
| State Layer | State Manager, memory, checkpoints, snapshots |
| Integration Layer | Model providers, deterministic tools, identity systems, observability systems, deployment targets |

Layer rules:

* The Specification Layer MUST produce validated semantic inputs before formal planning.
* The Planning Layer MUST produce valid construction plans before execution.
* The Execution Layer MUST operate under governance and traceability control.
* The Control Layer MUST be able to observe and influence readiness, planning, execution, repair, and deployment preparation.
* The Artifact Layer MUST preserve generated outputs, validation evidence, metadata, and lifecycle history.
* The State Layer MUST provide durable, recoverable state for execution, memory, and recovery.
* The Integration Layer MUST be accessed through approved abstraction interfaces.

### 2.4 Subsystem Responsibilities

#### 2.4.1 Specification Engine

The Specification Engine is the entry point through which authored ISL specifications enter the platform. It protects the platform from malformed, incomplete, unsupported, or unstructured specifications.

The Specification Engine MUST:

* accept authored ISL specifications
* validate document structure according to ISL v1.0
* verify required sections and required fields
* detect language-level errors
* identify the declared ISL version
* extract structured elements for normalization
* preserve source location metadata
* produce structural validation reports
* route valid structured input to the Semantic Model Engine

The Specification Engine MUST reject specifications with blocking ISL v1.0 structural errors when formal validation is requested. It MUST preserve source location information sufficient to report validation failures to authors. It MUST NOT perform autonomous construction or artifact generation.

#### 2.4.2 Semantic Model Engine

The Semantic Model Engine converts structured specification input into the canonical semantic model defined by ISL v1.1. It produces the authoritative machine representation used by readiness evaluation, planning, governance, traceability, and execution.

The Semantic Model Engine MUST:

* normalize parsed specification content into canonical entities
* assign or validate canonical identifiers
* construct canonical relationships
* resolve references
* validate entity schemas and relationship constraints
* detect semantic ambiguity
* produce canonical validation reports
* maintain canonical model versions
* expose canonical graph queries to authorized subsystems

The Semantic Model Engine MUST produce exactly one Project entity for each canonical model. It MUST reject canonical models with blocking semantic errors before Machine-Valid readiness. It MUST NOT infer missing required business semantics. It MUST preserve prior canonical model versions for traceability and impact analysis.

#### 2.4.3 Readiness Evaluation

Readiness evaluation determines whether a specification may progress from Draft to Reviewable, Machine-Valid, or Autonomous-Ready. It may be implemented as a separate subsystem or as a coordinated capability across the Specification Engine, Semantic Model Engine, Governance Engine, and Traceability Engine.

The platform MUST provide readiness evaluation capability that can:

* evaluate readiness criteria defined in ISL v1.3
* produce readiness reports and readiness transition records
* detect readiness regression
* verify evidence packages
* consult governance approval gates
* prevent unauthorized readiness progression
* expose current readiness state

A specification MUST NOT be marked Machine-Valid without passing canonical validation. A specification MUST NOT be marked Autonomous-Ready without required governance authorization. Readiness state MUST be version-specific, and readiness history MUST be preserved.

#### 2.4.4 Planning Engine

The Planning Engine transforms the canonical semantic model into a construction task graph. It is the bridge between specification meaning and executable work.

The Planning Engine MUST:

* validate planning preconditions
* generate construction tasks from canonical entities
* assign task types and responsible roles
* resolve dependencies and detect circular dependencies
* generate verification tasks and artifact production plans
* identify parallel groups and calculate critical path
* identify governance constraints
* validate construction plans
* adapt plans using impact analysis
* produce execution handoff records

The Planning Engine MUST NOT produce an executable plan for a specification below the readiness level permitted by ISL v1.3. Every construction task MUST trace to a specification entity or platform rule. A construction plan MUST NOT be handed to execution unless it passes planning validation.

#### 2.4.5 Agent Orchestrator

The Agent Orchestrator coordinates reasoning agents used during interpretation, planning, generation, review, repair, testing, security validation, and deployment preparation. It does not replace governance, validation, or execution control; it operates under the Execution Runtime and Governance Engine.

The Agent Orchestrator MUST:

* manage agent roles and assignments
* receive structured task requests from the Execution Runtime
* assemble permitted task context
* route reasoning tasks to model providers through the model abstraction boundary
* capture and validate agent outputs against expected structure
* return outputs to the Execution Runtime
* record model invocation metadata where applicable
* preserve traceability from task to agent output

Standard agent roles and their contracts are defined in ISL v2.1. Agents MUST operate on structured task context. Agents MUST NOT directly mutate the canonical model, task graph, artifact repository, or governance state unless mediated by authorized platform interfaces. Agent outputs MUST NOT be accepted as valid artifacts without deterministic validation where validation is applicable. Agent activity MUST be traceable to tasks and model invocations.

#### 2.4.6 Model Abstraction Boundary

The model abstraction boundary decouples agent logic from provider-specific behavior. The platform may use local models, enterprise model services, or external model APIs, but agent logic MUST remain provider-neutral. The full model integration and abstraction layer is defined in ISL v2.1.

The model abstraction boundary MUST:

* provide a standard model invocation interface
* enforce model provider configuration and governance restrictions
* route requests to approved providers
* apply context assembly rules
* capture invocation metadata
* normalize model outputs where required
* support error handling and fallback where approved

Agents MUST invoke models through the model abstraction boundary. The platform MUST NOT expose unbounded specification, artifact, or governance context to models without context control. External model usage MUST be governed where policy requires. Model outputs MUST be treated as reasoning outputs, not deterministic validation results.

#### 2.4.7 Execution Runtime

The Execution Runtime is the operational core of autonomous construction. It executes construction task graphs and coordinates artifact generation, validation, repair, testing, policy validation, consolidation, and deployment preparation. Its full behavior is defined in Section 3.0.

The Execution Runtime MUST:

* accept validated execution handoff records
* initialize execution state
* execute construction phases defined in ISL v1.2
* schedule eligible tasks
* invoke the Agent Orchestrator for reasoning tasks and the Tool Integration Layer for deterministic tool tasks
* write generated artifacts to the Artifact Repository
* initiate validation and repair cycles and enforce repair termination rules
* consult the Governance Engine at control points
* update traceability during execution
* produce execution records and completion reports

The Execution Runtime MUST NOT begin execution unless ISL v1.2 preconditions are satisfied. It MUST NOT mark artifacts valid without required validation. It MUST enforce governance decisions. It MUST maintain execution state sufficient for recovery.

#### 2.4.8 Tool Integration Layer

The Tool Integration Layer connects the platform to deterministic engineering tools used for compilation, testing, static analysis, security scanning, infrastructure validation, packaging, deployment preparation, and policy checking. It is the validation boundary between generated artifacts and trusted outputs.

The Tool Integration Layer MUST:

* maintain a Tool Registry with Tool Records and trust profiles
* select tools by declared capability
* enforce tool governance restrictions
* invoke tools through structured requests in controlled environments
* normalize tool outputs and produce structured findings
* detect tool failures, timeouts, conflicts, and version drift
* emit tool telemetry and record tool traceability links

The platform MUST NOT invoke unregistered tools. Tool outputs MUST be normalized before influencing execution state. Tool failure MUST NOT be interpreted as success. Security, deployment, and validation tool evidence MUST be retained where required by governance.

#### 2.4.9 Traceability Engine

The Traceability Engine maintains the persistent graph linking specification entities, tasks, artifacts, validations, repairs, decisions, tool invocations, model invocations, governance events, and deployment artifacts.

The Traceability Engine MUST:

* create traceability nodes and edges and enforce traceability identifier rules
* associate artifacts with source specification entities
* record task, validation, repair, tool, model, and governance links
* create traceability snapshots
* perform traceability integrity checks
* support impact analysis and bidirectional traceability queries
* preserve historical traceability across versions

Traceability MUST be recorded at lifecycle event time. Untraced artifacts MUST NOT be accepted as stable outputs. Execution completion MUST be blocked if required traceability is incomplete. Traceability snapshots MUST be created at required lifecycle points.

#### 2.4.10 Governance Engine

The Governance Engine enforces the governance and control model across readiness, planning, execution, tool usage, model usage, repair, waiver, override, deployment, and audit. Governance is an active runtime control layer, not a passive approval record.

The Governance Engine MUST:

* manage role assignments
* evaluate policies and enforce violation responses
* manage approval gates
* assign or validate risk tiers
* enforce separation-of-duties rules
* manage waivers and overrides
* open and resolve escalations
* evaluate governance runtime checks
* authorize or block deployment
* record immutable audit events and expose governance audit queries

The Governance Engine MUST return explicit decisions to runtime governance checks. Runtime components MUST obey governance decisions. Governance audit events MUST be immutable. Expired approvals, waivers, or overrides MUST NOT authorize progression.

#### 2.4.11 Observability

Observability provides visibility into platform activity, execution state, task progress, artifact generation, validation outcomes, tool performance, agent activity, governance events, and runtime health. Autonomous construction must be explainable and controllable.

Observability MUST:

* collect execution, agent, tool, governance, and artifact lifecycle telemetry
* expose task and phase progress, validation and repair status, and errors and escalations
* support dashboards or query interfaces and operational alerts where configured

The platform MUST record telemetry for major lifecycle events. Telemetry MUST be correlated with execution, task, artifact, tool, model, and governance identifiers where applicable. Telemetry does not replace audit logs or traceability records. The full telemetry event catalog is defined in ISL v2.3; the common telemetry event structure is defined in ISL v0.1 §9.

#### 2.4.12 Artifact Repository

The Artifact Repository stores generated outputs and related metadata produced during autonomous construction. It bridges autonomous generation and conventional software engineering practices by preserving source code, configuration, schemas, tests, contracts, infrastructure definitions, documentation, packages, and deployment materials.

The Artifact Repository MUST:

* store generated artifacts, metadata, and versions
* support repository organization by system, module, service, or artifact type
* support artifact status tracking
* preserve validation evidence or references and deployment preparation outputs
* support artifact retrieval by platform subsystems
* integrate with traceability records
* prevent stable acceptance of untraced artifacts

Artifact states use the shared Artifact State enum defined in ISL v0.1 §6.7. An artifact MUST NOT be marked stable unless it has required traceability and validation status. Artifact version history MUST be preserved. Superseded artifacts MUST remain available for audit or reconstruction where required.

#### 2.4.13 Reusable Asset Registry

The Reusable Asset Registry catalogs approved reusable assets, their provenance, validation state, and dependency impact. The Planning Engine queries it for applicable reuse before generation, and the Execution Runtime consults it for reuse-governed tasks. Reuse behavior is defined in ISL v1.5.

#### 2.4.14 State Manager

The State Manager maintains durable platform state and memory, including checkpoints, snapshots, transactions, and recovery state, as defined in ISL v2.2. It is the source of truth for execution progress and recovery. The Execution Runtime MUST commit state updates affecting task completion, artifact status, validation, repair, or governance through the State Manager.

### 2.5 Platform Data Flow

The standard platform data flow is:

1. Authored specification enters the Specification Engine.
2. Parsed specification enters the Semantic Model Engine.
3. Canonical model enters readiness evaluation.
4. Machine-Valid canonical model enters the Planning Engine.
5. Construction Task Graph performs reuse discovery and records Reuse Decisions where applicable.
6. Construction Task Graph enters governance evaluation and execution handoff.
7. Execution Runtime initializes the Execution Graph.
8. Agent Orchestrator produces reasoning outputs for authorized generation, delta generation, wrapping, extension, composition, and repair tasks.
9. Artifact outputs enter the Artifact Repository with pending status.
10. Tool Integration Layer validates artifacts.
11. Validation results update execution, traceability, and governance state.
12. Repair cycles update artifacts and validation records.
13. Consolidated artifacts enter stable repository state.
14. Approved reusable assets enter or update the Reusable Asset Registry.
15. Deployment preparation artifacts are generated and validated.
16. Completion report, traceability snapshot, and governance evidence are stored.

Data flow rules:

* The platform MUST NOT allow artifact generation before required readiness, reuse discovery, and governance conditions are met.
* The platform MUST NOT allow artifact acceptance before traceability and validation requirements are satisfied.
* The platform MUST NOT allow deployment authorization without governance approval.
* The platform MUST preserve evidence and traceability across data flow boundaries.

### 2.6 Control Boundaries

Control boundaries prevent one subsystem from bypassing another subsystem's responsibilities.

| Boundary | Control Requirement |
| -------- | ------------------- |
| Authored Specification → Canonical Model | Must pass parsing and normalization |
| Canonical Model → Readiness | Must pass semantic validation |
| Machine-Valid → Planning | Must satisfy planning permissions |
| Plan → Execution | Must pass planning validation and governance checks |
| Agent Output → Artifact Acceptance | Must pass deterministic validation where applicable |
| Tool Result → Runtime Decision | Must be normalized |
| Artifact → Stable Repository | Must have traceability and valid status |
| Runtime Action → Governed Action | Must consult Governance Engine when required |
| Deployment Preparation → Deployment Authorization | Must pass governance authorization |

Subsystems MUST NOT bypass required control boundaries. A platform implementation that combines subsystems into one deployable component MUST still enforce logical control boundaries. Control boundary violations MUST be recorded as platform integrity errors.

### 2.7 Platform State

The platform MUST maintain state for specifications, canonical models, readiness levels, construction plans, execution graphs, artifacts, validations, repairs, traceability graphs, governance records, tool records, model invocation records, telemetry and monitoring records, and deployment preparation records.

Platform state MUST be persistent where required for audit, recovery, traceability, or governance. Execution state MUST be recoverable after interruption. Governance and traceability state MUST NOT be silently overwritten. State updates affecting readiness, execution, artifacts, or governance MUST be auditable.

### 2.8 Security Architecture

Autonomous construction introduces security risk because the platform processes specifications, invokes models, executes tools, generates artifacts, and interacts with repositories and deployment systems.

The platform architecture MUST support:

* identity and access control for users and services
* role-based governance authorization
* controlled model invocation and controlled tool execution
* sandboxing for generated code execution where applicable
* artifact repository access control
* audit logging and secret handling restrictions
* network access controls for tool and model integrations
* protection of governance and traceability records
* dependency and supply-chain validation where applicable

Security boundary rules: Tools MUST execute through the Tool Integration Layer. Models MUST be invoked through the model abstraction boundary. Artifacts MUST enter the repository through controlled interfaces. Governance records MUST be protected from unauthorized modification.

### 2.9 Local-First and Enterprise Scalability

A conforming platform SHOULD support local deployment of all core subsystems, local deterministic tools, and local or self-hosted model providers where configured. The platform MUST NOT require external services for core specification validation, planning, traceability, governance, and artifact repository functions unless the deployment profile explicitly declares such dependencies. External provider usage MUST be configurable and governable.

The architecture SHOULD permit independent scaling of Execution Runtime workers, Agent Orchestrator capacity, model invocation infrastructure, Tool Integration workers, Artifact Repository storage, Traceability query infrastructure, and telemetry and monitoring storage. Scaling MUST NOT weaken governance, traceability, validation, or audit requirements. Distributed execution MUST preserve task state consistency. Parallel execution MUST respect dependencies and governance constraints. Multi-project operation MUST isolate specification, artifact, traceability, and governance contexts.

### 2.10 Platform Interfaces

A conforming platform SHOULD expose controlled interfaces for specification submission and retrieval, structural validation, canonical model retrieval, readiness evaluation, construction planning, execution initiation and monitoring, artifact repository access, traceability queries, governance approvals and audit queries, tool registry management, and telemetry and operational status.

Platform interfaces MUST enforce access control and preserve specification version context. Governance-controlled actions MUST require governance authorization. Interfaces that expose artifacts, governance records, or traceability records MUST enforce appropriate permissions.

---

## 3.0 Execution Runtime

### 3.1 Runtime Principles

The Execution Runtime is the platform subsystem responsible for executing an executable construction plan. A deployment MAY contain one or more runtime instances. The runtime coordinates many subsystems but MUST NOT absorb their responsibilities.

The runtime is governed by the following principles:

1. **Plan-Driven** — The runtime MUST execute work from a valid Construction Task Graph. It MUST NOT execute arbitrary construction work that is not represented as a task, lifecycle obligation, governance action, validation action, or recovery action.
2. **State-Driven** — The runtime MUST make scheduling and execution decisions from durable platform state. It MUST NOT depend on hidden process memory as the source of truth for execution progress.
3. **Governed** — The runtime MUST consult the Governance Engine at required control points. A governance decision of `block`, `escalate`, `approval-required`, `waiver-required`, or `override-required` MUST prevent the governed action from continuing.
4. **Traceable** — The runtime MUST create traceability links at the time runtime actions occur, connecting tasks, workers, agents, tools, artifacts, validations, repairs, governance events, and state transitions.
5. **Validation-Centered** — The runtime MUST NOT treat generated artifacts as valid until required validation has passed or a valid governance waiver exists.
6. **Recoverable** — The runtime MUST persist enough state, events, leases, checkpoints, and artifact metadata to recover safely after interruption.
7. **Structured Errors** — Every runtime error that affects execution progress, artifact status, governance, traceability, validation, or recovery MUST be recorded as a structured runtime error.

### 3.2 Runtime Responsibilities

The Execution Runtime MUST:

* accept execution handoff records from the Planning Engine
* validate execution admission preconditions and initialize execution state
* maintain execution graph state
* schedule eligible tasks and manage execution queues
* dispatch work to runtime workers
* manage task leases and runtime locks
* invoke agents through the Agent Orchestrator and tools through the Tool Integration Layer
* interact with the Artifact Repository through controlled interfaces
* initiate validation and repair tasks when permitted
* enforce retry, timeout, and repair limits
* consult the Governance Engine at required control points
* update traceability as runtime events occur
* update state and memory according to ISL v2.2
* emit runtime telemetry
* create checkpoints and recover after interruption
* produce runtime completion records

The runtime MUST NOT: execute a non-executable construction plan; begin autonomous construction for a non-Autonomous-Ready specification; bypass governance checks or artifact repository admission; mark artifacts valid without validation or waiver; modify canonical models directly; silently drop failed tasks; retry indefinitely; treat tool or agent timeout as success; or complete execution while required traceability is incomplete.

### 3.3 Runtime Inputs and Outputs

The runtime requires the following inputs before execution may begin: Execution Handoff Record, Construction Task Graph, Readiness State (Autonomous-Ready), Canonical Model Reference, Traceability State, Governance State, Artifact Repository Reference, Runtime Configuration, Tool Registry, Agent Role Registry, State Store Reference, and Observability Configuration (conditional).

All runtime inputs MUST reference the same specification-id and specification-version. The Construction Task Graph MUST match the plan-id in the Execution Handoff Record. The plan MUST have status executable, and the specification version MUST be Autonomous-Ready. The runtime MUST reject execution admission if input versions conflict.

The runtime produces: Execution Graph, Runtime State Records, Work Item Records, Task Execution Records, Agent and Tool Invocation Records, Artifact Records, Validation Records, Repair Records, Governance Check Records, Runtime Error Records, Runtime Telemetry, Checkpoint Records, and a Completion Report.

Runtime outputs MUST be persisted before they are used as evidence for downstream lifecycle actions. Runtime outputs that affect audit, governance, traceability, validation, artifacts, or recovery MUST NOT be stored only in transient memory.

### 3.4 Runtime Lifecycle

The runtime lifecycle uses the shared Execution Status enum defined in ISL v0.1 §6.11: `initialized`, `admission-checking`, `admitted`, `preparing`, `running`, `pausing`, `paused`, `recovering`, `draining`, `completed`, `failed`, `halted`, `cancelled`, `escalated`.

| State | Meaning |
| ----- | ------- |
| initialized | Runtime instance is available but no execution is active |
| admission-checking | Runtime is validating execution admission |
| admitted | Execution request accepted |
| preparing | Runtime is initializing queues, state, workers, and checkpoints |
| running | Runtime is actively executing work |
| pausing | Runtime is moving toward paused state |
| paused | Runtime is suspended but resumable |
| recovering | Runtime is validating and reconstructing state after interruption |
| draining | Runtime is finishing in-flight work but not accepting new dispatches |
| completed | Execution completed successfully |
| failed | Execution ended due to unrecoverable failure |
| halted | Execution stopped by governance, runtime, or operator decision |
| cancelled | Execution was intentionally cancelled before completion |
| escalated | Execution awaits governance or human action |

Lifecycle transition rules:

| From | To | Condition |
| ---- | -- | --------- |
| initialized | admission-checking | Execution request received |
| admission-checking | admitted | Admission checks pass |
| admission-checking | failed | Admission checks fail |
| admitted | preparing | Runtime begins setup |
| preparing | running | Queues, state, and checkpoints initialized |
| running | pausing | Pause requested or governance requires pause |
| pausing | paused | In-flight policy satisfied or suspended |
| paused | running | Resume authorized |
| running | draining | Completion, halt, cancellation, or controlled shutdown begins |
| draining | completed | Completion criteria satisfied |
| draining | halted | Halt confirmed |
| draining | cancelled | Cancellation confirmed |
| running | escalated | Blocking escalation opened |
| escalated | running | Escalation resolved with resume |
| escalated | halted | Escalation resolved with halt |
| running | recovering | Interruption or consistency failure detected |
| recovering | running | Recovery outcome is resume-safe |
| recovering | halted | Recovery cannot safely resume |
| running | failed | Unrecoverable failure occurs |

No other lifecycle transitions are permitted unless an approved extension defines them.

### 3.5 Execution Admission

Execution admission is the runtime gate that determines whether a construction plan may enter runtime execution. The runtime MUST verify that: the specification is Autonomous-Ready; the construction plan status is executable; the execution handoff record is valid; the canonical model reference exists; traceability state is initialized and active; governance state permits execution; required approval gates are satisfied; required tool capabilities and agent roles are available; required Reuse Decision Records exist for reuse-governed tasks; selected reusable assets are active and authorized where applicable; required Construction Boundaries and Connection Contexts are valid; the artifact repository and state store are available; runtime configuration is valid; no blocking regression exists; no expired waiver or override is being used; and no unresolved planning validation error remains.

An Admission Record captures the execution-request-id, plan-id, specification-id and version, admission-outcome (`admitted`, `rejected`, `escalated`), checks performed, failed checks, reuse-decision-ids, construction-boundary-ids, connection-context-ids, governance-check-id, required-action, evaluated-at, and evaluated-by.

A rejected execution request MUST NOT initialize execution queues. An escalated execution request MUST wait for governance resolution. An admitted execution request MUST create an Execution Graph and initial checkpoint.

### 3.6 Execution Graph

The runtime MUST maintain an Execution Graph derived from the Construction Task Graph and enriched with runtime state. It records the execution-graph-id, plan-id, specification-id and version, runtime-instance-id, execution-state, task nodes, work item nodes, artifact nodes, validation nodes, repair nodes, governance nodes, edges, and timestamps.

The Execution Graph MUST remain consistent with task, artifact, validation, repair, governance, and traceability state. Runtime workers MUST update Execution Graph state only through authorized runtime state transitions. The Execution Graph MUST be recoverable from durable state and events.

### 3.7 Task Scheduling

Task scheduling determines which tasks may run and when. Scheduling MUST respect dependencies, readiness, governance, resource limits, locks, priorities, worker availability, and runtime configuration.

A task is eligible for scheduling only when: task state is pending; all required dependencies are satisfied; required source artifacts or inputs exist; required governance gates are satisfied or not required; required Reuse Decision Records are present and not violated; required Construction Boundary and Connection Context records are valid; required agent role or tool capability is available; required runtime resources are available; required locks can be acquired; the task has not exceeded retry limits; and execution state permits dispatch.

The scheduler SHOULD consider task priority, critical path membership, dependency fan-out, resource requirements, worker/agent/model/tool availability, governance constraints, artifact lock conflicts, retry count, age in queue, risk tier, and parallel group membership.

The scheduler MUST NOT dispatch a task with unsatisfied required dependencies, a task blocked by governance, a reuse-governed artifact-producing task without a Reuse Decision Record, a task that crosses a Construction Boundary without a valid Connection Context, or a task requiring a lock that cannot be acquired. The scheduler MAY delay eligible tasks to optimize resource usage or preserve critical path scheduling. Scheduling decisions MUST be recorded.

### 3.8 Execution Queues

Execution queues hold work items awaiting dispatch. Queues may be implemented in memory, durable message brokers, relational tables, distributed queues, or other mechanisms, provided required semantics are preserved.

| Queue Type | Purpose |
| ---------- | ------- |
| ready | Work items eligible for execution |
| delayed | Work items delayed by time, resource, or retry policy |
| blocked | Work items blocked by dependency, governance, lock, or resource |
| retry | Work items awaiting retry |
| repair | Repair work items |
| validation | Validation and deterministic tool work |
| governance | Governance check or approval work |
| dead-letter | Work items that cannot proceed automatically |

A Work Item records its work-item-id, task-id, execution-graph-id, work-item-type (`agent`, `tool`, `validation`, `repair`, `governance`, `repository`, `platform`), queue-id, priority, required capabilities and locks, retry-count and max-retries, lease-id, available-at, and timestamps.

Queues used for distributed execution MUST be durable or recoverable. A work item MUST exist in exactly one queue at a time. A work item MUST NOT be dispatched unless it is in the ready, validation, repair, governance, or retry queue and available-at has passed. A work item that exceeds retry limits MUST be moved to dead-letter or escalated according to policy.

### 3.9 Runtime Workers

Runtime workers execute work items assigned by the scheduler. Workers may be local threads, background jobs, containers, distributed nodes, or services.

| Worker Type | Purpose |
| ----------- | ------- |
| agent-worker | Invokes agents through Agent Orchestrator |
| tool-worker | Invokes deterministic tools through Tool Integration Layer |
| validation-worker | Runs validation work and records results |
| repair-worker | Coordinates repair workflows |
| repository-worker | Performs repository operations |
| governance-worker | Executes governance checks and waits for approvals |
| platform-worker | Performs runtime lifecycle actions such as checkpoints and cleanup |

A Worker Record captures worker-id, worker-type, runtime-instance-id, capabilities, status (`idle`, `leased`, `running`, `draining`, `failed`, `disabled`), current-work-item-id, resource-profile, heartbeat-at, and started-at.

A worker MUST declare capabilities before receiving work and MUST send heartbeats while executing leased work. A worker that misses heartbeat thresholds MUST be treated as suspect. A failed worker MUST release or expire active leases through recovery logic. A worker MUST NOT execute work outside its declared capabilities.

### 3.10 Task Leases

Task leases prevent duplicate execution of the same work item in parallel or distributed environments. A Lease records lease-id, work-item-id, task-id, worker-id, lease-state (`active`, `renewed`, `released`, `expired`, `revoked`), acquired-at, expires-at, renewed-at, and release-reason.

A work item MUST NOT have more than one active lease. A worker MUST renew long-running leases before expiration. An expired lease MAY be reclaimed by another worker only after recovery checks confirm that no unsafe side effects remain. Lease expiration MUST NOT automatically mark a task failed; it MUST trigger recovery evaluation.

### 3.11 Runtime Locks

Runtime locks protect shared resources from unsafe concurrent modification.

| Lock Type | Protected Resource |
| --------- | ------------------ |
| artifact-lock | Artifact content or metadata |
| path-lock | Repository path |
| branch-lock | Repository branch |
| package-lock | Package manifest or bundle |
| state-lock | State record or transaction |
| governance-lock | Approval, waiver, or override target |
| tool-lock | Exclusive tool runtime or environment |
| model-lock | Restricted model capacity or provider constraint |

A Lock Record captures lock-id, lock-type, resource-id, owner-id, scope (`read`, `write`, `exclusive`), acquired-at, expires-at, status (`active`, `released`, `expired`, `revoked`), and release-reason.

A task that modifies an artifact MUST acquire the required artifact or path lock. A merge into stable repository state MUST acquire branch lock. A package creation operation MUST acquire package lock. Locks MUST be released after successful completion, failure handling, or recovery. A stale lock MUST trigger recovery before reuse of the protected resource.

### 3.12 Task Execution Lifecycle

Each task follows a controlled lifecycle within the runtime:

| Stage | Purpose |
| ----- | ------- |
| initialize | Load task definition and runtime context |
| governance-check | Consult governance where required |
| reuse-check | Verify Reuse Decision and selected asset authorization where required |
| acquire-resources | Acquire worker, locks, tools, models, and repository access |
| assemble-context | Assemble agent, tool, artifact, and validation context |
| execute | Perform agent, tool, repository, validation, or platform action |
| capture-output | Persist outputs, findings, artifacts, or records |
| validate-output | Validate agent output, tool result, artifact, or repository change |
| update-state | Commit state transitions and events |
| update-traceability | Record runtime traceability |
| determine-next-action | Proceed, repair, retry, escalate, halt, or complete |
| release-resources | Release leases, locks, and transient resources |

A Task Execution Record captures task-execution-record-id, task-id, work-item-id, execution-graph-id, worker-id, lifecycle-stage-results, timestamps, outcome (`completed`, `failed`, `retry`, `repaired`, `escalated`, `halted`, `cancelled`), produced artifacts, validation results, error records, and next-action.

A task MUST NOT enter the execute stage until required governance checks and resource acquisition complete. A reuse-governed task MUST NOT enter execute until the runtime verifies the Reuse Decision Record and selected reusable asset records. A boundary-scoped task MUST NOT enter execute until required Connection Contexts are valid and context assembly is bounded to the active Construction Boundary. A task that produces artifacts MUST route them through Artifact Repository admission. A task that requires validation MUST NOT be marked completed until validation passes or is waived. A task that fails validation MUST trigger repair, retry, escalation, or failure according to policy.

### 3.13 Agent Invocation During Runtime

The runtime invokes agents through the Agent Orchestrator according to ISL v2.1. The runtime MUST provide task-scoped context package requirements, request agent invocation through the Agent Orchestrator, preserve agent invocation records, validate agent outputs before downstream use, route artifact candidates to the Artifact Repository, route review/security/repair findings to appropriate tasks, apply retry limits for agent invocation failures, and escalate role violations or repeated failures.

Agent invocation runtime outcomes:

| Outcome | Runtime Action |
| ------- | -------------- |
| completed-valid-output | Continue to artifact admission, review, validation, or downstream task |
| completed-partial-output | Accept only if task contract permits partial output |
| output-invalid | Retry, request revision, or escalate |
| timeout | Retry according to policy, then escalate |
| role-violation | Reject and escalate if material |
| governance-blocked | Pause or halt affected task |
| model-failure | Retry, fallback, or escalate according to model policy |

### 3.14 Tool Invocation During Runtime

The runtime invokes deterministic tools through the Tool Integration Layer according to ISL v1.2. The runtime MUST select tools only through declared capabilities, invoke only registered and permitted tools, enforce tool trust profiles, pass structured invocation requests, record invocation results, normalize tool outputs before runtime decision-making, treat timeout and error as non-success outcomes, map tool findings to artifacts/validations/policies/tasks, and trigger repair or escalation when required.

Tool invocation runtime outcomes:

| Outcome | Runtime Action |
| ------- | -------------- |
| passed | Mark validation satisfied and proceed |
| failed | Trigger repair, retry, fail, or escalate |
| warning | Proceed only if warning is acceptable under policy |
| timeout | Retry once unless configuration is stricter |
| error | Retry, substitute tool, escalate, or fail |
| not-applicable | Record justification and confirm validation coverage remains sufficient |

### 3.15 Artifact Runtime Handling

The runtime interacts with artifacts only through the Artifact Repository model. The runtime MUST route generated artifact candidates through repository admission, assign or obtain artifact identifiers, record artifact metadata, preserve source task and source entity links, update artifact state after validation, prevent failed artifacts from stable promotion, acquire locks before artifact modification, preserve supersession history during repair, and trigger repository integrity checks at promotion and consolidation.

Artifact runtime state actions:

| Runtime Event | Artifact Action |
| ------------- | --------------- |
| artifact generated | admit as candidate or pending |
| validation passed | update validation-status to passed |
| validation failed | update state to failed |
| repair started | update or create repaired artifact version |
| repair validated | update state to valid or failed |
| promotion allowed | update state to stable |
| supersession created | link prior and replacement artifact |
| merge conflict detected | block promotion and create conflict record |

### 3.16 Validation Feedback Loop

Validation feedback loops ensure that artifacts converge toward correctness under deterministic validation. When validation is required, the runtime MUST: identify the validation task or criterion; select the appropriate tool or validation method; invoke validation; normalize and record the result; update artifact and task state; determine whether to proceed, repair, retry, escalate, or halt; and record traceability and telemetry.

Validation failures MUST NOT be ignored. Validation results MUST reference the artifact version evaluated. A failed validation MUST prevent artifact stable promotion unless waived. A validation result that triggers repair MUST link to the repair record.

### 3.17 Repair Runtime Management

Repair cycles are controlled runtime subflows triggered by validation, test, policy, or repository failure. The runtime MAY initiate repair only when: the failure is repairable; repair is permitted by governance; repair limits have not been reached; the failed artifact and validation result are available; a repair task can be generated or already exists; the required agent role is available; and required locks can be acquired.

The runtime MUST enforce repair limits:

| Parameter | Default |
| --------- | ------- |
| Maximum repair iterations per artifact | 5 |
| Maximum total repair iterations per construction plan | 50 |
| Escalation threshold for same artifact | 3 consecutive failures |
| Retry after tool timeout | 1 retry unless stricter configuration |
| Retry after agent timeout | 2 retries unless stricter configuration |

A repair MUST produce or reference a Repair Record. A repaired artifact MUST be revalidated. A repair MUST NOT mark an artifact valid without validation or waiver. A repair that reaches the iteration limit MUST escalate. A repair that introduces a governance violation MUST pause and route to the Governance Engine.

### 3.18 Retry and Timeout Model

Retries and timeouts prevent transient failures from becoming immediate terminal failures while also preventing indefinite execution. Timeout categories include task, agent, model, tool, validation, repository, governance, lease, and lock timeouts.

A Retry Policy records retry-policy-id, applies-to (`task`, `agent`, `model`, `tool`, `validation`, `repository`, `governance`), max-attempts, backoff-strategy (`none`, `fixed`, `exponential`, `jittered`), initial-delay-ms, max-delay-ms, retryable and non-retryable error classes, and escalation-after-exhaustion.

Retries MUST be bounded. A retry MUST create or update the retry count. A retry MUST NOT bypass governance or validation. Non-retryable errors MUST NOT be retried. Retry exhaustion MUST result in escalation, failure, dead-letter, or halt according to policy.

### 3.19 Parallel and Distributed Execution

The runtime MAY execute tasks in parallel when dependency, lock, governance, resource, and traceability requirements permit. Tasks MAY run in parallel only when: no dependency path exists between them; they do not require conflicting locks; they do not modify the same artifact or path without isolation; they do not require exclusive governance review; required workers and resources are available; traceability can be recorded independently; and runtime configuration permits concurrency.

The runtime MUST respect parallel groups produced by the Planning Engine. It MAY reduce concurrency below the planned parallel group but MUST NOT increase concurrency in a way that violates dependencies or governance constraints. Parallel task failure MUST pause dependent tasks but SHOULD NOT halt unrelated tasks unless policy requires halt.

A distributed runtime MUST provide durable shared or replicated state consistency, durable or recoverable queues, lease-based task claiming, heartbeat monitoring, lock coordination, artifact repository consistency, traceability event ordering, governance decision consistency, and recovery after worker failure. Distributed workers MUST NOT rely on local-only state for lifecycle-critical decisions. State updates affecting task completion, artifact status, validation, repair, or governance MUST be committed through the State Manager. Split-brain execution MUST be detected and halted or resolved before continuing.

### 3.20 Runtime Governance Enforcement

The runtime MUST consult governance before: execution start; governed task dispatch; restricted model or tool use; high-risk artifact generation; repair of high-risk artifacts; waiver or override application; artifact promotion when governed; merge into stable branch when governed; package creation when governed; and deployment preparation completion.

Governance runtime actions use the shared Governance Decision enum defined in ISL v0.1 §6.6:

| Governance Decision | Runtime Action |
| ------------------- | --------------- |
| allow | Continue |
| warn | Continue and record warning |
| block | Stop affected action |
| escalate | Pause affected action and open escalation |
| approval-required | Open or wait for approval gate |
| waiver-required | Pause until valid waiver exists |
| override-required | Pause until valid override exists |

The runtime MUST obey governance decisions and record governance check responses. The runtime MUST NOT continue a governed action using an expired approval, waiver, or override. A governance audit write failure MUST halt the governed action.

### 3.21 Runtime Checkpoints

Runtime checkpoints provide recovery-safe markers. The runtime MUST create checkpoints after execution admission, before execution start, before and after artifact consolidation, before deployment preparation, before and after high-risk repair, before governance override application, after plan adaptation, before controlled shutdown, and at execution completion.

A Checkpoint records checkpoint-id, execution-graph-id, plan-id, specification-id and version, checkpoint-type (`admission`, `start`, `phase`, `repair`, `consolidation`, `deployment`, `governance`, `shutdown`, `completion`), included-state-categories, queue/lease/lock state references, artifact-state-reference, traceability-snapshot-id, created-at, and recovery-eligible.

A checkpoint MUST be durable. A recovery-eligible checkpoint MUST include enough state to resume or safely halt. A checkpoint MUST NOT claim recovery eligibility if required state categories are missing.

### 3.22 Runtime Recovery

Runtime recovery restores execution after interruption, crash, worker failure, lease expiration, lock expiration, or state inconsistency. The runtime MUST initiate recovery when: the runtime process restarts during active execution; a worker heartbeat expires while a lease is active; a lease or lock expires during in-progress work; a state transaction fails; repository state differs from artifact state; traceability recording fails; an execution graph integrity check fails; distributed split-brain is detected; or checkpoint validation fails.

Runtime recovery MUST: enter the recovering state; stop new dispatches; identify active leases and locks; validate worker heartbeats; load the latest recovery-eligible checkpoint; inspect state events after the checkpoint; validate task, artifact, validation, repair, traceability, governance, and repository state; requeue safe work items; escalate unsafe or ambiguous work; release stale locks when safe; record the recovery event; and resume, halt, or escalate.

The runtime MUST NOT resume dispatch until recovery validation completes. In-flight work with uncertain side effects MUST be escalated or isolated before retry. Artifacts produced by interrupted tasks MUST remain pending, failed, or quarantined until validated. Recovery MUST preserve traceability and audit history.

### 3.23 Runtime Completion

Execution MAY be marked completed only when: all required tasks are completed, skipped, or waived; no required work item remains ready, delayed, retry, repair, validation, or blocked; all required artifacts are stable, waived, or governed as non-deployable; required validations passed or were waived; repair cycles are resolved; no blocking governance gate is pending; traceability and repository integrity checks passed; deployment preparation completed where required; a completion report is generated; and a final checkpoint exists.

A Completion Record captures runtime-completion-id, execution-graph-id, plan-id, specification-id and version, final-state (`completed`, `failed`, `halted`, `cancelled`, `escalated`), tasks completed/failed, artifacts stable, validations passed, waivers used, errors recorded, final-checkpoint-id, and completed-at.

The runtime MUST NOT mark execution completed while any blocking error remains unresolved, or if required traceability or repository integrity fails. A failed, halted, cancelled, or escalated final state MUST include required-action or resolution guidance.

### 3.24 Runtime Configuration

Runtime configuration controls scheduling, workers, retries, timeouts, isolation, governance checks, and telemetry. It records runtime-configuration-id, configuration-version, scheduling-policy, max-parallel-tasks, max-workers, default task/agent/tool timeouts, retry policies, repair limits, lease-duration-seconds, heartbeat-interval-seconds, lock-timeout-seconds, isolation-profile, governance-enforcement-level (`strict`, `standard`, `advisory`), telemetry-profile, and timestamps.

Runtime configuration MUST be versioned. The configuration active at execution start MUST be recorded. Configuration changes during active execution MUST trigger impact evaluation. Governance-enforcement-level `advisory` MUST NOT be used for Autonomous-Ready execution unless governance explicitly permits it for the risk tier.

### 3.25 Runtime Isolation and Resource Control

The runtime MUST support task workspace isolation, artifact write controls, tool sandboxing through the Tool Integration Layer, model invocation controls through model abstraction, repository branch or path isolation, secret access restrictions, network access restrictions, resource limits for worker execution, and cleanup of transient workspaces.

A worker MUST NOT exceed configured resource limits without authorization. A task requiring generated-code execution SHOULD run in an isolated environment. A tool requiring network access MUST operate under an approved network policy. Secrets MUST NOT be injected into task context unless explicitly required and governed.

### 3.26 Dead-Letter and Quarantine Handling

A work item MUST be moved to dead-letter or escalated when: retry limit is exceeded; required capability remains unavailable; governance blocks continuation; a malformed work item cannot be corrected; state consistency cannot be established; or repeated worker failure occurs.

Artifacts or outputs MUST be quarantined when: provenance is unclear; traceability is missing; a security finding indicates unsafe content; repository integrity is uncertain; an interrupted task produced uncertain side effects; or an artifact was created outside the approved repository flow.

Quarantined artifacts MUST NOT be promoted and MUST be isolated from stable repository state. Quarantine MUST produce a runtime error and repository finding. Governance or recovery action is required before release from quarantine.

### 3.27 Runtime Security and Audit

The runtime MUST enforce worker identity, repository and governance access controls, task workspace isolation, secret access control, prevention of direct agent writes to stable artifacts, prevention of unregistered tool execution and model invocation outside the approved abstraction layer, detection of sandbox violations, recording of security-relevant runtime errors, and halting or isolation of unsafe execution paths.

Secrets MUST NOT be included in agent context packages unless explicitly required and governed. Secrets used by tools MUST be scoped to the task and environment. Secret exposure findings MUST be treated as security errors.

The runtime MUST preserve audit-relevant records: execution admission records, execution graph records, scheduling records, task execution records, worker and lease records, lock records for governed operations, agent and tool invocation records, validation records, repair records, artifact state transitions, governance check records, runtime error records, recovery records, and completion records. Audit-relevant records MUST include specification-id and specification-version where applicable and MUST be retained according to governance and state retention rules. Runtime records used for governance decisions MUST be immutable or correction-only.

---

## 4.0 Conformance

A platform conforms to ISL v2.0 if it includes or implements the required logical responsibilities for the Specification Engine, Semantic Model Engine, readiness evaluation, Planning Engine, Agent Orchestrator, model abstraction boundary, Execution Runtime, Tool Integration Layer, Traceability Engine, Governance Engine, Observability, Artifact Repository, Reusable Asset Registry, and State Manager.

A platform conforms to subsystem boundary requirements if it enforces specification-to-canonical validation boundaries, readiness gates before planning and execution, planning validation before execution, deterministic validation before artifact acceptance, traceability before stable artifact acceptance, governance decisions at runtime, and deployment authorization before deployment.

A platform conforms to integration requirements if it invokes models only through the model abstraction boundary, invokes tools only through the Tool Integration Layer, stores artifacts only through controlled repository interfaces, records traceability through the Traceability Engine, records governance decisions through the Governance Engine, and records telemetry through Observability.

A runtime instance conforms to ISL v2.0 if it can accept and validate execution handoff records, enforce execution admission checks, initialize and maintain execution graph state, schedule tasks based on dependencies and governance, enforce Reuse Decision and Connection Context checks before dispatch, manage queues, leases, locks, workers, and retries, invoke agents through the Agent Orchestrator and tools through the Tool Integration Layer, route artifacts through the Artifact Repository, enforce validation before artifact acceptance, enforce bounded repair cycles, create checkpoints, recover after interruption, emit runtime telemetry, and produce runtime error and completion records.

A platform conforms to enterprise architecture expectations if it supports auditability, role-based governance, historical record preservation, versioned specifications and artifacts, a local-first deployment profile, controlled scaling, protection of sensitive state and artifacts, and required governance, traceability, and monitoring queries.

A conforming ALETHEIA platform MUST preserve the architecture's control boundaries even when subsystems are implemented within a single process or service. The platform may vary in technology, deployment topology, and scale, but it MUST remain specification-driven, traceable, governed, observable, and validation-centered.
