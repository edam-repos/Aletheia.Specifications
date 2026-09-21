# ALETHEIA Specification Language (ISL) v3.2
# Integration Components: Agents, Model Protocol, Tool Plugins

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x, ISL v2.x
**Supersedes:** ISL r2 v3.4 The Agent Implementation Model, ISL r2 v3.8 The Model Interaction Protocol, ISL r2 v3.9 The Tool Plugin Architecture
**Document Type:** Execution Layer Specification

---

## 1.0 Scope

This document defines the three integration components of the ALETHEIA execution layer: agent implementations, the Model Interaction Protocol, and the Tool Plugin Architecture. It specifies how reasoning agents, reasoning-model interactions, and deterministic engineering tools are packaged, registered, invoked, validated, governed, observed, and deployed.

The three components are mirror-images of a single integration skeleton. Each is a bounded component that is packaged with a manifest, registered in a registry, invoked through a standard runtime contract, validated before its output is consumed, and governed, traced, and observed. This document defines the shared skeleton once and then the component-specific contracts.

Shared conventions — normative language, field requirement levels, identifiers, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and referenced here rather than redefined.

---

## 2.0 Integration Component Principles

The following principles govern all three integration components.

### 2.1 Contract-First

Every component MUST be based on a declared contract (role contract, output contract, or capability contract). A component MUST NOT create operational behavior that exceeds its declared responsibilities.

### 2.2 Runtime-Mediated Invocation

Components MUST be invoked by the runtime orchestrator or an authorized runtime component. They MUST NOT self-schedule, directly invoke other components, directly mutate platform state, directly write stable artifacts, or directly approve governance gates.

### 2.3 Context Is an Input, Not Ambient Memory

Components MUST receive context through explicit context packages. They MUST NOT rely on hidden ambient memory, global state, unrestricted repository access, or unrecorded prior state.

### 2.4 Output Is a Contract

Component outputs MUST conform to declared output contracts. Free-form text MAY be included as rationale, but machine-consumed outputs MUST be structured and validated.

### 2.5 Deterministic Follow-Up Is Required

Component outputs that affect artifacts, validation, security, deployment, or governance MUST be followed by deterministic platform handling. Generated code MUST be compiled or tested where applicable. Proposed repair MUST be revalidated. Security findings MUST be routed through governance or deterministic checks where required.

### 2.6 Provider-Neutral Contracts

The platform-facing contracts MUST be independent of any specific model provider, tool, runtime, API, SDK, or deployment topology. Provider and tool adapters MAY translate the platform contract into provider- or tool-specific calls, but the platform-facing request and response contracts MUST remain stable and provider-neutral.

### 2.7 Traceable and Observable

Every lifecycle-relevant component activity MUST be traceable and observable. Component results that affect validation, repair, artifact promotion, release readiness, deployment preparation, or governance MUST be linked to the relevant task, artifact, validation record, and execution graph.

### 2.8 Testable

Each component MUST be independently testable using fixture context packages, expected output contracts, invalid-output cases, and failure scenarios.

---

## 3.0 Shared Component Skeleton

The agent, model, and tool components share a common structure. This section defines that skeleton once; the component sections that follow specify the component-specific fields and behavior.

### 3.1 Package Structure

Each component MUST be packaged in a predictable structure.

| Path | Purpose |
| ---- | ------- |
| `/manifest` | Component manifest |
| `/contracts` | Component-specific request/result contract extensions, if any |
| `/templates` | Prompt and instruction templates (agents) |
| `/adapter` | Adapter implementation (tools) |
| `/validators` | Output and context validators |
| `/mappers` | Mapping between provider/tool outputs and platform outputs |
| `/schemas` | Result, finding, and configuration schemas |
| `/config` | Non-secret configuration templates |
| `/fixtures` | Local fixture context packages for testing |
| `/tests` | Tests and fixtures |
| `/docs` | Documentation |
| `/examples` | Example invocations and expected results |
| `/evidence` | Certification or validation evidence where applicable |

#### 3.1.1 Package Rules

A package MUST include a manifest. A package MUST include at least one output validator. A package MUST include tests or a test coverage plan. A package MUST declare supported output contracts. A package MUST NOT include provider-specific credentials, plaintext secrets, or secret values. A package MUST declare supported platform and ISL versions.

### 3.2 Manifest

Each component MUST provide a manifest describing its identity, role or type, capabilities, inputs, outputs, and constraints.

#### 3.2.1 Common Manifest Fields

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| component-id | string | REQUIRED | Unique implementation identifier |
| component-version | semver | REQUIRED | Implementation version |
| supported-isl-versions | array | REQUIRED | ISL versions supported |
| supported-output-contracts | array | REQUIRED | Output contracts this component can produce |
| required-context-categories | array | REQUIRED | Context categories required |
| optional-context-categories | array | CONDITIONAL | Context categories optionally used |
| prohibited-context-categories | array | CONDITIONAL | Context categories the component MUST NOT receive |
| risk-tier-limit | enum | CONDITIONAL | Highest risk tier allowed without additional governance |
| human-review-required | boolean | REQUIRED | Whether output requires human review |
| status | enum | REQUIRED | active, experimental, deprecated, disabled, revoked |
| registered-at | ISO 8601 | REQUIRED | Registration timestamp |
| registered-by | string | REQUIRED | Authority or subsystem registering implementation |

Component-specific manifest fields are defined in §4.2 (agents), §5.2 (model protocol), and §6.2 (tool plugins).

#### 3.2.2 Manifest Rules

A component MUST NOT be registered without a valid manifest. A component with status revoked MUST NOT be invoked. An experimental component MUST NOT be used for High or Critical risk systems unless governance explicitly approves it. The runtime MUST validate the manifest before invoking the component.

### 3.3 Registry

Each component type has a registry that stores available implementations and their manifests.

