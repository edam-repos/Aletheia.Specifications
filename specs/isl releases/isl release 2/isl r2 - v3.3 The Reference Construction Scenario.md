# ALETHEIA Specification Language (ISL) v3.3

# The Reference Construction Scenario

**Status:** Informative
**Release:** r2
**Depends On:** ISL v0.0, ISL v1.0, ISL v1.1, ISL v1.2, ISL v1.3, ISL v1.4, ISL v1.5, ISL v1.6, ISL v1.7, ISL v2.0, ISL v2.1, ISL v2.2, ISL v2.3, ISL v2.4, ISL v2.5, ISL v2.6, ISL v2.7, ISL v3.0, ISL v3.1, ISL v3.2
**Supersedes:** ISL r0 v3.3 The Reference Construction Scenario
**Document Type:** Informative Minimal System Scenario and Construction Target

---

## 1.0 Scope

In r2 this document is retained as an informative scenario, regression fixture, and minimal construction target. Requirements language in this document constrains only the Reference Construction Scenario when an implementation chooses to claim Reference Construction Scenario conformance. It MUST NOT define enterprise platform conformance, override normative ISL r2 documents, or make Reference Construction Scenario the center of the ALETHEIA architecture.

This document defines the Reference Construction Scenario. It is the smallest practical ALETHEIA system implementation that can demonstrate autonomous software construction from a structured ISL specification to a validated, stable artifact repository.

This document applies to the first executable reference system built by the ALETHEIA platform. It defines the minimum system target, supported specification domain, required input specification, required generated artifacts, construction task graph, runtime behavior, validation profile, repair behavior, traceability requirements, telemetry requirements, and success criteria for Reference Construction Scenario.

This document defines:

* Reference Construction Scenario purpose and objectives
* minimal target system scope
* supported ISL subset
* required Reference Construction Scenario input specification
* canonical entity requirements
* task graph requirements
* generated artifact requirements
* runtime execution requirements
* deterministic validation requirements
* repair and convergence rules
* artifact repository requirements
* traceability requirements
* telemetry requirements
* success, failure, and escalation criteria
* conformance requirements

---

## 2.0 Purpose of Reference Construction Scenario

The purpose of Reference Construction Scenario is to prove that the ALETHEIA platform can execute the smallest meaningful autonomous construction cycle. Reference Construction Scenario is not intended to prove every future enterprise capability. It is intended to prove that the platform’s core loop works.

The core Reference Construction Scenario loop is:

1. read a structured ISL specification
2. normalize it into the canonical semantic model
3. generate a construction task graph
4. execute the task graph through the runtime
5. invoke reasoning agents for generation and repair
6. invoke deterministic tools for validation
7. repair generated artifacts when validation fails
8. promote valid artifacts to stable repository state
9. preserve traceability and evidence

Reference Construction Scenario exists to answer one question: can ALETHEIA construct a functioning software artifact from a formal specification without manually writing the final implementation?

---

## 3.0 Reference Construction Scenario Design Principles

### 3.1 Minimal but Real

Reference Construction Scenario MUST be intentionally small, but it MUST be real. It MUST generate a working software system that can be built and tested with deterministic tools.

Reference Construction Scenario MUST NOT be a static demonstration that copies prewritten final outputs and claims autonomous construction.

### 3.2 Specification-First

Reference Construction Scenario MUST begin from a versioned ISL specification. The specification is the authoritative source of system intent.

Generated artifacts MUST be traceable to entities in the Reference Construction Scenario input specification.

### 3.3 Deterministic Validation Is Required

Reference Construction Scenario MUST compile or build the generated system and MUST execute automated tests.

A Reference Construction Scenario run MUST NOT be considered successful if build or test validation fails.

### 3.4 Repair Is Required but Bounded

Reference Construction Scenario MUST demonstrate the ability to respond to validation failure through repair behavior.

Repair cycles MUST be bounded. Reference Construction Scenario MUST NOT permit unlimited repair attempts.

### 3.5 Evidence Is the Definition of Success

Reference Construction Scenario success MUST be demonstrated by evidence records: parsed specification, canonical model, construction plan, execution graph, generated artifacts, validation results, repair records if applicable, traceability snapshot, telemetry summary, and completion report.

---

## 4.0 Reference Construction Scenario Target System

The default Reference Construction Scenario target system is a minimal .NET REST service. The original Reference Construction Scenario document identifies the first reference system as a minimal .NET REST service with project structure, endpoint, data model, and tests. 

### 4.1 Default Target

The default Reference Construction Scenario target MUST be named:

**Reference Greeting Service**

The generated service MUST expose one HTTP operation that returns a greeting response.

### 4.2 Default Target Behavior

The service MUST satisfy the following behavior:

* accept a name value from the caller
* return a greeting message containing that name
* return a default greeting when no name is provided, if the generated platform chooses to support optional input
* expose at least one deterministic validation test proving expected behavior

### 4.3 Default Target Technology

The default implementation target SHOULD be:

| Field           | Value                               |
| --------------- | ----------------------------------- |
| target-platform | .NET                                |
| target-language | C#                                  |
| service-style   | minimal REST API                    |
| test-framework  | xUnit, NUnit, MSTest, or equivalent |
| build-tool      | dotnet CLI or equivalent            |

### 4.4 Equivalent Target Rule

An implementation MAY choose a different language or framework only if the target still provides:

* generated source code
* callable operation
* data model or response object
* automated test artifact
* deterministic build validation
* deterministic test validation
* artifact metadata
* traceability links

---

## 5.0 Supported ISL Subset for Reference Construction Scenario

