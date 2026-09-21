# ALETHEIA Specification Language (ISL) v3.3
# Runtime Orchestration and Autonomous Development Loop

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x, ISL v2.x
**Supersedes:** ISL r2 v3.5 The Runtime Orchestration Architecture, ISL r2 v3.6 The Autonomous Development Loop
**Document Type:** Execution Layer Specification

---

## 1.0 Scope

This document defines the Runtime Orchestration Architecture and the Autonomous Development Loop of the ALETHEIA execution layer. It specifies the repeatable control cycle through which the platform transforms formal specifications into validated, traceable, repository-controlled, package-ready, and deployment-prepared software artifacts, and the runtime orchestration that executes that cycle.

The Autonomous Development Loop is the platform-level control cycle. Runtime orchestration is its executor: the loop defines what must happen and in what order; the orchestrator turns the executable construction plan into ordered, governed, observable, recoverable work. This document presents the construction flow once as the control cycle and then specifies orchestration as its executor.

Shared conventions — normative language, field requirement levels, identifiers, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and referenced here rather than redefined.

---

## 2.0 Control Model

The platform operates as a governed feedback system. Each iteration produces artifacts, evidence, validation results, state transitions, traceability links, telemetry, and decisions that determine whether the platform should proceed, repair, replan, package, prepare deployment, escalate, halt, or fail.

The loop is the control cycle; orchestration is its executor. The loop defines lifecycle states, stage gates, convergence criteria, and termination rules. The orchestrator realizes those states and gates by scheduling, dispatching, and coordinating work through the platform services (Agent Orchestrator, Model Integration Layer, Tool Integration Layer, Artifact Service, Governance Service, Traceability Service, State Manager, Telemetry Service).

---

## 3.0 Principles

### 3.1 Specification-Driven Operation

The loop MUST begin from a versioned specification or a governed change to an existing specification. The loop MUST NOT begin from informal instructions, hidden context, or untracked implementation changes.

### 3.2 Evidence-Based Progression

A loop stage MUST NOT be considered complete unless required evidence exists. Evidence MAY include canonical model records, construction plans, task state, artifacts, validation results, repair records, traceability snapshots, governance decisions, or completion reports.

### 3.3 Validation Before Acceptance

Generated or modified artifacts MUST be validated before they are promoted to stable state. Agent or model assertion MUST NOT substitute for deterministic validation when deterministic validation is applicable.

### 3.4 Bounded Repair

Repair cycles MUST be bounded by configured repair limits. The loop MUST escalate or fail when repair limits are reached.

### 3.5 State-Controlled Continuation

The loop MUST use durable platform state to determine continuation. Telemetry, logs, or in-memory task status MUST NOT be the sole authority for loop progression.

### 3.6 Governance-Aware Autonomy

The loop MAY operate autonomously only within governance constraints. Governance decisions MUST be treated as loop control signals.

### 3.7 Targeted Re-Entry

When a specification changes, the loop SHOULD use traceability and impact analysis to identify affected tasks and artifacts. The platform SHOULD avoid full regeneration when targeted reconstruction is sufficient and governed.

### 3.8 Reuse Before Generation

The loop MUST prefer approved reusable assets over new generation when reuse is applicable. Artifact-producing work MUST NOT enter unconstrained generation until required Reuse Discovery and Reuse Decision Records exist.

### 3.9 Boundary-Controlled Context

The loop MUST execute tasks within their active Construction Boundary and MUST transfer context across boundaries only through valid Connection Contexts. Context packages SHOULD be minimized so local and resource-constrained model deployments can operate without inheriting unrelated construction history.

### 3.10 Orchestrator Coordinates but Does Not Own All Work

The runtime orchestrator MUST coordinate execution but MUST NOT absorb the responsibilities of other platform services. It MUST call the Agent Orchestrator for agent work, the Tool Integration Layer for deterministic tool work, the Artifact Service for artifact lifecycle work, the Governance Service for governed decisions, the Traceability Service for traceability writes, and the State Manager for state transitions.

### 3.11 Task Graph Is the Source of Runtime Work

The orchestrator MUST execute work derived from a valid Construction Task Graph. It MUST NOT create arbitrary construction work that is not represented as a task, validation obligation, repair obligation, governance action, repository action, recovery action, or lifecycle action.

### 3.12 Runtime State Is Authoritative

The orchestrator MUST make scheduling and continuation decisions from durable runtime state. In-memory scheduler state MAY be used for efficiency but MUST NOT be the sole source of truth.

### 3.13 Events Trigger Work, State Controls Work

The orchestration architecture MAY be event-driven. However, events MUST NOT bypass state validation. An event may trigger scheduling evaluation, but the current state MUST determine whether work is eligible.

### 3.14 Concurrency Requires Isolation

Parallel execution MUST be permitted only when dependencies, locks, leases, artifact paths, governance constraints, and resource limits allow it.

### 3.15 Recovery Is an Orchestration Responsibility

The orchestrator MUST detect interrupted, ambiguous, expired, or inconsistent runtime work and coordinate recovery before resuming dispatch.

---

## 4.0 The Construction Flow

The construction flow is the canonical control cycle of the loop. It is defined once here; the sections that follow specify the orchestration that executes it and the gates that govern progression.

The flow is: **spec → normalize → plan → execute → validate → repair → promote → trace → report**.

### 4.1 Stage 1 — Specification Interpretation (spec → normalize)

Specification Interpretation converts the authored specification into canonical form, giving the loop a machine-interpretable source of truth. Required inputs: authored specification, ISL syntax or representation schema, canonical entity schemas, identifier policy, readiness target. Required outputs: parsed specification record, canonical model record, canonical entity records, canonical relationship records, canonical validation report, specification-to-canonical traceability links.