#### 3.3.1 Common Registry Record Fields

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| registry-record-id | string | REQUIRED | Unique registry record |
| component-id | string | REQUIRED | Registered implementation |
| component-version | semver | REQUIRED | Version registered |
| manifest-reference | string | REQUIRED | Manifest location or record |
| registration-status | enum | REQUIRED | active, restricted, experimental, deprecated, disabled, revoked |
| governance-profile-id | string | CONDITIONAL | Governance profile controlling use |
| registered-at | ISO 8601 | REQUIRED | Registration time |
| registered-by | string | REQUIRED | Registering authority or subsystem |

#### 3.3.2 Registry Rules

The runtime MUST select implementations from the registry. A task requiring an unavailable component MUST fail, queue, or escalate according to runtime policy. Registry changes MUST be auditable. A registry update during active execution MUST trigger impact evaluation if it changes behavior.

### 3.4 Lifecycle

Components MUST support explicit lifecycle states.

#### 3.4.1 Common Lifecycle States

| State | Meaning |
| ----- | ------- |
| registered | Component is known to the platform |
| available | Component can receive invocations |
| selected | Component selected for a task |
| context-bound | Context package bound to invocation |
| invoked | Execution started |
| output-produced | Component produced output |
| output-validated | Output validation completed |
| completed | Invocation completed successfully |
| failed | Invocation failed |
| escalated | Invocation requires external resolution |
| disabled | Component unavailable for new work |

#### 3.4.2 Lifecycle Rules

A component MUST NOT enter invoked state without context-bound state. A component MUST NOT enter completed state unless output validation passes or the contract permits partial output. Lifecycle transitions MUST be recorded as state events.

### 3.5 Invocation Request and Response

Components MUST expose a standard runtime interface.

#### 3.5.1 Common Invocation Request Fields

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| invocation-request-id | string | REQUIRED | Unique runtime request identifier |
| invocation-id | string | REQUIRED | Invocation identifier |
| component-id | string | REQUIRED | Implementation selected |
| task-id | string | REQUIRED | Construction task |
| execution-graph-id | string | CONDITIONAL | Execution graph during runtime |
| context-package-id | string | REQUIRED | Context package supplied |
| output-contract-id | string | REQUIRED | Required output contract |
| governance-check-id | string | CONDITIONAL | Governance check authorizing invocation |
| timeout-seconds | integer | REQUIRED | Maximum execution time |
| correlation-id | string | REQUIRED | Correlation identifier |
| requested-at | ISO 8601 | REQUIRED | Request timestamp |

#### 3.5.2 Common Invocation Response Fields

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| invocation-response-id | string | REQUIRED | Unique response identifier |
| invocation-request-id | string | REQUIRED | Matching request |
| invocation-id | string | REQUIRED | Invocation identifier |
| outcome | enum | REQUIRED | completed, failed, timeout, cancelled, escalated |
| output-id | string | CONDITIONAL | Output produced |
| validation-status | enum | REQUIRED | not-evaluated, passed, failed, warning |
| findings | array | CONDITIONAL | Findings from execution or validation |
| produced-artifact-candidates | array | CONDITIONAL | Candidate artifacts produced |
| telemetry-event-ids | array | CONDITIONAL | Telemetry emitted |
| completed-at | ISO 8601 | REQUIRED | Completion timestamp |
| next-action | enum | REQUIRED | accept, retry, revise, validate-artifacts, review, repair, escalate, reject |

#### 3.5.3 Invocation Rules

An invocation request MUST include task-id, context-package-id, and output-contract-id. An invocation response MUST identify outcome and next-action. An invocation response MUST NOT claim completed when output validation fails unless the output is explicitly partial and accepted by the contract.

### 3.6 Output Validation

Each component MUST include validators or reference shared validators.

#### 3.6.1 Output Validation Checks

Output validation MUST check:

* schema conformance
* required field presence
* output type compatibility
* role/type compatibility
* task compatibility
* referenced entity validity
* artifact candidate completeness
* prohibited action detection
* governance constraint compliance
* deterministic validation requirement flag
* sensitivity and security constraints

#### 3.6.2 Output Validation Result Fields

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| output-validation-id | string | REQUIRED | Unique validation record |
| output-id | string | REQUIRED | Output evaluated |
| component-id | string | REQUIRED | Producing implementation |
| output-contract-id | string | REQUIRED | Contract evaluated |
| validation-outcome | enum | REQUIRED | passed, failed, warning, escalated |
| findings | array | CONDITIONAL | Findings from validation |
| validated-at | ISO 8601 | REQUIRED | Validation timestamp |
| validated-by | string | REQUIRED | Validator component |
| next-action | enum | REQUIRED | accept, retry, request-revision, review, escalate, reject |

#### 3.6.3 Validation Rules

An output that fails validation MUST NOT be accepted. An output that proposes prohibited actions MUST be rejected or escalated. An output that omits required traceability identifiers MUST be rejected. A warning MAY be accepted only if the contract and governance policy permit it.

### 3.7 Traceability

Component activity MUST be traceable. The platform MUST record links between:

* construction task and invocation
* invocation and implementation
* invocation and context package
* implementation and output contract
* output and canonical entities
* artifact candidates and output
* output validation result and output

A task MUST NOT be marked completed if required component traceability is missing. An artifact candidate MUST NOT be admitted if its producing output is untraceable. Traceability records MUST remain queryable after execution completion.

### 3.8 Telemetry

Components MUST emit telemetry consistent with the common telemetry model (ISL v0.1 §9) and the telemetry catalog (ISL v2.3). Telemetry MUST include component-id and invocation-id when available, and MUST include correlation-id. Telemetry MUST NOT expose restricted context or raw secrets.

### 3.9 Versioning and Compatibility

Component versions MUST use semantic versioning. A breaking change to output structure MUST increment the major version. A change to required context categories MUST be treated as compatibility-impacting. Runtime records MUST capture the component version used for each invocation.

### 3.10 Extension

New components or implementations MAY be introduced through the extension model. An extension MUST provide a manifest, declare capabilities and output contracts, include tests, and MUST NOT bypass model, governance, traceability, state, artifact, or validation boundaries. High or Critical risk use of an extension MUST require governance approval.

### 3.11 Security