Reference Construction Scenario supports a constrained ISL subset sufficient to describe a minimal service application.

### 5.1 Required Entity Types

The Reference Construction Scenario input specification MUST include the following canonical entity types.

| Entity Type | Minimum Count | Purpose                           |
| ----------- | ------------: | --------------------------------- |
| Project     |             1 | Identifies the constructed system |
| Context     |             1 | Defines scope and assumptions     |
| Requirement |             1 | Defines expected behavior         |
| Capability  |             1 | Groups functional behavior        |
| Service     |             1 | Defines the service to construct  |
| Interface   |             1 | Defines the callable operation    |
| DataEntity  |             1 | Defines request or response data  |
| Validation  |             1 | Defines acceptance checks         |

### 5.2 Optional Entity Types

The Reference Construction Scenario input specification MAY include:

* Actor
* Workflow
* Policy
* Infrastructure
* additional Requirement entities
* additional Interface entities
* additional DataEntity entities
* additional Validation entities

### 5.3 Unsupported Scope

Reference Construction Scenario is not required to support:

* multiple services
* asynchronous workflows
* persistence stores
* authentication
* authorization
* message brokers
* infrastructure provisioning
* deployment automation
* multi-tenant behavior
* advanced security policies
* domain-specific integration patterns

Unsupported content MUST be reported as unsupported, ignored with warning, or rejected according to the configured Reference Construction Scenario validation profile.

---

## 6.0 Required Reference Construction Scenario Input Specification

This section defines the required input fixture. This is the most important correction to the original v3.3 document: Reference Construction Scenario MUST include the actual ISL specification it is expected to construct.

### 6.1 Fixture Location

The reference implementation SHOULD store the Reference Construction Scenario input specification at:

`/examples/specifications/reference/reference-greeting-service.isl.md`

If a different path is used, the path MUST be declared in the Reference Construction Scenario run configuration.

### 6.2 Fixture Identifier Values

The default Reference Construction Scenario input fixture SHOULD use the following identifiers.

| Entity      | Identifier              |
| ----------- | ----------------------- |
| Project     | PRJ-reference-greeting-00001 |
| Context     | CTX-reference-greeting-00001 |
| Requirement | REQ-reference-greeting-00001 |
| Capability  | CAP-reference-greeting-00001 |
| Service     | SVC-reference-greeting-00001 |
| Interface   | INT-reference-greeting-00001 |
| DataEntity  | DAT-reference-greeting-00001 |
| Validation  | VAL-reference-greeting-00001 |

### 6.3 Required Fixture Metadata

The fixture MUST declare:

| Field                 | Required Value or Rule                    |
| --------------------- | ----------------------------------------- |
| specification-id      | reference-greeting                             |
| specification-version | 1.0.0                                     |
| isl-version           | supported ISL version                     |
| name                  | Reference Greeting Service                     |
| owner                 | reference construction implementation             |
| readiness-target      | Autonomous-Ready                          |
| risk-tier             | Standard                                  |
| target-platform       | .NET unless equivalent target is declared |
| target-language       | C# unless equivalent target is declared   |
| validation-profile    | build-and-test                            |

### 6.4 Required Fixture Content

The fixture MUST define:

* one project named Reference Greeting Service
* one context describing that the service is a minimal reference scenario target
* one requirement requiring a greeting response
* one capability representing greeting generation
* one service implementing the greeting capability
* one interface exposing a greeting operation
* one data entity representing the greeting response
* one validation requiring build and test success

---

## 7.0 Example Reference Construction Scenario Input Specification Content

The following content defines the minimum semantic content of the Reference Construction Scenario input specification fixture. Implementations MAY express this content in Markdown, YAML, JSON, or another supported ISL representation, but a Reference Construction Scenario-conforming normalized canonical model must preserve the same semantics.

### 7.1 Project Entity

| Field           | Value                                                                                                           |
| --------------- | --------------------------------------------------------------------------------------------------------------- |
| id              | PRJ-reference-greeting-00001                                                                                         |
| type            | Project                                                                                                         |
| name            | Reference Greeting Service                                                                                           |
| description     | Minimal service used to prove that ALETHEIA can construct a working software artifact from an ISL specification. |
| version         | 1.0.0                                                                                                           |
| readiness-level | Autonomous-Ready                                                                                                |
| risk-tier       | Standard                                                                                                        |

### 7.2 Context Entity

| Field       | Value                                                                                                                                                |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| id          | CTX-reference-greeting-00001                                                                                                                              |
| type        | Context                                                                                                                                              |
| name        | Minimal Construction Context                                                                                                                         |
| description | The system is intentionally limited to one HTTP-accessible greeting operation, one response data model, and deterministic build and test validation. |
| version     | 1.0.0                                                                                                                                                |

### 7.3 Requirement Entity

| Field               | Value                                                                                  |
| ------------------- | -------------------------------------------------------------------------------------- |
| id                  | REQ-reference-greeting-00001                                                                |
| type                | Requirement                                                                            |
| name                | Generate Greeting Response                                                             |
| description         | The service MUST return a greeting message that includes the caller-provided name.     |
| version             | 1.0.0                                                                                  |
| priority            | must-have                                                                              |
| acceptance-criteria | Calling the greeting operation with name Ada returns a response containing Hello, Ada. |

### 7.4 Capability Entity

| Field       | Value                                                                  |
| ----------- | ---------------------------------------------------------------------- |
| id          | CAP-reference-greeting-00001                                                |
| type        | Capability                                                             |
| name        | Greeting Generation                                                    |
| description | Provides the ability to generate a greeting message from caller input. |
| version     | 1.0.0                                                                  |
| satisfies   | REQ-reference-greeting-00001                                                |