Interpretation is complete only when required specification metadata exists, required sections and entities are parsed, canonical model generation succeeds, canonical validation has no blocking findings, canonical model state is persisted, and traceability links from source specification to canonical entities exist. A blocking parse error MUST fail or escalate the loop. A blocking canonical validation error MUST prevent planning.

### 4.2 Stage 2 — Construction Planning (plan)

Construction Planning converts the canonical model into an executable construction plan. Required inputs: canonical model record, readiness state, governance constraints, tool capability catalog, agent role registry, reusable asset registry, active Construction Boundaries, applicable Connection Contexts, artifact policy, traceability graph. Required outputs: construction plan record, construction task graph, task dependency records, expected artifact declarations, reuse discovery records where applicable, reuse decision records where applicable, boundary continuation records where applicable, validation task declarations, repair task declarations, planning validation report.

Planning is complete only when every required canonical entity is addressed by a task or justified exclusion, task dependencies are valid, required agent roles and tool capabilities are available, expected artifacts are declared, applicable Reuse Discovery has completed, applicable Reuse Decision Records authorize full reuse, delta generation, wrapper, extension, composition, or new generation, active Construction Boundaries and Connection Contexts are valid, validation tasks are declared, repair policy is attached, and planning validation passes.

A construction plan with unsatisfied dependencies, missing required validation tasks, or missing required Reuse Discovery or Reuse Decision Records MUST NOT become executable. A plan requiring unavailable required tools or agents MUST be rejected, delayed, or escalated. A plan that requires boundary crossing without a valid Connection Context MUST NOT become executable.

### 4.3 Stage 3 — Reuse-Gated Runtime Execution and Artifact Generation (execute)

Runtime Execution performs the construction tasks that generate, modify, or prepare artifacts. Agents, models, tools, and repository services work under runtime orchestration to create candidate implementation artifacts, admit reused assets, or produce authorized deltas, wrappers, extensions, and compositions. Required inputs: executable construction plan, execution graph, runtime configuration, agent registry, model routing policy, reuse decision records, selected reusable asset records where applicable, active Construction Boundary, Connection Contexts required for boundary crossing, artifact repository workspace, governance state, traceability state. Required outputs: task execution records, agent invocation records, model invocation records when models are used, candidate artifact records, artifact content references, reuse provenance records where applicable, artifact admission records, artifact state records, runtime telemetry.

Execution is complete only when required artifact-producing tasks have completed, full generation was not used when reuse, delta, wrapper, extension, or composition was authorized instead, generated artifacts are admitted into repository working state, artifacts include metadata, reused or reuse-derived artifacts include reuse provenance metadata, artifacts reference producing tasks and source entities, model invocations remained within the active Construction Boundary and authorized Connection Contexts, task state is persisted, and generation-related traceability links exist.

A failed artifact-producing task MUST trigger retry, repair, escalation, or failure according to runtime policy. An artifact candidate missing required metadata MUST be rejected. An artifact produced outside repository admission MUST be quarantined or rejected. An artifact generated contrary to a Reuse Decision Record MUST be rejected or routed to governance as a reuse exception. Context drift outside the active Construction Boundary or authorized Connection Context MUST halt the affected task and trigger repair, replan, or escalation.

### 4.4 Stage 4 — Deterministic Validation (validate)

Deterministic Validation verifies artifacts using registered tools and defined validation criteria. Required inputs: pending or repaired artifacts, validation tasks, tool capability catalog, validation profile, artifact metadata, governance policy. Required outputs: tool invocation records, normalized tool results, validation result records, validation findings, validation evidence, artifact validation state updates, validation-to-artifact traceability links.

The loop SHOULD support compile or build validation, unit test validation, integration test validation where applicable, static analysis where configured, security scan where configured, dependency audit where configured, configuration validation where configured, repository integrity validation, and traceability integrity validation.

Validation is complete only when required validation tasks have run, validation outcomes are recorded, validation evidence is retained, artifact state reflects validation results, failed validations are classified, and next action is determined for each failed validation. A failed required validation MUST prevent artifact promotion unless a valid waiver exists. A tool timeout MUST NOT be treated as success. A missing validation result MUST block convergence. A validation result without artifact version reference MUST be invalid.

### 4.5 Stage 5 — Automated Repair (repair)

Automated Repair responds to validation failures and repository findings. Required inputs: failed validation result, failed artifact metadata, failed artifact content reference, tool findings, source canonical entities, repair policy, prior repair records, governance constraints. Required outputs: repair analysis record, repair proposal, repair task record, revised artifact version or correction record, supersession metadata when applicable, revalidation request, repair outcome record.

| Parameter | Default | Configurable |
| --------- | ------: | ------------ |
| Maximum repair iterations per artifact | 5 | YES |
| Maximum total repair iterations per construction plan | 50 | YES |
| Escalation threshold for same artifact | 3 consecutive failures | NO |

Repair is complete only when the failed validation is linked to a repair record, a repair proposal is produced or escalation is recorded, affected artifacts are identified, revised artifacts are admitted to repository state, revised artifacts are revalidated, and repair outcome is recorded. Repair MUST NOT continue beyond configured repair limits. Three consecutive failures on the same artifact MUST escalate. A repair proposal that cannot identify affected artifacts MUST escalate. A repaired artifact MUST NOT be promoted without revalidation or a valid waiver.

### 4.6 Stage 6 — Convergence Evaluation (promote)

Convergence Evaluation determines whether the generated system satisfies the current loop scope. Required inputs: execution graph, task states, artifact states, validation records, repair records, traceability graph, governance state, repository integrity report. Required outputs: convergence evaluation record, convergence outcome, unresolved issue list, next action decision.

| Outcome | Meaning |
| ------- | ------- |
| converged | All required criteria satisfied |
| repair-required | Validation or repository failure can be repaired |
| replan-required | Plan is insufficient or invalid |
| waiver-required | Validation or policy exception requires governance waiver |
| escalation-required | Human or governance decision required |
| failed | Required criteria cannot be satisfied |
| halted | Runtime or governance halt applies |

