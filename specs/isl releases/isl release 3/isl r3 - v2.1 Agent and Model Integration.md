# ALETHEIA Specification Language (ISL) v2.1
# Agent and Model Integration
**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.x
**Supersedes:** ISL r2 v2.1 The Agent Collaboration Model, ISL r2 v2.5 The Model Integration and Abstraction Layer
**Document Type:** Agent and Model Integration Specification

---

## 1.0 Scope

This document defines the Agent Collaboration Model and the Model Integration and Abstraction Layer for the ALETHEIA platform. It establishes the standard reasoning agent roles, agent lifecycle, invocation and output contracts, collaboration patterns, handoff rules, failure handling, and governance controls, together with the provider-neutral interface through which agents obtain model capabilities.

This document applies to reasoning agents operating under the platform architecture defined in ISL v2.0. Agents participate in specification interpretation, architecture planning, construction planning, implementation generation, repair analysis, test generation, review, security validation, and deployment preparation. Agents do not operate independently of the platform: they are invoked by the runtime or orchestrator, receive structured context, produce structured outputs, and remain subject to deterministic validation, traceability, and governance controls.

This document defines:

* standard agent roles and role contracts
* agent lifecycle, invocation inputs, and output contracts
* collaboration and handoff rules, cross-agent review, and conflict resolution
* agent failure handling, governance, traceability, and security controls
* model provider abstraction, registration, and capability declaration
* model selection, routing, invocation, and output normalization
* context control, structured output contracts, retry, and fallback
* model governance, trust profiles, lifecycle, and evidence retention
* conformance requirements

Shared conventions — normative language, field requirement levels, identifiers, shared enums, design principles, the common error model, the common telemetry model, and the conformance framework — are defined in ISL v0.1 and are referenced here rather than redefined. Construction Boundaries and Connection Contexts are authoritatively defined in ISL v1.5 and are referenced here rather than redefined.

---

## 2.0 Agent Collaboration

### 2.1 Agent Collaboration Principles

Agents are role-bounded reasoning components invoked by the platform to perform interpretation, analysis, synthesis, generation, review, repair, validation support, or deployment preparation. The following principles govern agent collaboration:

1. **Role-Bounded Reasoning** — Each agent MUST operate within the responsibilities of its assigned role. An agent MUST NOT assume authority outside its role contract unless explicitly authorized by the runtime and governance rules.
2. **Structured Inputs and Outputs** — Agents MUST receive structured inputs and MUST produce structured outputs. Free-form reasoning MAY be included as rationale, but operational outputs MUST conform to the applicable output schema.
3. **Runtime-Mediated Collaboration** — Agents MUST collaborate through the Agent Orchestrator and Execution Runtime. Agents MUST NOT directly invoke other agents, mutate platform state, modify artifacts, bypass governance, or invoke deterministic tools unless mediated by authorized platform interfaces.
4. **Deterministic Validation Boundary** — Agent outputs MUST NOT be treated as objective proof of correctness. Where objective validation is applicable, deterministic tools governed by ISL v1.2 MUST validate artifacts or claims before acceptance. Agents may propose, generate, interpret, or repair; deterministic tools validate.
5. **Traceable Reasoning** — Every agent invocation and significant agent output MUST be traceable to the task, specification entities, context package, model invocation, output record, and downstream decisions it affects.
6. **Governed Model Usage** — Agents obtain reasoning capability through model providers governed by the model abstraction boundary. Agent collaboration MUST obey model provider restrictions, context-sharing rules, risk-tier controls, and governance approvals.
7. **Failure Is Explicit** — Agent failures MUST be detected, classified, recorded, and handled through defined retry, fallback, review, repair, escalation, or halt behavior. The platform MUST NOT silently accept incomplete, malformed, contradictory, or unsupported agent outputs.

### 2.2 Standard Agent Roles

| Agent Role | Primary Responsibility |
| ---------- | ---------------------- |
| Specification Interpreter | Interprets canonical specification content for execution context |
| Architecture Planner | Produces architectural interpretation and service/data/interface design guidance |
| Construction Planner | Supports task graph generation, dependency reasoning, and plan adaptation |
| Implementation Generator | Generates implementation artifacts from task context |
| Repair Analyst | Analyzes validation failures and proposes repair actions |
| Test Generator | Generates test artifacts and validation scenarios |
| Review Agent | Reviews agent outputs, artifacts, and reasoning consistency |
| Security Validator | Reviews security, policy, access, data protection, and compliance implications |
| Deployment Preparer | Produces deployment preparation guidance and artifacts |

A platform MAY support agent roles progressively based on implementation maturity, but a platform claiming full autonomous construction capability MUST support all standard roles. Capability levels and their required roles are: advisory (Specification Interpreter, Review Agent); planning (adds Architecture Planner, Construction Planner); generation (adds Implementation Generator); repair-capable (adds Repair Analyst, Test Generator); and full-autonomous (all standard roles).

Each agent invocation MUST identify exactly one primary agent role. An agent MAY support multiple roles only if the platform treats each role invocation as a separate role-bound execution. A role-bound invocation MUST use the input and output contract for that role.

### 2.3 Agent Lifecycle

The lifecycle of an agent invocation provides a consistent structure for assigning work, assembling context, invoking the model layer, validating output, recording traceability, and returning results to the runtime.

| State | Meaning |
| ----- | ------- |
| requested | Runtime or orchestrator requested an agent invocation |
| context-assembled | Context package has been assembled |
| governance-checked | Governance checks required before invocation have passed or returned decision |
| model-invoked | Model request has been submitted through the model abstraction layer |
| output-received | Raw or structured output received from model/provider |
| output-normalized | Output converted into the expected agent output schema |
| output-validated | Output passed schema and role-contract validation |
| completed | Invocation completed successfully |
| failed | Invocation failed and cannot proceed automatically |
| escalated | Invocation requires governance or human intervention |
| cancelled | Invocation was cancelled before completion |

An agent invocation MUST NOT proceed to the model-invoked state until required context assembly and governance checks complete. An agent invocation MUST NOT be marked completed until output validation succeeds. An agent invocation with invalid output MUST enter the failed or escalated state. Lifecycle transitions MUST be recorded in telemetry, and significant transitions MUST be traceable to task and execution state.

### 2.4 Agent Invocation Contract

The common invocation contract ensures that every agent receives the task, specification, governance, traceability, and artifact context required to operate within platform boundaries. Role-specific contracts extend this base contract.

An Agent Invocation Request records agent-invocation-id, agent-role, task-id, execution-graph-id (conditional), plan-id (conditional), specification-id and version, canonical-model-version, source-entity-ids, invocation-purpose (`interpret`, `plan`, `generate`, `review`, `repair`, `test`, `security-validate`, `deploy-prepare`), context-package-id, expected-output-contract, governance-check-id (conditional), model-selection-requirements (conditional), timeout-seconds, requested-at, and requested-by.

Every agent invocation MUST reference a task-id, identify source specification entities, use a declared expected-output-contract, and use the model abstraction boundary. An agent invocation MUST NOT include unrestricted platform state unless context rules explicitly allow it.

### 2.5 Agent Context Package

Context packages are critical because agent quality depends on relevant information, but excessive or uncontrolled context creates security, privacy, cost, and reliability risks. The platform MUST assemble context according to task need, role contract, governance rules, and model provider constraints.

