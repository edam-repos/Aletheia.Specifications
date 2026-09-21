# SAGE-X to ISL Implementation Plan

# Local-First Schema-Guided Plan for Moving from Specifications to Implementation

**Status:** Draft Implementation Plan  
**Date:** 2026-06-18  
**Planning Basis:** SAGE-X v1r0, ISL r2, ISL-Sage-X Integration Profile, SAGE-X Platform Schema  
**Primary Schema:** `sage-x releases/sagex v1r0/sagex.00.v1r0.platform-schema.json`  
**Companion Execution Plan:** `implementation planning/sagex-to-isl.implementation.execution-plan.json`  
**Companion Task Graph:** `implementation planning/sagex-to-isl.implementation-task-graph.json`

---

# 1.0 Purpose

This plan defines the first controlled movement from specification assets into implementation assets.

The implementation must not begin as blind coding. It must begin as schema-guided construction: every module, service, adapter, test, and evidence record should map to an artifact type already defined in the SAGE-X Platform Schema.

This revision keeps the original macro phases but decomposes them into local-first work packets. The macro phases explain architectural intent. The work packets are the real implementation units.

The immediate objective is to produce a minimal but real implementation foundation that can:

* load the consolidated platform schema
* validate platform artifacts
* register capabilities
* register specification plugins
* ingest a small ISL fixture
* compile the fixture into Knowledge Units
* generate an execution plan
* execute a small governed step sequence
* record state, trace events, checkpoints, validation results, failures, and evidence
* expose a minimal control surface
* preserve enough structure to support future ISL/ALETHEIA reuse

---

# 2.0 Guiding Principles

## 2.1 Schema as Implementation Spine

The schema is the implementation spine.

The first implementation should create code only where a schema-backed artifact or operation requires it. If a required behavior does not map to a platform schema artifact, the team must either:

* add a schema definition before implementation
* classify it as internal implementation detail
* defer it until a later phase

## 2.2 Local-First Granularity

Implementation work must be decomposed into limited-context work packets.

A work packet is the smallest useful construction unit. It should:

* have one purpose
* produce one primary artifact, module, fixture, or test group
* depend on explicit inputs
* define explicit expected outputs
* include one validation method
* be resumable after failure
* be traceable to one macro phase and one schema artifact group

This rule applies even if strong compute, context, or team resources are available. Smaller work packets reduce semantic drift, improve validation, and keep generated output closer to specification intent.

## 2.3 Macro Plan Versus Execution Task Graph

The implementation has two layers:

| Layer | Purpose | Granularity |
| ----- | ------- | ----------- |
| Macro phase plan | Explains architectural sequence and intent | Broad phase-level goals |
| Local task graph | Drives actual implementation | Small executable work packets |

The companion execution-plan JSON represents the Sage-X runnable view. The companion task-graph JSON represents the local-first work-packet view.

---

# 3.0 Primary Implementation Artifacts

The following schema definitions are treated as first-class implementation contracts.

