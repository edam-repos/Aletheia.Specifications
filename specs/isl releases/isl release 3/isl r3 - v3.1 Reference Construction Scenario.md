# ALETHEIA Specification Language (ISL) v3.1
# Reference Construction Scenario
**Status:** Informative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x, ISL v2.x, ISL v3.0
**Supersedes:** ISL r2 v3.2 The Reference Construction Pipeline, ISL r2 v3.3 The Reference Construction Scenario
**Document Type:** Execution Layer Specification — Informative Reference Construction Scenario

---

## 1.0 Scope

This document defines the Reference Construction Scenario for the ALETHEIA platform. It is the smallest practical system implementation that demonstrates autonomous software construction from a structured ISL specification to a validated, stable artifact repository, and the executable pipeline that performs that construction.

This document is retained as an informative scenario, regression fixture, and minimal construction target. Requirements language in this document constrains only the Reference Construction Scenario when an implementation chooses to claim Reference Construction Scenario conformance. It MUST NOT define enterprise platform conformance, override normative ISL documents, or make the Reference Construction Scenario the center of the ALETHEIA architecture. Enterprise conformance is defined by the normative corpus, not by this scenario.

This document defines:

* scenario objectives
* supported input scope
* required input fixture and example semantic content
* minimal target system
* minimum platform components
* construction pipeline stages
* required records, evidence, and run records
* task graph and generated artifact requirements
* deterministic validation and repair requirements
* tool, agent, repository, governance, and telemetry profiles
* success, failure, and escalation criteria
* scenario conformance requirements

Shared conventions — norm language, field levels, identifiers, shared enums, design principles, the common error model, and the common telemetry model — are defined in ISL v0.1 and referenced here rather than redefined.

---

## 2.0 Scenario Objectives

The Reference Construction Scenario exists to provide the first executable proof that the ALETHEIA architecture can function in practice: a structured ISL specification can be transformed into a working software artifact through canonical normalization, construction planning, agent-assisted generation, deterministic validation, bounded repair, artifact finalization, traceability, and telemetry.

Its value is not in the complexity of the generated system, but in proving that the construction loop works end to end without informal manual steps, hidden assumptions, or unrecorded decisions.

### 2.1 Primary Objectives

The scenario MUST demonstrate the ability to:

* ingest a structured ISL specification
* validate specification structure
* normalize the specification into canonical entities
* generate and validate a construction task graph
* execute the task graph through the runtime
* invoke at least one implementation-generation agent
* generate source, test, and configuration artifacts
* admit generated artifacts into an artifact repository
* compile or otherwise build the generated system
* execute deterministic validation tests
* detect validation failures
* perform bounded repair cycles and revalidate repaired artifacts
* finalize stable artifacts
* create traceability links from specification entities to artifacts
* emit telemetry for major pipeline stages
* produce a completion report

### 2.2 Secondary Objectives

The scenario SHOULD demonstrate deterministic task ordering, basic retry behavior, repair history preservation, artifact versioning after repair, tool result normalization, model invocation traceability, repository integrity checking, minimal queryable telemetry output, and repeatable execution from the same input.

### 2.3 Objective Rules

A primary objective MUST be satisfied for the scenario to be considered successful. A secondary objective SHOULD be satisfied where feasible; failure to satisfy a secondary objective MUST be recorded as a limitation. A run MUST distinguish primary-objective failure from secondary-objective limitation.

---

## 3.0 Design Principles

### 3.1 Narrow Scope, Real Execution

The scenario MUST be narrow in supported domain but MUST execute real platform behavior. It MUST NOT be a scripted demo that skips canonical normalization, planning, artifact generation, deterministic validation, state recording, or traceability. Implementations MAY use simplified modules, but those modules MUST preserve the same control boundaries defined in ISL v3.0.

### 3.2 Specification-First

The scenario MUST begin from a versioned ISL specification. The specification is the authoritative source of system intent. Generated artifacts MUST be traceable to entities in the input specification.

### 3.3 No Fake Success

The scenario MUST NOT report success unless generated artifacts compile or otherwise pass the configured deterministic validation tools. A successful natural-language explanation from an agent MUST NOT be treated as successful validation.

### 3.4 Evidence Before Claim

Every success criterion MUST be supported by a record, artifact, validation result, traceability link, state transition, or telemetry event. The pipeline MUST produce an evidence bundle sufficient to review the run after completion.

### 3.5 Repair Must Be Bounded

The scenario MUST support repair cycles, but repair MUST be bounded by the repair termination rules defined in ISL v1.2, v1.5, and v2.4. The implementation MUST NOT allow an unbounded generate-validate-repair loop.

