# ALETHEIA Specification Language (ISL) v1.2
# The Execution Model

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0, ISL v1.1
**Supersedes:** ISL r2 v1.2 ASC Execution Model, ISL r2 v1.6 The Tool Integration Model
**Document Type:** Execution Model Specification

---

## 1.0 Scope

This document defines the execution model used by the ALETHEIA platform to transform an Autonomous-Ready ISL specification into validated construction outputs, and the Tool Integration Model through which it invokes, manages, and governs deterministic engineering tools.

The execution model defines the required execution phases, phase entry and exit criteria, preconditions, inputs and outputs, execution and artifact state, repair cycles, completion criteria, recovery behavior, and governance integration. The Tool Integration Model defines the contracts for tool registration, capability declaration, invocation, result normalization, validation outcome handling, security boundaries, version control, conflict handling, and tool governance.

Shared conventions — including the common error model (§8), telemetry model (§9), design principles (§7), conformance framework (§10), and shared enums such as Validation Outcome, Governance Decision, Artifact State, and Execution Status — are defined in ISL v0.1 and are referenced rather than redefined here.

This document does not define the authored language syntax, canonical entity schemas, readiness criteria, construction planning model, traceability model, or governance role model. Those are defined in other ISL documents. This document defines how execution proceeds once those preconditions are satisfied.

A conforming **Execution Runtime** MUST use this model to ensure that autonomous construction is:

* gated by readiness and governance
* driven by a validated canonical specification
* executed through ordered phases
* validated by deterministic tools
* traceable to specification entities
* bounded by repair termination rules
* observable through structured execution records
* halted or escalated when required conditions fail

---

## 2.0 Preconditions for Execution

The platform MUST NOT begin execution unless all of the following preconditions are satisfied. Execution is not permitted merely because a specification exists.

| Precondition | Required Evidence | Enforced By |
| --- | --- | --- |
| Specification has reached Autonomous-Ready readiness level | Readiness transition record | ISL v1.3 |
| Canonical normalization completed without blocking errors | Canonical validation report | ISL v1.1 |
| All required governance approvals have been recorded | Approval records | ISL v1.3 |
| Construction plan has been generated and validated | Construction Task Graph | ISL v1.5 |
| No unresolved interpretation ambiguities remain | Interpretation Record | §6.0 |
| Required deterministic tools are registered | Tool Records | §19.0 |
| Traceability graph is initialized | Traceability state | ISL v1.4 |
| Execution Runtime configuration is valid | Runtime configuration record | this document |

### 2.1 Precondition Failure

If any precondition fails, execution MUST NOT begin. The runtime MUST produce a Precondition Failure Record that includes a failure identifier, specification identifier and version, the failed precondition, evidence, blocking severity, a recorded timestamp, and the required action before retry. Precondition failures MUST be recorded in governance state when they affect readiness, approval, policy, or authorization.

---

## 3.0 Execution Inputs

The runtime MUST receive structured inputs, not informal instructions, and MUST reject execution requests that lack required inputs or contain inconsistent versions across inputs.

### 3.1 Required Input Set

| Input | Required | Description |
| --- | --- | --- |
| Canonical Model | YES | Normalized ISL v1.1 specification graph |
| Readiness Record | YES | Evidence the specification is Autonomous-Ready |
| Construction Task Graph | YES | Plan generated under ISL v1.5 |
| Governance State | YES | Approvals, policies, waivers, risk tier |
| Tool Registry | YES | Registered deterministic tools and capabilities |
| Traceability State | YES | Initial traceability graph and identifier registry |
| Runtime Configuration | YES | Execution limits, concurrency, timeout, repair limits |
| Artifact Repository Reference | YES | Target repository for generated artifacts |
| Execution Request | YES | Request to initiate execution for a specific specification version |

### 3.2 Input Consistency Rules

All execution inputs MUST reference the same specification identifier and specification version. The Construction Task Graph MUST be derived from the same canonical model version submitted. Governance approvals MUST apply to the same specification version. The runtime MUST reject execution when version inconsistency is detected.

---

## 4.0 Execution Outputs

Execution is complete only when the runtime has produced validated artifacts, records, telemetry, traceability links, and completion evidence, structured so governance, audit, debugging, and future reconstruction can inspect the execution lifecycle.

| Output | Description |
| --- | --- |
| Execution Record | Summary of the full execution lifecycle |
| Execution Graph | Runtime graph of phases, tasks, dependencies, and outcomes |
| Generated Artifacts | Source, config, schema, contract, test, infrastructure, documentation, or deployment artifacts |
| Artifact Metadata | Artifact identifiers, types, statuses, sources, timestamps |
| Validation Results | Deterministic tool outcomes and findings |
| Repair Records | Records of repair cycles and outcomes |
| Test Results | Generated and executed test results |
| Policy Evaluation Records | Security and governance validation outcomes |
| Traceability Updates | Links between specification entities, tasks, artifacts, and validations |
| Deployment Preparation Records | Deployment artifacts and readiness evidence |
| Completion Report | Final summary of success, failure, or escalation |

### 4.1 Output Integrity Rules

Every generated artifact MUST be recorded in the Artifact Repository. Every generated artifact MUST have traceability to at least one source entity or a justified inferred derivation. Every validation result MUST reference the artifact, task, tool, and validation criteria evaluated. Every execution failure MUST produce a structured error or escalation record.

---

## 5.0 Execution Phase Model

Executions proceed through the following ordered phases. Each phase has entry criteria, required activities, required outputs, and exit criteria. A phase MUST NOT be considered complete until its exit criteria are satisfied.