Components MUST protect context, artifacts, and platform state. Components MUST NOT receive secrets unless explicitly governed. Components MUST treat specification content, artifact content, logs, and tool outputs as potentially untrusted. Templates MUST distinguish platform instructions from untrusted context. Outputs MUST be checked for prohibited actions, unsafe content, and policy violations. Restricted context MUST NOT be exposed in telemetry or logs.

If a component detects instruction override attempts, secret exfiltration attempts, role changes, governance bypass requests, or output contract bypass attempts in context, the component or context controller MUST record a security finding. The invocation MUST be blocked, sanitized, or escalated according to governance policy.

### 3.12 Testing

Components MUST be testable before use in autonomous execution. Required test categories include manifest validation, context validation, contract validation, fixture output, invalid output, role/type boundary, artifact candidate, and telemetry/traceability verification. A component MUST NOT be marked active unless manifest-validation and contract-validation pass.

---

## 4.0 Agent Implementations

This section specifies the agent-specific contracts that specialize the shared skeleton.

### 4.1 Standard Agent Roles

The reference implementation SHOULD provide implementations for the standard agent roles.

| Agent Role | Implementation Responsibility |
| ---------- | ---------------------------- |
| Specification Interpreter | Converts canonical entities into execution-oriented interpretation records |
| Architecture Planner | Produces architecture guidance, decomposition findings, and architecture decision suggestions |
| Construction Planner | Supports task decomposition, dependency reasoning, and plan adaptation |
| Implementation Generator | Produces candidate implementation artifacts |
| Repair Analyst | Analyzes validation failures and proposes bounded repair actions |
| Test Generator | Produces test plans and candidate test artifacts |
| Review Agent | Reviews outputs, artifacts, assumptions, and traceability completeness |
| Security Validator | Reviews policies, security constraints, sensitive data handling, and security findings |
| Deployment Preparer | Produces deployment preparation artifacts and operational readiness findings |

A platform MAY implement a subset of standard agents, but it MUST declare supported roles in the Agent Registry. A platform claiming full autonomous construction capability MUST implement all standard agent roles.

### 4.2 Agent Role Manifest

The agent manifest specializes the shared manifest with:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| agent-implementation-id | string | REQUIRED | Unique implementation identifier |
| agent-role | enum | REQUIRED | Standard or extension role |
| role-contract-version | semver | REQUIRED | Role contract version implemented |
| supported-task-types | array | REQUIRED | Construction task types supported |
| required-capabilities | array | REQUIRED | Required model capabilities |
| artifact-producing | boolean | REQUIRED | Whether agent may produce artifact candidates |
| deterministic-validation-required | boolean | REQUIRED | Whether outputs require deterministic validation |
| telemetry-profile | string | CONDITIONAL | Agent telemetry profile |

### 4.3 Agent Context Packages

Agents receive context packages assembled by the platform. Context categories include canonical-entities, task-definition, planning-context, artifact-context, validation-context, repair-context, governance-context, traceability-context, repository-context, security-context, and deployment-context.

The Agent Orchestrator MUST assemble or request a context package before invocation. The agent MUST verify that required context categories are present and MUST reject or escalate context packages containing prohibited categories. Context package identifiers MUST be recorded in agent invocation records.

### 4.4 Agent Outputs and Artifact Candidates

Agent outputs MUST be structured and contract-validatable. An output MUST conform to its output-contract-id, MUST reference canonical entities, and MUST identify artifact candidates when it produces artifacts. An output that cannot be validated MUST NOT be consumed downstream.

Artifact-producing agents (Implementation Generator, Test Generator, Deployment Preparer, and approved extension roles) MUST produce artifact candidates, not stable artifacts. Artifact candidates MUST enter the Artifact Repository through admission. Artifact-producing agents MUST NOT directly write to stable repository state. Artifact candidates MUST include source-entity-ids. Executable or structural artifacts MUST require deterministic validation.

### 4.5 Repair Agent Behavior

Repair Analyst implementations analyze validation failures and propose repairs. They MUST receive failed validation results, failed artifact references, tool findings, source entity references, and repair limits. They MUST produce a repair proposal that identifies affected artifacts, states whether revalidation is required, and identifies convergence risk. Repair Analyst MUST NOT mark artifacts repaired, valid, or stable.

### 4.6 Agent Deployment

Agent implementations may be deployed in-process, as worker modules, as services, sandboxed, or as external extensions. Local-first deployments MAY use in-process agents. Distributed deployments SHOULD use worker-hosted or service-hosted agents. Sandboxed deployment SHOULD be used for experimental or untrusted agent extensions. External-extension agents MUST be governed and registered. Deployment mode MUST NOT alter role contract obligations.

---

## 5.0 Model Interaction Protocol

This section specifies the Model Integration Layer protocol through which agents and runtime services interact with reasoning models.

### 5.1 Reasoning Transaction Lifecycle

Every model interaction MUST be treated as a structured reasoning transaction. A transaction MUST have a request envelope, response envelope, output contract, context reference, selected model profile, validation status, and traceability links.

| Stage | Purpose |
| ----- | ------- |
| requested | Agent or runtime requests model reasoning |
| context-bound | Context package is selected and controlled |
| contract-bound | Output contract is selected |
| model-routed | Model router selects model provider and profile |
| governance-checked | Governance controls are evaluated when required |
| invocation-dispatched | Request is sent through provider adapter |
| response-received | Raw response is received |
| normalized | Raw response is converted into platform output form |
| validated | Normalized output is checked against output contract |
| accepted | Valid response is accepted for downstream handling |
| corrected | Response correction requested |
| retried | Request retried under retry policy |
| fallback-routed | Request routed to alternate model |
| escalated | Human or governance intervention required |
| rejected | Response rejected and not used |
| completed | Transaction closed |

A transaction MUST NOT enter invocation-dispatched until context-bound, contract-bound, and model-routed stages are complete. A governed transaction MUST NOT enter invocation-dispatched until governance permits the invocation. A transaction MUST NOT enter accepted state unless response validation passes or a valid governance waiver permits manual acceptance. A rejected response MUST NOT be consumed by downstream platform components.