### 3.6 Minimal Governance, Not No Governance

The scenario MAY use a simplified governance profile, but it MUST still represent readiness, execution authorization, validation waivers if any, and audit-relevant decisions. It MUST NOT bypass governance-controlled state transitions merely because it is a reference scenario.

---

## 4.0 Non-Goals

The first reference scenario is not required to demonstrate: multi-service distributed system generation, production deployment, container image generation, cloud infrastructure provisioning, database persistence or migration generation, authentication or authorization, message brokers, multi-tenant isolation, enterprise identity integration, complex policy governance, advanced security scanning, full infrastructure provisioning, distributed runtime workers, high availability, disaster recovery, advanced UI generation, multiple programming languages, domain-specific enterprise architecture modeling, or human approval workflow automation beyond minimal records.

The scenario MAY include any of these only if they do not obscure or weaken the primary objectives.

---

## 5.0 Supported Input Scope

The scenario MUST support a constrained subset of ISL sufficient to describe a minimal service-based application, structured enough to be parsed, normalized, planned, and traced.

### 5.1 Required Input Entities

The input specification MUST include at least:

| Entity | Minimum Count | Purpose |
| ------ | ------------: | ------- |
| Project | 1 | Identifies the system being constructed |
| Context | 1 | Describes scope and assumptions |
| Requirement | 1 | Defines expected behavior |
| Capability | 1 | Groups behavior into a platform capability |
| Service | 1 | Defines the generated service |
| Interface | 1 | Defines an externally visible operation |
| DataEntity | 1 | Defines request or response structure |
| Validation | 1 | Defines acceptance or verification conditions |

### 5.2 Optional Input Entities

The input MAY include `Actor`, `Workflow`, `Policy`, `Infrastructure`, and additional `Requirement`, `Interface`, `DataEntity`, or `Validation` entities.

### 5.3 Unsupported Scope

The scenario is not required to support: multiple services, asynchronous workflows, persistence stores, authentication, authorization, message brokers, infrastructure provisioning, deployment automation, multi-tenant behavior, advanced security policies, or domain-specific integration patterns. Unsupported content MUST be reported as unsupported, ignored with warning, or rejected according to the configured validation profile.

### 5.4 Input Format Rules

The scenario MUST support at least one structured representation format (for example, Markdown, YAML, or JSON). The supported format MUST be documented, and the input MUST be stored as a versioned example under the reference repository. The input MUST have deterministic identifiers for canonical entities and MUST be sufficient to generate the minimal target system without hidden manual requirements.

---

## 6.0 Required Input Fixture

The reference implementation MUST include a concrete input specification fixture so that the scenario input is never absent.

### 6.1 Fixture Location

The reference implementation SHOULD store the input fixture at:

`/examples/specifications/reference/reference-greeting-service.isl.md`

If another location is used, the path MUST be declared in the scenario run configuration.

### 6.2 Fixture Identifier Values

The default fixture SHOULD use the following identifiers:

| Entity | Identifier |
| ------ | ---------- |
| Project | `PRJ-reference-greeting-00001` |
| Context | `CTX-reference-greeting-00001` |
| Requirement | `REQ-reference-greeting-00001` |
| Capability | `CAP-reference-greeting-00001` |
| Service | `SVC-reference-greeting-00001` |
| Interface | `INT-reference-greeting-00001` |
| DataEntity | `DAT-reference-greeting-00001` |
| Validation | `VAL-reference-greeting-00001` |

### 6.3 Required Fixture Metadata

| Field | Value or Rule |
| ----- | ------------- |
| specification-id | `reference-greeting` |
| specification-version | `1.0.0` |
| isl-version | supported ISL version |
| name | Reference Greeting Service |
| owner | reference construction implementation |
| readiness-target | `autonomous-ready` (see ISL v0.1 §6.1) |
| risk-tier | `standard` (see ISL v0.1 §6.2) |
| target-platform | .NET unless an equivalent target is declared |
| target-language | C# unless an equivalent target is declared |
| validation-profile | build-and-test |

### 6.4 Required Fixture Content

The fixture MUST define one project named Reference Greeting Service; one context describing the service as a minimal reference scenario target; one requirement requiring a greeting response; one capability representing greeting generation; one service implementing that capability; one interface exposing a greeting operation; one data entity representing the greeting response; and one validation requiring build and test success. The fixture MUST describe one externally callable operation, one request/response data structure, expected success behavior, and expected generated artifact types.

### 6.5 Example Semantic Content