| Phase | Title | Depends On |
| --- | --- | --- |
| 1 | Specification Interpretation | Preconditions satisfied |
| 2 | Construction Planning Confirmation | Phase 1 |
| 3 | Reuse-Gated Artifact Generation | Phase 2 and required Reuse Discovery decisions |
| 4 | Deterministic Validation | Phase 3 |
| 5 | Repair Cycles | Phase 4, conditional on failure |
| 6 | Test Generation and Execution | Phase 4 or Phase 5 |
| 7 | Security and Policy Validation | Phase 6 |
| 8 | Artifact Consolidation | Phase 7 |
| 9 | Deployment Preparation | Phase 8 |

### 5.1 Phase Ordering Rules

Each phase MUST complete successfully before the next begins unless this document explicitly permits conditional or parallel behavior. Phase 5 occurs only when validation failure requires repair. Execution of unaffected parallel tasks MAY continue while a specific task is in repair or escalation, provided dependency and governance rules permit. Phase 8 MUST NOT begin until all required validation, repair, test, and policy obligations have completed successfully or been resolved through governance-approved waiver. Phase 9 MUST NOT begin until artifacts are consolidated into a stable repository state.

---

## 6.0 Phase 1 — Specification Interpretation

Specification Interpretation converts the canonical model into an execution-oriented understanding, producing an Interpretation Record that identifies the entities, assumptions, risks, and ambiguities relevant to construction. It is a final interpretation gate before planning confirmation.

### 6.1 Entry Criteria

Phase 1 MUST NOT begin unless all execution preconditions are satisfied, canonical validation has passed, governance approvals required for execution are present, and runtime configuration has been validated.

### 6.2 Required Activities

The runtime or Specification Interpreter agent MUST inspect the canonical model; identify entities relevant to execution; identify unresolved ambiguity; identify assumptions affecting planning or generation; identify policy constraints relevant to execution; and produce an Interpretation Record.

### 6.3 Interpretation Record

The Interpretation Record MUST include an interpretation identifier, specification identifier and version, canonical model version, the interpreted entity identifiers, execution assumptions (conditional), ambiguities (conditional), policy constraints (conditional), a generation timestamp, and the generating agent or component.

### 6.4 Exit Criteria

Phase 1 completes successfully only when an Interpretation Record has been produced, all required canonical entities have been interpreted, no unresolved blocking ambiguity remains, and interpretation output has been recorded in traceability state. Execution MUST NOT proceed to Phase 2 if unresolved blocking ambiguities remain.

---

## 7.0 Phase 2 — Construction Planning Confirmation

Construction Planning Confirmation verifies that the Construction Task Graph generated under ISL v1.5 is still valid and executable in the current runtime context. A plan may be structurally valid but not currently executable due to missing tools, governance restrictions, unavailable capacity, or version mismatch.

### 7.1 Entry Criteria

Phase 2 MUST NOT begin unless Phase 1 completed successfully.

### 7.2 Required Activities

The runtime MUST verify that the Construction Task Graph references the current specification version; all task dependencies are resolvable; all required agent roles are available; all required tool capabilities are registered; all declared artifact types are supported; all governance constraints affecting task execution are known; and parallel groups do not violate dependency or governance constraints.

### 7.3 Planning Confirmation Record

The Planning Confirmation Record MUST include a confirmation identifier, plan identifier, specification identifier and version, an executable flag, blocked tasks (conditional), required tool capabilities, required agent roles, governance constraints (conditional), and a confirmation timestamp.

### 7.4 Exit Criteria

Phase 2 completes successfully only when the plan is confirmed executable, no blocking task, tool, role, or governance dependency is unresolved, and the Planning Confirmation Record has been stored. If the plan is not executable, execution MUST halt and produce a Planning Execution Failure Record.

---

## 8.0 Boundary-Aware Execution

The Execution Runtime MUST execute construction tasks according to both task dependencies and construction boundary dependencies defined in ISL v1.5. It MUST validate Boundary Entry Criteria before beginning execution within a construction boundary and evaluate Boundary Exit Criteria before a boundary may be marked complete. Cross-boundary execution activity MUST occur only through valid Connection Contexts or approved governance overrides. The runtime MUST preserve boundary state and Boundary Continuation Records so autonomous construction can resume after interruption without loss of execution integrity.

Deterministic tool execution MUST also respect construction boundaries and Connection Contexts: a tool integration MUST NOT consume artifacts, validation outputs, or execution context originating from another construction boundary unless a valid Connection Context authorizes that interaction, and invocation results MUST remain traceable to the construction boundary and task that initiated them.

---

## 9.0 Phase 3 — Artifact Generation

Artifact Generation creates implementation artifacts from construction tasks. Generated artifacts are not trusted at creation time; they enter the repository with provisional status and must later pass deterministic validation before becoming stable outputs.

### 9.1 Entry Criteria

Phase 3 MUST NOT begin unless Phase 2 completed successfully. A task MUST NOT enter artifact generation unless all of its dependencies are satisfied. For task types governed by the Reuse Model, a task MUST NOT enter artifact generation unless a Reuse Discovery outcome and Reuse Decision Record exist; the runtime MUST refuse to dispatch full generation when the Reuse Decision authorizes only full reuse, wrapping, extension, composition, or delta generation.

### 9.2 Artifact Types

`source`, `config`, `schema`, `infrastructure`, `contract`, `test`, `documentation`.

### 9.3 Required Activities