The loop converges only when all required tasks are completed, skipped, or waived; all required artifacts are valid or waived; no required validation is missing; no blocking repair remains unresolved; repository integrity passes; traceability integrity passes; governance gates are closed or validly waived; and an evidence bundle can be created. The loop MUST NOT converge when required validation failed, when stable artifacts are untraced, when governance blocks continuation, or solely because no runnable tasks remain.

### 4.7 Stage 7 — Artifact Packaging (promote)

Artifact Packaging prepares stable artifacts for distribution, release, or deployment preparation. It occurs only after convergence or after a governed packaging-only loop entry. Required inputs: stable artifact records, artifact content references, validation evidence, traceability snapshot, package profile, governance state. Required outputs: package manifest, package artifact records, package validation records, package evidence records, package traceability links.

Packaging is complete only when the package manifest references exact artifact versions, package contents are stable or explicitly governed exceptions, required package validation passes, a package hash or equivalent integrity marker exists where supported, package evidence is retained, and package traceability links exist. Packaging MUST NOT include failed or untraced artifacts. Packaging MUST NOT include waived artifacts without recording waiver references in the package manifest. Packaging failures MUST trigger repair, repackaging, escalation, or failure.

### 4.8 Stage 8 — Deployment Preparation (report)

Deployment Preparation prepares packaged artifacts for operational deployment. It bridges construction and operation; it does not necessarily deploy the system, but prepares the deployment evidence, manifests, infrastructure definitions, and authorization records required for deployment. Required inputs: package manifest, stable package artifacts, target environment profile, deployment preparation profile, infrastructure or deployment requirements, governance state, operational readiness criteria. Required outputs: deployment preparation record, deployment manifest or deployment descriptor, environment compatibility report, deployment validation results, operational readiness findings, deployment authorization request when required.

Deployment Preparation is complete only when deployment manifests or descriptors are produced where required, target environment compatibility is verified or exceptions are recorded, required deployment validation passes, operational readiness findings are resolved, waived, or escalated, and a deployment authorization request is created when required. Deployment preparation MUST NOT authorize production deployment by itself. Deployment authorization MUST follow governance rules. Deployment preparation MUST preserve traceability to package artifacts and specification version.

### 4.9 Trace and Report

Throughout the flow, the platform MUST record traceability links and produce evidence. A successful loop MUST produce a complete evidence bundle; a failed loop SHOULD produce a partial evidence bundle when possible. Every loop MUST produce a completion report. The traceability snapshot and evidence bundle are required outputs of every loop run.

---

## 5.0 Loop Lifecycle States

The Autonomous Development Loop MUST maintain explicit lifecycle state.

| State | Meaning |
| ----- | ------- |
| not-started | Loop has not begun |
| admitted | Loop has passed admission checks |
| interpreting | Specification interpretation is active |
| planning | Construction planning is active |
| executing | Artifact generation and runtime execution are active |
| validating | Deterministic validation is active |
| repairing | Repair cycle is active |
| converging | Platform is evaluating whether generated system satisfies completion criteria |
| packaging | Artifact packaging is active |
| preparing-deployment | Deployment preparation is active |
| completed | Loop completed successfully for current scope |
| re-entering | Loop is restarting due to specification, artifact, policy, tool, or environment change |
| escalated | Loop requires governance or human resolution |
| halted | Loop was stopped by policy, runtime, or operator decision |
| failed | Loop ended because required criteria could not be satisfied |
| cancelled | Loop was intentionally cancelled |

### 5.1 Lifecycle Transition Rules

| From | To | Condition |
| ---- | -- | --------- |
| not-started | admitted | Admission checks pass |
| admitted | interpreting | Specification interpretation begins |
| interpreting | planning | Canonical model passes required validation |
| planning | executing | Construction plan is executable and applicable reuse decisions and boundary contexts are complete |
| executing | validating | Generated artifacts are ready for validation |
| validating | repairing | Required validation fails and repair is permitted |
| repairing | validating | Repaired artifacts are ready for revalidation |
| validating | converging | Required validation passes or valid waivers exist |
| converging | packaging | Convergence criteria satisfied and packaging required |
| converging | completed | Convergence criteria satisfied and packaging not required |
| packaging | preparing-deployment | Packaging succeeds and deployment preparation required |
| packaging | completed | Packaging succeeds and deployment preparation not required |
| preparing-deployment | completed | Deployment preparation criteria satisfied |
| completed | re-entering | Governed change requires new loop cycle |
| re-entering | interpreting | New or changed specification scope admitted |
| any active state | escalated | Governance, repair, validation, or ambiguity requires resolution |
| any active state | halted | Runtime, governance, security, or operator halt occurs |
| any active state | failed | Terminal failure occurs |
| any active state | cancelled | Cancellation is authorized |

No other transition is permitted unless defined by an approved extension.

---

## 6.0 Loop Inputs and Outputs

### 6.1 Loop Inputs

Each loop run MUST declare its inputs.

| Input | Required | Description |
| ----- | -------- | ----------- |
| specification-id | YES | Specification being constructed or changed |
| specification-version | YES | Version being constructed or changed |
| canonical-model-version | CONDITIONAL | Required after interpretation |
| readiness-state | YES | Readiness level and execution eligibility |
| construction-plan-id | CONDITIONAL | Required after planning |
| reusable-asset-registry-id | CONDITIONAL | Required when reuse discovery applies |
| active-construction-boundary-id | CONDITIONAL | Required when execution is boundary-scoped |
| connection-context-ids | CONDITIONAL | Required when work crosses Construction Boundaries |
| governance-profile-id | YES | Governance profile controlling loop |
| runtime-configuration-id | YES | Runtime configuration |
| model-routing-policy-id | CONDITIONAL | Required when agents use models |
| tool-profile-id | YES | Tool capabilities available |
| artifact-repository-id | YES | Repository target |
| traceability-graph-id | YES | Traceability graph for loop |
| telemetry-profile-id | YES | Telemetry profile |
| change-trigger-id | CONDITIONAL | Required for re-entry cycles |