The normalized canonical model of the fixture MUST preserve the following semantics (expressed here as the minimum semantic content; any supported representation MAY be used):

| Entity | Key Fields |
| ------ | ---------- |
| Project | id `PRJ-reference-greeting-00001`; name Reference Greeting Service; version 1.0.0; readiness-level `autonomous-ready`; risk-tier `standard` |
| Context | id `CTX-reference-greeting-00001`; scope limited to one HTTP greeting operation and deterministic build/test validation |
| Requirement | id `REQ-reference-greeting-00001`; name Generate Greeting Response; the service MUST return a greeting message that includes the caller-provided name; priority `must-have` (see ISL v0.1 §6.9); acceptance criterion "calling the greeting operation with name Ada returns a response containing Hello, Ada" |
| Capability | id `CAP-reference-greeting-00001`; name Greeting Generation; satisfies `REQ-reference-greeting-00001` |
| Service | id `SVC-reference-greeting-00001`; name Greeting Service; provides `CAP-reference-greeting-00001` |
| Interface | id `INT-reference-greeting-00001`; name Get Greeting; method GET; path `/greeting/{name}`; service `SVC-reference-greeting-00001`; response-entity `DAT-reference-greeting-00001` |
| DataEntity | id `DAT-reference-greeting-00001`; name Greeting Response; attribute `message:string:required` |
| Validation | id `VAL-reference-greeting-00001`; validation-type `build-and-test`; validates `REQ-reference-greeting-00001` and `INT-reference-greeting-00001`; required-tool-capabilities `compile`, `unit-test` |

### 6.6 Fixture Rules

The fixture MUST be version controlled, MUST be used by automated scenario tests, MUST be sufficient to reproduce a scenario run, and MUST NOT contain real secrets, regulated data, or proprietary production content. Changes to the fixture MUST trigger scenario regression testing.

---

## 7.0 Target System

### 7.1 Default Target

The default target system MUST be named **Reference Greeting Service**, a minimal .NET REST service exposing one HTTP operation that returns a greeting response.

The service MUST satisfy the following behavior:

* accept a name value from the caller
* return a greeting message containing that name
* return a default greeting when no name is provided, if the generated platform chooses to support optional input
* expose at least one deterministic validation test proving expected behavior

### 7.2 Default Target Technology

| Field | Value |
| ----- | ----- |
| target-platform | .NET |
| target-language | C# |
| service-style | minimal REST API |
| test-framework | xUnit, NUnit, MSTest, or equivalent |
| build-tool | dotnet CLI or equivalent |

### 7.3 Equivalent Target

An implementation MAY choose another platform or language if it provides equivalent proof. An equivalent target MUST include an executable or buildable project structure, a callable interface or operation, generated source and test artifacts, deterministic build validation, deterministic test validation, artifact repository metadata, and traceability links.

### 7.4 Target System Rules

* The target system MUST be generated from the ISL fixture. It MUST NOT be prewritten and copied as final output without agent or generation activity.
* Boilerplate templates MAY be used only if artifact metadata records which content was templated and which was derived from specification entities.
* The generated system MUST be independently buildable using the declared validation profile.

---

## 8.0 Minimum Platform Components

The scenario MUST include functional implementations or constrained substitutes for: Specification Engine, Semantic Model Engine, Readiness Evaluator, Planning Engine, Execution Runtime, Agent Orchestrator, Model Integration Layer, Artifact Repository, Tool Integration Layer, Traceability Engine, State Manager, Observability Emitter, and Governance Module.

A simplified component MUST still preserve the contract of the subsystem it represents. A mocked or stubbed component MUST be clearly identified in the scenario run report, and MUST NOT be used to satisfy deterministic validation, artifact creation, or completion evidence unless the test explicitly declares itself non-conforming.

Permitted simplifications include identity integration, distributed worker scheduling, dashboard presentation, complex model routing, enterprise policy libraries, artifact branch strategy, deployment packaging, and external telemetry export.

---

## 9.0 Construction Pipeline Overview

The scenario pipeline consists of ordered stages. Stages MUST execute in the listed order unless a later revision defines a valid parallelization model. A stage MUST NOT be marked complete unless its required outputs exist. A failed stage MUST produce a structured error record (using the common error model, ISL v0.1 §8) and MAY trigger retry, repair, escalation, or terminal failure according to this document and ISL v2.0.