| Area | Schema Artifacts | Implementation Role |
| ---- | ---------------- | ------------------- |
| Specification processing | `specificationIngestionRequest`, `specificationModel`, `specificationPluginRegistration`, `pluginValidationResult` | Plugin registry, ISL ingestion, parsing, validation |
| Knowledge layer | `knowledgeUnit` | Compiled specification knowledge store |
| Planning | `executionPlan`, `executionStep`, `dependency`, `planCheckpoint`, `executionModes`, `executionMode`, `planSegment`, `autonomyPolicy` | Plan generation, scoped plan sections, execution-mode control, and validation |
| Runtime control | `executionState`, `stateTransition`, `checkpoint` | Runtime state manager and checkpointing |
| Execution | `controlRequest`, `controlResponse`, `capabilityInvocationRequest`, `capabilityInvocationResponse` | Control surface and capability invocation |
| Capabilities | `capabilityRegistration`, `capabilityInterface`, `capabilityDiscoveryQuery`, `capabilityDiscoveryResponse`, `capabilityError` | Capability registry and adapter boundary |
| Validation | `validationRules`, `validationResult`, `validationViolation` | Validation engine and result recording |
| Failure and recovery | `failureRecord`, `checkpoint`, `traceEvent` | Failure detection, rollback eligibility, recovery trace |
| Governance | `governancePolicy`, `approvalRecord`, `overrideRecord`, `governanceDecision`, `governance` | Policy checks, approvals, overrides |
| Observability | `traceEvent`, `traceEventType`, `logEntry`, `explanationRecord` | Trace, logs, explanations |
| Security | `securityContext`, `accessDecision` | Access control and audit decisions |
| Deployment | `deploymentTopology`, `serviceDescriptor` | Logical service layout and boundaries |
| Certification | `conformanceReport`, `conformanceTestResult` | Conformance evidence |
| Packaging | `artifactManifest` | Implementation output manifest |
| Implementation planning | `implementationTaskGraph`, `implementationWorkPacket`, `workPacketValidation`, `workPacketTraceability`, `implementationRisk`, `fixtureRecord`, `implementationEvidenceBundle` | Local-first task graph, work packets, fixtures, risks, and evidence |
| Bridge preparation | `bridgeAssetRecord`, `bridgeReuseAssessment`, `bridgeReuseDecision`, `contractClassification`, `compatibilityMatrix`, `bridgeEvidenceBundle` | Smart reuse, contract classification, bridge compatibility, and bridge evidence |

---

# 4.0 Implementation Assumptions

The first implementation should be local-first and modular.

The initial runtime may be a modular monolith, but internal package boundaries must preserve the logical service model:

* specification processing
* knowledge
* planning
* execution core
* execution engine
* validation
* trace and observability
* governance
* recovery
* control surface

The implementation language is not fixed by this plan. However, the implementation must support:

* JSON Schema validation
* deterministic tests
* file-based local artifact storage
* explicit module boundaries
* simple command-line or API control surface
* reproducible execution from fixtures

---

# 5.0 Work Packet Definition

Each implementation work packet must conform to the `implementationWorkPacket` schema and define:

| Field | Meaning |
| ----- | ------- |
| Packet ID | Stable task identifier |
| Macro Phase | Parent phase |
| Purpose | Single reason the packet exists |
| Inputs | Files, schemas, fixtures, or prior packets required |
| Outputs | One primary module, fixture, or test group |
| Validation | One local validation command or check |
| Traceability | Schema artifact or specification section it satisfies |

A packet is too broad if it contains more than one unrelated module, has no single validation method, or cannot be completed in one focused implementation pass.

---

# 6.0 Macro Phase Model and Local Work Packets

## 6.1 Phase 0 - Repository and Contract Foundation

**Goal:** Establish the implementation workspace and schema validation foundation.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0001 | Create repository/module skeleton | local module layout | expected directories exist |
| WP-0002 | Load platform schema | schema loader | schema JSON parses |
| WP-0003 | Index schema definitions | artifact definition index | required definition names resolve |
| WP-0004 | Validate artifacts by definition name | validator wrapper | valid fixture passes and invalid fixture fails |
| WP-0005 | Create fixture directory and naming convention | fixture scaffold | fixture path convention test |
| WP-0006 | Create first artifact manifest fixture | artifact manifest fixture | fixture validates |
| WP-0007 | Create contract test harness | contract test runner | all current fixtures run locally |

Exit criteria:

* platform schema parses
* definitions are discoverable by name
* fixtures can be validated individually
* contract tests run locally

## 6.2 Phase 1 - Specification Plugin and Knowledge Layer