For each eligible task, the runtime MUST assign the task to the appropriate agent, generator, or tool; provide structured task context; verify the task's Reuse Decision Record when reuse discovery applies; generate declared artifacts; assign each artifact an identifier; associate each artifact with source specification entities; record artifact metadata; write artifacts to the Artifact Repository; and update task and traceability state.

### 9.4 Artifact Metadata

Each artifact MUST be recorded with an artifact identifier, artifact type, source entity identifiers, task identifier, derivation type (`direct`, `derived`, `inferred`, `repair`, `reused`, `reuse-delta`, `wrapper`, `extension`, `composition`), generation timestamp and generator, status, repository location, and content hash when supported.

### 9.5 Artifact Generation Rules

An artifact MUST NOT be created without a task identifier. An artifact MUST NOT be created without at least one source entity identifier unless it is marked `inferred` with justification. An artifact's initial status MUST be `pending`. An artifact MUST NOT be marked `valid` during generation. An artifact produced from reuse, partial reuse, wrapping, extension, or composition MUST reference its Reuse Decision Record and selected asset identifiers. If a task completes without producing its declared artifacts, the runtime MUST mark the task failed.

### 9.6 Exit Criteria

Phase 3 completes successfully when all eligible artifact-generation tasks have produced their declared artifacts and moved to validation, failed with structured failure records, or entered escalation according to governance rules. Execution MUST NOT proceed to final validation outcomes for an artifact not recorded in the Artifact Repository.

---

## 10.0 Phase 4 — Deterministic Validation

Deterministic Validation evaluates generated artifacts using registered deterministic tools. This phase is the foundation of execution reliability: reasoning may generate, but deterministic tools establish whether artifacts satisfy objective execution criteria. The runtime MUST treat tool results as authoritative for objective checks such as compilation, schema validation, static analysis, tests, and security scans where applicable.

### 10.1 Entry Criteria

Phase 4 begins for an artifact only after the artifact has been generated and recorded with status `pending` or `repaired`.

### 10.2 Required Activities

The runtime MUST identify validation tasks associated with generated artifacts; select registered tools with required capabilities; invoke tools using the Tool Invocation Contract (§22.0); record invocation results; update artifact status based on validation outcomes; initiate repair when required; and record validation results in traceability state.

### 10.3 Validation Result

A Validation Result MUST include a validation result identifier, artifact identifier, task identifier, tool identifier, capability, an outcome from the shared Validation Outcome enum (`passed`, `failed`, `warning`, `timeout`, `error`, `not-applicable`), findings for non-passing outcomes, an evaluated timestamp, duration, the confirmed tool version, and a next action.

### 10.4 Outcome Handling

| Outcome | Action |
| --- | --- |
| passed | Mark artifact valid and proceed |
| failed | Mark artifact failed and initiate repair cycle |
| warning | Record finding and proceed unless governance requires escalation |
| timeout | Retry once; if it recurs, treat as failed or escalate |
| error | Record tool error; retry, substitute, escalate, or fail per policy |
| not-applicable | Record justification and proceed only if validation coverage remains sufficient |

### 10.5 Artifact Status Rules

An artifact MAY be marked `valid` only after required deterministic validation passes. An artifact MUST be marked `failed` when validation fails. An artifact MAY remain `pending` only while awaiting validation. An artifact MUST be marked `escalated` when repair or validation cannot continue without governance or human intervention. An artifact with an approved waiver MUST be marked `waived`, not `valid`, unless the waived condition is unrelated to artifact correctness and deterministic validation has otherwise passed. An artifact included for evidence, debugging, review, or isolation while not suitable for deployment MUST be marked `non-deployable`.

### 10.6 Exit Criteria

Phase 4 completes for an artifact when validation produces a terminal outcome: passed, an accepted warning, failed and transferred to repair, escalated, or halted. Warning acceptance MUST be supported by policy or governance rationale and MUST NOT override a blocking deterministic failure. Phase 4 completes for the plan only when all required validation activities have reached terminal outcomes.

---

## 11.0 Phase 5 — Repair Cycles

Repair Cycles are initiated when deterministic validation identifies failures. They are the controlled feedback loop for convergence, and MUST operate under explicit iteration limits, escalation thresholds, and termination rules.

### 11.1 Entry Criteria

A repair cycle MUST NOT begin unless a failed validation result exists, the failed artifact and task are identified, repair limits have not been exceeded, and governance policy permits automated repair for the artifact or task type.

### 11.2 Repair Termination Policy

| Parameter | Default | Configurable |
| --- | --- | --- |
| Maximum repair iterations per artifact | 5 | YES |
| Maximum total repair iterations per construction plan | 50 | YES |
| Escalation threshold | 3 consecutive failures on the same artifact | NO |
| Maximum repair duration per artifact | implementation-defined | YES |
| Maximum repair duration per construction plan | implementation-defined | YES |

A stricter runtime configuration MUST be approved by governance.

### 11.3 Repair Record

A Repair Record MUST be produced for each repair cycle and include a repair identifier, artifact and task identifiers, the failed validation result identifier, the iteration number, a failure summary, root cause analysis (conditional), the proposed correction, modified artifacts (conditional), an outcome (`resolved`, `unresolved`, `escalated`, `abandoned`), and a timestamp.

### 11.4 Repair Execution Rules

The runtime MUST increment the repair iteration count per attempt. A repaired artifact MUST be returned to deterministic validation and MUST NOT be marked valid without it. The repair cycle MUST preserve prior artifact versions or content hashes where supported. A repaired artifact MUST retain traceability to the original source entity and failed validation result.

### 11.5 Escalation Rules