All loop inputs MUST reference compatible specification and version context. The loop MUST reject input sets with conflicting specification versions, and MUST reject execution if required tools, agents, stores, or governance profiles are unavailable. A re-entry loop MUST identify the trigger that caused re-entry. The loop MUST reject execution when a required Connection Context is missing, expired, superseded, or inconsistent with the active Construction Boundary.

### 6.2 Loop Outputs

Each loop run MUST produce outputs.

| Output | Required | Description |
| ------ | -------- | ----------- |
| autonomous-loop-run-record | YES | Primary loop run record |
| canonical-model-record | CONDITIONAL | Required after interpretation |
| construction-plan-record | CONDITIONAL | Required after planning |
| reuse-discovery-records | CONDITIONAL | Required when reuse discovery applies |
| reuse-decision-records | CONDITIONAL | Required when reuse decisions are required |
| boundary-continuation-records | CONDITIONAL | Required when work crosses Construction Boundaries |
| execution-graph-record | YES | Runtime execution graph |
| artifact-records | YES | Generated or modified artifact metadata |
| validation-records | YES | Validation results |
| repair-records | CONDITIONAL | Required when repair occurs |
| package-records | CONDITIONAL | Required when packaging occurs |
| deployment-preparation-records | CONDITIONAL | Required when deployment preparation occurs |
| governance-records | CONDITIONAL | Required for governed actions |
| traceability-snapshot | YES | Final traceability snapshot |
| telemetry-summary | YES | Loop telemetry summary |
| evidence-bundle | YES | Evidence bundle |
| loop-completion-report | YES | Final loop report |

A successful loop MUST produce a complete evidence bundle. A failed loop SHOULD produce a partial evidence bundle when possible. Loop outputs MUST be traceable to loop inputs.

---

## 7.0 Canonical Run Record

Each loop execution MUST produce one canonical run record that captures the entire run. This is the single run record for the loop; orchestration records (§9) are its supporting detail.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| loop-run-id | string | REQUIRED | Unique loop run identifier |
| loop-run-type | enum | REQUIRED | initial-construction, repair-only, revalidation, change-driven, packaging-only, deployment-preparation |
| specification-id | string | REQUIRED | Specification identifier |
| specification-version | semver | REQUIRED | Specification version |
| change-trigger-id | string | CONDITIONAL | Change trigger for re-entry |
| prior-loop-run-id | string | CONDITIONAL | Prior loop run when continuing or re-entering |
| current-state | enum | REQUIRED | Lifecycle state from §5.0 |
| governance-profile-id | string | REQUIRED | Governance profile |
| runtime-configuration-id | string | REQUIRED | Runtime configuration |
| reusable-asset-registry-id | string | CONDITIONAL | Registry consulted for reuse discovery |
| active-construction-boundary-id | string | CONDITIONAL | Construction Boundary controlling current loop scope |
| connection-context-ids | array | CONDITIONAL | Connection Contexts authorized for boundary crossing |
| artifact-repository-id | string | REQUIRED | Artifact repository |
| traceability-graph-id | string | REQUIRED | Traceability graph |
| execution-graph-id | string | REQUIRED | Runtime execution graph |
| started-at | ISO 8601 | REQUIRED | Start time |
| completed-at | ISO 8601 | CONDITIONAL | Completion time |
| final-outcome | enum | CONDITIONAL | succeeded, failed, escalated, halted, cancelled |
| evidence-bundle-id | string | CONDITIONAL | Evidence bundle produced |

---

## 8.0 Stage Gates and Progression

Each stage of the construction flow is governed by completion criteria (defined in §4) and by governance gates. A loop stage MUST NOT be considered complete unless its completion criteria are satisfied and required evidence exists. Governance decisions directly affect progression: a block, approval-required, waiver-required, override-required, or escalation decision MUST pause, stop, or route execution according to the decision.

---

## 9.0 Runtime Orchestration Components

The runtime orchestration implementation SHOULD include the following components.

| Component | Responsibility |
| --------- | -------------- |
| Runtime Admission Controller | Verifies execution may begin |
| Execution Graph Manager | Maintains runtime execution graph |
| Eligibility Evaluator | Determines which tasks may run |
| Scheduler | Selects work for dispatch |
| Queue Manager | Manages ready, delayed, retry, repair, validation, governance, and dead-letter queues |
| Dispatch Manager | Assigns work items to workers or services |
| Worker Coordinator | Registers workers, monitors heartbeats, and manages worker capacity |
| Lease Manager | Claims and renews work item leases |
| Lock Manager | Coordinates locks over artifacts, paths, branches, packages, and state |
| Agent Dispatch Adapter | Invokes the Agent Orchestrator |
| Model Dispatch Adapter | Invokes the Model Integration Layer |
| Tool Dispatch Adapter | Invokes the Tool Integration Layer |
| Artifact Dispatch Adapter | Invokes the Artifact Service |
| Governance Dispatch Adapter | Invokes the Governance Service |
| Traceability Dispatch Adapter | Invokes the Traceability Service |
| State Transition Coordinator | Commits state transitions and events |
| Repair Coordinator | Orchestrates repair cycles |
| Recovery Coordinator | Orchestrates runtime recovery |
| Telemetry Coordinator | Emits orchestration telemetry |