| Stage | Name | Primary Purpose |
| ----: | ---- | --------------- |
| 1 | Specification Intake | Load and structurally validate the input fixture |
| 2 | Semantic Normalization | Produce the canonical semantic model |
| 3 | Readiness and Governance Admission | Confirm execution eligibility |
| 4 | Construction Planning | Generate the executable task graph |
| 5 | Runtime Initialization | Initialize execution graph, state, telemetry, repository workspace |
| 6 | Artifact Generation | Generate source, test, config, and metadata artifacts |
| 7 | Repository Admission | Admit artifacts into controlled repository state |
| 8 | Deterministic Validation | Compile/build and test generated artifacts |
| 9 | Repair Cycle | Analyze failures, revise artifacts, revalidate |
| 10 | Artifact Finalization | Promote valid artifacts to stable state |
| 11 | Traceability and Evidence Closure | Produce links, snapshots, evidence bundle |
| 12 | Completion Reporting | Produce the final scenario completion report |

### 9.1 Stage 1 — Specification Intake

Specification Intake loads the fixture and verifies it is structurally valid for the supported ISL subset. The Specification Engine MUST verify required metadata, required sections, required entity identifiers and types, syntactic validity of entity references, and MUST report unsupported sections and record blocking structural errors. Outputs include the Parsed Specification Record, Structural Validation Report, source location map (SHOULD), and Specification State Record. If required metadata or entities are missing, intake MUST fail. If source cannot be parsed, pipeline execution MUST stop before semantic normalization.

### 9.2 Stage 2 — Semantic Normalization

Semantic Normalization converts the parsed specification into canonical entities and relationships, producing the Canonical Model Record, canonical entity and relationship records, Canonical Validation Report, and Canonical Model State Record. Each parsed entity MUST map to a canonical entity or produce a finding; every canonical entity MUST include required base fields; every canonical relationship MUST reference valid entity identifiers. The canonical model MUST include the required relationships (project contains context and requirement; capability groups requirement; service implements capability; service exposes interface; interface produces data entity; validation validates requirement and interface) and MUST pass required-entity-presence, identifier-format, relationship-target-resolution, required-field, interface-to-service, validation-to-requirement, and validation-tool-capability checks. A canonical model with blocking validation errors MUST NOT proceed to planning.

### 9.3 Stage 3 — Readiness and Governance Admission

This stage confirms the specification and run are eligible for execution. Outputs include the Readiness Evaluation Record, Governance Check Record, Execution Admission Decision, and an Audit-Support Record (SHOULD). The run MUST NOT proceed to planning unless canonical validation passed or non-blocking warnings are explicitly accepted by the governance profile. The scenario MUST use `standard` risk tier unless the input fixture declares a different tier, MUST record execution authorization even if simplified, and a blocked governance decision MUST stop the pipeline.

### 9.4 Stage 4 — Construction Planning

Construction Planning produces the construction plan record and construction task graph, planning validation report, and task-to-entity traceability seeds. Inputs include the canonical model, readiness and governance records, tool capability catalog, agent role registry, and artifact type policy.

The task graph MUST include tasks equivalent to specification-interpretation, architecture (SHOULD), implementation, schema (CONDITIONAL), interface, test, verification-build, verification-test, repair, artifact-finalization, traceability-closure, and completion-report. For the Reference Greeting Service, required tasks are: interpret, plan, project, service, data, tests, build, test-run, repair, finalize, trace, and complete, with dependency ordering such that plan follows interpret; project follows plan; service and data follow project; tests follow service and data; build follows service and data; test-run follows build and tests; repair follows build or test-run failure; finalize follows build and test-run; trace follows finalize; complete follows trace.

Planning rules: every task MUST trace to at least one canonical entity or pipeline obligation; every artifact-producing task MUST declare expected artifact types; every validation task MUST declare required tool capability; every repair task MUST declare repair limits; the plan MUST NOT be executable until planning validation passes.

### 9.5 Stage 5 — Runtime Initialization

Runtime Initialization produces the execution graph, initial runtime state record, initial checkpoint, work item records, and repository workspace record. The runtime MUST initialize an execution graph before dispatching work, MUST create an initial checkpoint, MUST create work items for eligible tasks, MUST emit execution-start telemetry, and MUST NOT dispatch work if state, repository, governance, or traceability components are unavailable.

### 9.6 Stage 6 — Artifact Generation

Artifact Generation creates candidate implementation artifacts from the task graph. Outputs include agent invocation records, model or local generation records, agent output validation records, candidate artifact records, and artifact content. The scenario MUST generate at least one source artifact (service entry point or handler), one source or schema artifact (data model/DTO), one test artifact, one config artifact, and artifact metadata. For a .NET target, generated artifacts SHOULD include the project file, program entry point, endpoint or controller handler, data model class or record, test project file, and test file.