The runtime MUST escalate when three consecutive repair attempts fail on the same artifact, the per-artifact or per-plan iteration maximum is reached, a governance policy blocks automated repair, the Repair Analyst cannot determine a correction, or repair introduces a new blocking policy violation. On escalation the runtime MUST suspend the affected task, mark the artifact escalated, record an escalation event in governance state, continue unaffected tasks only when dependency rules permit, and require governance or human resolution before retry.

### 11.6 Convergence Failure

If repair limits are reached without successful validation, the construction plan MUST be marked failed unless governance explicitly authorizes continuation under a documented waiver, and a failed Completion Report MUST be produced.

---

## 12.0 Phase 6 — Test Generation and Execution

Test Generation and Execution verifies that generated artifacts satisfy functional requirements, workflows, policies, and acceptance criteria, extending validation into behavior verification. Tests MUST be derived from Requirement and Validation entities.

### 12.1 Entry Criteria

Phase 6 MUST NOT begin until generated artifacts required for test derivation have passed deterministic validation or have accepted warnings that do not block testing.

### 12.2 Required Activities

The runtime MUST derive tests from Requirement and Validation entities; generate test artifacts where required; link each test to the entities it validates; execute tests using registered testing tools where automation applies; record individual test results; initiate repair when test failures indicate defects; and escalate when failures require human decision or governance review.

### 12.3 Test Types

`unit`, `integration`, `acceptance`, `contract`, `security`, `operational`.

### 12.4 Test Result

A Test Result MUST include a test result identifier, test identifier, validation identifier, requirement identifier (conditional), artifact identifiers under test, test type, an outcome (`passed`, `failed`, `skipped`, `blocked`), an executed timestamp, failure detail when failed or blocked, and evidence when applicable.

### 12.5 Test Coverage Rules

At least one test or validation method MUST exist for each must-have functional requirement. A test associated with a must-have requirement MUST NOT be skipped without governance-approved justification. A failed test linked to a must-have requirement MUST block progression to Phase 7 unless waived. Aggregate results without individual case detail MUST NOT satisfy test execution requirements.

---

## 13.0 Phase 7 — Security and Policy Validation

Security and Policy Validation evaluates generated artifacts, execution outputs, and deployment preparation candidates against Policy entities and governance rules. It ensures constructed systems comply with declared security, compliance, operational, technology, and risk constraints.

### 13.1 Entry Criteria

Phase 7 MUST NOT begin until Phase 6 completes successfully or all blocking test failures are resolved, repaired, or waived.

### 13.2 Required Activities

The Security Validator agent, deterministic tools, or governance engine MUST identify all applicable Policy entities; evaluate each against relevant artifacts and execution records; perform required security scans or policy checks; record policy evaluation outcomes; initiate repair for correctable non-compliance; escalate governance-controlled violations; and apply waiver rules where permitted.

### 13.3 Minimum Security Validation Coverage

Automation, authorization, dependency vulnerability exposure, secret detection (when source or config artifacts exist), access control, data protection, interface exposure, and deployment policy constraints.

### 13.4 Policy Evaluation Record

A Policy Evaluation Record MUST include an evaluation identifier, policy identifier, evaluated target identifier, enforcement point, an outcome (`compliant`, `non-compliant`, `not-applicable`, `waived`), an evaluated timestamp and evaluator, findings for non-compliant outcomes, and a next action. A non-compliant outcome for a policy with violation-response `block` MUST prevent progression until the violation is resolved or a valid waiver is granted. Critical or high security findings MUST be treated as failed outcomes unless governance approves a different defined response.

---

## 14.0 Phase 8 — Artifact Consolidation

Artifact Consolidation organizes validated artifacts into a stable project structure with manifests, documentation, dependency declarations, traceability records, and build-ready organization.

### 14.1 Entry Criteria

Phase 8 MUST NOT begin until Phase 7 completes successfully.

### 14.2 Required Activities

The runtime MUST consolidate source, configuration, schema, interface contract, test, infrastructure (when applicable), and documentation artifacts, plus a dependency manifest, build configuration, traceability metadata, and generated execution summary.

### 14.3 Consolidation Rules

Only valid artifacts MAY be included as deployable stable outputs. Waived artifacts MAY be included only when the waiver explicitly permits inclusion and the artifact is marked `waived`. Governance-approved artifacts that have not passed deterministic validation MUST be isolated and marked `non-deployable` unless the approval explicitly authorizes deployment risk acceptance. Failed, pending, or escalated artifacts MUST NOT be included as stable outputs unless isolated and marked non-deployable. The consolidated project MUST be tagged with the specification version that produced it and MUST preserve traceability metadata.

### 14.4 Exit Criteria

Phase 8 completes successfully when a consolidated project structure exists, all included artifacts are eligible, build and dependency manifests exist, a traceability snapshot exists, and the consolidation record is stored.

---

## 15.0 Phase 9 — Deployment Preparation

Deployment Preparation produces or validates the artifacts needed to prepare the constructed system for controlled deployment. It does not deploy the system; deployment authorization remains a governance-controlled action.

### 15.1 Entry Criteria

Phase 9 MUST NOT begin until Phase 8 completes successfully.

### 15.2 Required Activities

The Deployment Preparer agent, runtime, or deterministic tools MUST produce or validate deployment manifests; infrastructure definitions; runtime configuration; environment variables or configuration templates; health check definitions; package or container build outputs when applicable; operational documentation; deployment validation results; and deployment authorization evidence.

### 15.3 Rules

The platform MUST NOT mark a construction plan complete until deployment preparation artifacts are generated and validated. Deployment artifacts MUST trace to Infrastructure entities or operational requirements. A status of ready does not imply production deployment is approved. The platform MUST NOT mark a construction plan complete until deployment preparation artifacts exist and pass required validation.