Each component MUST expose a defined interface or internal contract. Components that mutate lifecycle state MUST do so through the State Transition Coordinator or equivalent State Manager contract. Dispatch adapters MUST NOT bypass target service contracts. The orchestrator MUST record structured errors from each component.

---

## 10.0 Orchestration Service Boundary

The Runtime Orchestration Service is the deployable service or in-process module that coordinates runtime execution. It MUST accept execution start requests, validate execution admission, initialize execution graph state, create work items, evaluate task eligibility, schedule and dispatch work items, monitor worker progress, coordinate agent, model, tool, and artifact operations, coordinate validation and repair, enforce governance decisions, update traceability, persist state transitions, emit telemetry, coordinate recovery, and produce completion outcomes.

The Runtime Orchestration Service MUST NOT directly call model providers, MUST NOT directly execute deterministic tools except through the Tool Integration Layer or registered tool worker path, MUST NOT directly promote artifacts except through the Artifact Service, and MUST NOT write governance audit records except through the Governance Service or an approved governance module.

---

## 11.0 Execution Admission

Execution admission determines whether runtime orchestration may begin. Admission inputs include the execution start request, executable construction plan, readiness state, governance state, runtime configuration, tool capability catalog, agent registry, artifact repository profile, traceability state, and state store health. Admission outputs include an execution admission record, initial execution graph, initial checkpoint, and admission error record where applicable.

The orchestrator MUST reject execution if the specification is not Autonomous-Ready, if the construction plan is not executable, or if required tool capabilities or agent roles are unavailable. The orchestrator MUST pause or escalate execution if governance requires approval before start. The orchestrator MUST NOT create work items until admission succeeds.

---

## 12.0 Execution Graph

The Execution Graph Manager maintains the runtime graph representing active construction. Node types include task-node, work-item-node, agent-invocation-node, model-invocation-node, tool-invocation-node, artifact-node, validation-node, repair-node, governance-node, state-event-node, and telemetry-node.

The graph MUST preserve task dependency relationships, link each work item to a task, link generated artifacts to producing work items, link validation results to evaluated artifacts, link repair records to failed validation results, link governance decisions to affected actions, and remain recoverable from durable state and events.

---

## 13.0 Eligibility, Scheduling, and Dispatch

### 13.1 Eligibility Evaluation

A task is eligible only when its state is pending or retry-authorized, all required predecessor tasks are satisfied, required source entities and artifacts are available, required agent role or tool capability is available, required locks can be acquired or reserved, governance does not block execution, retry and repair limits have not been exceeded, runtime configuration allows execution, and project or tenant isolation constraints are satisfied.

A task with unsatisfied dependencies MUST NOT be queued as ready. A governance-blocked task MUST be routed to governance handling. A task requiring unavailable capability MUST be delayed, failed, or escalated according to policy. Eligibility evaluation MUST be recorded for tasks that are blocked, delayed, escalated, or rejected.

### 13.2 Scheduling and Queues

Scheduling converts eligible tasks into executable work items and places them into queues. The orchestrator SHOULD support ready, delayed, retry, validation, repair, governance, repository, and dead-letter queues. The scheduler MUST NOT queue duplicate active work items for the same task unless the task contract permits parallel sub-work, MUST preserve dependency order, SHOULD prioritize critical path tasks when all other constraints are equal, and MUST move unrecoverable work items to dead-letter or escalation handling.

### 13.3 Dispatch

Dispatch assigns work to an appropriate execution path. Agent work dispatches to the Agent Orchestrator or Agent Worker; model work dispatches to the Model Integration Layer through the agent/model contract; tool work dispatches to the Tool Integration Layer or Tool Worker; validation dispatches to the Tool Integration Layer, Validation Worker, or Review Agent depending on validation type; repair dispatches to the Repair Coordinator and Repair Analyst; repository work dispatches to the Artifact Service or Repository Worker; governance dispatches to the Governance Service or Governance Worker; traceability dispatches to the Traceability Service.

A work item MUST NOT be dispatched without an active lease when leases are enabled. A work item requiring exclusive resource access MUST NOT be dispatched without required locks. Dispatch failures MUST produce structured runtime errors. Dispatch MUST preserve correlation identifiers.

---

## 14.0 Queues, Leases, Locks, and Workers

### 14.1 Leases

A work item MUST have no more than one active lease. Expired leases MUST trigger recovery evaluation. A lease MUST NOT be reassigned until the orchestrator determines whether prior execution produced side effects. Long-running work MUST renew leases.

### 14.2 Locks

Artifact-modifying work MUST acquire artifact or path locks. Branch merge work MUST acquire branch locks. Package creation work MUST acquire package locks. State transition work requiring exclusive access MUST acquire state locks or use transactional state updates. Stale locks MUST trigger recovery before reuse.

### 14.3 Workers

The Worker Coordinator MUST register workers, validate worker capabilities, monitor worker heartbeats, track worker assignments, detect failed workers, reclaim expired leases through recovery, support worker draining, and support worker disablement. A worker MUST receive only work matching its declared capabilities and MUST NOT receive work outside its project or tenant authorization scope. A worker MUST send heartbeat events while executing work. A worker failure MUST trigger recovery evaluation for active work.

---

## 15.0 Agent, Model, Tool, and Artifact Orchestration

### 15.1 Agent Orchestration

Agent orchestration coordinates reasoning work through the Agent Orchestrator. The flow is: identify the task requiring an agent role, validate role availability, assemble the context package, perform a governance check if required, bind model capabilities through the Model Integration Layer, invoke the agent, validate the agent output, route artifact candidates, findings, repair proposals, or review outputs, update state and traceability, and emit telemetry.

Agents MUST be invoked through the Agent Orchestrator. Model calls MUST be routed through the Model Integration Layer. Agent context MUST be task-scoped. Agent output MUST be validated before use. Artifact-producing agent outputs MUST enter Artifact Service admission. Agent failure MUST be classified and handled through runtime policy.