### 7.5 Service Entity

| Field       | Value                                                                 |
| ----------- | --------------------------------------------------------------------- |
| id          | SVC-reference-greeting-00001                                               |
| type        | Service                                                               |
| name        | Greeting Service                                                      |
| description | Minimal REST service that exposes the greeting generation capability. |
| version     | 1.0.0                                                                 |
| provides    | CAP-reference-greeting-00001                                               |

### 7.6 Interface Entity

| Field          | Value                                                                    |
| -------------- | ------------------------------------------------------------------------ |
| id             | INT-reference-greeting-00001                                                  |
| type           | Interface                                                                |
| name           | Get Greeting                                                             |
| description    | HTTP GET operation that returns a greeting response for a supplied name. |
| version        | 1.0.0                                                                    |
| method         | GET                                                                      |
| path           | /greeting/{name}                                                         |
| service        | SVC-reference-greeting-00001                                                  |
| response-entity | DAT-reference-greeting-00001                                                 |

### 7.7 DataEntity Entity

| Field       | Value                                                      |
| ----------- | ---------------------------------------------------------- |
| id          | DAT-reference-greeting-00001                                    |
| type        | DataEntity                                                 |
| name        | Greeting Response                                          |
| description | Response object containing the generated greeting message. |
| version     | 1.0.0                                                      |
| attributes  | message:string:required                                    |

### 7.8 Validation Entity

| Field                      | Value                                                                                                                                 |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| id                         | VAL-reference-greeting-00001                                                                                                               |
| type                       | Validation                                                                                                                            |
| name                       | Greeting Service Build and Behavior Validation                                                                                        |
| description                | Generated service MUST build successfully and automated tests MUST verify that /greeting/Ada returns a message containing Hello, Ada. |
| version                    | 1.0.0                                                                                                                                 |
| validates                  | REQ-reference-greeting-00001, INT-reference-greeting-00001                                                                                      |
| validation-type            | build-and-test                                                                                                                        |
| required-tool-capabilities | compile, unit-test                                                                                                                    |

---

## 8.0 Reference Construction Scenario Canonical Model Requirements

The Reference Construction Scenario Semantic Model Engine MUST normalize the input fixture into canonical entities and relationships.

### 8.1 Required Canonical Relationships

The canonical model MUST include these relationships.

| Source                  | Relationship         | Target                  |
| ----------------------- | -------------------- | ----------------------- |
| PRJ-reference-greeting-00001 | contains             | CTX-reference-greeting-00001 |
| PRJ-reference-greeting-00001 | contains             | REQ-reference-greeting-00001 |
| CAP-reference-greeting-00001 | groups               | REQ-reference-greeting-00001 |
| SVC-reference-greeting-00001 | implements           | CAP-reference-greeting-00001 |
| SVC-reference-greeting-00001 | exposes              | INT-reference-greeting-00001 |
| INT-reference-greeting-00001 | produces             | DAT-reference-greeting-00001 |
| VAL-reference-greeting-00001 | validates            | REQ-reference-greeting-00001 |
| VAL-reference-greeting-00001 | validates            | INT-reference-greeting-00001 |

### 8.2 Canonical Validation Rules

The Reference Construction Scenario canonical model MUST pass:

* required entity presence validation
* identifier format validation
* relationship target resolution
* required field validation
* interface-to-service relationship validation
* validation-to-requirement relationship validation
* validation tool capability resolution

A canonical validation failure MUST stop Reference Construction Scenario before planning.

---

## 9.0 Reference Construction Scenario Construction Task Graph

The Planning Engine MUST produce a construction task graph from the canonical Reference Construction Scenario model.

### 9.1 Required Task Types

The Reference Construction Scenario task graph MUST include:

| Task ID Pattern             | Task Type                    | Purpose                                    |
| --------------------------- | ---------------------------- | ------------------------------------------ |
| TSK-reference-greeting-interpret | specification-interpretation | Interpret canonical model for execution    |
| TSK-reference-greeting-plan      | construction-planning        | Confirm artifact plan and dependency order |
| TSK-reference-greeting-project   | implementation               | Generate project structure                 |
| TSK-reference-greeting-service   | implementation               | Generate service endpoint                  |
| TSK-reference-greeting-data      | schema                       | Generate response data model               |
| TSK-reference-greeting-tests     | test                         | Generate automated tests                   |
| TSK-reference-greeting-build     | verification                 | Build or compile generated project         |
| TSK-reference-greeting-test-run  | verification                 | Run automated tests                        |
| TSK-reference-greeting-repair    | repair                       | Repair failed generated artifacts          |
| TSK-reference-greeting-finalize  | artifact-finalization        | Promote valid artifacts                    |
| TSK-reference-greeting-trace     | traceability-closure         | Create final traceability snapshot         |
| TSK-reference-greeting-complete  | completion-report            | Produce completion report                  |

### 9.2 Required Task Dependencies

| Task                       | Depends On                                                    |
| -------------------------- | ------------------------------------------------------------- |
| TSK-reference-greeting-plan     | TSK-reference-greeting-interpret                                   |
| TSK-reference-greeting-project  | TSK-reference-greeting-plan                                        |
| TSK-reference-greeting-service  | TSK-reference-greeting-project                                     |
| TSK-reference-greeting-data     | TSK-reference-greeting-project                                     |
| TSK-reference-greeting-tests    | TSK-reference-greeting-service, TSK-reference-greeting-data             |
| TSK-reference-greeting-build    | TSK-reference-greeting-service, TSK-reference-greeting-data             |
| TSK-reference-greeting-test-run | TSK-reference-greeting-build, TSK-reference-greeting-tests              |
| TSK-reference-greeting-repair   | TSK-reference-greeting-build or TSK-reference-greeting-test-run failure |
| TSK-reference-greeting-finalize | TSK-reference-greeting-build, TSK-reference-greeting-test-run           |
| TSK-reference-greeting-trace    | TSK-reference-greeting-finalize                                    |
| TSK-reference-greeting-complete | TSK-reference-greeting-trace                                       |