A Context Package records context-package-id, agent-role, task-id, specification-id and version, included-entities, included-artifacts, included-validation-results, included-repair-records, included-governance-constraints, included-prior-agent-outputs, exclusions, sensitivity-classification, context-size-metrics, assembled-at, and assembled-by.

Context inclusion rules:

* The context package MUST include the canonical entities required to satisfy the task.
* The context package SHOULD include only the minimum relevant artifacts and records needed by the role.
* The context package MUST include applicable governance constraints when they affect output.
* The context package MUST include failed validation results when invoking the Repair Analyst, and security policies when invoking the Security Validator.
* The context package MUST NOT include secrets unless explicitly permitted by governance and task requirements.

Context exclusions MUST be recorded when relevant information is withheld due to governance, sensitivity, provider restriction, or risk tier. An agent output MUST NOT be accepted if the context package omitted required information for the role contract. A context package MUST be immutable after invocation begins; corrections require a new context package and new invocation.

### 2.6 Agent Output Contract

The common output contract ensures that agent responses can be normalized, validated, traced, reviewed, and routed by platform components. Role-specific outputs extend this base schema.

A Base Agent Output records agent-output-id, agent-invocation-id, agent-role, task-id, output-type (`interpretation`, `plan-fragment`, `artifact-content`, `repair-proposal`, `test-plan`, `review-finding`, `security-finding`, `deployment-preparation`), summary, structured-output, produced-artifact-candidates, referenced-entities, referenced-artifacts, assumptions, constraints-applied, confidence (`high`, `medium`, `low`, `unknown`), requires-review, requires-deterministic-validation, produced-at, and output-status (`valid`, `invalid`, `partial`, `failed`, `escalated`).

An agent output MUST conform to the declared expected-output-contract, reference the invocation that produced it and the task it supports, and identify whether deterministic validation is required. An output containing artifact content MUST NOT be accepted as a stable artifact until it enters the Artifact Repository and passes required validation. An output marked partial MUST NOT be consumed as completed work unless the downstream contract explicitly accepts partial output.

### 2.7 Agent Output Validation

Agent output validation is distinct from deterministic artifact validation. It checks whether the output satisfies the expected structure, role contract, task relevance, and governance constraints.

The Agent Orchestrator MUST validate: output schema compliance; required fields; role and task compatibility; referenced entity and artifact validity; prohibited content or unsupported actions; governance constraint compliance; output completeness; the deterministic validation requirement flag; and structured-output payload validity.

An Output Validation Result records agent-output-validation-id, agent-output-id, agent-invocation-id, validation-outcome (`passed`, `failed`, `warning`, `escalated`), findings, validated-at, validated-by, and next-action (`accept`, `retry`, `request-revision`, `cross-agent-review`, `escalate`, `reject`).

An output that fails schema validation or references undefined entities MUST NOT be accepted. An output that proposes actions outside the role contract MUST be rejected or escalated. An output that conflicts with governance constraints MUST be escalated or rejected according to the governance response. A valid agent output MAY still require deterministic validation before artifact acceptance.

### 2.8 Communication and Handoff Model

Agents communicate indirectly through the platform by producing structured outputs that are stored, validated, traced, and then supplied to other agents or subsystems as context. Agents MUST NOT communicate through hidden channels, direct unmanaged calls, unrecorded messages, or implicit shared memory.

A Handoff Record captures handoff-id, from-agent-role, to-agent-role (conditional), from-task-id, to-task-id (conditional), agent-output-id, handoff-type (`sequential`, `review`, `repair`, `validation-support`, `escalation-support`, `planning-support`), handoff-purpose, required, accepted-by-target, created-at, and created-by.

Every agent-to-agent handoff MUST be mediated by the Agent Orchestrator. A handoff MUST reference a validated agent output. A required handoff MUST be satisfied before the dependent task can proceed. A handoff rejected by the target role MUST produce a collaboration finding or escalation. Handoffs MUST be traceable.

### 2.9 Collaboration Patterns

Standard collaboration patterns describe how agent roles cooperate across construction tasks and execution phases. A platform MAY implement additional patterns, but standard patterns MUST preserve the control, traceability, and validation rules defined here.

* **Sequential Handoff** — One agent's output becomes the input to a later agent (e.g., Specification Interpreter → Architecture Planner → Implementation Generator). Sequential handoff MUST preserve the upstream output, the downstream context package, and the handoff record.
* **Review and Challenge** — One agent reviews another agent's output and produces findings, objections, or validation support. Review outputs MUST identify the reviewed output and any findings.
* **Repair Collaboration** — Validation failure causes the Repair Analyst to analyze findings and provide repair guidance to the Implementation Generator. Repair collaboration MUST include the failed validation result, failed artifact, prior repair history, and applicable repair limits.
* **Security Review Collaboration** — The Security Validator evaluates specification entities, planned tasks, generated artifacts, interface contracts, data handling, or deployment outputs. It MUST include applicable Policy entities and governance constraints.
* **Planning Feedback Collaboration** — Execution, review, security validation, or repair feedback requires the Construction Planner to adapt the construction plan. It MUST preserve the feedback source and trace resulting plan changes.
* **Deployment Readiness Collaboration** — Validated artifacts, infrastructure expectations, operational requirements, security findings, and governance constraints are supplied to the Deployment Preparer. It MUST preserve deployment-related traceability to Infrastructure and Policy entities.

### 2.10 Agent Role Contracts

Each standard agent role MUST have a role contract. A Role Contract records role-name, role-purpose, responsibilities, required-inputs, permitted-outputs, prohibited-actions, output-contracts, validation-requirements, downstream-consumers, failure-modes, and escalation-conditions.

An agent invocation MUST be validated against its role contract. An output outside the role's permitted outputs MUST be rejected or escalated. A role contract MUST NOT grant authority to bypass runtime, tool, traceability, or governance controls.

The following subsections define the standard role contracts. Each contract lists responsibilities, required inputs, permitted outputs, prohibited actions, and failure handling.

#### 2.10.1 Specification Interpreter

The Specification Interpreter converts canonical specification content into execution-oriented interpretation records, clarifying how entities, relationships, assumptions, constraints, and ambiguity conditions should be understood for planning and execution.

Responsibilities: interpret canonical entities for execution context; identify ambiguity, execution-relevant assumptions, and policy constraints; produce interpretation records; and recommend clarification needs.

Required inputs: canonical model version, Project entity, source entity identifiers, relevant Context entities, readiness record, governance constraints (conditional), and prior interpretation records (conditional).

Permitted outputs: `interpretation`, `review-finding`, `escalation-support`. The structured payload records interpreted-entities, execution-assumptions, ambiguity-findings, policy-constraints, clarification-required, and recommended-next-action (`proceed`, `review`, `clarify`, `escalate`).

Prohibited actions: MUST NOT generate implementation artifacts, modify the canonical model, approve readiness transitions, waive ambiguity findings, or bypass governance approval.

Failure handling: missing required context → fail and request corrected context; unresolvable ambiguity → escalate; invalid output schema → retry or request revision; interpretation conflicting with canonical model → reject and escalate.

#### 2.10.2 Architecture Planner

The Architecture Planner interprets the canonical model into architectural structure, reasoning about service boundaries, data ownership, interface relationships, deployment implications, and architectural constraints.