### 5.2 Model Request Envelope

The request envelope is the normative structure for model invocation. It specializes the shared invocation request with:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| model-interaction-request-id | string | REQUIRED | Unique request identifier |
| protocol-version | semver | REQUIRED | Model Interaction Protocol version |
| agent-invocation-id | string | REQUIRED | Agent invocation causing request |
| agent-role | enum | REQUIRED | Agent role framing the request |
| specification-id | string | REQUIRED | Specification identifier |
| specification-version | semver | REQUIRED | Specification version |
| construction-boundary-id | string | CONDITIONAL | Active Construction Boundary governing task context |
| connection-context-ids | array | CONDITIONAL | Connection Contexts authorizing boundary crossing |
| reuse-decision-id | string | CONDITIONAL | Reuse Decision constraining generation behavior |
| reusable-asset-ids | array | CONDITIONAL | Reusable assets provided to the model |
| instruction-template-id | string | REQUIRED | Instruction template used |
| reasoning-objective | string | REQUIRED | Bounded objective of request |
| constraints | array | REQUIRED | Operational, governance, security, and role constraints |
| selected-model-profile-id | string | REQUIRED | Model profile selected by router |
| selected-provider-id | string | REQUIRED | Provider selected |
| routing-decision-id | string | REQUIRED | Routing decision |
| invocation-mode | enum | REQUIRED | synchronous, asynchronous, streaming, batch |
| requested-output-format | enum | REQUIRED | json, yaml, markdown, code, mixed, artifact-content |
| retry-policy-id | string | CONDITIONAL | Retry policy |
| sensitivity-classification | enum | REQUIRED | public, internal, confidential, restricted |
| requested-by | string | REQUIRED | Requesting subsystem |

A request MUST include agent-role, task-id, context-package-id, output-contract-id, selected-model-profile-id, and correlation-id. A request MUST identify specification-id and specification-version, declare timeout-seconds, and declare sensitivity-classification. A request for artifact generation, repair, planning, or review MUST declare construction-boundary-id when the task is boundary-scoped. A request that depends on context outside the active Construction Boundary MUST declare valid connection-context-ids. A request constrained by a Reuse Decision Record MUST declare reuse-decision-id and MUST state whether the model may generate full artifact content, reuse only, reuse-delta, wrapper, extension, or composition output. A request MUST NOT include raw secrets or provider-specific fields except inside adapter-private metadata.

### 5.3 Context Package Reference

The request envelope references a context package rather than embedding uncontrolled context. The context package MUST be assembled before model invocation and MUST be controlled for sensitivity, redaction, relevance, and size. Restricted context MUST NOT be sent to a model whose trust profile disallows restricted context. Context that contains secrets MUST block invocation unless explicitly permitted by governance. An expired, superseded, missing, or policy-incompatible Connection Context MUST block invocation. The context package SHOULD provide summaries, selectors, and minimal asset metadata before full artifact content when the selected model profile is local, low-memory, or otherwise resource-constrained. The platform MUST record a boundary drift finding when the assembled context includes information outside the active boundary without an authorizing Connection Context.

### 5.4 Instruction Structure

An instruction template MUST include protocol-header, agent-role, task-objective, permitted-actions, prohibited-actions, context-summary, constraints, output-contract, traceability-requirements, deterministic-follow-up, and failure-behavior sections. Instructions MUST distinguish platform instructions from untrusted context, MUST identify the required output contract, MUST direct the model to return only the requested output format when machine parsing is required, and MUST NOT invite the model to alter role, governance, validation, or traceability requirements. If the model cannot satisfy the request, the instruction MUST require a structured failure response.

### 5.5 Model Response Envelope

The response envelope is the normative structure returned to the platform after provider invocation and normalization.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| model-interaction-response-id | string | REQUIRED | Unique response identifier |
| model-interaction-request-id | string | REQUIRED | Request being answered |
| protocol-version | semver | REQUIRED | Protocol version used |
| selected-model-profile-id | string | REQUIRED | Model used |
| selected-provider-id | string | REQUIRED | Provider used |
| raw-provider-response-reference | string | CONDITIONAL | Raw response reference if retained |
| normalized-output-reference | string | CONDITIONAL | Normalized response reference |
| output-contract-id | string | REQUIRED | Output contract used |
| response-outcome | enum | REQUIRED | completed, failed, timeout, cancelled, blocked, invalid-output, normalized |
| output-validation-status | enum | REQUIRED | not-evaluated, passed, failed, warning, waived |
| findings | array | CONDITIONAL | Validation, normalization, or policy findings |
| boundary-drift-findings | array | CONDITIONAL | Findings where output exceeds active boundary or context authority |
| reuse-decision-compliance | enum | CONDITIONAL | compliant, non-compliant, not-applicable, unknown |
| duration-ms | integer | REQUIRED | Invocation duration |
| retry-count | integer | REQUIRED | Retry count at response time |
| fallback-used | boolean | REQUIRED | Whether fallback model was used |
| deterministic-follow-up-required | boolean | REQUIRED | Whether follow-up is required |
| next-action | enum | REQUIRED | accept, correct, retry, fallback, validate-artifacts, human-review, escalate, reject, halt |
| received-at | ISO 8601 | REQUIRED | Response receipt time |

A response MUST reference the original request, identify the model and provider used, and identify output-contract-id. A timeout MUST NOT be marked completed. A response with output-validation-status failed MUST NOT be accepted. A response requiring deterministic follow-up MUST NOT be treated as final workflow success.

### 5.6 Output Normalization

Output normalization converts raw model output into the declared contract structure. The platform MUST retrieve the raw provider response, identify the expected output contract, extract the structured payload, remove irrelevant provider wrapper text, parse the required format, map provider-specific fields to platform fields, preserve the raw response reference if retention policy permits, produce a normalized output reference, and record normalization findings. A response MUST be normalized before platform consumption. Partial normalization MUST NOT be accepted unless the output contract permits partial output. Normalization failure MUST trigger correction, retry, fallback, escalation, or rejection.

### 5.7 Response Validation