### 9.3 Task Graph Rules

Every Reference Construction Scenario task MUST trace to a canonical entity or pipeline obligation.

Every artifact-producing task MUST declare expected artifact types.

Every validation task MUST declare required tool capabilities.

The repair task MUST be present even if no repair is needed during a successful run.

The task graph MUST be validated before runtime execution.

---

## 10.0 Required Generated Artifacts

Reference Construction Scenario MUST generate a minimal but complete implementation artifact set.

### 10.1 Required Artifacts for Default .NET Target

| Artifact                     | Artifact Type    | Required |
| ---------------------------- | ---------------- | -------- |
| Service project file         | config           | YES      |
| Program entry point          | source           | YES      |
| Greeting endpoint or handler | source           | YES      |
| Greeting response model      | source or schema | YES      |
| Test project file            | config           | YES      |
| Greeting behavior test       | test             | YES      |
| Artifact metadata records    | metadata         | YES      |
| Validation evidence records  | evidence         | YES      |

### 10.2 Artifact Naming Convention

The default .NET Reference Construction Scenario implementation SHOULD use:

| Artifact                | Suggested Path                                            |
| ----------------------- | --------------------------------------------------------- |
| Service project file    | `/services/reference-greeting/source/ReferenceGreeting.Api.csproj`  |
| Program entry point     | `/services/reference-greeting/source/Program.cs`               |
| Greeting response model | `/services/reference-greeting/source/GreetingResponse.cs`      |
| Test project file       | `/services/reference-greeting/tests/ReferenceGreeting.Tests.csproj` |
| Greeting test           | `/services/reference-greeting/tests/GreetingEndpointTests.cs`  |
| Metadata                | `/metadata/artifacts/reference-greeting-artifacts.json`        |
| Evidence                | `/evidence/reference-greeting/`                                |

### 10.3 Artifact Rules

Artifacts MUST be admitted to the Artifact Repository before validation.

Artifacts MUST have artifact identifiers.

Artifacts MUST have metadata records.

Artifacts MUST reference source canonical entities.

Artifacts MUST NOT be marked stable before validation passes.

---

## 11.0 Reference Construction Scenario Runtime Requirements

The Reference Construction Scenario runtime MAY be local and simplified, but it MUST execute real lifecycle behavior.

### 11.1 Required Runtime Capabilities

Reference Construction Scenario MUST support:

* execution admission
* execution graph initialization
* task state tracking
* work item execution
* agent invocation or local generation equivalent
* tool invocation
* artifact state updates
* validation result recording
* bounded repair handling
* traceability updates
* completion report generation

### 11.2 Runtime Simplification Rules

Reference Construction Scenario MAY run all runtime components in-process.

Reference Construction Scenario MAY use an in-memory queue if state is persisted at stage boundaries.

Reference Construction Scenario MAY use a simple sequential scheduler.

Reference Construction Scenario MUST NOT skip task state tracking.

Reference Construction Scenario MUST NOT skip deterministic validation.

Reference Construction Scenario MUST NOT skip artifact repository admission.

Reference Construction Scenario MUST NOT skip traceability closure.

---

## 12.0 Reference Construction Scenario Agent Requirements

Reference Construction Scenario MUST use bounded agent roles or implementation-equivalent generators.

### 12.1 Required Agent Roles

| Agent Role                | Required | Purpose                                                         |
| ------------------------- | -------- | --------------------------------------------------------------- |
| Specification Interpreter | YES      | Convert canonical model into execution-oriented interpretation  |
| Implementation Generator  | YES      | Generate project, endpoint, model, and implementation artifacts |
| Test Generator            | YES      | Generate behavior validation tests                              |
| Repair Analyst            | YES      | Analyze build or test failures                                  |
| Review Agent              | SHOULD   | Review generated artifacts or agent output                      |
| Construction Planner      | SHOULD   | Assist with task graph validation                               |

### 12.2 Agent Output Rules

Agent outputs MUST be structured.

Agent outputs MUST reference task identifiers.

Agent outputs MUST reference canonical entity identifiers.

Agent outputs that produce artifacts MUST declare produced artifact candidates.

Agent outputs MUST be validated before artifact admission.

---

## 13.0 Reference Construction Scenario Model Integration Requirements

Reference Construction Scenario MAY use a local model, external governed model, deterministic template generator, or hybrid generation mode. Regardless of generation strategy, the model/generation boundary MUST be explicit.

### 13.1 Generation Modes

| Mode                   | Description                                                                 |
| ---------------------- | --------------------------------------------------------------------------- |
| model-generated        | Artifacts are generated primarily through model invocation                  |
| template-assisted      | Templates provide scaffolding and agents fill specification-derived content |
| deterministic-template | Artifacts are generated deterministically from canonical entities           |
| hybrid                 | Combination of templates, model output, and deterministic generation        |

### 13.2 Generation Mode Rules

The selected generation mode MUST be recorded in the Reference Construction Scenario run configuration.

If model-generated or hybrid mode is used, model invocations MUST be recorded.

If deterministic-template mode is used, template versions MUST be recorded.

Template-generated content MUST still be traceable to canonical entities or platform generation rules.

The implementation MUST NOT hide prewritten final output as if it were model-generated output.