**Goal:** Implement the first ISL-compatible specification ingestion path.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0101 | Define ISL plugin registration fixture | plugin registration fixture | validates as `specificationPluginRegistration` |
| WP-0102 | Implement plugin registry | plugin registry module | registration lookup test |
| WP-0103 | Create valid specification ingestion fixture | ingestion request fixture | validates as `specificationIngestionRequest` |
| WP-0104 | Create invalid specification ingestion fixture | invalid fixture | expected validation failure |
| WP-0105 | Implement ingestion validator | ingestion validation module | valid/invalid fixture tests pass |
| WP-0106 | Implement parsed specification model output | specification model builder | validates as `specificationModel` |
| WP-0107 | Implement plugin validation result writer | validation result writer | validates as `pluginValidationResult` |
| WP-0108 | Implement Knowledge Unit mapper | Knowledge Unit compiler | deterministic Knowledge Unit test |
| WP-0109 | Implement local Knowledge Unit store | file-backed store | write/read round trip test |

Exit criteria:

* a minimal ISL fixture can be ingested
* invalid fixture content is rejected with structured errors
* valid fixture content produces deterministic Knowledge Units
* Knowledge Units preserve origin metadata

## 6.3 Phase 2 - Planning Engine

**Goal:** Generate a schema-valid execution plan from Knowledge Units.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0201 | Build execution step factory | step builder | step validates as `executionStep` |
| WP-0202 | Build dependency graph builder | dependency graph module | graph references known steps |
| WP-0203 | Build checkpoint planner | plan checkpoint module | checkpoints validate |
| WP-0204 | Build execution modes defaults | execution modes factory | modes validate |
| WP-0205 | Build execution plan assembler | plan generator | plan validates as `executionPlan` |
| WP-0206 | Add plan determinism test | determinism test | same input produces same plan |
| WP-0207 | Add malformed plan rejection test | plan validation test | invalid plan fails |

Exit criteria:

* same Knowledge Units produce the same execution plan
* every execution step has dependencies, inputs, outputs, required rules, and validation rules
* checkpoints are present at validation boundaries
* plan validation blocks malformed plans

## 6.4 Phase 3 - Capability Registry and Invocation

**Goal:** Create the executable boundary between the execution engine and implementation capabilities.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0301 | Create mock capability registration fixture | capability fixture | validates as `capabilityRegistration` |
| WP-0302 | Implement capability registry | registry module | active capability lookup test |
| WP-0303 | Implement capability discovery query | discovery module | response validates |
| WP-0304 | Implement invocation request builder | request builder | validates as `capabilityInvocationRequest` |
| WP-0305 | Implement invocation response builder | response builder | validates as `capabilityInvocationResponse` |
| WP-0306 | Implement capability error mapping | error mapper | validates as `capabilityError` |
| WP-0307 | Add disabled capability rejection | lifecycle guard | disabled invocation fails |

Exit criteria:

* only registered active capabilities can be invoked
* disabled capabilities are rejected
* capability invocations are traceable
* capability failures produce structured errors

## 6.5 Phase 4 - Execution Core, State, Checkpoints, and Trace

**Goal:** Execute a small plan through the state machine and record trace evidence.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0401 | Implement state enum | state constants | enum matches schema |
| WP-0402 | Implement transition table | transition table | valid transitions listed |
| WP-0403 | Implement transition validator | validator module | invalid transition rejected |
| WP-0404 | Implement execution state record builder | state builder | validates as `executionState` |
| WP-0405 | Implement checkpoint writer | checkpoint module | validates as `checkpoint` |
| WP-0406 | Implement trace event writer | trace module | validates as `traceEvent` |
| WP-0407 | Implement log entry writer | log module | validates as `logEntry` |
| WP-0408 | Implement explanation record writer | explanation module | validates as `explanationRecord` |
| WP-0409 | Add trace reconstruction test | trace query test | execution can be reconstructed |

Exit criteria:

* execution follows valid state transitions
* invalid transitions are rejected
* checkpoint creation follows validation boundaries
* trace events are ordered and immutable by policy
* execution can be reconstructed from trace events

## 6.6 Phase 5 - Validation, Failure, and Recovery