Generation rules: generated artifacts MUST reference source canonical entities; MUST be produced as candidates before repository admission; agent outputs MUST pass output validation before admission; generated behavior MUST NOT exceed the supported specification scope unless explicitly recorded as implementation scaffolding.

### 9.7 Stage 7 — Repository Admission

Repository Admission accepts candidate artifacts into controlled working state, producing artifact admission records, artifact metadata records, artifact state records, and a repository state record. Every candidate artifact MUST receive an artifact identifier, MUST have metadata, MUST be associated with a producing task and with one or more source entities or pipeline obligations. Artifacts with missing content or metadata MUST be rejected. Artifacts admitted before validation MUST have `pending` state (see ISL v0.1 §6.7).

### 9.8 Stage 8 — Deterministic Validation

Deterministic Validation verifies generated artifacts through build and test tools, producing tool invocation records and results, validation result records, tool evidence records, and artifact state updates. The scenario MUST perform source build or compile, automated test execution, project/configuration validity (SHOULD), repository integrity check (SHOULD), and traceability completeness check (YES before finalization).

Validation rules: a build or test failure MUST produce a `failed` validation result (see ISL v0.1 §6.5); a tool timeout MUST NOT be treated as success; a validation result MUST reference the artifact version evaluated; a failed validation MUST prevent artifact finalization unless repaired or waived.

### 9.9 Stage 9 — Repair Cycle

Repair Cycle responds to failed validation by analyzing findings, generating corrected artifacts, and revalidating. Outputs include a repair analysis record, repair proposal, repair task record, revised artifact version (CONDITIONAL), revalidation result (YES when repair modifies artifacts), and repair outcome record. A repair cycle MUST reference the validation failure that triggered it; a repaired artifact MUST create a new version or supersession metadata; a repaired artifact MUST be revalidated; a repair MUST NOT be resolved without successful revalidation or a valid waiver.

The scenario MUST enforce the following limits:

| Parameter | Required Default |
| --------- | ---------------: |
| Maximum repair iterations per artifact | 5 |
| Maximum total repair iterations per scenario run | 50 |
| Escalation threshold for same artifact | 3 consecutive failures |

Repair exhaustion MUST escalate or fail the scenario run.

### 9.10 Stage 10 — Artifact Finalization

Artifact Finalization promotes valid artifacts into stable repository state, producing artifact promotion records, stable artifact records, a repository integrity check, and a final repository state record. An artifact MUST NOT be promoted to stable unless validation passed or a valid waiver exists, unless traceability is complete, and unless repository integrity checks pass. The scenario MUST produce a stable artifact repository or fail.

### 9.11 Stage 11 — Traceability and Evidence Closure

Traceability and Evidence Closure ensures outputs can be reviewed after the run. The scenario MUST record links between: specification and canonical model; canonical entities and construction tasks; construction tasks and agent invocations; agent invocations and generated artifacts; generated artifacts and validation results; failed validation results and repair records; repaired artifacts and superseded artifacts; stable artifacts and source entities; pipeline run and completion report.

The scenario MUST produce an evidence bundle (see §16). Traceability closure MUST run before completion reporting; missing required traceability or evidence MUST fail the scenario.

### 9.12 Stage 12 — Completion Reporting

Completion Reporting determines and records the final outcome. The scenario MUST persist a completion report. The final outcome MUST be `completed` only when all required success criteria pass; MUST be `failed` when a required stage fails without repair, waiver, or valid escalation resolution; and MUST be `escalated` when human or governance intervention is required and not resolved during the run.

Completion report fields include `completion-report-id`, `reference-run-id`, `specification-id`, `specification-version`, `target-system`, `final-outcome`, `artifacts-produced`, `stable-artifacts` (CONDITIONAL), `validations-executed`, `repair-count`, `waivers-used` (CONDITIONAL), `traceability-snapshot-id`, `evidence-bundle-id`, `limitations` (CONDITIONAL), and `completed-at`.

---

## 10.0 Run Record

Each scenario execution MUST produce a single Run Record identified by `reference-run-id`. This is the authoritative run identifier used by run configuration, telemetry, evidence, and the completion report.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| reference-run-id | string | REQUIRED | Unique scenario run identifier |
| input-specification-id | string | REQUIRED | Input specification identifier |
| input-specification-version | semver | REQUIRED | Input specification version |
| target-system | string | REQUIRED | Reference Greeting Service or equivalent |
| target-platform | string | REQUIRED | Target platform |
| target-language | string | REQUIRED | Target language |
| generation-mode | enum | REQUIRED | model-generated, template-assisted, deterministic-template, hybrid |
| runtime-configuration-id | string | REQUIRED | Runtime configuration used |
| governance-profile-id | string | REQUIRED | Governance profile used |
| tool-profile-id | string | REQUIRED | Tool profile used |
| artifact-workspace-id | string | REQUIRED | Artifact workspace |
| status | enum | REQUIRED | Execution Status values (ISL v0.1 §6.11): initialized, running, paused, recovering, completed, failed, halted, cancelled, escalated |
| started-at | ISO 8601 | REQUIRED | Start time |
| completed-at | ISO 8601 | CONDITIONAL | Completion time |