### 15.2 Tool Orchestration

Tool orchestration coordinates deterministic tools through the Tool Integration Layer. The flow is: identify the required tool capability, select a registered tool or plugin, validate the tool trust profile, prepare the tool invocation request, execute the tool through the Tool Integration Layer or Tool Worker, normalize the tool result, record evidence, map findings to artifacts, validations, tasks, or policies, determine proceed, repair, retry, escalate, or fail, and emit telemetry.

Tools MUST be registered before use. Tool invocation MUST produce structured result records. A tool timeout MUST NOT be treated as success. Tool failure MUST trigger repair, retry, escalation, or task failure. Tool evidence required for validation MUST be retained.

### 15.3 Artifact Orchestration

Artifact orchestration coordinates generated and modified artifacts through the Artifact Service. The flow is: receive the artifact candidate, request repository admission, create or update artifact metadata, set artifact state to pending, trigger validation where required, update artifact state based on validation, route failed artifacts to repair when allowed, promote valid and traced artifacts to stable state, preserve evidence and supersession history, and emit repository and artifact telemetry.

Generated artifacts MUST NOT bypass repository admission. Artifacts MUST NOT become stable without validation and traceability. Repair MUST create a new version or supersession metadata when artifact content changes. Repository integrity failure MUST block promotion and completion.

---

## 16.0 Validation Orchestration

Validation orchestration coordinates deterministic checks and review checks. The flow is: identify validation criteria from task, artifact, policy, or specification, select a validation tool or review path, dispatch a validation work item, record the validation result, update artifact, task, and execution state, trigger repair or escalation if failed, link the validation result to the traceability graph, and emit telemetry.

Validation results MUST reference evaluated artifact versions. A failed required validation MUST block task completion unless waived. A waived validation MUST reference a valid governance waiver. Validation evidence MUST be retained for stable artifacts.

---

## 17.0 Repair Orchestration

Repair orchestration manages bounded convergence loops. The flow is: receive a failed validation result, confirm repair is permitted, check repair limits, create a repair work item, invoke the Repair Analyst, produce a repair proposal, invoke the Implementation Generator or appropriate artifact-producing path, admit the revised artifact, revalidate the revised artifact, record the repair outcome, and continue, escalate, or fail.

Repair MUST be triggered by a recorded validation failure or repository finding. Repair MUST preserve prior artifact history. Repair MUST NOT exceed configured limits. Repair success MUST be based on revalidation, not agent assertion. Repair exhaustion MUST create escalation.

---

## 18.0 Convergence Evaluation

Convergence Evaluation determines whether the generated system satisfies the current loop scope (see §4.6). The orchestrator evaluates convergence using the execution graph, task states, artifact states, validation records, repair records, traceability graph, governance state, and repository integrity report, and produces a convergence evaluation record, convergence outcome, unresolved issue list, and next action decision.

---

## 19.0 Packaging and Deployment Preparation

Packaging and Deployment Preparation are the final construction-flow stages (see §4.7 and §4.8). The orchestrator coordinates them through the Artifact Service and governed deployment preparation paths, and MUST NOT complete execution until required packaging and deployment preparation criteria are satisfied or explicitly not required.

---

## 20.0 Change-Driven Re-Entry and Impact Analysis

### 20.1 Re-Entry Triggers

The loop MUST support re-entry for specification version change, canonical model change, requirement change, policy change, tool version change affecting validation confidence, model routing or capability change affecting generated outputs, reusable asset promotion, supersession, deprecation, or retirement, Reuse Decision Record change, Construction Boundary or Connection Context change, artifact repository conflict, failed deployment preparation, operational feedback requiring specification update, and governance reauthorization requirement.

### 20.2 Change Trigger Record

A change trigger record declares trigger-type (specification, canonical-model, artifact, reusable-asset, reuse-decision, validation, policy, tool, model, configuration, environment, construction-boundary, connection-context, operational-feedback), source-object-id, prior-version, new-version, affected-specification-id, affected-specification-version, detected-at, detected-by, and impact-analysis-required.

### 20.3 Impact Analysis

A re-entry loop MUST perform impact analysis unless governance requires full reconstruction. Impact analysis uses the change trigger record, traceability graph, artifact metadata, reusable asset records, reuse decision records, construction plan, Construction Boundary and Connection Context records, validation records, package manifests, and governance policy. It MUST traverse reuses, extends, wraps, and composes links.

Impact analysis outputs affected canonical entities, tasks, artifacts, reusable assets, reuse decisions, Construction Boundaries or Connection Contexts, validations, packages, and deployment preparation records, plus unaffected artifacts with rationale and required actions. Required actions include no-action, revalidate, repair, regenerate, reuse, reselect, replan, repackage, reauthorize, and retire.

Affected artifacts MUST be revalidated, repaired, regenerated, repackaged, or retired. Unaffected artifacts SHOULD remain stable if impact analysis confidence is complete. Partial or uncertain impact analysis MUST escalate or trigger broader reconstruction. Affected stable artifacts MUST NOT remain stable without required action. Uncertain impact analysis MUST NOT be treated as no-action.

---

## 21.0 Governance Control

Governance controls loop entry, progression, exception handling, escalation, packaging, deployment preparation, and re-entry. The loop MUST consult governance before initial loop admission, readiness transition use, execution start, restricted model use, restricted tool use, waiver application, override application, artifact promotion when governed, packaging when governed, deployment preparation completion when governed, and change-driven re-entry when policy requires reauthorization.

| Decision | Loop Effect |
| -------- | ----------- |
| allow | Continue |
| warn | Continue and record warning |
| block | Stop affected stage |
| escalate | Enter escalated state |
| approval-required | Pause until approval exists |
| waiver-required | Pause until waiver exists |
| override-required | Pause until override exists |