Responsibilities: propose service decomposition guidance; identify data ownership boundaries, interface responsibilities, architectural dependencies, and architectural risks; recommend artifact grouping or module structure; and produce architecture decision records.

Required inputs: canonical model version; Service, DataEntity, Interface, Workflow, and Requirement entities; Context and Policy entities (conditional); prior interpretation records; and governance constraints (conditional).

Permitted outputs: `plan-fragment`, `review-finding`, `interpretation`, `escalation-support`. The structured payload records service-boundary, data-ownership, interface, and dependency findings; architecture-decisions; risks; recommended-plan-updates; and recommended-next-action (`proceed`, `review`, `replan`, `escalate`).

Prohibited actions: MUST NOT create executable tasks directly without Planning Engine mediation, generate implementation artifacts, override canonical requirements, approve architecture review gates, or bypass governance constraints.

Failure handling: conflicting architecture interpretation → cross-agent review; missing service/data/interface context → fail and request context correction; architecture violates policy → escalate to Governance Engine; invalid output schema → retry or request revision.

#### 2.10.3 Construction Planner

The Construction Planner supports creation, validation, and adaptation of the Construction Task Graph, reasoning about task decomposition, dependency ordering, verification coverage, repair insertion, and impact-based replanning. It assists the Planning Engine but does not replace Planning Engine validation.

Responsibilities: recommend task decomposition, dependency relationships, and verification task insertion; identify planning gaps; support critical path analysis and plan adaptation; identify affected tasks after change impact analysis; and produce planning findings.

Required inputs: canonical model version; Construction Task Graph (conditional); planning configuration; traceability state; impact analysis result (conditional); governance constraints (conditional); and tool capability catalog (conditional).

Permitted outputs: `plan-fragment`, `review-finding`, `escalation-support`. The structured payload records proposed-tasks, proposed-dependencies, verification-recommendations, planning-gaps, affected-task-recommendations, and recommended-next-action (`proceed`, `revise-plan`, `validate-plan`, `escalate`).

Prohibited actions: MUST NOT mark a plan executable, bypass Planning Engine validation or readiness restrictions, directly execute tasks, or directly create artifacts.

Failure handling: circular dependency proposed → reject proposed output; unresolved source entity reference → fail output validation; missing verification coverage → return planning finding; governance constraint conflict → escalate.

#### 2.10.4 Implementation Generator

The Implementation Generator produces candidate implementation artifacts based on construction tasks, canonical entities, architecture guidance, prior artifacts, and validation constraints. Its outputs remain candidates until repository admission, traceability association, and deterministic validation occur.

Responsibilities: generate source, configuration, schema, interface implementation, and documentation artifacts; modify artifacts during repair when instructed; and produce implementation rationale.

Required inputs: task definition; source entity identifiers; relevant canonical entities; architecture guidance (conditional); existing artifact context (conditional); Reuse Decision Record and Reusable Asset context (conditional when reuse, wrapping, extension, composition, or delta generation applies); validation criteria; governance constraints (conditional); and repair proposal (conditional for repair-generated implementation).

Permitted outputs: `artifact-content`, `review-finding`, `repair-proposal`. The structured payload records artifact-candidates, artifact-type (`source`, `config`, `schema`, `infrastructure`, `contract`, `test`, `documentation`), source-entity-links, implementation-summary, assumptions, dependencies-introduced, reused-asset-links, delta-generation-rationale, validation-expectations, and requires-deterministic-validation (MUST be true for executable or structural artifacts).

Prohibited actions: MUST NOT mark artifacts valid, write directly to stable artifact repository state without runtime mediation, introduce behavior not traceable to specification entities or approved inference, generate a full implementation when a Reuse Decision Record authorizes full reuse, ignore a Reuse Decision Record that limits generation to delta/wrapper/extension/composition logic, bypass security or policy constraints, perform deployment authorization, or silently change requirements or policies.

Failure handling: output artifact lacks source links → reject; artifact content missing or malformed → request revision or retry; generated behavior exceeds specification → escalate or reject; generated artifact violates policy → route to Security Validator or Governance Engine; repeated invalid outputs → escalate.

#### 2.10.5 Repair Analyst

The Repair Analyst analyzes deterministic validation failures, test failures, policy findings, or runtime defects and proposes corrective action. It does not itself declare repaired artifacts valid. Repair analysis MUST remain bounded by repair termination rules defined in ISL v1.2 and v1.5.

Responsibilities: analyze validation findings; identify likely root causes; propose artifact corrections; recommend repair scope; recommend whether repair should proceed or escalate; produce repair records; and identify non-converging repair patterns.

Required inputs: failed validation result, failed artifact identifier, failed task identifier, tool findings (when tool-triggered), test results (conditional), prior repair records (conditional), source specification entities, repair limits, and governance constraints (conditional).

Permitted outputs: `repair-proposal`, `review-finding`, `escalation-support`. The structured payload records failure-summary, likely-root-cause, repair-scope (`artifact`, `task`, `service`, `interface`, `schema`, `policy`, `plan`), proposed-corrections, affected-artifacts, revalidation-required (MUST be true when artifact changes), convergence-risk, and recommended-next-action (`repair`, `replan`, `escalate`, `halt`).

Prohibited actions: MUST NOT mark repair successful without revalidation, exceed repair iteration limits, modify artifacts directly without runtime-mediated task assignment, waive validation failures, suppress tool findings, or bypass governance escalation.

Failure handling: root cause cannot be determined → escalate or request review; repair would exceed limits or violate policy → escalate; repeated same failure → escalate after threshold; invalid repair proposal schema → retry or request revision.

#### 2.10.6 Test Generator

The Test Generator produces test artifacts, test scenarios, validation support, and acceptance checks derived from Requirement, Workflow, Interface, Policy, and Validation entities. It supports validation coverage but does not decide whether the system is accepted; test execution and policy evaluation determine outcomes.

Responsibilities: generate unit, integration, acceptance, contract, and security test candidates (security when policy-driven); map tests to Validation entities; and identify validation coverage gaps.

Required inputs: Requirement and Validation entities; Workflow, Interface, Policy entities (conditional); generated artifacts (conditional); test framework constraints (conditional); and governance constraints (conditional).

Permitted outputs: `test-plan`, `artifact-content`, `review-finding`. The structured payload records test-candidates, validates, test-type (`unit`, `integration`, `acceptance`, `contract`, `security`, `operational`), execution-requirements, coverage-gaps, expected-evidence, and recommended-next-action (`proceed`, `add-tests`, `revise-requirement`, `escalate`).

Prohibited actions: MUST NOT mark tests passed, mark requirements satisfied, waive missing validation coverage, modify requirements to fit generated tests, or bypass deterministic test execution.

Failure handling: requirement not testable → return coverage finding; missing validation entity → return planning or readiness finding; unsupported test framework → escalate or request tool capability; generated test invalid → retry or request revision.

#### 2.10.7 Review Agent

The Review Agent evaluates agent outputs, candidate artifacts, plans, repair proposals, and reasoning consistency. It provides structured findings but does not replace deterministic validation or governance approval.

Responsibilities: review agent outputs for completeness and consistency; review candidate artifacts for specification alignment; review task outputs before downstream handoff; detect missing traceability references, unsupported assumptions, and role contract violations; and recommend cross-agent review or escalation.