---

## 11.0 Tool Profile

The scenario MUST define a minimum deterministic tool profile.

### 11.1 Required Tool Capabilities

| Capability | Required | Purpose |
| ---------- | -------- | ------- |
| compile or build | YES | Verify generated source can build |
| unit-test or equivalent | YES | Verify generated behavior |
| dependency-resolve | SHOULD | Verify dependencies restore |
| format-check | OPTIONAL | Validate formatting |
| repository-integrity-check | SHOULD | Verify repository consistency |

For the default .NET target, the profile SHOULD support equivalent commands for dependency restore, project build, test execution, and repository integrity verification.

### 11.2 Tool Profile Rules

* Tools MUST be registered before use.
* Tool versions MUST be recorded.
* Tool invocation outcomes MUST be normalized (see Tool Integration Layer, ISL v3.0 §15).
* Tool evidence MUST be retained.
* A missing required tool capability MUST fail execution admission.
* A compile or test failure MUST prevent success; a tool timeout MUST NOT be treated as success.

---

## 12.0 Agent Profile

### 12.1 Required Agent Roles

| Agent Role | Required | Purpose |
| ---------- | -------- | ------- |
| Specification Interpreter | YES | Convert canonical model into execution-oriented interpretation |
| Implementation Generator | YES | Generate project, endpoint, model, and implementation artifacts |
| Test Generator | YES | Generate behavior validation tests |
| Repair Analyst | YES | Analyze build or test failures |
| Review Agent | SHOULD | Review generated artifacts or agent output |
| Construction Planner | SHOULD | Assist with task graph validation |
| Security Validator | OPTIONAL | Only required if security policy included |
| Deployment Preparer | NO | Not required for the first scenario |

### 12.2 Agent Rules

Required agent roles MUST have role contracts. Agent invocations MUST produce structured outputs that reference task and canonical entity identifiers, MUST declare produced artifact candidates where applicable, and MUST be validated before artifact admission. Agent failures MUST be recorded and handled according to ISL v2.1 and v2.4.

---

## 13.0 Model Integration Layer Requirements

The scenario MAY use a local model, external governed model, deterministic template generator, or hybrid generation mode. Regardless of strategy, the model/generation boundary MUST be explicit.

### 13.1 Generation Modes

| Mode | Description |
| ---- | ----------- |
| model-generated | Artifacts generated primarily through model invocation |
| template-assisted | Templates provide scaffolding and agents fill specification-derived content |
| deterministic-template | Artifacts generated deterministically from canonical entities |
| hybrid | Combination of templates, model output, and deterministic generation |

### 13.2 Generation Mode Rules

* The selected generation mode MUST be recorded in the run configuration (ISL v0.1 §9 telemetry model and the Model Interaction Protocol, ISL v3.2, apply when model invocation is used).
* If model-generated or hybrid mode is used, model invocations MUST be recorded.
* If deterministic-template mode is used, template versions MUST be recorded.
* Template-generated content MUST still be traceable to canonical entities or platform generation rules.
* The implementation MUST NOT hide prewritten final output as if it were model-generated output.

---

## 14.0 Repository, Governance, and State Profiles

### 14.1 Repository Profile

The scenario artifact repository MAY be local but MUST preserve artifact control. It MUST support artifact admission, metadata, states, versions, content storage, validation evidence references, traceability references, stable promotion, and repository integrity checking. Generated files MUST NOT be treated as stable until promotion; artifacts MUST have identifiers and metadata; stable artifacts MUST have validation evidence and traceability links.

The workspace MUST distinguish `candidate`, `pending`, `failed`, `repaired`, `valid`, `stable`, `escalated`, and `waived` states (see ISL v0.1 §6.7). Each metadata record MUST include `artifact-id`, `artifact-name`, `artifact-type`, `artifact-version`, `specification-id`, `specification-version`, `task-id`, `source-entity-ids`, `repository-location`, `current-state`, `validation-status`, and `traceability-status`. Generated artifacts MUST enter `pending` state before validation; artifacts that fail validation enter `failed` or `repaired` state; artifacts that pass validation and traceability closure MAY enter `stable` state; stable artifacts MUST NOT be overwritten by repair (repair creates a new version or supersession record).