---

## 14.0 Reference Construction Scenario Tool Profile

Reference Construction Scenario MUST use deterministic tools to validate generated artifacts.

### 14.1 Required Tool Capabilities

| Capability                 | Required | Purpose                                  |
| -------------------------- | -------- | ---------------------------------------- |
| dependency-resolve         | SHOULD   | Restore project dependencies             |
| compile                    | YES      | Build generated source                   |
| unit-test                  | YES      | Execute generated tests                  |
| format-check               | OPTIONAL | Check formatting                         |
| repository-integrity-check | SHOULD   | Verify artifact and metadata consistency |

### 14.2 Default .NET Tool Commands

For the default .NET target, the tool profile SHOULD support equivalent commands for:

| Capability                 | Example Tool Behavior                    |
| -------------------------- | ---------------------------------------- |
| dependency-resolve         | restore project dependencies             |
| compile                    | build generated project                  |
| unit-test                  | run generated tests                      |
| repository-integrity-check | verify metadata and artifact consistency |

### 14.3 Tool Rules

Tools MUST be registered before use.

Tool invocation results MUST be normalized.

Tool failures MUST be recorded.

A compile failure MUST prevent success.

A test failure MUST prevent success.

A tool timeout MUST NOT be treated as success.

---

## 15.0 Reference Construction Scenario Deterministic Validation

Validation determines whether Reference Construction Scenario has produced a working system.

### 15.1 Required Validation Results

Reference Construction Scenario MUST produce:

| Validation Result             | Required |
| ----------------------------- | -------- |
| dependency restore result     | SHOULD   |
| build or compile result       | YES      |
| automated test result         | YES      |
| repository integrity result   | SHOULD   |
| traceability integrity result | YES      |

### 15.2 Validation Pass Criteria

Reference Construction Scenario validation passes only when:

* generated project builds successfully
* generated automated tests pass
* required artifact metadata exists
* required traceability links exist
* no blocking repository integrity finding exists
* no blocking governance finding exists

### 15.3 Validation Failure Rules

A build failure MUST trigger repair or terminal failure.

A test failure MUST trigger repair or terminal failure.

A traceability integrity failure MUST trigger repair, traceability correction, escalation, or terminal failure.

A repository integrity failure MUST trigger repair, repository correction, escalation, or terminal failure.

---

## 16.0 Reference Construction Scenario Repair and Convergence

Reference Construction Scenario MUST demonstrate repair behavior either through an actual validation failure or a failure-injection scenario.

### 16.1 Required Repair Behavior

When validation fails, Reference Construction Scenario MUST:

1. record the failed validation result
2. classify the failure
3. invoke or simulate Repair Analyst behavior
4. produce a repair proposal
5. modify or regenerate affected artifacts
6. create new artifact version or repair metadata
7. rerun deterministic validation
8. record repair outcome

### 16.2 Repair Limits

Reference Construction Scenario MUST enforce:

| Parameter                                    |                                   Value |
| -------------------------------------------- | --------------------------------------: |
| Maximum repair iterations per artifact       |                                       5 |
| Maximum total repair iterations per Reference Construction Scenario run |                                      50 |
| Escalation threshold                         | 3 consecutive failures on same artifact |

### 16.3 Repair Success Criteria

A repair is successful only when the repaired artifact passes deterministic validation or receives a valid waiver.

A repair MUST NOT be considered successful based only on agent assertion.

### 16.4 Repair Failure Criteria

Reference Construction Scenario MUST escalate or fail when:

* repair iteration limit is reached
* same artifact fails three consecutive repair attempts
* repair proposal cannot be produced
* repaired artifact cannot be validated
* repair creates a governance or traceability violation

---

## 17.0 Reference Construction Scenario Artifact Repository Requirements

Reference Construction Scenario MUST maintain repository-controlled artifact state.

### 17.1 Repository Workspace

Reference Construction Scenario SHOULD use a local artifact workspace for the first implementation.

The workspace MUST distinguish:

* candidate artifacts
* pending artifacts
* failed artifacts
* repaired artifacts
* valid artifacts
* stable artifacts
* evidence records
* metadata records

### 17.2 Artifact Metadata Requirements

Each Reference Construction Scenario artifact metadata record MUST include:

| Field                 | Required |
| --------------------- | -------- |
| artifact-id           | YES      |
| artifact-name         | YES      |
| artifact-type         | YES      |
| artifact-version      | YES      |
| specification-id      | YES      |
| specification-version | YES      |
| task-id               | YES      |
| source-entity-ids     | YES      |
| repository-location   | YES      |
| current-state         | YES      |
| validation-status     | YES      |
| traceability-status   | YES      |

### 17.3 Repository Rules

Generated artifacts MUST enter pending state before validation.

Artifacts that fail validation MUST enter failed or repaired state.

Artifacts that pass validation and traceability closure MAY enter stable state.

Stable artifacts MUST NOT be overwritten by repair; repair MUST create a new version or supersession record.

---

## 18.0 Reference Construction Scenario Traceability Requirements

Reference Construction Scenario MUST prove that generated artifacts are derived from specification intent.

### 18.1 Required Traceability Links

Reference Construction Scenario MUST record links between:

| Source                   | Target                       |
| ------------------------ | ---------------------------- |
| Project entity           | Construction plan            |
| Requirement entity       | Implementation task          |
| Capability entity        | Service artifact             |
| Service entity           | Program or endpoint artifact |
| Interface entity         | Endpoint artifact            |
| DataEntity entity        | Response model artifact      |
| Validation entity        | Test artifact                |
| Test artifact            | Validation result            |
| Failed validation result | Repair record                |
| Repaired artifact        | Superseded artifact          |
| Stable artifact          | Source canonical entities    |