**Goal:** Validate outputs, halt on failure, and recover from a valid checkpoint.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0501 | Implement validation rule evaluator | validation evaluator | pass/fail rule test |
| WP-0502 | Implement validation result writer | result writer | validates as `validationResult` |
| WP-0503 | Implement validation violation writer | violation writer | validates as `validationViolation` |
| WP-0504 | Implement failure classifier | failure classifier | taxonomy mapping test |
| WP-0505 | Implement failure record writer | failure writer | validates as `failureRecord` |
| WP-0506 | Implement recovery point selector | recovery selector | nearest valid checkpoint selected |
| WP-0507 | Implement rollback and resume path | recovery module | resumes from checkpoint only |

Exit criteria:

* validation failure prevents forward execution
* failures are classified using the Sage-X taxonomy
* rollback targets only valid checkpoints
* recovery actions are traceable

## 6.7 Phase 6 - Governance and Security Controls

**Goal:** Add policy checks, approval records, override records, access decisions, and governed control behavior.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0601 | Create governance policy fixture | policy fixture | validates as `governancePolicy` |
| WP-0602 | Implement policy registry | policy registry | policy lookup test |
| WP-0603 | Implement policy evaluator | evaluator module | block/allow test |
| WP-0604 | Implement approval record writer | approval writer | validates as `approvalRecord` |
| WP-0605 | Implement override record writer | override writer | validates as `overrideRecord` |
| WP-0606 | Implement governance decision writer | decision writer | validates as `governanceDecision` |
| WP-0607 | Implement security context fixture and builder | security context builder | validates as `securityContext` |
| WP-0608 | Implement access decision writer | access decision writer | validates as `accessDecision` |

Exit criteria:

* policy-defined approvals pause execution
* rejected approvals block execution
* overrides require authorization and validation
* access decisions are recorded
* governance decisions are traceable

## 6.8 Phase 7 - Control Surface

**Goal:** Provide the first usable control interface for local execution.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0701 | Implement control request parser | parser | validates as `controlRequest` |
| WP-0702 | Implement control response builder | response builder | validates as `controlResponse` |
| WP-0703 | Implement execution control operations | start/pause/resume/stop handlers | operation tests |
| WP-0704 | Implement guided step execution operation | step handler | one step execution test |
| WP-0705 | Implement planning control operations | plan handlers | generate/retrieve/validate tests |
| WP-0706 | Implement governance control operations | approval/override handlers | approval flow test |
| WP-0707 | Implement observability query operations | trace/state/failure query handlers | query returns schema-backed records |
| WP-0708 | Implement recovery control operations | rollback/resume/branch handlers | recovery operation test |

Exit criteria:

* all supported operations use schema-backed request/response records
* direct state mutation is not exposed
* failed operations return structured responses

## 6.9 Phase 8 - Bridge Reuse Preparation

**Goal:** Prepare the implementation to support smart reuse between Sage-X and ISL.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0801 | Add ISL origin metadata to Knowledge Units | origin metadata support | metadata preserved test |
| WP-0802 | Link plan steps to Knowledge Units | trace link support | step-to-KU references resolve |
| WP-0803 | Link outputs to plan steps | output trace support | output-to-step references resolve |
| WP-0804 | Implement artifact manifest writer | manifest writer | validates as `artifactManifest` |
| WP-0805 | Add placeholder bridge asset reference field handling | bridge-ready metadata | field round trip test |

Exit criteria:

* implementation outputs are listed in an artifact manifest
* each output can be traced to a plan step
* each plan step can be traced to a Knowledge Unit
* future bridge reuse records can be attached without restructuring the runtime

## 6.10 Phase 9 - Conformance Evidence

**Goal:** Produce the first conformance report for the implementation foundation.

Local work packets:

| Packet | Purpose | Primary Output | Validation |
| ------ | ------- | -------------- | ---------- |
| WP-0901 | Implement conformance test result writer | test result writer | validates as `conformanceTestResult` |
| WP-0902 | Implement conformance report writer | report writer | validates as `conformanceReport` |
| WP-0903 | Add Level 1 conformance test group | Level 1 tests | tests pass/fail deterministically |
| WP-0904 | Add known deviations section | deviation report | deviations are explicit |
| WP-0905 | Produce first implementation evidence bundle | evidence bundle | manifest, report, traces resolve |

Exit criteria:

* Level 1 Core Conformance evidence is produced
* gaps to Level 2 Controlled Execution are explicitly recorded
* implementation can be rerun from fixtures

---

# 7.0 First Vertical Slice

The first implementation milestone should be intentionally narrow.

Input:

* one minimal ISL-like fixture describing a single rule, workflow, or validation behavior

Execution:

1. load platform schema
2. validate fixture
3. register ISL specification plugin
4. ingest fixture
5. produce one specification model
6. produce two to five Knowledge Units
7. generate a small execution plan
8. register one mock validation capability
9. start execution
10. execute one validation step
11. record validation result
12. create checkpoint
13. record trace events
14. produce artifact manifest
15. produce one conformance test result

Success criteria:

* all emitted artifacts validate against the platform schema
* trace can reconstruct the run
* no generated output is accepted without validation
* failure behavior can be demonstrated with one intentionally invalid fixture

---

# 8.0 Implementation Order

The recommended build order is the work packet order in the companion JSON execution plan.

The first ten packets are the most important because they establish the local-first spine:

1. WP-0001 repository/module skeleton
2. WP-0002 schema loader
3. WP-0003 schema definition index
4. WP-0004 validator wrapper
5. WP-0005 fixture scaffold
6. WP-0006 artifact manifest fixture
7. WP-0007 contract test harness
8. WP-0101 ISL plugin registration fixture
9. WP-0102 plugin registry
10. WP-0103 valid ingestion fixture

This order keeps the system testable from the beginning and prevents late addition of traceability and governance.

---

# 9.0 Required Initial Fixtures

The first implementation should include fixtures for:

* valid specification ingestion request
* invalid specification ingestion request
* valid specification model
* Knowledge Unit set
* execution plan
* capability registration
* capability invocation request
* capability invocation response
* execution state
* checkpoint
* validation result
* trace event sequence
* failure record
* governance policy
* approval record
* control request
* control response
* artifact manifest
* conformance report

Each fixture should be schema-validated during tests.

---

# 10.0 Done Criteria for Implementation Start

Implementation should begin only after the following are true:

* platform schema is stable enough for the first vertical slice
* granular execution plan JSON in this folder parses successfully
* each local work packet has clear inputs, outputs, and validation
* unresolved schema questions are listed as implementation risks
* first fixtures are selected
* target repository layout is chosen

---

# 11.0 Risks and Controls

| Risk | Control |
| ---- | ------- |
| Macro tasks produce vague implementation | Local work packets are the executable units |
| Schema becomes aspirational but code ignores it | Contract tests must validate emitted artifacts |
| Runtime is built before traceability | Trace writer appears before recovery/governance |
| Governance is deferred too long | Policy hooks are included in plan and step schemas from the beginning |
| ISL semantics leak into Sage-X core | ISL-specific behavior stays in plugin, metadata, or bridge layer |
| Capability adapters become hardcoded | All execution work goes through capability registry contracts |
| Recovery is improvised | Checkpoints, failures, and recovery control requests are schema-backed |
| Conformance is subjective | Conformance reports and test results are emitted as artifacts |

---

# 12.0 Summary

This plan starts implementation from the schema surface rather than from informal service ideas.

The macro phases explain the architecture. The local work packets drive the build.

The first build should prove that Sage-X can take a specification-like input, compile it into Knowledge Units, plan execution, invoke a registered capability, validate output, record state and trace, handle at least one failure path, and produce evidence.

That foundation then becomes the reusable execution substrate that ISL/ALETHEIA can build on through the bridge profile.