Response validation checks normalized output against the output contract. The platform MUST check output contract version match, required field presence, prohibited field absence, field type correctness, enum value correctness, required traceability identifiers, referenced entity existence, referenced artifact existence where applicable, reuse decision compliance where applicable, task compatibility, agent role compatibility, Construction Boundary compatibility, Connection Context compatibility, security and sensitivity constraints, deterministic follow-up flag correctness, and downstream consumer compatibility.

A response MUST NOT be accepted unless validation passes, warning is accepted under policy, or waiver exists. A response missing required traceability identifiers MUST fail validation. A response that exceeds the agent role boundary MUST fail validation. A response containing prohibited content MUST be rejected or escalated. A response that introduces entities, dependencies, assumptions, artifacts, or policies outside the active Construction Boundary without valid Connection Context authority MUST fail validation. A response that proposes full generation where a Reuse Decision Record authorizes reuse, reuse-delta, wrapper, extension, or composition only MUST fail validation.

### 5.8 Correction, Retry, and Fallback

A correction request asks the same model to repair its response format or missing fields without changing task scope. Correction MAY be attempted only when the output contract allows correction, the original request is valid, the failure is limited to correctable structure, missing field, formatting, or minor ambiguity, the retry policy permits correction, and governance does not require escalation. Correction MUST NOT change the task objective, MUST NOT add new context except validation findings and correction instructions, and MUST be bounded.

Retry MAY occur when a provider timeout occurred, the provider is unavailable, the response is incomplete, the output is malformed, or a transient routing or adapter failure occurred. Retry MUST NOT occur when governance blocks model use, context violates policy, output contains a security violation requiring escalation, the requested model lacks a required capability, or the retry limit has been reached.

Fallback MAY occur only when the routing policy permits fallback, an eligible fallback model exists, the fallback model satisfies required capabilities, the fallback model trust profile permits the context sensitivity, governance permits the fallback provider, and the output contract remains unchanged or compatible.

| Parameter | Default |
| --------- | ------: |
| Maximum correction attempts per response | 2 |
| Maximum retry attempts per request | 3 |
| Maximum fallback attempts per request | 2 |
| Escalation threshold for repeated invalid outputs | 3 consecutive invalid outputs |

Retry and fallback attempts MUST be recorded and MUST preserve correlation identifiers. Fallback MUST produce or amend routing decision records. Repeated invalid outputs MUST escalate.

### 5.9 Deterministic Follow-Up

Model outputs that affect platform state require deterministic follow-up.

| Output Type | Required Follow-Up |
| ----------- | ------------------ |
| artifact-candidate | Artifact Repository admission and deterministic validation |
| repair-proposal | Repair task creation and revalidation after modification |
| test-plan | Test artifact generation and test execution |
| review-finding | Finding classification and governed handling |
| security-finding | Security policy evaluation and governance routing |
| deployment-preparation | Manifest validation and operational readiness checks |
| plan-fragment | Planning validation and dependency validation |
| interpretation | Canonical model or task consistency checks |

A model output MUST NOT directly promote artifacts, mark validation passed, approve governance gates, or authorize deployment. Deterministic follow-up MUST be recorded as linked evidence.

### 5.10 Streaming and Batch Interaction

Streaming MAY be used only when the output contract permits streaming, partial output cannot be consumed as final output, stream completion is detected, and the final assembled response is normalized and validated. Individual chunks MUST NOT be treated as accepted model output unless the output contract explicitly supports chunk-level validation. Interrupted streams MUST trigger retry, fallback, escalation, or rejection.

A batch request MAY contain multiple model interaction requests only when each request has its own request identifier, output contract, and context package or declared shared context, failures can be isolated per request, and traceability can be recorded per request. A failed item in a batch MUST NOT invalidate successful items unless dependency or policy requires it. Batch responses MUST be normalized and validated per request.

### 5.11 Governance and Security

Model interaction MUST obey governance policy. Governance MAY control external model provider use, restricted context transmission, experimental model use, fallback to weaker trust profile, security task model use, High or Critical risk model interaction, raw response retention, human review bypass, response waiver, and model-generated artifact acceptance. A governance block MUST prevent invocation. An approval-required decision MUST pause invocation. A waiver-required decision MUST prevent acceptance until waiver exists. Governance decisions MUST be recorded and linked to the model transaction.

The protocol MUST protect model interaction from malicious or untrusted context. The platform MUST classify context sensitivity, separate platform instructions from untrusted context, mark untrusted context explicitly, prevent untrusted content from redefining role, output contract, governance, or validation rules, redact secrets, block unauthorized restricted context, record prompt or context injection findings, and reject outputs that attempt governance, traceability, validation, or role bypass. An instruction-override attempt MUST NOT alter platform instructions. A governance-bypass attempt MUST be rejected or escalated. A secret-exfiltration attempt MUST block invocation or output acceptance.

---

## 6.0 Tool Plugins

This section specifies the Tool Integration Layer through which deterministic engineering tools are integrated.

### 6.1 Plugin Types

Tool plugins are categorized by the operation they expose.

| Plugin Type | Purpose |
| ----------- | ------- |
| compiler-plugin | Compiles or builds source artifacts |
| test-plugin | Executes automated tests |
| analysis-plugin | Performs static analysis, quality analysis, complexity analysis, or duplication detection |
| security-plugin | Performs vulnerability scanning, dependency audit, secret detection, or security policy checks |
| infrastructure-plugin | Validates infrastructure definitions, environment configuration, or provisioning plans |
| packaging-plugin | Builds packages, bundles, manifests, containers, or release artifacts |
| deployment-plugin | Performs deployment preparation or controlled deployment actions |
| repository-plugin | Performs repository operations, branch validation, merge checks, or metadata checks |
| reuse-plugin | Performs reusable asset discovery, reuse fitness checks, duplicate asset detection, or registry validation |
| context-plugin | Performs Construction Boundary, Connection Context, minimization, or drift checks |
| documentation-plugin | Validates documentation, generated API references, or documentation completeness |
| format-plugin | Formats or validates formatting of generated artifacts |
| custom-plugin | Provides an approved domain-specific deterministic capability |