Required inputs: agent output under review; task definition; source specification entities; applicable output contract; validation criteria (conditional); governance constraints (conditional); and related artifacts (conditional).

Permitted outputs: `review-finding`, `escalation-support`, `interpretation`. The structured payload records reviewed-output-id, review-outcome (`accepted`, `accepted-with-warning`, `rejected`, `escalate`), findings, specification-alignment, traceability-completeness, contract-compliance, and recommended-next-action (`accept`, `revise`, `revalidate`, `cross-review`, `escalate`, `reject`).

Prohibited actions: MUST NOT approve governance gates, mark artifacts valid without deterministic validation, suppress policy findings, modify outputs directly, or override role contract violations.

Failure handling: reviewed output missing → fail invocation; conflicting review results → conflict resolution; insufficient context → request corrected context; review identifies governance issue → escalate.

#### 2.10.8 Security Validator

The Security Validator evaluates security, compliance, data protection, access control, dependency, interface exposure, tool/model usage, and policy implications of specifications, plans, artifacts, and deployment outputs. It supports governance and deterministic security tooling but does not replace required security tools or authorized security review gates.

Responsibilities: review Policy entity coverage; review authentication/authorization implications, sensitive data handling, interface exposure risks, dependency and supply-chain risks, and generated artifacts for security concerns; interpret security tool findings; and recommend escalation, waiver, repair, or additional validation.

Required inputs: Policy entities; security-related requirements; DataEntity sensitivity classifications (conditional); interface definitions (conditional); generated artifacts (conditional); security tool findings (conditional); governance risk tier; and model/tool trust constraints (conditional).

Permitted outputs: `security-finding`, `review-finding`, `repair-proposal`, `escalation-support`. The structured payload records security-findings, affected-targets, severity (`critical`, `high`, `medium`, `low`, `informational`), policy-references, recommended-response (`proceed`, `repair`, `escalate`, `block`, `waive-review`), evidence, and residual-risk.

Prohibited actions: MUST NOT waive security findings, approve security review gates unless acting under a governance role outside agent context, suppress tool findings, mark security validation passed without evidence, or bypass governance risk-tier rules.

Failure handling: policy context missing → fail and request context correction; critical finding detected → escalate or block according to policy; security tool findings conflict → conflict resolution or governance review; risk cannot be assessed → escalate.

#### 2.10.9 Deployment Preparer

The Deployment Preparer produces deployment preparation guidance and artifacts based on validated implementation outputs, Infrastructure entities, operational requirements, governance constraints, and deployment target profiles. It prepares for deployment authorization but does not authorize deployment.

Responsibilities: generate deployment artifact, manifest, runtime configuration template, and health check candidates; identify operational readiness gaps and deployment validation requirements; and produce deployment preparation records.

Required inputs: consolidated artifact set; Infrastructure entities; operational requirements; deployment target profile; validation results; policy evaluations (conditional); governance constraints; and traceability snapshot.

Permitted outputs: `deployment-preparation`, `artifact-content`, `review-finding`, `escalation-support`. The structured payload records deployment-artifact-candidates, target-environments, runtime-configuration, health-checks, operational-readiness-findings, deployment-validation-requirements, governance-dependencies, and recommended-next-action (`proceed-to-validation`, `revise`, `escalate`, `block`).

Prohibited actions: MUST NOT authorize production deployment, bypass deployment authorization, include invalid or untraced artifacts in stable deployment outputs, ignore unresolved blocking policy violations, or change Infrastructure requirements without traceable reauthorization.

Failure handling: deployment target missing → fail invocation; consolidated artifacts incomplete → block deployment preparation; unresolved policy violation → escalate; deployment artifact invalid → route to validation or repair.

### 2.11 Cross-Agent Review and Conflict Resolution

Agents are probabilistic reasoning components; therefore, the platform must define how conflicting conclusions are detected and resolved.

Conflict types: role-conflict (output exceeds or contradicts role authority); semantic-conflict (output contradicts canonical model or specification entity); artifact-conflict (multiple outputs propose incompatible artifact changes); architecture-conflict; repair-conflict; security-conflict; plan-conflict; and governance-conflict.

An Agent Conflict Record captures conflict-id, conflict-type, involved-output-ids, involved-agent-roles, task-id, affected-entities, affected-artifacts, severity, resolution-method (`reviewer-decision`, `deterministic-validation`, `governance-review`, `re-invocation`, `replan`, `halt`), resolution, and resolved-at.

Conflict resolution rules:

* Semantic conflicts with the canonical model MUST be resolved in favor of the canonical model unless the specification is formally changed.
* Security conflicts MUST be routed to the Security Validator and Governance Engine when policy-relevant.
* Artifact conflicts MUST be resolved before artifact repository admission.
* Plan conflicts MUST be routed to the Construction Planner or Planning Engine.
* Governance conflicts MUST be resolved by the Governance Engine.
* A blocking conflict MUST prevent downstream task completion until resolved.

### 2.12 Agent Failure Model

Standard agent failure classes and default handling:

| Failure Class | Default Handling |
| ------------- | ---------------- |
| agent-context-missing / agent-context-invalid | fail and request corrected context |
| agent-timeout | retry once or escalate |
| agent-output-empty | retry or fail |
| agent-output-schema-invalid | retry or request revision |
| agent-output-role-violation | reject and escalate if material |
| agent-output-untraceable | reject |
| agent-output-contradicts-specification | reject or escalate |
| agent-output-policy-violation | escalate or block |
| agent-model-invocation-failed | retry, fallback, or escalate |
| agent-nonconvergent | escalate |
| agent-conflict-unresolved | escalate or halt |

An Agent Failure Record captures failure-id, failure-class, agent-role, agent-invocation-id, task-id, severity, message, required-action, retry-count, and recorded-at.

An agent invocation MAY be retried when failure is caused by timeout, transient model failure, empty output, or schema-invalid output. An agent invocation MUST NOT be retried indefinitely. The default retry limit SHOULD be two retries per invocation unless runtime configuration specifies a stricter limit. A repeated failure after the retry limit MUST escalate.

The platform MUST escalate when: output contradicts the canonical specification and cannot be resolved; output violates policy; repeated retries fail; required context cannot be assembled; a role conflict is material; an agent conflict remains unresolved; or governance requires human review.

### 2.13 Collaboration State

A Collaboration Session records collaboration-session-id, task-id, execution-graph-id (conditional), participating-agent-roles, invocation-ids, output-ids, handoff-ids, conflict-ids, failure-ids, status (`pending`, `active`, `completed`, `failed`, `escalated`, `cancelled`), created-at, and updated-at.

A collaboration session MUST be created when a task requires more than one agent invocation. It MUST record all invocations and handoffs. It MUST NOT be marked completed while blocking conflicts or failures remain unresolved. Collaboration state MUST be persisted when required for execution recovery.

### 2.14 Governance of Agent Collaboration

Governance MAY control: model provider selection; use of external model providers; restricted context disclosure; high-risk artifact generation; security validation outputs; repair of high-risk artifacts; deployment preparation; override of agent findings; use of experimental agents; and cross-agent conflict resolution.