### 14.2 Governance Profile

The scenario governance profile MAY be simplified but MUST be explicit. It MUST record risk tier assignment (`standard` by default), ready/execution admission decision, governance profile used, policy violations if any, waivers if any, escalations if any, and completion authorization or local equivalent. A waiver MUST NOT be silently assumed; an escalation MUST stop the affected stage until resolved or recorded as unresolved. Governance records MAY be local but MUST be persisted. The scenario MUST NOT silently waive validation failure, MUST NOT continue after a governance block, and MUST record escalation when repair thresholds are reached.

### 14.3 State and Recovery Profile

The scenario MUST record state for the run, specification, canonical model, planning, execution, task, artifact, validation, repair (where applicable), traceability, and completion. The scenario SHOULD create checkpoints after specification intake, after canonical normalization, after construction planning, before and after deterministic validation, before artifact finalization, and at completion. If the scenario supports recovery, it MUST validate artifact, state, repository, governance, and traceability consistency before resuming. If it does not support recovery in the first implementation, it MUST declare recovery unsupported and MUST NOT claim recovery conformance.

---

## 15.0 Telemetry Profile

The scenario MUST emit telemetry sufficient to understand the run, using the common telemetry model (ISL v0.1 §9). Telemetry MUST include `reference-run-id`, SHOULD include `specification-id`, `specification-version`, `task-id`, `artifact-id`, `validation-result-id`, and outcome where applicable, and MUST preserve correlation identifiers. Telemetry MUST NOT replace evidence records and MUST NOT expose restricted context or secrets. A telemetry summary MUST be included in the evidence bundle.

The scenario SHOULD emit the following scenario-specific events: scenario run started; stage started, completed, and failed; specification parsed; canonical model generated; construction plan generated; execution graph initialized; task started and completed; agent invoked; model invoked; artifact admitted; tool invoked; validation passed and failed; repair started and completed; artifact promoted; traceability snapshot created; scenario run completed.

---

## 16.0 Run Configuration and Evidence Bundle

### 16.1 Run Configuration

Each run SHOULD use an explicit run configuration identifying `reference-run-configuration-id`, `reference-run-id`, `input-specification-location`, `target-platform`, `target-language`, `generation-mode`, `validation-profile-id`, `governance-profile-id`, `telemetry-profile-id`, `artifact-workspace`, `repair-enabled`, `max-repair-iterations-per-artifact`, `max-total-repair-iterations`, `failure-injection-enabled`, and `created-at`.

### 16.2 Evidence Bundle

The scenario MUST produce an evidence bundle for every successful run and SHOULD produce a partial evidence bundle for failed runs. The bundle MUST reference the `reference-run-id` and be stored in the artifact repository or evidence store.

Evidence required for success includes: input specification; parsed specification record; canonical model record; canonical validation report; readiness and governance admission record; construction task graph; execution graph; agent invocation records; model invocation or template generation records (CONDITIONAL); artifact metadata records; artifact content references; tool invocation records; validation result records; repair records (CONDITIONAL); final traceability snapshot; telemetry summary; and completion report.

A successful run MUST have a complete evidence bundle. A failed run SHOULD preserve all evidence produced before failure.

---

## 17.0 Success Criteria

The scenario is successful only if ALL of the following criteria are satisfied. This is the single authoritative success-criteria set for the scenario; success is determined by evidence, not assertion.

| Criterion | Required Evidence |
| --------- | ----------------- |
| Input fixture parsed successfully | Structural Validation Report |
| Canonical model generated and validated | Canonical Model Record and Canonical Validation Report |
| Construction plan (task graph) generated and validated | Planning Validation Report / Construction Plan Record |
| Runtime executed the task graph | Execution Graph |
| Required artifacts generated | Artifact Metadata Records |
| Artifacts admitted to repository | Artifact Admission Records |
| Build or compile validation passed | Tool Invocation Result |
| Test validation passed | Test/Validation Result |
| Repair path demonstrated (actual or verified by failure injection) | Repair Record or Scenario Test |
| Artifacts promoted to stable | Artifact Promotion Records |
| Traceability closure passed | Traceability Snapshot |
| Telemetry emitted | Telemetry Summary |
| Completion report produced | Completion Report |

Success rules: a run MUST NOT be considered successful if build validation fails, if automated test validation fails, if required traceability is missing, if artifacts are generated but not admitted to repository state, if stable artifacts lack traceability, if the evidence bundle is incomplete, or if the completion report lacks required evidence references.