### 18.2 Traceability Snapshot

Reference Construction Scenario MUST produce a final traceability snapshot.

The snapshot MUST show:

* all required entities
* all required tasks
* all stable artifacts
* all validation results
* repair records if any
* completion report relationship

### 18.3 Traceability Failure Rules

Reference Construction Scenario MUST NOT succeed if stable artifacts are untraced.

Reference Construction Scenario MUST NOT succeed if validation results are not linked to the artifacts they evaluated.

Reference Construction Scenario MUST NOT succeed if the final traceability snapshot cannot be produced.

---

## 19.0 Reference Construction Scenario Telemetry Requirements

Reference Construction Scenario MUST emit enough telemetry to understand the construction run.

### 19.1 Required Telemetry Events

Reference Construction Scenario SHOULD emit:

* reference-run-started
* reference-stage-started
* reference-stage-completed
* reference-stage-failed
* specification-parsed
* canonical-model-generated
* construction-plan-generated
* execution-graph-initialized
* task-started
* task-completed
* agent-invoked
* model-invoked
* artifact-admitted
* tool-invoked
* validation-passed
* validation-failed
* repair-started
* repair-completed
* artifact-promoted
* traceability-snapshot-created
* reference-run-completed

### 19.2 Required Telemetry Attributes

Telemetry SHOULD include:

| Attribute             | Required When Applicable |
| --------------------- | ------------------------ |
| reference-run-id           | YES                      |
| specification-id      | YES                      |
| specification-version | YES                      |
| task-id               | YES                      |
| artifact-id           | YES                      |
| validation-result-id  | YES                      |
| repair-record-id      | CONDITIONAL              |
| model-invocation-id   | CONDITIONAL              |
| tool-invocation-id    | CONDITIONAL              |
| outcome               | YES                      |

### 19.3 Telemetry Rules

Telemetry MUST NOT replace evidence records.

Telemetry MUST preserve correlation identifiers.

Telemetry MUST be included in the Reference Construction Scenario evidence bundle as summary or reference.

---

## 20.0 Reference Construction Scenario Governance Requirements

Reference Construction Scenario MAY use a simplified governance profile, but governance MUST be represented explicitly.

### 20.1 Minimum Governance Controls

Reference Construction Scenario MUST record:

* risk tier assignment
* readiness or execution admission decision
* governance profile used
* policy violations if any
* waivers if any
* escalations if any
* completion authorization or local equivalent

### 20.2 Default Governance Profile

The default Reference Construction Scenario governance profile SHOULD be:

| Field                    | Value                                        |
| ------------------------ | -------------------------------------------- |
| risk-tier                | Standard                                     |
| model-use                | permitted                                    |
| tool-use                 | permitted                                    |
| waiver-use               | permitted only with explicit record          |
| deployment-authorization | not-required                                 |
| human approval           | not-required for local reference scenario unless configured |

### 20.3 Governance Rules

Reference Construction Scenario MUST NOT silently waive validation failure.

Reference Construction Scenario MUST NOT continue after governance block.

Reference Construction Scenario MUST record escalation when repair thresholds are reached.

---

## 21.0 Reference Construction Scenario State and Recovery Requirements

Reference Construction Scenario SHOULD support local recovery behavior sufficient for reference scenario reliability.

### 21.1 Required State Records

Reference Construction Scenario MUST record:

* Reference Construction Scenario run state
* specification state
* canonical model state
* planning state
* execution state
* task state
* artifact state
* validation state
* repair state where applicable
* traceability state
* completion state

### 21.2 Checkpoints

Reference Construction Scenario SHOULD create checkpoints at:

* after specification intake
* after canonical normalization
* after construction planning
* before deterministic validation
* after deterministic validation
* before artifact finalization
* at completion

### 21.3 Recovery Rules

If Reference Construction Scenario supports recovery, it MUST validate artifact, state, repository, governance, and traceability consistency before resuming.

If Reference Construction Scenario does not support recovery in the first implementation, it MUST declare recovery as unsupported and MUST not claim recovery conformance.

---

## 22.0 Reference Construction Scenario Run Configuration

Each Reference Construction Scenario run MUST use a run configuration.

| Field                              | Type     | Required | Description                                                        |
| ---------------------------------- | -------- | -------- | ------------------------------------------------------------------ |
| reference-run-configuration-id          | string   | REQUIRED | Unique run configuration identifier                                |
| reference-run-id                        | string   | REQUIRED | Unique run identifier                                              |
| input-specification-location       | string   | REQUIRED | Path to Reference Construction Scenario input specification                                   |
| target-platform                    | string   | REQUIRED | Target platform                                                    |
| target-language                    | string   | REQUIRED | Target language                                                    |
| generation-mode                    | enum     | REQUIRED | model-generated, template-assisted, deterministic-template, hybrid |
| validation-profile-id              | string   | REQUIRED | Validation profile                                                 |
| governance-profile-id              | string   | REQUIRED | Governance profile                                                 |
| telemetry-profile-id               | string   | REQUIRED | Telemetry profile                                                  |
| artifact-workspace                 | string   | REQUIRED | Artifact repository workspace                                      |
| repair-enabled                     | boolean  | REQUIRED | Whether repair cycles are enabled                                  |
| max-repair-iterations-per-artifact | integer  | REQUIRED | Repair iteration limit                                             |
| max-total-repair-iterations        | integer  | REQUIRED | Total repair limit                                                 |
| failure-injection-enabled          | boolean  | REQUIRED | Whether failure injection is enabled                               |
| created-at                         | ISO 8601 | REQUIRED | Configuration creation time                                        |