An agent invocation MUST NOT proceed when governance returns `block`. An agent invocation requiring approval MUST pause until approval is granted. Agent outputs that propose waivers, overrides, or policy exceptions MUST be routed to the Governance Engine. Agents MUST NOT approve governance gates in their agent capacity. Agent outputs affecting High or Critical risk systems MAY require review before use according to governance configuration.

### 2.15 Traceability Requirements for Agents

The platform MUST record traceability links between: task and agent invocation; agent invocation and context package; agent invocation and model invocation; agent invocation and agent output; agent output and source specification entities; agent output and produced artifact candidates; agent output and downstream handoff; agent output and review findings; agent output and deterministic validation results where applicable; agent failure and affected task; and agent conflict and involved outputs.

A Decision Record MUST be created when an agent output materially affects architecture, implementation generation, repair direction, security response, deployment preparation, escalation, or governance recommendation. Decision Records MUST follow ISL v1.4.

An agent output that cannot be traced to source entities and task context MUST be rejected. A task MUST NOT be marked completed if required agent traceability is missing.

### 2.16 Security and Context Protection for Agents

The platform MUST enforce context minimization, classify context sensitivity, restrict secret exposure, enforce model provider restrictions, prevent direct agent access to unauthorized repositories or state stores, prevent agents from bypassing tool validation, record context packages used for invocations, apply governance policies before restricted model or context use, and sanitize or structure agent outputs before downstream processing.

Agent context assembly MUST distinguish trusted platform instructions from untrusted specification content, artifact content, logs, tool output, or external input. Untrusted content MUST NOT be allowed to override platform instructions, governance rules, output contracts, or role boundaries. If a context package contains untrusted content that attempts to change agent role, bypass validation, reveal secrets, or override governance, the platform MUST record a security finding and MAY sanitize, exclude, or escalate the content.

Restricted or confidential context MUST be provided only to model providers and agents authorized for that sensitivity level. Agent outputs derived from restricted context MUST inherit appropriate sensitivity metadata unless downgraded by an approved governance process.

### 2.17 Agent Extension Model

New agent roles MAY be introduced through approved extensions. Extensions must not weaken core platform controls.

An Agent Role Extension records extension-role-id, role-name, role-purpose, responsibilities, required-inputs, permitted-outputs, prohibited-actions, output-contracts, governance-profile (conditional), compatibility, registered-at, and registered-by.

An agent extension MUST declare a role contract, MUST NOT redefine standard roles, MUST NOT bypass model abstraction, traceability, deterministic validation, or governance controls, and MUST be registered before use. Governance MAY restrict extension roles by risk tier, environment, or project.

---

## 3.0 Model Integration and Abstraction

### 3.1 Model Integration Principles

The Model Integration and Abstraction Layer isolates the platform and its agents from dependency on any specific model provider, model runtime, model architecture, or model API. It is the controlled boundary through which agents request reasoning services.

The following principles govern model integration:

1. **Provider Neutrality** — Agents MUST interact with models only through the Model Integration and Abstraction Layer. Agents MUST NOT directly invoke local runtimes, remote APIs, provider SDKs, or provider-specific prompt interfaces.
2. **Local-First Operation** — The platform MUST be capable of operating with locally hosted or locally available models where the deployment profile requires local-first operation. External models MAY be used only when governance and configuration permit them.
3. **Explicit Capability Declaration** — Model capabilities MUST be declared explicitly. The platform MUST NOT infer capabilities from model names, provider branding, marketing descriptions, or informal labels.
4. **Capability-Based Routing** — The platform MUST select models based on declared capabilities, task requirements, agent role, context requirements, governance constraints, and runtime policy.
5. **Context Minimization** — The platform MUST provide models only the context required for the invocation. Context assembly MUST respect sensitivity, governance, risk tier, and provider restrictions.
6. **Structured Output Preference** — Model invocations SHOULD use structured output contracts when downstream platform components will interpret the result automatically.
7. **Runtime-Governed Model Use** — Restricted model use MUST be governed at runtime. A model invocation MUST NOT proceed when governance returns `block`, `approval-required`, `waiver-required`, `override-required`, or `escalate`.
8. **Traceable Reasoning** — Every model invocation that contributes to planning, generation, review, repair, security validation, deployment preparation, governance support, or artifact creation MUST be traceable.

### 3.2 Model Integration Architecture

The Model Integration and Abstraction Layer is the controlled interface between agents and model providers. Its logical components are:

| Component | Responsibility |
| --------- | -------------- |
| Model Registry | Stores provider records, model profiles, capabilities, and lifecycle state |
| Provider Adapter Registry | Stores provider adapter records and supported invocation methods |
| Model Router | Selects models for invocations |
| Capability Evaluator | Validates model capability compatibility with requested work |
| Context Controller | Enforces context selection, minimization, sensitivity, and redaction |
| Invocation Manager | Executes model invocations through provider adapters |
| Output Normalizer | Converts provider responses into standard result structures |
| Policy Enforcer | Applies governance and model-use restrictions |
| Fallback Manager | Selects alternate models when permitted |
| Telemetry Emitter | Emits model interaction telemetry |
| Traceability Connector | Records model invocation links to tasks, agents, outputs, and decisions |

All model invocations MUST pass through the Model Integration Layer. Provider-specific credentials, SDKs, endpoints, and runtime details MUST be isolated inside provider adapters or controlled configuration. The Model Router MUST NOT select models that are not registered, not active, or not permitted by governance. The Output Normalizer MUST validate model output before downstream platform use.

### 3.3 Model Provider Types

| Provider Type | Description |
| ------------- | ----------- |
| local-runtime | Model hosted locally on the same machine or local network |
| embedded-runtime | Model embedded directly inside a platform process or component |
| enterprise-service | Organization-managed model service |
| private-cloud-service | Cloud-hosted service under enterprise control |
| public-cloud-api | External public model provider API |
| specialized-service | Domain-specific model service, such as code, security, test, or document reasoning |
| brokered-provider | Provider accessed through an internal model gateway or broker |

A provider MUST declare its provider type. A public-cloud-api provider MUST be governed before use. A local-runtime provider SHOULD be preferred when local-first mode is active and capability requirements are satisfied. A specialized-service provider MAY be selected when it satisfies a task-specific capability better than a general-purpose model.

### 3.4 Model Provider Registration

A provider MUST be registered before any model associated with that provider may be invoked.

A Provider Record captures provider-id, provider-name, provider-type, adapter-id, deployment-location (`local`, `on-premises`, `private-cloud`, `public-cloud`, `hybrid`), network-access-required, data-residency-profile (conditional), supported-model-ids, authentication-profile-id (conditional), trust-profile-id, status (`proposed`, `active`, `restricted`, `disabled`, `deprecated`, `revoked`), registered-at, and registered-by.

A provider MUST NOT be used unless status is active or restricted with satisfied conditions. A revoked provider MUST NOT be used. Provider registration MUST be auditable. Provider configuration changes affecting routing, trust, data exposure, authentication, or location MUST be recorded as governance-relevant configuration changes.

### 3.5 Provider Adapter Model

Provider adapters translate platform model invocation requests into provider-specific calls.

A Provider Adapter Record captures adapter-id, provider-type-supported, adapter-version, invocation-method (`local-call`, `cli`, `http-api`, `grpc`, `sdk`, `message-bus`), supported-request-version, supported-response-version, supports-streaming, supports-structured-output, supports-tool-use, supports-context-cache, status (`active`, `deprecated`, `disabled`, `revoked`), and registered-at.