---

## 16.0 Execution Graph and State Model

### 16.1 Execution Graph

The Execution Graph is the runtime structure coordinating phases, tasks, dependencies, artifacts, validations, repairs, and outcomes. It is derived from the Construction Task Graph and includes runtime state and execution records. The Execution Graph MUST be stored as part of platform state; updated whenever task, artifact, validation, repair, or governance state changes; and MUST preserve enough dependency information to resume execution after interruption. It MUST support traversal from phase to task, task to artifact, artifact to validation result, validation failure to repair record, artifact to source specification entity, and policy violation to governance event.

### 16.2 Construction Plan States

`pending`, `in-progress`, `completed`, `failed`, `escalated`, `halted`.

### 16.3 Phase States

`not-started`, `in-progress`, `completed`, `skipped`, `failed`, `escalated`.

### 16.4 Artifact States

The runtime uses the shared Artifact State enum (ISL v0.1 §6.7). At minimum `pending`, `valid`, `failed`, `repaired`, `escalated`, `deprecated` apply during execution.

### 16.5 State Transition Rules

The runtime MUST enforce valid state transitions; invalid transitions MUST be recorded as execution integrity violations. A failed artifact MAY transition to `repaired` only through a Repair Record. A `pending` artifact MAY transition to `valid` only through a passed validation result. An escalated task or artifact MUST NOT resume until the escalation is resolved.

---

## 17.0 Agent Role Participation

Agents operate under runtime control and do not independently define execution flow.

### 17.1 Agent Role Mapping

| Role | Primary Phase |
| --- | --- |
| Specification Interpreter | Phase 1 |
| Architecture Planner | Phase 2 |
| Construction Planner | Phase 2 |
| Implementation Generator | Phase 3 |
| Review Agent | Phase 4, Phase 6 |
| Repair Analyst | Phase 5 |
| Test Generator | Phase 6 |
| Security Validator | Phase 7 |
| Deployment Preparer | Phase 9 |

### 17.2 Agent Execution Rules

An agent MUST receive structured input from the runtime. Agent output MUST be recorded before it influences execution state. Agent output MUST NOT mark artifacts valid without deterministic validation. An agent action MUST be associated with a task identifier, phase, and traceability context. If an agent fails to produce a valid output, the runtime MUST record an agent failure and decide whether to retry, route to another agent, repair, escalate, or halt.

---

## 18.0 Deterministic Tool Participation

Deterministic tools provide objective validation and transformation capabilities that reasoning agents cannot guarantee. They MUST be used for applicable checks including compilation, linting, schema validation, contract validation, unit and integration test execution, security scanning, dependency auditing, infrastructure validation, and package or container build validation.

---

## 19.0 Tool Registry and Registration

### 19.1 Tool Integration Architecture

All tool invocations pass through the Tool Integration Layer, which receives structured invocation requests, routes them to registered tool integrations, captures raw and structured outputs, normalizes findings, and returns a standardized result. The layer comprises the Tool Registry, Tool Selector, Tool Adapter, Execution Sandbox, Result Normalizer, Evidence Store, Governance Filter, and Telemetry Emitter. The runtime MUST NOT directly invoke unregistered tools. The layer MUST enforce governance restrictions before invoking, normalize output before returning results, and record invocation telemetry and traceability links.

### 19.2 Tool Record

A tool MUST be registered before it may be selected or invoked. A Tool Record MUST include a tool identifier and name, an approved tool version or version range, one or more categories, capability declarations, invocation type (`cli`, `api`, `container`, `plugin`, `service`), execution environment, default timeout, supported artifact types, supported platforms (conditional), configuration and result schema references (conditional), a trust profile identifier, registration metadata, and a lifecycle status (`active`, `deprecated`, `disabled`, `experimental`, `revoked`).

### 19.3 Catalog and Capability Rules

A tool MAY declare capabilities from multiple categories. A tool MUST NOT declare a capability unless it can perform it for every declared artifact type and environment profile. A capability MUST include supported artifact types and MAY include constraints (languages, file formats, platforms, runtime versions). Enterprise and Regulated profiles MUST require `duplication-detection` for generated source, schema, interface, contract, and infrastructure artifacts; a significant duplication finding MUST reference the candidate asset, and a blocking duplication finding MUST route the task back through Reuse Discovery before promotion to stable.

### 19.4 Registration Rules

A tool MUST NOT be invoked unless it has an active Tool Record. A disabled, revoked, or deprecated tool MUST NOT be selected for new execution unless governance explicitly authorizes it; an experimental tool MAY be used only where governance permits. A Tool Record MUST be version-controlled or auditable, and changes affecting validation, security, deployment, or readiness MUST be recorded as governance-relevant configuration changes.

---

## 20.0 Tool Trust Profiles

A Trust Profile controls which tools may be used, where they may run, what they may access, and what outcomes they influence.

### 20.1 Trust Profile

A Trust Profile MUST include a trust level (`approved`, `restricted`, `experimental`, `prohibited`), allowed environments, allowed risk tiers, network access (`none`, `restricted`, `approved-endpoints`, `unrestricted`), filesystem access (`read-only`, `workspace-only`, `restricted-write`, `unrestricted`), secret access (`none`, `scoped`, `unrestricted`), whether approval is required, whether raw evidence retention and human review are required, and a policy reference.

### 20.2 Trust Level Rules

`approved` — approved for declared use cases; `restricted` — only under defined constraints; `experimental` — non-production or governed evaluation only; `prohibited` — MUST NOT be invoked.