---

## 23.0 Reference Construction Scenario Run Record

Each Reference Construction Scenario execution MUST produce a Reference Construction Scenario Run Record.

| Field                       | Type     | Required    | Description                                               |
| --------------------------- | -------- | ----------- | --------------------------------------------------------- |
| reference-run-id                 | string   | REQUIRED    | Unique Reference Construction Scenario run identifier                                |
| input-specification-id      | string   | REQUIRED    | Input specification identifier                            |
| input-specification-version | semver   | REQUIRED    | Input specification version                               |
| target-system               | string   | REQUIRED    | Reference Greeting Service or equivalent                       |
| target-platform             | string   | REQUIRED    | Target platform                                           |
| generation-mode             | enum     | REQUIRED    | Generation mode used                                      |
| runtime-configuration-id    | string   | REQUIRED    | Runtime configuration used                                |
| status                      | enum     | REQUIRED    | pending, running, succeeded, failed, escalated, cancelled |
| started-at                  | ISO 8601 | REQUIRED    | Run start time                                            |
| completed-at                | ISO 8601 | CONDITIONAL | Run completion time                                       |
| evidence-bundle-id          | string   | CONDITIONAL | Evidence bundle produced                                  |

---

## 24.0 Reference Construction Scenario Evidence Bundle

Reference Construction Scenario MUST produce an evidence bundle for every successful run and SHOULD produce a partial evidence bundle for failed runs.

### 24.1 Evidence Bundle Contents

| Evidence                                                | Required for Success |
| ------------------------------------------------------- | -------------------- |
| input specification                                     | YES                  |
| parsed specification record                             | YES                  |
| canonical model record                                  | YES                  |
| canonical validation report                             | YES                  |
| readiness and governance admission record               | YES                  |
| construction task graph                                 | YES                  |
| execution graph                                         | YES                  |
| agent invocation records                                | YES                  |
| model invocation records or template generation records | CONDITIONAL          |
| artifact metadata records                               | YES                  |
| artifact content references                             | YES                  |
| tool invocation records                                 | YES                  |
| validation result records                               | YES                  |
| repair records                                          | CONDITIONAL          |
| final traceability snapshot                             | YES                  |
| telemetry summary                                       | YES                  |
| completion report                                       | YES                  |

### 24.2 Evidence Bundle Rules

A successful Reference Construction Scenario run MUST have a complete evidence bundle.

A failed Reference Construction Scenario run SHOULD preserve all evidence produced before failure.

Evidence records MUST reference the Reference Construction Scenario run identifier.

Evidence records MUST be stored in the artifact repository or evidence store.

---

## 25.0 Reference Construction Scenario Completion Report

Reference Construction Scenario MUST produce a completion report.

| Field                     | Type     | Required    | Description                             |
| ------------------------- | -------- | ----------- | --------------------------------------- |
| reference-completion-report-id | string   | REQUIRED    | Unique completion report identifier     |
| reference-run-id               | string   | REQUIRED    | Reference Construction Scenario run identifier                     |
| final-outcome             | enum     | REQUIRED    | succeeded, failed, escalated, cancelled |
| specification-id          | string   | REQUIRED    | Input specification identifier          |
| specification-version     | semver   | REQUIRED    | Input specification version             |
| target-system             | string   | REQUIRED    | Target system generated                 |
| artifacts-produced        | array    | REQUIRED    | All artifacts produced                  |
| stable-artifacts          | array    | CONDITIONAL | Artifacts promoted to stable            |
| validations-executed      | array    | REQUIRED    | Validation result identifiers           |
| repair-count              | integer  | REQUIRED    | Number of repair attempts               |
| waivers-used              | array    | CONDITIONAL | Waivers used                            |
| traceability-snapshot-id  | string   | REQUIRED    | Final traceability snapshot             |
| evidence-bundle-id        | string   | REQUIRED    | Evidence bundle                         |
| limitations               | array    | CONDITIONAL | Known limitations of the run            |
| completed-at              | ISO 8601 | REQUIRED    | Completion timestamp                    |

---

## 26.0 Reference Construction Scenario Success Criteria

Reference Construction Scenario succeeds only when the platform demonstrates a full autonomous construction cycle.

### 26.1 Required Success Criteria

| Criterion                          | Required Evidence                                           |
| ---------------------------------- | ----------------------------------------------------------- |
| input specification parsed         | parsed specification record                                 |
| canonical model generated          | canonical model record                                      |
| canonical validation passed        | canonical validation report                                 |
| construction task graph generated  | construction plan record                                    |
| runtime execution initialized      | execution graph                                             |
| required artifacts generated       | artifact metadata and content references                    |
| artifacts admitted to repository   | artifact admission records                                  |
| build or compile validation passed | validation result                                           |
| automated test validation passed   | validation result                                           |
| repair path demonstrated           | actual repair record or failure-injection scenario evidence |
| artifacts promoted to stable       | artifact promotion records                                  |
| traceability closure passed        | final traceability snapshot                                 |
| telemetry emitted                  | telemetry summary                                           |
| completion report produced         | completion report                                           |

### 26.2 Success Rules

Reference Construction Scenario MUST NOT succeed if build validation fails.

Reference Construction Scenario MUST NOT succeed if automated tests fail.

Reference Construction Scenario MUST NOT succeed if required artifacts are missing.

Reference Construction Scenario MUST NOT succeed if stable artifacts lack traceability.

Reference Construction Scenario MUST NOT succeed if the evidence bundle is incomplete.

---

## 27.0 Reference Construction Scenario Failure and Escalation Criteria