An adapter MUST normalize provider responses into the platform Model Invocation Result schema, MUST NOT expose provider-specific response formats directly to agents, MUST record provider error details in structured error records, and MUST enforce timeout and cancellation where supported by the provider.

### 3.6 Model Profile

A model MUST have a Model Profile before routing.

A Model Profile captures model-profile-id, model-id, provider-id, model-family (conditional), model-version, model-type (`general`, `coding`, `reasoning`, `review`, `security`, `planning`, `embedding`, `classification`, `multimodal`), supported-agent-roles, declared-capabilities, context-window-tokens (conditional), max-output-tokens (conditional), supports-json-output, supports-markdown-output, supports-function-calling, supports-vision-input, supports-file-input, latency-profile, cost-profile, data-retention-profile (`none`, `transient`, `provider-retained`, `unknown`), trust-profile-id, status (`active`, `restricted`, `experimental`, `deprecated`, `disabled`, `revoked`), registered-at, and registered-by.

A model MUST NOT be routed unless its profile status permits use. A model with experimental status MAY be used only when governance permits experimental model use for the risk tier and environment. A model with unknown data-retention-profile MUST NOT receive confidential or restricted context unless governance explicitly authorizes it.

### 3.7 Model Capability Declaration

Model capabilities MUST be declared explicitly. Standard capability categories are: reasoning (requirement-interpretation, architecture-reasoning, planning-reasoning, impact-analysis-reasoning); generation (code-generation, schema-generation, interface-generation, test-generation, documentation-generation); review (artifact-review, plan-review, requirement-review, traceability-review); repair (failure-analysis, repair-proposal, repair-risk-assessment); security (security-review, policy-interpretation, threat-analysis, data-protection-review); deployment (deployment-preparation, infrastructure-reasoning, operational-readiness-review); structured-output (json-output, schema-constrained-output, table-output, decision-record-output); context (long-context, multi-document-context, artifact-context, validation-context); multimodal (image-understanding, diagram-understanding, document-vision); and classification (routing-classification, risk-classification, sensitivity-classification).

A Capability Declaration records capability-id, capability-name, capability-category, capability-level (`unsupported`, `basic`, `standard`, `advanced`, `expert`), confidence-basis (`vendor-declared`, `benchmarked`, `validated`, `manually-approved`, `inferred`), supported-agent-roles, supported-input-types, supported-output-contracts, context-requirements, limitations, validation-record-id, and last-validated-at.

A model MUST NOT claim a capability without a capability declaration. A capability with confidence-basis `inferred` MUST NOT be used for High or Critical risk systems unless governance approves. A capability required for artifact generation SHOULD be validated before use in Autonomous-Ready execution. A capability required for security validation MUST be validated or manually approved before use in High or Critical risk systems.

### 3.8 Capability Validation

Capability validation determines whether a model's declared capability may be trusted for routing. Validation methods include provider-declaration, platform-benchmark, role-contract-test, historical-performance, governance-approval, manual-evaluation, and external-certification.

A Capability Validation Record captures validation-record-id, model-profile-id, capability-name, validation-method, validation-scope, validation-outcome (`passed`, `failed`, `warning`, `expired`, `not-evaluated`), evidence, evaluator, evaluated-at, and expires-at.

A failed capability validation MUST prevent routing for that capability. An expired capability validation MUST NOT satisfy a routing requirement unless governance permits temporary use. Capability validation evidence SHOULD be retained for audit. A model selected for a role MUST satisfy every required capability for that role invocation.

### 3.9 Agent Role Model Requirements

Default role capability requirements:

| Agent Role | Required Capabilities |
| ---------- | --------------------- |
| Specification Interpreter | requirement-interpretation, long-context, schema-constrained-output |
| Architecture Planner | architecture-reasoning, planning-reasoning, decision-record-output |
| Construction Planner | planning-reasoning, impact-analysis-reasoning, table-output |
| Implementation Generator | code-generation, schema-constrained-output, artifact-context |
| Repair Analyst | failure-analysis, repair-proposal, validation-context |
| Test Generator | test-generation, requirement-interpretation, schema-constrained-output |
| Review Agent | artifact-review, traceability-review, decision-record-output |
| Security Validator | security-review, policy-interpretation, data-protection-review |
| Deployment Preparer | deployment-preparation, infrastructure-reasoning, operational-readiness-review |

An agent invocation MUST declare required model capabilities. The Model Router MUST reject models that do not satisfy required capabilities. A model MAY be selected for a role only if the role is listed in supported-agent-roles or governance authorizes exceptional use. A role requiring structured output MUST select a model that supports the required output contract or select an adapter capable of reliable output normalization.

### 3.10 Model Selection and Routing

The Model Router MUST consider: agent role; task type; required capabilities; required output contract; context size; input type; sensitivity classification; risk tier; provider and model trust profiles; local-first policy; latency and cost constraints; availability; fallback policy; governance restrictions; and historical performance where available.

A Routing Decision records routing-decision-id, agent-invocation-id, task-id, specification-id and version, required-capabilities, candidate-models, rejected-models, selected-model-profile-id, selected-provider-id, routing-policy-id, selection-rationale, governance-check-id (conditional), decided-at, and decided-by.

The router MUST NOT select a model that lacks required capabilities, whose provider is revoked/disabled/prohibited, or whose trust profile forbids the context sensitivity or risk tier. When local-first mode is active, the router MUST prefer local models that satisfy required capabilities, unless governance or routing policy permits remote selection. When multiple models satisfy requirements, the router MUST apply deterministic tie-breaking rules. A routing decision MUST be recorded for every model invocation.

### 3.11 Routing Policies

A Routing Policy records routing-policy-id, policy-name, scope (`global`, `project`, `specification`, `agent-role`, `risk-tier`, `environment`), priority-order, local-first, remote-allowed, restricted-context-allowed, fallback-allowed, max-cost-profile, max-latency-profile, required-trust-level, active-from, and status (`draft`, `active`, `superseded`, `revoked`).

Unless overridden by governance, routing SHOULD prioritize: governance permission; required capability satisfaction; context sensitivity compatibility; local-first preference; role suitability; output contract compatibility; latency profile; cost profile; and historical success rate.

Routing policies MUST be versioned. Routing policies affecting High or Critical systems MUST be governed. A revoked routing policy MUST NOT be used. A routing policy change during active execution MUST trigger impact evaluation when it changes model selection behavior.

### 3.12 Model Invocation Contract

A Model Invocation Request records model-invocation-id, agent-invocation-id, agent-role, task-id, execution-graph-id (conditional), specification-id and version, selected-model-profile-id, selected-provider-id, routing-decision-id, context-package-id, input-format (`text`, `markdown`, `json`, `yaml`, `code`, `multimodal`, `mixed`), output-contract-id, invocation-mode (`synchronous`, `asynchronous`, `streaming`, `batch`), parameters, timeout-seconds, sensitivity-classification, governance-check-id (conditional), requested-at, and requested-by.

A model invocation MUST reference an agent invocation, a routing decision, a context package, and an output contract. A model invocation MUST NOT proceed when governance blocks the selected provider, model, context, or task.

### 3.13 Model Invocation Result Contract