A governance block MUST prevent affected loop progression. A pending approval MUST pause affected loop progression. A waiver MUST be explicit and persisted. An expired waiver or approval MUST NOT authorize continuation. Governance audit write failure MUST halt governed loop actions.

---

## 22.0 Event-Driven and Parallel Execution

### 22.1 Event-Driven Orchestration

The runtime orchestration architecture MAY use events to trigger work. Events include execution-admitted, task-state-changed, work-item-queued, work-item-dispatched, agent-output-validated, artifact-admitted, validation-failed, repair-required, repair-completed, governance-decision-received, lock-expired, lease-expired, checkpoint-created, recovery-required, and execution-completed.

Events MUST include correlation identifiers. Events MUST NOT be considered authoritative without corresponding state records where lifecycle state is affected. Event handling MUST be idempotent where duplicate delivery is possible. Event-triggered scheduling MUST re-check current state before dispatch.

### 22.2 Parallel Execution

Tasks MAY run in parallel only when no dependency relationship requires ordering, no shared exclusive lock is required, no artifact path conflict exists, no governance gate requires sequencing, worker capacity is available, project or tenant isolation is preserved, and traceability can be recorded independently.

The orchestrator MUST prevent duplicate execution of the same work item, MUST prevent conflicting artifact writes, MUST pause dependent tasks when prerequisite tasks fail, and MAY continue unrelated tasks after a local failure when governance and runtime policy permit it. Parallel execution MUST NOT weaken validation or traceability requirements.

---

## 23.0 Recovery

Recovery orchestration restores safe execution after interruption, failure, or inconsistency. Recovery MUST be triggered by runtime restart during active execution, worker heartbeat expiration, lease expiration, stale lock detection, state transaction failure, repository inconsistency, traceability write failure, governance audit write failure, distributed split-brain detection, or checkpoint validation failure.

The recovery flow is: pause dispatch, mark affected execution recovering, load the latest checkpoint, inspect active leases and locks, validate worker state, validate state events after checkpoint, validate artifact repository state, validate traceability state, validate governance state, requeue safe work, quarantine ambiguous artifacts, escalate unsafe work, record a recovery event, and resume, halt, or escalate.

The orchestrator MUST NOT resume dispatch until recovery validation completes. Ambiguous side effects MUST be quarantined or escalated. Recovery MUST NOT erase prior state events. Recovery failure MUST halt affected execution.

---

## 24.0 Completion and Termination

### 24.1 Completion Checks

Execution may complete only when all required tasks are completed, skipped, or waived; no ready, delayed, retry, validation, repair, or governance work remains unresolved; all required artifacts are stable or governed as non-deployable; required validation results passed or were waived; repair records are resolved; traceability integrity passes; repository integrity passes; governance gates are closed; a completion report is produced; and a final checkpoint is created.

The orchestrator MUST NOT complete execution when blocking work remains, when traceability is incomplete, or when repository integrity fails. Completion MUST produce a completion record and telemetry event.

### 24.2 Terminal Outcomes

The loop MUST terminate explicitly. Terminal outcomes are succeeded, failed, escalated, halted, and cancelled.

The loop may terminate as succeeded only when convergence criteria are satisfied, required packaging is complete or not required, required deployment preparation is complete or not required, required governance gates are closed, the evidence bundle is complete, and a completion report is produced.

The loop MUST terminate as failed when required specification interpretation fails, required planning fails, required artifact generation fails without recovery, required validation fails after repair exhaustion, repair cannot proceed and no waiver is available, traceability closure fails, the evidence bundle cannot be produced, or recovery fails and execution cannot safely resume.

A terminal outcome MUST be recorded in the loop run record, MUST produce a completion report when possible, MUST emit telemetry, and MUST preserve evidence generated before termination.

---

## 25.0 State, Evidence, and Telemetry

### 25.1 State

The loop MUST maintain durable state and memory. The loop MUST update specification, canonical model, readiness, planning, execution, task, artifact, validation, repair, package, deployment preparation, governance, traceability, and telemetry summary state. Loop stage transitions MUST be state events. Loop continuation decisions MUST be persisted. A loop restart MUST recover from state and checkpoints. A loop MUST NOT resume from ambiguous state.

### 25.2 Evidence

The loop MUST produce evidence demonstrating what occurred. A complete loop evidence bundle MUST include the input specification reference, canonical model reference, readiness and governance admission records, construction plan, reuse discovery records, reuse decision records, selected reusable asset records where applicable, Construction Boundary and Connection Context records, execution graph, task execution records, agent invocation records, model invocation records where applicable, tool invocation records, artifact metadata records, validation records, repair records where applicable, package records where applicable, deployment preparation records where applicable, traceability snapshot, telemetry summary, and completion report.

A successful loop MUST have complete evidence. A failed loop SHOULD preserve partial evidence. Evidence records MUST be retained according to governance policy. Evidence MUST be sufficient to explain why the loop continued, repaired, escalated, halted, failed, or completed.

### 25.3 Telemetry

The loop MUST emit telemetry consistent with the common telemetry model (ISL v0.1 §9) and the telemetry catalog (ISL v2.3). Loop telemetry MUST include loop-run-id and correlation-id, and SHOULD include specification-id, execution-graph-id, task-id, artifact-id, reusable-asset-id, reuse-decision-id, construction-boundary-id, connection-context-id, validation-result-id, and repair-record-id where applicable. Telemetry MUST NOT replace durable loop state or evidence, and MUST NOT expose restricted context or secrets.

---

## 26.0 Local and Distributed Modes

The runtime orchestrator MUST support local-first operation and MAY support distributed execution.

In local mode, orchestration MAY run in-process with local queues and local workers. Local mode MUST still preserve task state, artifact repository admission, deterministic validation, governance checks, traceability writes, telemetry emission, and repair limits.