### 27.1 Failure Criteria

Reference Construction Scenario MUST fail when:

* input specification cannot be parsed
* canonical model cannot be generated
* required canonical entities are missing
* construction task graph cannot be generated
* execution runtime cannot initialize
* required generation mode is unavailable
* required tools are unavailable
* artifact generation fails
* repository admission fails
* build validation fails after repair exhaustion
* automated tests fail after repair exhaustion
* traceability closure fails
* evidence bundle cannot be produced

### 27.2 Escalation Criteria

Reference Construction Scenario MUST escalate when:

* repair reaches iteration limit
* same artifact fails three consecutive repairs
* model output repeatedly violates output contract
* tool result cannot be normalized
* artifact conflict cannot be resolved
* governance blocks execution pending decision
* state consistency cannot be verified

### 27.3 Failure Rules

Every failure MUST produce a structured error record.

A terminal failure MUST produce a completion report with final-outcome failed when possible.

A terminal escalation MUST produce a completion report with final-outcome escalated when possible.

---

## 28.0 Reference Construction Scenario Non-Goals

Reference Construction Scenario is not required to support:

* production deployment
* container image generation
* cloud infrastructure provisioning
* database persistence
* authentication or authorization
* multi-service architecture
* advanced domain modeling
* distributed workers
* multi-tenant execution
* high availability
* disaster recovery
* complex governance workflows
* full enterprise security scanning
* multiple target languages

These are future platform capabilities. Reference Construction Scenario MUST remain focused on proving the first construction loop.

---

## 29.0 Reference Construction Scenario Evolution Path

After Reference Construction Scenario succeeds, the platform MAY evolve in controlled increments.

### 29.1 Recommended Evolution Sequence

| Increment | New Capability                       |
| --------- | ------------------------------------ |
| REF-1    | Minimal REST service generation      |
| REF-2    | Multiple endpoints                   |
| REF-3    | Multiple data entities               |
| REF-4    | Input validation and error responses |
| REF-5    | Basic persistence                    |
| REF-6    | Security policy validation           |
| REF-7    | Deployment package preparation       |
| REF-8    | Multi-service construction           |
| REF-9    | Enterprise governance profile        |
| REF-10   | Distributed runtime execution        |

### 29.2 Evolution Rules

Each Reference Construction Scenario evolution increment MUST define:

* supported ISL subset expansion
* new generated artifact types
* new validation requirements
* new traceability requirements
* new governance implications
* new success criteria
* regression tests against prior Reference Construction Scenario capability

Reference Construction Scenario evolution MUST NOT remove the ability to run the original Reference Greeting Service scenario unless formally deprecated.

---

## 30.0 Reference Construction Scenario Conformance Requirements

### 30.1 Reference Construction Scenario Input Conformance

A Reference Construction Scenario input specification conforms to the informative Reference Construction Scenario if it:

* declares required metadata
* includes all required entity types
* uses valid identifiers
* defines one minimal service
* defines one callable interface
* defines at least one data entity
* defines at least one validation criterion
* maps to the required canonical model
* can be used to generate the Reference Construction Scenario target system

### 30.2 Reference Construction Scenario Pipeline Conformance

A Reference Construction Scenario pipeline conforms to the informative Reference Construction Scenario if it:

* consumes the Reference Construction Scenario input specification
* produces a canonical model
* produces a construction task graph
* executes the task graph through the runtime
* generates required artifacts
* admits artifacts into repository state
* performs deterministic build and test validation
* supports bounded repair cycles
* promotes valid artifacts to stable state
* records traceability links
* emits telemetry
* produces evidence bundle and completion report

### 30.3 Reference Construction Scenario Runtime Conformance

A Reference Construction Scenario runtime conforms to the informative Reference Construction Scenario if it:

* initializes execution state
* tracks task state
* invokes required agents or equivalent generators
* invokes required deterministic tools
* records validation and repair outcomes
* enforces repair limits
* updates artifact state
* records traceability closure
* produces completion state

### 30.4 Reference Construction Scenario Artifact Conformance

A Reference Construction Scenario artifact set conforms to the informative Reference Construction Scenario if it:

* includes required source, test, config, metadata, and evidence artifacts
* builds successfully
* passes automated tests
* has artifact metadata
* has validation evidence
* has traceability to canonical entities
* is promoted to stable state only after validation and traceability closure

### 30.5 Reference Construction Scenario Success Conformance

A Reference Construction Scenario run conforms as successful only if:

* all required success criteria pass
* no blocking failure remains unresolved
* no required validation is missing
* no stable artifact is untraced
* repair behavior is demonstrated or validated by failure injection
* completion report references a complete evidence bundle

---

## 31.0 Summary

ISL v3.3 retains the Reference Construction Scenario as an informative scenario. Reference Construction Scenario is a useful first named system for proving an implementation path, but r2 enterprise conformance is defined by the normative ISL corpus rather than by Reference Construction Scenario.

This revision strengthens the original Reference Construction Scenario document by defining the Reference Construction Scenario target system, supported ISL subset, required input fixture, example semantic content, canonical model requirements, task graph, generated artifacts, runtime requirements, agent requirements, model generation modes, deterministic validation, bounded repair, artifact repository behavior, traceability, telemetry, governance, state, evidence, success criteria, failure criteria, evolution path, and scenario conformance requirements.

A Reference Construction Scenario-conforming implementation demonstrates the first autonomous construction loop: specification to canonical model, canonical model to task graph, task graph to generated artifacts, generated artifacts to deterministic validation, validation failure to bounded repair, valid artifacts to stable repository state, and stable artifacts to traceable evidence.