A Model Invocation Result records model-invocation-result-id, model-invocation-id, agent-invocation-id, selected-model-profile-id, selected-provider-id, outcome (`completed`, `failed`, `timeout`, `cancelled`, `blocked`, `normalized`, `invalid-output`), raw-output-reference, normalized-output-reference, output-validation-status (`not-evaluated`, `passed`, `failed`, `warning`), findings, token-usage, duration-ms, provider-status, completed-at, and next-action (`accept`, `normalize`, `retry`, `fallback`, `escalate`, `reject`, `halt`).

A timeout MUST NOT be treated as completed. An invalid-output outcome MUST NOT be accepted for downstream agent output use. A result with output-validation-status `failed` MUST trigger retry, fallback, escalation, or rejection. Raw output retention MUST follow governance and sensitivity rules.

### 3.14 Context Control

The Model Integration Layer MUST enforce context rules before invocation. Before invocation, the Context Controller MUST verify: the context-package-id exists and was assembled for the requesting agent role; the active Construction Boundary is identified where required; required Connection Contexts are valid where boundary crossing occurs; Reuse Decision Records constrain generation mode where applicable; context sensitivity is compatible with the selected provider and model; context size fits selected model limits; required entities and records are present; excluded context is documented; secrets are not present unless explicitly authorized; untrusted content is marked or isolated; governance constraints are included; provider data retention profile is compatible with context sensitivity; and context is minimized for the selected model profile, especially local or resource-constrained models.

A Context Control Record captures context-control-id, context-package-id, model-invocation-id, selected-model-profile-id, construction-boundary-id (conditional), connection-context-ids (conditional), reuse-decision-id (conditional), sensitivity-classification, control-outcome (`passed`, `failed`, `warning`, `redacted`, `escalated`), redactions-applied, findings, evaluated-at, and evaluated-by.

Context control MUST pass before model invocation. A context package containing unauthorized secrets MUST block invocation. A restricted context package MUST NOT be routed to a model or provider whose trust profile disallows restricted context. An invocation that crosses a Construction Boundary MUST NOT proceed unless valid Connection Contexts authorize the crossing. An invocation constrained by a Reuse Decision Record MUST NOT request full artifact generation unless the decision authorizes full generation. For local or resource-constrained model profiles, the Context Controller SHOULD prefer minimized summaries, selectors, and reusable asset metadata over broad artifact history whenever those representations satisfy the role contract. If redaction changes required context, the invocation MUST be rejected or escalated unless the role contract accepts redacted context.

### 3.15 Structured Interaction and Output Contracts

Structured outputs allow platform components to consume model responses safely.

An Output Contract records output-contract-id, contract-name, contract-version, applies-to-agent-roles, output-format (`json`, `yaml`, `markdown`, `table`, `artifact-content`, `mixed`), schema-reference, required-fields, allowed-output-types, validation-rules, fallback-contract-id, and status (`active`, `deprecated`, `disabled`).

An agent invocation requiring downstream automation MUST use an active output contract. A model selected for structured output SHOULD support the required output format natively or through reliable adapter normalization. A model response that does not satisfy the output contract MUST be rejected, retried, normalized if safe, or escalated. Output contract versions MUST be recorded in model invocation records.

### 3.16 Output Normalization

Output normalization converts provider-specific responses into platform-compatible outputs. The Output Normalizer MUST: parse the provider response; extract the expected output payload; remove provider-specific wrapper content; validate against the output contract; identify missing required fields and malformed content; classify output confidence where applicable; preserve the raw output reference when required; produce a normalized output reference; and produce findings when normalization is incomplete or unsafe.

A Normalization Record captures normalization-record-id, model-invocation-id, output-contract-id, normalization-outcome (`passed`, `failed`, `warning`, `partial`), normalized-output-reference, findings, normalized-at, and normalized-by.

Normalization failure MUST prevent downstream use unless governance permits manual review. Partial normalization MUST NOT satisfy a role contract unless the role contract accepts partial output. A normalized output MUST remain linked to the original model invocation.

### 3.17 Model Failure and Error Handling

Model failures MUST be classified and handled predictably. Standard model error classes and default handling:

| Error Class | Default Handling |
| ----------- | ---------------- |
| model-provider-unavailable | retry or fallback |
| model-profile-not-found / model-capability-missing | reject routing |
| model-governance-blocked | block or escalate |
| model-context-too-large | reduce context or fallback |
| model-context-policy-violation | block |
| model-timeout | retry then fallback or escalate |
| model-rate-limited | retry with backoff or fallback |
| model-authentication-failed | halt or escalate |
| model-output-empty | retry |
| model-output-invalid | retry, fallback, or escalate |
| model-output-unsafe | block and escalate |
| model-normalization-failed | retry, fallback, or escalate |
| model-routing-failed | escalate |
| model-fallback-exhausted | escalate or fail task |

A Model Error Record captures model-error-id, error-class, severity, model-invocation-id, routing-decision-id, model-profile-id, provider-id, agent-invocation-id, task-id, message, default-handling (`retry`, `fallback`, `block`, `escalate`, `reject`, `halt`), handling-status, and detected-at.

Model errors MUST be recorded. Retryable model errors MUST respect the runtime retry policy. Governance-blocked model errors MUST NOT be retried with the same blocked model. Output-invalid errors MAY be retried if retry policy permits. Output-unsafe errors MUST block downstream use and escalate when policy requires.

### 3.18 Retry and Fallback

Fallback allows the platform to continue when a selected model fails, provided governance and routing policy permit fallback. Fallback MAY occur only when: routing policy allows fallback; an eligible fallback model exists; the fallback model satisfies required capabilities and context sensitivity constraints; governance permits the fallback provider and model; and retry policy does not require escalation first.

A Fallback Record captures fallback-record-id, original-model-profile-id, fallback-model-profile-id, provider-id, model-invocation-id, fallback-reason, capability-check-result, governance-check-id (conditional), and selected-at.

Fallback MUST produce a new routing decision or record an amendment to the original routing decision. Fallback MUST NOT reduce the required capability level unless governance approves degradation. Fallback to a provider with a weaker trust profile MUST require governance authorization when context is confidential or restricted. Fallback exhaustion MUST escalate or fail the task.

### 3.19 Model Trust Profiles

Model trust profiles define where, how, and for what type of context a model may be used.

A Model Trust Profile records trust-profile-id, trust-level (`approved`, `restricted`, `experimental`, `prohibited`), allowed-risk-tiers, allowed-sensitivity-levels, allowed-agent-roles, allowed-environments, external-transmission-allowed, provider-retention-allowed, human-review-required, approval-required, policy-reference, and status (`active`, `superseded`, `revoked`).

A prohibited model MUST NOT be used. A restricted model MUST be used only within declared constraints. A model requiring approval MUST NOT be invoked until approval is recorded. A model trust profile change MUST be audited.

### 3.20 Governance and Policy Control

Model usage is governance-controlled. Governance MAY control: provider registration; model registration; external model use; restricted context transmission; use of experimental models; model fallback; model use by agent role or risk tier; output use after failed normalization; provider data retention; and model invocation for security-sensitive tasks.

The Model Integration Layer MUST request governance checks when model usage is governed. A governance decision of `block` MUST prevent invocation. A governance decision of `approval-required` MUST pause invocation until approval exists. A governance decision of `escalate` MUST open escalation and prevent invocation until resolved. Expired approvals, waivers, or overrides MUST NOT authorize model use.