### 20.3 Enforcement

The Tool Integration Layer MUST enforce trust profiles before invocation. A prohibited tool MUST NOT be invoked. A restricted tool MUST be invoked only within declared constraints. An invocation requiring approval MUST NOT proceed until approval exists. A trust profile change MUST be recorded as a governance event.

---

## 21.0 Tool Selection

Tool selection MUST be deterministic and auditable. The Tool Selector considers required capability, artifact type, language or format, specification risk tier, governance policy, trust profile, environment compatibility, tool version, timeout and resource constraints, prior reliability telemetry, and configured preference order. A tool MUST NOT be selected unless it declares the required capability or its trust profile prohibits use for the risk tier or environment. When multiple tools satisfy a capability, the Selector MUST apply a deterministic selection policy, and the selection MUST be recorded in a Tool Selection Record (selection identifier, task identifier, required capability, candidate tools, selected tool, rationale, governance constraints, timestamp).

---

## 22.0 Tool Invocation Contract

Every tool invocation MUST use the standardized invocation contract.

### 22.1 Invocation Request

An Invocation Request MUST include an invocation identifier; tool identifier; capability; task identifier; specification identifier and version; artifact identifiers (or explicit justification for artifact-free invocation); input locations (conditional); parameters (conditional); an environment profile; a timeout; a timeout override reason when it differs from the Tool Record; expected output types (conditional); an invoked timestamp and invoker; and a governance authorization identifier when the invocation needs approval.

### 22.2 Invocation Rules

Every invocation MUST reference a registered active Tool Record and a task identifier, and MUST reference one or more artifacts or explicitly justify artifact-free invocation. Parameters MUST satisfy the tool's configuration schema when declared. Timeout overrides MUST be recorded with justification. The invocation MUST be linked to traceability state. Artifact-free invocation is permitted only when the capability allows it, the task identifier is present, the target is represented in parameters or context, and the rationale is recorded.

---

## 23.0 Invocation Results, Findings, and Normalization

### 23.1 Invocation Result

Every invocation MUST produce an Invocation Result or a Tool Invocation Error Record. An Invocation Result MUST include a result identifier; matching invocation identifier; tool identifier; capability; task identifier; artifact identifiers; an outcome (`passed`, `failed`, `warning`, `timeout`, `error`, `not-applicable`); findings for failed, warning, timeout, or error outcomes; output artifacts (conditional); evidence locations (conditional); duration; completion timestamp; the confirmed tool version; confirmed environment (conditional); the normalizer; and a next action (`proceed`, `repair`, `escalate`, `retry`, `halt`, `record-only`).

### 23.2 Result Rules

The tool-version-confirmed field MUST be populated when the tool starts successfully. A timeout MUST NOT be converted to passed. An error MUST NOT be converted to passed. A warning MAY permit progression only when validation criteria and governance policy allow warnings. Raw outputs SHOULD be preserved when evidence retention is required.

### 23.3 Finding

A Finding MUST include a finding identifier, severity, category, description, affected artifact (conditional), location (conditional), rule identifier (conditional), related policy or validation identifier (conditional), suggested remediation (conditional), raw reference (conditional), and confidence (conditional). Critical and high severity security findings MUST be treated as failed outcomes unless governance explicitly allows another response with a valid waiver. Findings of informational severity MUST NOT block progression unless a policy defines them as blocking. A finding affecting must-have requirement validation MUST be blocking unless waived.

### 23.4 Result Normalization

The Result Normalizer MUST parse tool output, determine the normalized outcome, extract findings, map findings to artifacts and policies or validations where possible, preserve raw output references, record normalization errors, confirm tool version where possible, and produce the Invocation Result schema. If tool execution succeeds but output cannot be normalized, the result MUST be treated as `error` unless governance permits manual interpretation, and a Tool Normalization Error Record MUST be produced.

---

## 24.0 Tool Conflict and Version Management

### 24.1 Conflict Handling

Conflicts occur when tools evaluate the same artifact, capability, policy, or validation criterion and produce incompatible outcomes (outcome, severity, policy, version, scope, or finding conflicts). Unless a governance policy defines otherwise, conflicts MUST be handled **fail-closed**: a passed result MUST NOT override a failed result; security or policy failures MUST remain blocking; conflicting results MUST be escalated or resolved before artifact acceptance; and the artifact MUST NOT be marked valid until conflict resolution completes. Conflict details MUST be recorded in a Tool Conflict Record. A governance profile MAY define tool precedence rules, but precedence MUST NOT allow a lower-trust tool to override a higher-trust blocking result unless governance explicitly permits it.

### 24.2 Version Management

Every Tool Record MUST declare an approved version or range. Every Invocation Result MUST include tool-version-confirmed; if the version cannot be confirmed, the result MUST include a finding or warning unless governance permits unknown versions. Version drift (confirmed version differing from registered) MUST be recorded and handled per governance; by default, drift in validation, security, infrastructure, or deployment tools MUST produce a warning or failed outcome depending on risk tier. Repeated drift or failure conditions SHOULD trigger governance alerting.

### 24.3 Configuration Management

A tool invocation MUST record configuration inputs sufficient to reproduce or explain the result (config file references, ruleset versions, severity thresholds, language settings, environment variables, plugin versions, policy profiles, execution flags). Configurations used for validation, security, infrastructure, or deployment MUST be versioned or identifiable; configuration changes affecting validation results SHOULD trigger revalidation of affected artifacts.

---

## 25.0 Execution Environment and Sandboxing