In distributed mode, orchestration MUST provide durable queues, distributed leases, distributed locks, worker heartbeats, a shared state store, artifact repository consistency, traceability consistency, governance consistency, and recovery after worker failure.

Distributed mode MUST NOT be enabled without durable state and recoverable queues. Local mode MUST NOT claim distributed conformance. Mode changes during active execution MUST trigger impact and safety evaluation.

---

## 27.0 Error Handling

Loop and orchestration errors specialize the common error taxonomy (ISL v0.1 §8). Each error MUST be recorded as a structured error record using the common error record structure. Loop errors MUST reference loop-run-id. Blocking errors MUST prevent progression until resolved. Retryable errors MUST respect retry policy. Repairable errors MUST respect repair policy.

The following extension error classes are registered:

| Error Class | Category | Default Handling |
| ----------- | -------- | ---------------- |
| loop-admission-failed | runtime | reject |
| loop-input-version-conflict | runtime | reject |
| loop-interpretation-failed | runtime | fail or escalate |
| loop-canonical-validation-failed | validation | fail or escalate |
| loop-planning-failed | runtime | fail or escalate |
| loop-plan-not-executable | runtime | reject |
| loop-reuse-discovery-missing | reuse | reject or replan |
| loop-reuse-decision-missing | reuse | reject or replan |
| loop-reuse-decision-violated | reuse | reject artifact or escalate |
| loop-context-boundary-drift | governance | halt task and replan or escalate |
| loop-artifact-generation-failed | runtime | retry or escalate |
| loop-artifact-admission-failed | repository | fail task |
| loop-validation-failed | validation | repair |
| loop-validation-missing | validation | block convergence |
| loop-repair-limit-reached | runtime | escalate |
| loop-convergence-blocked | runtime | continue, repair, replan, or escalate |
| loop-packaging-failed | deployment | repair, retry, or escalate |
| loop-deployment-preparation-failed | deployment | repair, retry, or escalate |
| loop-impact-analysis-uncertain | runtime | escalate or broaden scope |
| loop-governance-blocked | governance | block |
| loop-traceability-incomplete | traceability | block convergence |
| loop-evidence-incomplete | observability | fail or escalate |
| loop-state-inconsistent | runtime | recover |
| loop-recovery-failed | runtime | halt |
| orchestration-admission-failed | runtime | reject |
| orchestration-eligibility-failed | runtime | escalate |
| orchestration-scheduling-failed | runtime | retry or escalate |
| orchestration-dispatch-failed | runtime | retry or escalate |
| orchestration-worker-unavailable | runtime | delay or escalate |
| orchestration-lease-conflict | runtime | halt and recover |
| orchestration-lock-conflict | runtime | delay or recover |
| orchestration-agent-dispatch-failed | agent | retry or escalate |
| orchestration-tool-dispatch-failed | tool | retry or escalate |
| orchestration-artifact-operation-failed | repository | retry, repair, or escalate |
| orchestration-validation-failed | validation | repair or escalate |
| orchestration-repair-limit-reached | runtime | escalate |
| orchestration-governance-pending | governance | pause |
| orchestration-governance-blocked | governance | block |
| orchestration-traceability-write-failed | traceability | halt or recover |
| orchestration-state-commit-failed | runtime | recover |
| orchestration-recovery-failed | runtime | halt |
| orchestration-completion-blocked | runtime | continue, repair, or escalate |

---

## 28.0 Testing

The loop and orchestration MUST be testable. Required test categories include admission, interpretation, planning, generation, validation, repair, convergence, packaging, deployment-preparation, re-entry, governance, state-recovery, telemetry, evidence, eligibility, scheduling, dispatch, lease-lock, agent-dispatch, tool-dispatch, artifact-flow, repair-loop, governance-flow, recovery, parallel-execution, and completion.

Repair tests MUST verify termination limits. Convergence tests MUST verify that missing validation or traceability blocks success. Re-entry tests MUST verify that affected artifacts are identified through traceability. Governance tests MUST verify that blocked actions do not continue. Recovery tests SHOULD use failure injection. Parallel execution tests MUST verify no conflicting artifact writes occur.

---

## 29.0 Conformance

Conformance to this document is evaluated against the conformance framework in ISL v0.1 §10.

### 29.1 Loop Run Conformance

A loop run conforms to ISL v3.3 if it declares loop inputs, creates a loop run record, maintains explicit lifecycle state, executes required stages for its loop-run-type, records stage outputs, enforces reuse discovery before applicable artifact generation, enforces Construction Boundary and Connection Context controls, enforces validation before artifact promotion, enforces bounded repair, evaluates convergence using required criteria, applies governance controls, records traceability, emits telemetry, and produces an evidence bundle and completion report.

### 29.2 Orchestrator Conformance

A runtime orchestrator conforms to ISL v3.3 if it validates execution admission, initializes execution graph state, evaluates task eligibility, schedules work items, dispatches work through authorized services or workers, manages queues, leases, locks, and worker coordination, coordinates agent, model, tool, artifact, validation, repair, governance, traceability, and state operations, supports completion evaluation, supports recovery, emits telemetry, and produces structured error records.

### 29.3 Platform Conformance

A platform conforms to ISL v3.3 if it implements the Autonomous Development Loop as a stateful, governed control cycle, supports initial construction and change-driven re-entry, integrates planning, runtime, agents, models, tools, artifacts, validation, repair, packaging, deployment preparation, state, governance, traceability, and telemetry, routes all runtime construction activity through runtime orchestration, prevents direct bypass of agents, tools, artifacts, governance, traceability, and state services, prevents unbounded repair, prevents evidence-free completion, prevents governance bypass, prevents reuse-decision bypass, prevents context drift across Construction Boundaries, prevents unvalidated artifacts from becoming stable, and supports loop testing and recovery.