### 3.21 Model Invocation Traceability

Every meaningful model interaction MUST be traceable. The platform MUST record links between: task and agent invocation; agent invocation and model invocation; model invocation and routing decision; model invocation and context package; model invocation and model profile; model invocation and provider; model invocation and normalized output; normalized output and agent output; model output and artifact candidate where applicable; model output and decision record where applicable; model error and affected task; and fallback record and model invocation.

A model invocation used for artifact generation MUST be traceable to the artifact candidate. A model invocation used for repair MUST be traceable to the failed validation and repair record. A model invocation used for governance support MUST be traceable to the governance event or decision. A task MUST NOT be marked completed when required model traceability is missing.

### 3.22 Model Observability

Model observability provides operational visibility into model usage, quality, failure, latency, cost, routing, and output validation. The platform SHOULD emit telemetry for model routing requested, routing decision made, context control passed/failed, model invocation started/completed/failed, model timeout, output normalization passed/failed, fallback selected/exhausted, model governance blocked, and model capability validation failed.

Minimum telemetry fields include event-id, event-type, timestamp, provider-id, model-profile-id, agent-role, task-id, specification-id and version, routing-decision-id, model-invocation-id, outcome, duration-ms, and severity. The common telemetry event structure is defined in ISL v0.1 §9; the full event catalog is defined in ISL v2.3.

Telemetry MUST NOT expose restricted context content to unauthorized observers. Model observability MUST be correlated with agent, runtime, governance, and traceability records. Model failure telemetry SHOULD support operational alerting.

### 3.23 Model Lifecycle Management

Models and providers evolve. The platform MUST manage lifecycle state explicitly. Model lifecycle states are: proposed, active, restricted, experimental, deprecated, disabled, revoked, and retired.

A revoked model MUST NOT be invoked. A deprecated model SHOULD NOT be selected for new invocations. A disabled model MUST NOT be selected until reactivated. A model lifecycle change MUST be audited. Lifecycle changes affecting active execution MUST trigger impact evaluation.

### 3.24 Model Configuration Management

Model configuration affects output behavior and MUST be controlled. A Model Configuration records model-configuration-id, model-profile-id, configuration-version, parameter-profile, provider-parameter-map, output-contract-defaults, safety-profile-id, context-policy-id, routing-policy-id, status (`draft`, `active`, `superseded`, `revoked`), configured-at, and configured-by.

Model configuration MUST be versioned. The model configuration used for an invocation MUST be recorded. Configuration changes that materially affect output behavior SHOULD trigger revalidation of model capability declarations. A revoked model configuration MUST NOT be used for new invocations.

### 3.25 Model Quality and Performance Evaluation

The platform SHOULD evaluate model quality over time. Metrics MAY include output contract pass rate, retry rate, fallback rate, timeout rate, average latency, cost per invocation, agent role success rate, repair convergence contribution, human review rejection rate, security finding rate, hallucination or unsupported claim findings, and deterministic validation pass rate for generated artifacts.

A Model Performance Record captures performance-record-id, model-profile-id, provider-id, evaluation-window-start/end, metrics, interpretation, recommended-action (`continue`, `restrict`, `revalidate`, `deprecate`, `escalate`), and recorded-at.

Model performance records SHOULD inform routing policy updates. A model with repeated output contract failures SHOULD be restricted, revalidated, or deprecated. A model used for High or Critical systems SHOULD have periodic performance review.

### 3.26 Security and Context Protection for Models

The platform MUST: prevent unauthorized context transmission; classify context sensitivity before invocation; prevent secret exposure; isolate untrusted content in prompts or input packages; prevent model outputs from bypassing validation; enforce provider data retention restrictions; record restricted model usage; detect prompt or context injection attempts where possible; and block unsafe or policy-violating outputs when detected.

Untrusted specification content, artifact content, logs, tool output, or external data MUST NOT be allowed to override platform instructions, agent role boundaries, output contracts, governance controls, or safety rules. Detected injection attempts MUST produce a security finding. A context injection finding MAY block invocation, trigger redaction, or escalate according to policy.

### 3.27 Model Interaction Evidence Retention

Model interaction evidence supports audit, debugging, quality evaluation, and traceability. Evidence MAY include routing decisions, context control records, context package references, model invocation requests, raw and normalized outputs, output validation records, error records, fallback records, governance decisions, and telemetry summaries.

Evidence MUST be retained when model output contributes to artifact generation, repair proposal, security finding, governance decision, deployment preparation, architecture decision, traceability decision, or High or Critical risk execution. Raw model outputs MAY be redacted or withheld according to sensitivity and retention policy, but metadata sufficient for audit MUST be preserved.

---

## 4.0 Conformance

An agent role conforms to ISL v2.1 if it has a defined role contract, accepts the required invocation schema, consumes structured context packages, produces outputs conforming to declared output contracts, operates within prohibited-action boundaries, supports required failure handling, and preserves traceability and governance controls.

An Agent Orchestrator conforms to ISL v2.1 if it can invoke standard agent roles, validate role contracts, assemble or request context packages, route invocations through the model abstraction boundary, validate agent outputs, manage handoffs, detect collaboration conflicts, manage retries and escalation, preserve collaboration state, record traceability links, emit agent telemetry, and enforce governance decisions.

A model provider conforms to ISL v2.1 if it has a valid Provider Record, uses a registered provider adapter, declares supported models, provider type, deployment location, and trust profile, supports required invocation and response semantics through its adapter, and is governed and auditable.

A Model Router conforms to ISL v2.1 if it can evaluate required capabilities, filter models by provider status, trust profile, and context sensitivity, enforce local-first and routing policies, produce routing decision records, support deterministic tie-breaking and governed fallback, and reject invocations when no eligible model exists.

A Model Integration Layer conforms to ISL v2.1 if it can register model providers, profiles, and adapters, manage and validate capabilities, route invocations, control context, invoke models through provider adapters, normalize outputs, validate output contracts, handle model errors, apply retry and fallback rules, enforce governance controls, preserve model traceability, emit model telemetry, and retain required evidence.

A platform conforms to ISL v2.1 if it implements or supports the required agent roles for its claimed capability level; prevents agents from bypassing runtime, governance, traceability, model, and tool boundaries; validates all agent outputs before downstream use; requires deterministic validation for objective artifact acceptance; records agent invocations, outputs, failures, handoffs, conflicts, and decisions; supports agent audit queries through traceability and observability systems; protects sensitive context; supports governed model usage; prevents agents from directly invoking model providers; routes all model use through the Model Integration Layer; enforces model governance and trust profiles; supports local-first model operation where configured; records routing decisions and model invocations; prevents invalid model outputs from downstream use; links model interactions to agent, task, artifact, traceability, and governance records; and supports lifecycle management for providers, adapters, models, capabilities, and configurations.

A conforming ALETHEIA platform MUST treat agents as controlled reasoning components and models as governed reasoning resources. Agents may interpret, plan, generate, review, repair, test, validate, and prepare deployment outputs, and models may reason, generate, and review, but both MUST remain mediated by the runtime, constrained by governance, grounded in canonical specifications, traced through the platform, and validated by deterministic tools where objective correctness is required.