A plugin MUST declare one primary plugin type and MAY declare secondary capabilities. A deployment-plugin MUST be governed before production or regulated use. A security-plugin used for release gates MUST have an active trust profile and validation record.

### 6.2 Tool Plugin Manifest

The plugin manifest specializes the shared manifest with:

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| tool-plugin-id | string | REQUIRED | Unique plugin identifier |
| plugin-name | string | REQUIRED | Human-readable plugin name |
| plugin-type | enum | REQUIRED | Plugin type from §6.1 |
| tool-name | string | REQUIRED | Underlying tool name |
| tool-version | string | REQUIRED | Underlying tool version or version range |
| supported-capabilities | array | REQUIRED | Capability declarations |
| supported-artifact-types | array | REQUIRED | Artifact types supported |
| supported-input-formats | array | REQUIRED | Input formats supported |
| invocation-method | enum | REQUIRED | cli, library, http-api, grpc, container, script, message-bus |
| execution-environment | enum | REQUIRED | host, process, container, vm, remote-service, sandbox |
| isolation-profile-id | string | REQUIRED | Isolation profile |
| trust-profile-id | string | REQUIRED | Trust profile |
| required-permissions | array | REQUIRED | Filesystem, network, process, secret, repository, deployment permissions |
| required-secrets | array | CONDITIONAL | Secret references by name only |
| network-access-required | boolean | REQUIRED | Whether network access is required |
| destructive-operation-capable | boolean | REQUIRED | Whether plugin can modify external state destructively |
| supports-dry-run | boolean | REQUIRED | Whether dry-run is supported |
| supports-timeout | boolean | REQUIRED | Whether timeout can be enforced |
| supports-cancellation | boolean | REQUIRED | Whether cancellation can be enforced |
| supports-evidence-capture | boolean | REQUIRED | Whether evidence capture is supported |

A plugin that declares destructive-operation-capable true MUST require governance review before use in production environments.

### 6.3 Capability Catalog

The platform SHOULD support the following standard tool capabilities.

| Category | Capabilities |
| -------- | ------------ |
| development | compile, build, package, dependency-resolve, lint, format-check |
| testing | unit-test, integration-test, acceptance-test, coverage-report, regression-test |
| analysis | static-analysis, complexity-analysis, duplication-detection, api-compatibility-check |
| reuse | reusable-asset-search, reuse-fitness-check, duplicate-asset-detection, reuse-provenance-check |
| context | boundary-scope-check, connection-context-validate, context-minimization-check, context-drift-detect |
| security | vulnerability-scan, dependency-audit, secret-detection, license-check, policy-check |
| infrastructure | infrastructure-validate, manifest-validate, configuration-check, environment-compatibility-check |
| deployment | artifact-package, container-build, deployment-plan-validate, service-deploy |
| repository | repository-integrity-check, branch-policy-check, merge-conflict-check, metadata-validate |
| documentation | documentation-build, documentation-link-check, api-doc-validate |

A plugin MUST declare each capability it supports. A capability declaration MUST include input types, output types, supported artifact types, and execution requirements. A task requiring a tool capability MUST NOT be dispatched unless at least one active, permitted plugin supports the capability. A failed capability validation MUST prevent runtime selection for that capability. Enterprise artifact-producing profiles SHOULD require active duplication-detection, reusable-asset-search, or equivalent reuse capability before full generation tasks are dispatched. Boundary-scoped model or agent tasks SHOULD require boundary-scope-check, connection-context-validate, or equivalent context capability before lifecycle-critical output is accepted.

### 6.4 Tool Selection

Tool selection maps runtime task requirements to registered plugin capabilities. The selector MUST consider required capability, task type, artifact type, input format, output contract, tool version constraints, plugin status, trust profile, isolation profile, risk tier, project or tenant constraints, governance policy, environment compatibility, historical reliability where available, and resource availability.

The selector MUST NOT select a plugin that lacks the required capability, whose trust profile disallows the risk tier or context, or whose isolation requirements cannot be satisfied. When multiple plugins satisfy requirements, the selector SHOULD prefer active, validated, least-privilege, environment-compatible tools.

### 6.5 Tool Invocation Request and Result

The Tool Invocation Request is the standard runtime request sent to a plugin. It specializes the shared invocation request with tool-selection-id, tool-plugin-id, plugin-version, tool-name, tool-version, capability-name, specification-id, specification-version, artifact-ids, artifact-version-ids, input-references, parameters, environment-profile-id, isolation-profile-id, dry-run, and requested-by. A tool invocation MUST reference a selected tool plugin, declare capability-name, declare exact artifact versions when validating artifacts, declare timeout-seconds, and declare isolation-profile-id. A governed tool invocation MUST NOT proceed until governance permits it.

The Tool Invocation Result is the normalized result returned by the plugin.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| tool-invocation-result-id | string | REQUIRED | Unique result identifier |
| tool-invocation-id | string | REQUIRED | Invocation request |
| tool-plugin-id | string | REQUIRED | Plugin invoked |
| tool-version | string | REQUIRED | Tool version used |
| capability-name | string | REQUIRED | Capability invoked |
| outcome | enum | REQUIRED | passed, failed, warning, timeout, error, cancelled, not-applicable |
| exit-code | integer | CONDITIONAL | Tool process exit code where applicable |
| findings | array | CONDITIONAL | Structured findings |
| evidence-references | array | CONDITIONAL | Logs, reports, output files, coverage, scan reports |
| affected-artifact-ids | array | CONDITIONAL | Artifacts affected |
| evaluated-artifact-version-ids | array | CONDITIONAL | Artifact versions evaluated |
| normalized-output-reference | string | CONDITIONAL | Normalized result payload |
| raw-output-reference | string | CONDITIONAL | Raw output if retained |
| duration-ms | integer | REQUIRED | Execution duration |
| started-at | ISO 8601 | REQUIRED | Start time |
| completed-at | ISO 8601 | REQUIRED | Completion time |
| next-action | enum | REQUIRED | proceed, repair, retry, escalate, fail-task, halt, ignore-warning |