---

## 18.0 Failure and Escalation Criteria

### 18.1 Failure Criteria

The scenario MUST fail when: the input specification cannot be parsed; the canonical model cannot be generated; required canonical entities are missing; the construction plan cannot be generated or planning validation fails; a required agent role is unavailable; a required tool capability is unavailable; the runtime cannot initialize; generated artifacts are missing; repository admission fails; build or test validation fails after repair exhaustion; traceability closure fails; or the required evidence bundle cannot be produced.

### 18.2 Escalation Criteria

The scenario MUST escalate when: a repair threshold is reached; the same artifact fails three consecutive repairs; governance blocks execution but resolution is possible; a validation failure cannot be automatically classified; an artifact conflict cannot be resolved; model output repeatedly fails the required output contract; tool output cannot be normalized; or state consistency cannot be confirmed.

### 18.3 Failure Rules

Every failure MUST produce a structured error record (ISL v0.1 §8). A terminal failure MUST produce a completion report with final-outcome `failed` when possible. A terminal escalation MUST produce a completion report with final-outcome `escalated` when possible. A terminal failure MUST produce a partial evidence bundle when possible, and a failed run SHOULD be reproducible from its run record and evidence.

---

## 19.0 Failure-Injection and Repeatability

### 19.1 Failure-Injection

A scenario that never encounters failure does not prove the repair path. The implementation SHOULD include at least one failure-injection scenario, such as generating an artifact with an intentional compile error, generating a test that initially fails, simulating a tool timeout, simulating malformed agent output, simulating missing artifact metadata, or simulating a traceability link omission. Failure injection MUST be explicitly configured, MUST NOT contaminate normal successful runs, and MUST prove that the expected error, repair, retry, escalation, or failure path occurs.

### 19.2 Repeatability

The scenario SHOULD record input specification version, pipeline version, runtime configuration, model provider and model version, tool versions, repository workspace, environment information, random seed where applicable, and generation template versions where applicable. A repeated run SHOULD produce equivalent artifact structure and validation outcomes; MAY produce textually different generated code if it still passes validation and traceability; and differences between runs SHOULD be visible through artifact metadata and run comparison records.

---

## 20.0 Evolution Path

After the scenario succeeds, the platform MAY evolve in controlled increments: minimal REST service generation; multiple endpoints; multiple data entities; input validation and error responses; basic persistence; security policy validation; deployment package preparation; multi-service construction; enterprise governance profile; and distributed runtime execution.

Each evolution increment MUST define the supported ISL subset expansion, new generated artifact types, new validation and traceability requirements, new governance implications, new success criteria, and regression tests against prior capability. Evolution MUST NOT remove the ability to run the original Reference Greeting Service scenario unless formally deprecated.

---

## 21.0 Scenario Conformance Requirements

### 21.1 Input Conformance

A scenario input specification conforms if it declares required metadata, includes all required entity types, uses valid identifiers (ISL v0.1 §4), defines one minimal service, one callable interface, at least one data entity, and at least one validation criterion, maps to the required canonical model, and can be used to generate the target system.

### 21.2 Pipeline Conformance

A scenario pipeline conforms if it consumes the input specification, produces a canonical model and construction task graph, executes the task graph through the runtime, generates required artifacts, admits them into repository state, performs deterministic build and test validation, supports bounded repair cycles, promotes valid artifacts to stable state, records traceability links, emits telemetry, and produces an evidence bundle and completion report.

### 21.3 Runtime Conformance

A scenario runtime conforms if it initializes execution state, tracks task state, invokes required agents or equivalent generators and required deterministic tools, records validation and repair outcomes, enforces repair limits, updates artifact state, records traceability closure, and produces completion state.

### 21.4 Artifact Conformance

A scenario artifact set conforms if it includes required source, test, config, metadata, and evidence artifacts; builds successfully; passes automated tests; has artifact metadata and validation evidence; has traceability to canonical entities; and is promoted to stable state only after validation and traceability closure.

### 21.5 Success Conformance

A scenario run conforms as successful only if all required success criteria pass, no blocking failure remains unresolved, no required validation is missing, no stable artifact is untraced, repair behavior is demonstrated or validated by failure injection, and the completion report references a complete evidence bundle.

### 21.6 Implementation Conformance

A reference implementation supports the informative scenario if it includes the required fixture and run configuration, includes the minimum required components, includes required tool capabilities and agent roles, preserves state across the run, records structured errors, supports failure-injection testing or equivalent repair proof, supports repeatable execution, and can be executed from the repository using documented commands.

---