Tools may compile code, execute tests, inspect files, access dependencies, or run infrastructure validation, so they MUST execute within controlled environments appropriate to their risk. Environments are `local-process`, `container`, `remote-service`, `restricted-vm`, or `plugin-host`. Each environment MUST define filesystem access boundaries, network access policy, environment variable access, secret access policy, resource limits, timeout controls, artifact input and output locations, and cleanup behavior. Tools MUST NOT receive unrestricted secrets by default and MUST NOT write outside approved workspace locations unless explicitly authorized. Tools executing generated code SHOULD run in isolated environments. Sandbox violations MUST be treated as tool errors and governance-relevant security events.

---

## 26.0 Category-Specific Tool Integration

### 26.1 Testing Tools

Testing tools MUST report individual test case outcomes; aggregate results without case detail MUST NOT satisfy test execution requirements for must-have requirements. Results MUST map to test artifact, Validation entity, and Requirement identifiers, artifacts under test, and the execution timestamp, and be converted into ISL v1.2 Test Result records. A failed or skipped test linked to a must-have requirement MUST block or require justification as defined in §12.5.

### 26.2 Security Tools

Platforms operating beyond advisory planning SHOULD support `vulnerability-scan`, `dependency-audit`, `secret-detection`, `policy-check`, and `license-scan`. Security findings MUST include severity and SHOULD map to Policy entities. Critical and high severity findings MUST be treated as failed outcomes unless governance waives or reclassifies them; a security result MUST NOT be downgraded without a governance record. Evidence MUST be retained for Elevated, High, or Critical risk systems, when governance requires audit evidence, when findings are waived, and when findings affect deployment authorization.

### 26.3 Infrastructure Tools

Infrastructure validation MUST verify deployment model and runtime platform compatibility, declared resource satisfiability, network configuration, environment configuration completeness, manifest syntax, and health check configuration. Findings affecting deployment feasibility MUST block deployment preparation; findings affecting security or access control MUST follow security policy rules; warnings MAY proceed only if governance allows them.

### 26.4 Development and Build Tools

Build tools SHOULD support `compile`, `lint`, `type-check`, `format-check`, `dependency-resolve`, and `package`. A source artifact MUST NOT be accepted as valid if compilation or equivalent language validation fails. Dependency resolution failures MUST block packaging unless governance permits deferred resolution. Lint or formatting failures MAY be warning-level unless policy defines them as blocking.

### 26.5 Deployment Tools

Deployment tools MAY provide `artifact-package`, `container-build`, `manifest-validate`, `deployment-dry-run`, `release-bundle-generate`, and `environment-compatibility-check`. Outputs MUST trace to Infrastructure entities or deployment tasks. Deployment validation failures MUST block deployment preparation readiness, and tool success MUST NOT override governance deployment authorization requirements.

### 26.6 Tool Feedback and Repair Loop

When a tool result triggers repair, the runtime MUST provide the Repair Analyst with the failed invocation result, findings, affected artifacts, related task and specification entities, prior repair history, tool version and configuration, and raw evidence references. A repair cycle MUST reference the triggering tool result, and a repaired artifact MUST be revalidated. A repair MUST NOT close a failed tool result without successful validation or governance waiver. Repeated findings SHOULD be used to detect non-converging repair cycles.

---

## 27.0 Governance Integration and Tool Governance

### 27.1 Governance Checkpoints

Execution MUST operate under the governance model defined in ISL v1.3. The runtime MUST consult governance before or during execution initiation, policy enforcement, repair escalation, waiver application, manual override, deployment preparation, deployment authorization, and continuation after escalation.

### 27.2 Governance Blocking Rule

If governance blocks an execution action, the runtime MUST NOT proceed until governance records a resolution, waiver, override, or authorization. The runtime MUST handle governance decisions using the shared Governance Decision enum (`allow`, `warn`, `block`, `escalate`, `approval-required`, `waiver-required`, `override-required`): proceed, record and proceed, do not proceed, pause and open escalation, create or wait for an approval gate, pause until a valid waiver exists, or pause until a valid override exists. Expired approvals, waivers, or overrides MUST NOT authorize progression.

### 27.3 Governance Event Recording

The runtime MUST record governance-relevant events including execution-started, execution-halted, policy-evaluated, violation-detected, repair-escalated, waiver-applied, override-applied, deployment-preparation-ready, execution-completed, and execution-failed.

### 27.4 Tool Governance

Governance MAY control tool registration, activation or revocation, allowed environments and risk tiers, network and secret access, tool version approval, use of experimental tools, waiver of tool findings, conflict resolution, and deployment tool authorization. A tool prohibited by governance MUST NOT be invoked; a governance-restricted tool MUST satisfy approval and environment constraints before invocation; a waiver of a tool finding MUST produce a governance waiver record. Tool evidence (raw logs, reports, normalized findings, output artifacts, configuration) MUST be retained when the result is failed, contains security findings, is waived, affects deployment readiness, governance requires retention, or the risk tier requires audit evidence.

---

## 28.0 Traceability During Execution

Traceability MUST be recorded as execution occurs, not reconstructed afterward. The runtime MUST record links between specification entities and construction tasks; construction tasks and generated artifacts; artifacts and validation results; validation failures and repair records; repair records and modified artifacts; policies and policy evaluation records; deployment artifacts and infrastructure entities; and governance events and affected tasks or artifacts. Traceability links MUST be recorded at the time the related event occurs. If required traceability cannot be recorded, the affected artifact or task MUST be marked failed or escalated, and a construction plan MUST NOT be marked completed while required traceability links are missing.