A timeout MUST NOT be treated as passed. An error MUST NOT be treated as passed. A result with outcome failed MUST trigger repair, retry, escalation, failure, or waiver handling according to policy. A result MUST reference evaluated artifact versions when used for artifact validation. Raw output retention MUST follow governance and sensitivity rules.

### 6.6 Tool Findings

Tool findings provide structured details from tool execution. A finding includes finding-category (compile, test, lint, format, static-analysis, reuse, duplication, context-boundary, security, dependency, license, infrastructure, deployment, repository, documentation, policy), severity, finding-code, message, affected-artifact-id, affected-artifact-version-id, affected-reusable-asset-id, affected-reuse-decision-id, affected-construction-boundary-id, affected-connection-context-id, location-reference, rule-reference, evidence-reference, recommended-action, and blocks-progression.

A blocking finding MUST prevent affected artifact promotion unless waived. A critical security finding MUST escalate according to governance policy. A finding used to trigger repair MUST link to the repair record. Tool-specific finding codes SHOULD be mapped to normalized finding categories. A duplication or reuse finding that identifies an approved reusable asset SHOULD reference the candidate Reusable Asset identifier and recommended reuse mode. A blocking context-boundary finding MUST prevent downstream model output acceptance, artifact promotion, or lifecycle progression until repaired, replanned, waived, or escalated.

### 6.7 Evidence Capture

Tool plugins MUST preserve evidence when results affect lifecycle decisions. Evidence MAY include raw stdout/stderr logs, build reports, test result files, coverage reports, static analysis reports, vulnerability scan reports, dependency manifests, SBOM files, package manifests, deployment plan outputs, infrastructure validation reports, and generated diagnostic files. Evidence required for validation, release, deployment authorization, or governance MUST be retained. Evidence MUST be linked to the tool invocation result. Evidence containing sensitive data MUST be classified and protected. Evidence used for High or Critical systems SHOULD include integrity markers.

### 6.8 Execution Isolation

Tool plugins MUST run under declared isolation controls.

| Isolation Profile | Description |
| ---------------- | ----------- |
| host-limited | Runs on host with restricted workspace and permissions |
| process-isolated | Runs in restricted process environment |
| container-isolated | Runs in container with controlled filesystem, network, and resources |
| vm-isolated | Runs in virtual machine or equivalent strong isolation |
| remote-service | Runs through external service boundary |
| sandboxed-generated-code | Runs generated code under strict sandbox controls |

Tools that execute generated code MUST use sandboxed-generated-code or equivalent strong isolation. A plugin MUST NOT receive broader filesystem, network, process, or secret access than its manifest declares. A plugin requiring unavailable isolation controls MUST NOT be invoked. Transient workspaces SHOULD be cleaned after execution unless evidence retention requires preservation.

### 6.9 Trust Profiles

Trust profiles define where and how a plugin may be used. A trust profile declares trust-level (approved, restricted, experimental, prohibited), allowed-risk-tiers, allowed-environments, allowed-project-scopes, external-service, data-retention-profile, human-review-required, approval-required, policy-reference, and status. A prohibited tool MUST NOT be invoked. A restricted tool MUST be used only under its declared constraints. A tool with unknown data-retention-profile MUST NOT receive confidential or restricted artifacts unless governance authorizes it. Trust profile changes MUST be audited.

### 6.10 Governance, Health, Certification, and Retirement

Tool usage may be controlled by governance policies. Governance MAY control plugin registration, plugin activation, restricted tool invocation, external tool service use, destructive operations, deployment tool invocation, security scan waiver, use of experimental plugins, use of tools on High or Critical systems, acceptance of failed or warning results, and evidence retention exceptions. A governance block MUST prevent tool invocation. An approval-required decision MUST pause tool invocation until approval exists. A waiver MUST be explicit when accepting a failed or blocking finding. Governance audit write failure MUST halt governed tool action.

Plugins SHOULD expose health checks. An unavailable plugin MUST NOT be selected. A misconfigured plugin MUST be disabled or restricted until corrected. A degraded plugin MAY be selected only when runtime policy permits.

Plugins MUST be testable before production use. A plugin used for enterprise release gates SHOULD have enterprise certification. A plugin used for regulated systems SHOULD have regulated certification. A failed certification MUST prevent activation for the certified scope. Expired certification MUST trigger revalidation or governance review.

A retired plugin MUST NOT be selected for new invocations. Historical invocation records MUST remain queryable. Retirement MUST preserve manifest, compatibility, certification, and invocation records.

---

## 7.0 Shared Output Contracts

The standard output contract types are defined once here and referenced by both agents and the model protocol. Each output contract declares its output type, required format, required fields, required traceability fields, validation rules, deterministic follow-up requirements, downstream consumers, correction/fallback/human-review behavior, and status.

### 7.1 Interpretation Output

Used by Specification Interpreter. Required fields: interpretation-id, referenced-entity-ids, interpreted-scope, assumptions, ambiguities, recommendations, confidence, requires-human-review.

### 7.2 Plan Fragment Output

Used by Architecture Planner or Construction Planner. Required fields: plan-fragment-id, referenced-entity-ids, proposed-task-ids, dependencies, expected-artifacts, validation-requirements, planning-rationale, risks.

### 7.3 Artifact Candidate Output

Used by Implementation Generator, Test Generator, or Deployment Preparer. Required fields: artifact-candidate-id, artifact-type, proposed-artifact-name, source-entity-ids, task-id, content-reference or content-payload, derivation-type, validation-expectations, assumptions, limitations.

### 7.4 Repair Proposal Output

Used by Repair Analyst. Required fields: repair-proposal-id, failed-validation-result-id, failed-artifact-ids, likely-root-cause, proposed-corrections, affected-artifacts, revalidation-required, convergence-risk, recommended-next-action.

### 7.5 Test Plan Output

Used by Test Generator. Required fields: test-plan-id, referenced-requirement-ids, referenced-interface-ids, test-scenarios, expected-results, generated-test-artifact-candidates, validation-tool-capabilities, coverage-notes.