Tool activity MUST also be traceable: links are required between construction task and tool invocation, invocation and result, result and artifacts evaluated, result and Validation entity where applicable, result and Policy entity where applicable, failed result and Repair Record, tool conflict and conflicting invocations, and version drift and invocation result. A tool invocation MUST NOT be discarded even if it fails.

---

## 29.0 Parallel Execution

Tasks MAY execute in parallel when no dependency relationship exists, they do not write to the same artifact location, they do not require exclusive access to the same state resource, governance policies do not require sequential review, required tools and agents are available, and traceability can be recorded independently. Tasks MUST NOT execute in parallel when one depends on another, both modify the same artifact without coordination, execution would violate a governance checkpoint, shared-state consistency cannot be guaranteed, or repair of one task may invalidate another's outputs. Failure of one parallel task MUST NOT automatically halt unrelated tasks unless dependency, governance, or runtime configuration requires it.

---

## 30.0 Runtime Recovery and Resumption

The runtime MAY resume execution only if Execution Graph state, Artifact Repository state, traceability state, and governance state are available and consistent, unresolved escalations are not bypassed, and runtime configuration remains compatible. On recovery the runtime MUST reload the Execution Graph, verify artifact repository consistency, verify task states and validation and repair records, identify incomplete tasks and tasks safe to resume, and record a recovery event (recovery identifier, execution graph reference, recovery reason, resumed and blocked tasks, and a consistency status of `consistent`, `inconsistent`, or `partially-consistent`). If consistency status is inconsistent, execution MUST NOT resume until the inconsistency is resolved.

---

## 31.0 Completion Criteria

### 31.1 Required Completion Conditions

Execution MAY be marked completed only when all required phases succeeded; all required tasks are completed or governance-waived; all required artifacts are valid or governance-waived; all must-have requirements have passed validation or approved waiver; all blocking policy violations are resolved or waived; all required tests passed or have approved waiver; artifact consolidation succeeded; deployment preparation succeeded; traceability links are complete; governance state contains required execution records; and a Completion Report has been produced.

### 31.2 Failed Completion

Execution MUST be marked failed when a blocking precondition cannot be satisfied, the construction plan cannot execute, repair limits are reached without convergence, required validation cannot pass or be waived, required policy violations cannot be resolved or waived, traceability integrity cannot be established, or repository state cannot be made consistent.

### 31.3 Escalated Completion

Execution MUST be marked escalated when progress is suspended pending governance or human action. An escalated execution MUST NOT be marked completed or failed until the escalation is resolved or governance formally closes the execution.

### 31.4 Completion Report

A Completion Report MUST be generated for completed, failed, halted, or escalated executions and must include a report identifier, execution graph reference, specification identifier and version, final status (`completed`, `failed`, `halted`, `escalated`), completed and failed phases, artifact counts, validation, repair, test, and policy summaries, traceability status, deployment preparation status, governance event references, and a generation timestamp. The report MUST be stored in the Artifact Repository or governance/audit store, reference immutable execution records where supported, and MUST NOT claim completion if required validation, traceability, or governance conditions remain unresolved.

---

## 32.0 Errors and Telemetry

Execution errors and tool errors extend the common Error Record model (ISL v0.1 §8). Extension classes registered under the `runtime`, `tool`, `governance`, `traceability`, `validation`, and `repository` error categories include: execution-precondition-failed, execution-plan-not-executable, execution-task-failed, execution-artifact-missing, execution-validation-failed, execution-tool-timeout, execution-tool-error, execution-agent-failure, execution-repair-limit-reached, execution-policy-violation, execution-traceability-failure, execution-state-integrity-violation, execution-repository-failure, execution-deployment-preparation-failed, tool-not-registered, tool-capability-missing, tool-trust-profile-blocked, tool-configuration-invalid, tool-output-normalization-failed, tool-version-drift, tool-result-conflict, tool-sandbox-violation, tool-evidence-missing, and tool-unsupported-artifact.

Per the common model, blocking errors MUST prevent completion; errors affecting governance, policy, traceability, or authorization MUST be recorded in governance state; errors affecting artifacts MUST be linked to artifact metadata; and errors affecting tasks MUST be reflected in the Execution Graph. A tool or execution timeout MUST NOT be treated as success.

Telemetry uses the common Telemetry Event model (ISL v0.1 §9) and MUST include correlation identifiers. The runtime MUST emit telemetry for phase and task start/completion, artifact generation, tool selection, tool invocation start/completion/failure, timeout, version drift, normalization failure, result conflict, validation results, repair cycles, test execution, policy evaluation, governance events, escalation, halt, recovery, security findings, sandbox violations, and completion. Telemetry does not replace audit logs or traceability records.

---

## 33.0 Conformance

An Execution Runtime conforms to this document if it can enforce execution preconditions; execute the required phase model; maintain execution state; invoke deterministic validation through the Tool Invocation Contract; initiate bounded repair cycles; enforce the repair termination policy; integrate governance checkpoints and tool governance; update traceability during execution; handle tool conflicts, version drift, and normalization failures; preserve tool evidence and trust profiles; produce required execution and tool records; enforce completion criteria; and produce Completion Reports.

An Execution Graph conforms if it references the correct specification and construction plan, records phases, tasks, artifacts, validations, repairs, and governance events, preserves dependency and derivation edges, supports recovery and audit traversal, and reflects current runtime state. Execution records and Tool Records conform if they use the required schemas, include required identifiers, reference related tasks, artifacts, tools, agents, and policies, use valid outcome values, include timestamps, preserve traceability context, and are stored in the required state, repository, telemetry, or governance location.