### 7.6 Review Finding Output

Used by Review Agent. Required fields: review-finding-id, reviewed-object-id, reviewed-object-type, finding-category, severity, finding-description, evidence-reference, recommended-action, blocks-progression.

### 7.7 Security Finding Output

Used by Security Validator. Required fields: security-finding-id, affected-object-id, affected-object-type, security-category, severity, policy-reference, finding-description, recommended-action, blocks-progression, governance-required.

### 7.8 Deployment Preparation Output

Used by Deployment Preparer. Required fields: deployment-preparation-output-id, package-id or artifact-ids, target-environment, deployment-artifact-candidates, environment-assumptions, operational-readiness-findings, validation-requirements, authorization-required.

### 7.9 Failure Response Output

Used when the model cannot satisfy the request. Required fields: failure-response-id, failure-category, failure-reason, missing-context, contract-field-unavailable, recommended-next-action, retry-recommended, escalation-recommended.

### 7.10 Output Contract Rules

Every model request MUST reference an active output contract. A revoked or disabled output contract MUST NOT be used. A model response MUST be validated against the exact output contract version used in the request. A breaking contract change MUST increment major version. A response that omits required traceability fields MUST fail validation.

---

## 8.0 Error Handling

Component-specific errors specialize the common error taxonomy (ISL v0.1 §8). Each component registers a small set of extension error classes under the common categories. Every component error MUST be recorded as a structured error record using the common error record structure, with error-class, severity, message, required-action, recorded-at, target-id, and evidence.

The following extension error classes are registered by the three components:

| Error Class | Category | Default Handling |
| ----------- | -------- | ---------------- |
| agent-manifest-missing | agent | disable |
| agent-manifest-invalid | agent | disable |
| agent-required-context-missing | agent | fail invocation |
| agent-prohibited-context-present | agent | block and escalate |
| agent-output-contract-failed | agent | retry or reject |
| agent-output-role-violation | agent | reject and escalate |
| agent-artifact-candidate-invalid | agent | reject |
| agent-repair-proposal-invalid | agent | reject or escalate |
| agent-traceability-missing | agent | block downstream use |
| agent-security-context-violation | agent | block and escalate |
| protocol-request-missing-required-field | model | reject |
| protocol-context-not-bound | model | reject |
| protocol-context-policy-violation | model | block |
| protocol-connection-context-invalid | model | block |
| protocol-construction-boundary-drift | model | reject or escalate |
| protocol-reuse-decision-violation | model | reject or escalate |
| protocol-output-contract-missing | model | reject |
| protocol-governance-blocked | model | block |
| protocol-provider-timeout | model | retry |
| protocol-response-malformed | model | correct or retry |
| protocol-normalization-failed | model | correct, retry, or fallback |
| protocol-validation-failed | model | correct, retry, fallback, or escalate |
| protocol-traceability-missing | model | block downstream use |
| protocol-security-injection-detected | model | block or escalate |
| protocol-fallback-exhausted | model | escalate |
| tool-plugin-manifest-missing | tool | reject registration |
| tool-plugin-manifest-invalid | tool | reject registration |
| tool-capability-missing | tool | reject selection |
| tool-capability-validation-failed | tool | disable capability |
| tool-plugin-health-failed | tool | disable or restrict |
| tool-isolation-unavailable | tool | block invocation |
| tool-governance-blocked | tool | block |
| tool-invocation-timeout | tool | retry or fail |
| tool-output-malformed | tool | retry, escalate, or fail |
| tool-result-contract-failed | tool | escalate or fail |
| tool-evidence-capture-failed | tool | escalate |
| tool-security-boundary-violation | tool | halt and escalate |
| tool-traceability-missing | tool | block downstream use |

---

## 9.0 Conformance

Conformance to this document is evaluated against the conformance framework in ISL v0.1 §10.

### 9.1 Component Conformance

A component conforms to ISL v3.2 if it has a defined responsibility, exposes structured contracts, records lifecycle-relevant state, emits telemetry where applicable, handles errors using defined error classes, and preserves governance, traceability, artifact, and state boundaries.

### 9.2 Agent Conformance

An agent implementation conforms to ISL v3.2 if it includes a valid manifest, declares a role contract, declares supported task types and required model capabilities, declares supported output contracts, includes output validation, includes tests or a test coverage plan, contains no provider credentials or secrets, binds to models through the Model Integration Layer, produces standard runtime responses, validates outputs before downstream use, records agent state and traceability, and emits telemetry.

### 9.3 Model Protocol Conformance

A model interaction conforms to ISL v3.2 if it includes required request envelope fields, references an agent invocation, task, context package, Construction Boundary and Connection Context records when required, Reuse Decision Records when generation is reuse-constrained, an active output contract, a selected model profile and routing decision, declares sensitivity classification and timeout, includes a correlation identifier, and passes context and governance checks. A response conforms if it references the original request, identifies the model and provider, references the output contract, records response outcome, normalization status, validation status, boundary drift findings where applicable, and reuse decision compliance where applicable, declares next action, preserves correlation, and does not treat timeout or invalid output as success.

### 9.4 Tool Plugin Conformance

A plugin package conforms to ISL v3.2 if it includes a valid manifest, declares plugin type, underlying tool and version, capabilities, invocation method, output contracts, isolation and trust profiles, contains no plaintext secrets, and includes tests or validation evidence. A tool invocation conforms if it references a selected registered plugin, declares the capability invoked, declares exact artifact versions when validating artifacts, declares isolation profile and timeout, records governance check where required, returns a structured result, records findings and evidence where applicable, emits telemetry, and creates traceability links.

### 9.5 Platform Conformance

A platform conforms to ISL v3.2 if it registers components through their registries, invokes agents only through the Agent Orchestrator, routes model usage through the Model Integration Layer, integrates deterministic tools through registered plugins, prevents direct bypass of agent, model, tool, artifact, governance, traceability, and state services, validates all component outputs, prevents direct stable artifact mutation by components, and records component state, traceability, telemetry, and errors.
