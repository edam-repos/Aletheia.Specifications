# ALETHEIA Specification Language (ISL) v1.1

# The Canonical Semantic Model

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0
**Supersedes:** ISL r2 v1.1 Canonical Semantic Model
**Document Type:** Canonical Model Specification

---

## 1.0 Scope

This document defines the canonical semantic model used by the ALETHEIA Specification Language. The canonical semantic model is the authoritative machine-interpretable representation of an authored ISL specification after parsing and normalization. It defines the entity types, required fields, relationships, identifier rules, validation constraints, and normalization behavior required for a specification to become Machine-Valid.

This document does not define the human-authored Markdown syntax of ISL; that responsibility belongs to ISL v1.0. It also does not define runtime execution, construction planning, artifact generation, repair cycles, or platform deployment. Its responsibility is narrower and more foundational: it defines the internal semantic graph that all later ISL processes rely on.

Normative language, field requirement levels, shared enums, and identifier conventions are defined in ISL v0.1 and MUST NOT be redefined here.

A conforming implementation MUST use this canonical model as the authoritative representation for:

* semantic validation
* readiness evaluation
* construction planning
* traceability
* governance policy evaluation
* impact analysis
* artifact derivation
* model and agent context assembly

The canonical model SHALL serve as the semantic source of truth after normalization.

---

## 2.0 Canonical Model Overview

The canonical semantic model is organized as a graph of typed entities. Each entity describes a specific aspect of the system being specified. Relationships define how these aspects depend on, govern, validate, implement, expose, or group one another.

The model uses fourteen core entity types, representing the minimum semantic vocabulary required to describe a system for autonomous construction:

| Entity Type    | Purpose                                                      |
| -------------- | ------------------------------------------------------------ |
| Project        | Root container for the specification                         |
| Context        | External environment, assumptions, and dependencies          |
| Stakeholder    | Party with interest, responsibility, or approval authority   |
| Actor          | Runtime participant that interacts with the system           |
| Requirement    | Verifiable behavior or constraint                            |
| Capability     | Higher-level functional grouping                             |
| Service        | Deployable or logical system component                       |
| DataEntity     | Structured information managed by the system                 |
| Workflow       | Multi-step behavior involving actors, services, or decisions |
| Interface      | Communication boundary exposed or consumed by services       |
| Policy         | Rule governing security, compliance, operation, or risk      |
| Infrastructure | Runtime and deployment expectation                           |
| Validation     | Criteria proving that specification intent is satisfied      |
| ReusableAsset  | Approved asset that may be reused, extended, wrapped, or composed during construction |

A canonical model MUST contain exactly one Project entity. Other entity types MAY occur one or more times depending on the scope of the system and readiness level.

ReusableAsset type-specific fields and registry lifecycle rules are defined by ISL v1.5. A canonical processor that supports reuse-first construction MUST support ReusableAsset references, relationship validation, and traceability links.

---

## 3.0 Canonical Graph Structure

### 3.1 Graph Definition

The canonical model SHALL be represented as a directed graph.

| Graph Element | Description                                                                           |
| ------------- | ------------------------------------------------------------------------------------- |
| Node          | A canonical entity                                                                    |
| Edge          | A canonical relationship                                                              |
| Root Node     | The Project entity                                                                    |
| Subgraph      | A connected subset of entities relevant to a capability, service, workflow, or policy |

### 3.2 Graph Rules

A canonical graph MUST satisfy the following rules:

* Exactly one Project entity MUST exist.
* Every non-Project entity MUST be reachable from the Project entity through one or more relationships.
* Every relationship target MUST resolve to an existing entity.
* Relationship types MUST be drawn from the relationship types defined in this document.
* The graph MUST NOT contain circular dependencies unless the relationship type explicitly permits cyclical structure.
* The graph MUST support traversal from requirements to implementing services and validations.
* The graph MUST support traversal from policies to governed entities.
* The graph MUST support traversal from data entities to owning or managing services.

### 3.3 Root Relationship Requirement

Every canonical entity MUST be associated with the Project entity either directly or indirectly. This ensures that no orphan semantic structures exist in the specification.

A canonical processor MUST reject a Machine-Valid candidate if any entity is disconnected from the Project graph.

---

## 4.0 Base Entity Schema

This section defines the schema that all canonical entities MUST satisfy. Type-specific schemas extend this base schema; they do not replace it.

### 4.1 Base Entity Fields

| Field          | Type   | Required    | Description                                            |
| -------------- | ------ | ----------- | ------------------------------------------------------ |
| id             | string | REQUIRED    | Traceability identifier conforming to §14.0            |
| type           | enum   | REQUIRED    | One of the fourteen canonical entity types             |
| name           | string | REQUIRED    | Human-readable entity name                             |
| description    | string | REQUIRED    | Purpose or meaning of the entity                       |
| version        | semver | REQUIRED    | Entity version                                         |
| relationships  | array  | CONDITIONAL | Explicit relationships to other entities               |
| source-section | string | REQUIRED    | Authored ISL section from which the entity was derived |
| status         | enum   | REQUIRED    | Entity status (see 4.3)                                |
| metadata       | object | OPTIONAL    | Implementation-neutral supplemental metadata           |

### 4.2 Base Entity Rules

Every canonical entity MUST include all REQUIRED base fields.

The `id` field MUST be immutable after assignment.

The `type` field MUST match one of the canonical entity types defined in §2.0.

The `description` field MUST be meaningful enough to support human review and audit. A description consisting only of a repeated name, placeholder, or empty string MUST fail semantic validation.

The `status` field MUST be set to `active` unless the entity is explicitly marked otherwise.

### 4.3 Entity Status Values

| Status     | Meaning                                                            |
| ---------- | ------------------------------------------------------------------ |
| active     | Entity is current and valid for use                                |
| draft      | Entity exists but is not complete                                  |
| deprecated | Entity remains traceable but MUST NOT be used for new construction |
| superseded | Entity has been replaced by a newer entity                         |

A deprecated or superseded entity MUST retain its identifier and MUST NOT be deleted from version history.

---

## 5.0 Base Relationship Schema

Relationships are first-class semantic structures that define how system intent becomes architecture, validation, governance, and construction behavior. Relationships MUST be explicit and typed; a platform MUST NOT depend on implicit references hidden in prose once the specification has been normalized.

### 5.1 Relationship Fields

| Field             | Type    | Required    | Description                                                     |
| ----------------- | ------- | ----------- | --------------------------------------------------------------- |
| relationship-id   | string  | REQUIRED    | Unique identifier for the relationship                          |
| source-id         | string  | REQUIRED    | Entity identifier where the relationship originates             |
| target-id         | string  | REQUIRED    | Entity identifier where the relationship points                 |
| relationship-type | enum    | REQUIRED    | Relationship type defined in §5.2                               |
| cardinality       | enum    | CONDITIONAL | one-to-one, one-to-many, many-to-one, many-to-many              |
| required          | boolean | REQUIRED    | Whether the relationship is mandatory                           |
| rationale         | string  | CONDITIONAL | Required when relationship is inferred                          |
| source-section    | string  | REQUIRED    | Authored section or canonical process creating the relationship |

### 5.2 Relationship Types

| Relationship Type | Meaning                                             |
| ----------------- | --------------------------------------------------- |
| contains          | Project includes another canonical entity           |
| depends-on        | Source requires target to be present or complete    |
| implements        | Source provides behavior required by target         |
| validates         | Source verifies target                              |
| governs           | Source constrains target                            |
| exposes           | Source makes target available through a boundary    |
| consumes          | Source uses target as input                         |
| produces          | Source creates target as output                     |
| groups            | Source aggregates target entities                   |
| manages           | Source owns or controls target data                 |
| supports          | Source provides operational or infrastructural support for target |
| participates-in   | Source participates in target workflow              |
| triggered-by      | Source begins when target event or condition occurs |
| constrained-by    | Source is limited by target                         |
| derived-from      | Source was normalized or inferred from target       |
| reuses            | Source depends on an approved reusable asset        |
| extends           | Source extends an approved reusable asset           |
| wraps             | Source adapts an approved reusable asset through a wrapper |
| composes          | Source combines one or more reusable assets         |
| supersedes        | Source replaces target                              |

### 5.3 Relationship Rules

All relationship references MUST resolve to existing canonical entity identifiers.

Relationship direction MUST be meaningful. For example, a Service may implement a Requirement, but a Requirement MUST NOT implement a Service.

A relationship marked `required` MUST be satisfied before the entity can be considered semantically complete.

Inferred relationships MUST include a rationale.

A relationship that cannot be validated MUST be recorded as a canonical validation error.

---

## 6.0 Project Entity

The Project entity is the root container for the entire canonical specification. Exactly one Project entity MUST exist in every canonical model; all other entities are interpreted within its context.

### 6.1 Schema

| Field                 | Type     | Required    | Description                                        |
| --------------------- | -------- | ----------- | -------------------------------------------------- |
| system-id             | string   | REQUIRED    | Stable system identifier                           |
| readiness-level       | enum     | REQUIRED    | draft, reviewable, machine-valid, autonomous-ready |
| isl-version           | string   | REQUIRED    | ISL version governing the specification            |
| owner                 | string   | REQUIRED    | Responsible organization or unit                   |
| domain                | string   | REQUIRED    | Business or technical domain                       |
| created               | ISO 8601 | REQUIRED    | Date the specification was first created           |
| last-modified         | ISO 8601 | REQUIRED    | Date of most recent revision                       |
| risk-tier             | enum     | CONDITIONAL | Required before Autonomous-Ready                   |
| governance-profile    | string   | CONDITIONAL | Required when governance policies apply            |
| specification-version | semver   | REQUIRED    | Version of the specification                       |

### 6.2 Rules

The Project entity MUST be the root of the canonical graph.

The `system-id` MUST match the authored System Identity section defined in ISL v1.0.

The `readiness-level` and `risk-tier` MUST use the enum values defined in ISL v0.1.

The Project entity MUST contain or reference every other entity in the specification graph.

The Project entity MUST NOT be deprecated while the specification remains active.

---

## 7.0 Context Entity

The Context entity describes the external environment in which the system operates: assumptions, dependencies, constraints, external systems, organizational boundaries, and operational conditions.

### 7.1 Schema

| Field                   | Type   | Required    | Description                                                        |
| ----------------------- | ------ | ----------- | ------------------------------------------------------------------ |
| external-systems        | array  | CONDITIONAL | External systems depended on or integrated with                    |
| integration-patterns    | array  | CONDITIONAL | sync-request-reply, async-event, batch, streaming                  |
| environment-constraints | array  | CONDITIONAL | Regulatory, geographic, operational, or organizational constraints |
| assumptions             | array  | CONDITIONAL | Assumptions required for interpretation                            |
| exclusions              | array  | CONDITIONAL | Explicit responsibilities outside the system boundary              |
| domain-context          | string | CONDITIONAL | Business or technical context affecting system behavior            |

### 7.2 Rules

A Context entity MUST be present when the authored specification declares external dependencies, assumptions, exclusions, or environmental constraints.

External systems referenced by actors, services, interfaces, or workflows MUST be represented in the Context entity or as Actor entities of type `external`.

Assumptions MUST NOT be used to bypass required fields in other canonical entities.

An assumption that materially affects construction, validation, governance, or deployment MUST be linked to the affected entities through relationships.

---

## 8.0 Stakeholder Entity

The Stakeholder entity represents a person, role, organization, governance authority, or group with an interest in the system. Stakeholders are not necessarily runtime users; their primary importance is that they establish source authority, review responsibility, governance participation, and traceable business motivation.

### 8.1 Schema

| Field              | Type    | Required    | Description                                                                                                            |
| ------------------ | ------- | ----------- | ---------------------------------------------------------------------------------------------------------------------- |
| role               | string  | REQUIRED    | Organizational, business, or governance role                                                                           |
| concerns           | array   | REQUIRED    | Outcomes, quality attributes, risks, or responsibilities                                                               |
| approval-authority | boolean | REQUIRED    | Whether the stakeholder can approve readiness or governance actions                                                    |
| authority-type     | enum    | CONDITIONAL | reviewer, security-reviewer, architecture-reviewer, authorizing-official, governance-administrator, override-authority |
| organization       | string  | CONDITIONAL | Organization or unit represented                                                                                       |
| contact-reference  | string  | OPTIONAL    | Non-sensitive reference to responsible party or role                                                                   |

### 8.2 Rules

Every Requirement MUST trace to at least one Stakeholder, Actor, or Objective-derived source.

A Stakeholder with `approval-authority` set to true MUST map to a governance role defined in ISL v1.3.

A single Stakeholder MUST NOT be assigned incompatible approval roles for the same specification when separation-of-duties rules apply.

Stakeholder concerns SHOULD be used to classify requirements, policies, and validation criteria.

---

## 9.0 Actor Entity

The Actor entity represents an entity that interacts with the system at runtime: a human, system, process, or external party. A system behavior involving an external participant MUST identify that participant as an Actor unless the participant is represented as an internal Service.

### 9.1 Schema

| Field                   | Type    | Required    | Description                                            |
| ----------------------- | ------- | ----------- | ------------------------------------------------------ |
| actor-type              | enum    | REQUIRED    | human, system, process, external                       |
| permissions             | array   | REQUIRED    | Operations the actor is permitted to perform           |
| interfaces-used         | array   | CONDITIONAL | Interface identifiers used by the actor                |
| authentication-required | boolean | REQUIRED    | Whether authenticated identity is required             |
| authorization-scope     | array   | CONDITIONAL | Permission scopes, roles, or policies governing access |
| data-access             | array   | CONDITIONAL | DataEntity identifiers the actor may access            |

### 9.2 Rules

Every Actor MUST define at least one permission.

If an Actor uses an Interface, the referenced Interface entity MUST exist.

If `authentication-required` is true, at least one Policy or Interface authentication rule MUST govern the Actor's access path.

Actor permissions MUST be consistent with security policies and workflow actions.

An Actor of type `external` MUST be represented in Context or linked to an external system dependency.

---

## 10.0 Requirement Entity

The Requirement entity represents a verifiable statement of system behavior, quality, constraint, regulation, or operational expectation. A Requirement MUST be specific enough to validate.

### 10.1 Schema

| Field                    | Type   | Required    | Description                                                   |
| ------------------------ | ------ | ----------- | ------------------------------------------------------------- |
| requirement-type         | enum   | REQUIRED    | functional, non-functional, regulatory, operational           |
| statement                | string | REQUIRED    | Active-voice verifiable requirement statement                 |
| priority                 | enum   | REQUIRED    | must-have, should-have, nice-to-have                          |
| source                   | array  | REQUIRED    | Actor, Stakeholder, Objective, Policy, or Context identifiers |
| acceptance-criteria      | array  | REQUIRED    | Validation entity identifiers                                 |
| iso-25010-characteristic | string | CONDITIONAL | Required for non-functional requirements                      |
| metric                   | string | CONDITIONAL | Required when measurable                                      |
| target                   | string | CONDITIONAL | Required when metric is present                               |
| rationale                | string | OPTIONAL    | Explanation of why the requirement exists                     |

### 10.2 Requirement Types

| Type           | Meaning                                                 |
| -------------- | ------------------------------------------------------- |
| functional     | Behavior the system MUST provide                        |
| non-functional | Quality attribute or constraint                         |
| regulatory     | Compliance or legal requirement                         |
| operational    | Deployment, runtime, monitoring, or support requirement |

### 10.3 Rules

Every Requirement MUST be verifiable.

Every Requirement MUST have at least one source.

Every must-have Requirement MUST link to at least one Validation entity before Autonomous-Ready readiness.

A non-functional Requirement MUST include an ISO/IEC 25010-aligned characteristic unless explicitly justified as outside that model.

Requirement statements MUST NOT contain ambiguous language that prevents validation.

A Requirement with priority `must-have` MUST NOT be ignored, deferred, or treated as optional during planning.

---

## 11.0 Capability Entity

The Capability entity groups related requirements into a higher-level function or business capability.

### 11.1 Schema

| Field           | Type   | Required    | Description                                           |
| --------------- | ------ | ----------- | ----------------------------------------------------- |
| requirements    | array  | REQUIRED    | Requirement identifiers grouped under this capability |
| implemented-by  | array  | CONDITIONAL | Service identifiers implementing the capability       |
| capability-type | enum   | OPTIONAL    | business, technical, operational, governance          |
| priority        | enum   | CONDITIONAL | must-have, should-have, nice-to-have                  |
| owner           | string | CONDITIONAL | Stakeholder or organizational owner                   |

### 11.2 Rules

A Capability MUST group at least one Requirement.

A Capability SHOULD be implemented by at least one Service before Autonomous-Ready readiness.

A Capability MUST NOT introduce behavior that is not represented by one or more Requirements.

Capability priority MUST NOT be lower than the highest priority Requirement it contains unless explicitly justified.

---

## 12.0 Service Entity

The Service entity represents a logical or deployable component responsible for implementing requirements, exposing interfaces, managing data, and participating in workflows.

### 12.1 Schema

| Field           | Type   | Required    | Description                                      |
| --------------- | ------ | ----------- | ------------------------------------------------ |
| responsibility  | string | REQUIRED    | Single-sentence responsibility statement         |
| interfaces      | array  | REQUIRED    | Interface identifiers exposed or consumed        |
| dependencies    | array  | CONDITIONAL | Service, Context, or external system identifiers |
| data-entities   | array  | CONDITIONAL | DataEntity identifiers managed or used           |
| capabilities    | array  | CONDITIONAL | Capability identifiers implemented               |
| requirements    | array  | REQUIRED    | Requirement identifiers implemented              |
| deployment-unit | string | CONDITIONAL | Logical deployment grouping                      |
| statefulness    | enum   | REQUIRED    | stateless, stateful, hybrid                      |

### 12.2 Rules

A Service MUST have exactly one primary responsibility statement.

A Service MUST implement or support at least one Requirement.

A Service MUST NOT declare a responsibility that substantially overlaps another Service unless the overlap is explicitly justified.

A Service exposing an Interface MUST reference a valid Interface entity.

A Service managing a DataEntity MUST be linked using a `manages` relationship.

A Service depending on another Service MUST use a `depends-on` relationship.

---

## 13.0 DataEntity Entity

The DataEntity entity represents structured information managed, stored, transmitted, validated, or transformed by the system. A DataEntity MUST be sufficiently structured for deterministic validation.

### 13.1 Schema

| Field            | Type   | Required    | Description                                            |
| ---------------- | ------ | ----------- | ------------------------------------------------------ |
| attributes       | array  | REQUIRED    | Attribute definitions                                  |
| relationships    | array  | CONDITIONAL | Relationships to other DataEntity instances            |
| sensitivity      | enum   | REQUIRED    | public, internal, confidential, restricted             |
| lifecycle        | enum   | OPTIONAL    | transient, persisted, archived, derived                |
| owner-service    | string | CONDITIONAL | Service identifier responsible for managing the entity |
| retention-policy | string | CONDITIONAL | Required for persisted sensitive data                  |

### 13.2 Attribute Schema

| Field       | Type    | Required    | Description                                    |
| ----------- | ------- | ----------- | ---------------------------------------------- |
| name        | string  | REQUIRED    | Attribute name                                 |
| data-type   | string  | REQUIRED    | Primitive, structured, enum, or reference type |
| nullable    | boolean | REQUIRED    | Whether null values are permitted              |
| constraints | array   | CONDITIONAL | Validation rules                               |
| default     | string  | OPTIONAL    | Default value when applicable                  |
| description | string  | CONDITIONAL | Attribute meaning                              |

### 13.3 Relationship Schema

| Field             | Type    | Required | Description                                        |
| ----------------- | ------- | -------- | -------------------------------------------------- |
| target-entity-id  | string  | REQUIRED | Related DataEntity identifier                      |
| relationship-type | string  | REQUIRED | Nature of the relationship                         |
| cardinality       | enum    | REQUIRED | one-to-one, one-to-many, many-to-one, many-to-many |
| required          | boolean | REQUIRED | Whether relationship must exist                    |

### 13.4 Rules

A DataEntity MUST contain at least one attribute.

Every attribute MUST declare `data-type` and `nullable`.

Relationships between DataEntity entities MUST specify cardinality.

A DataEntity with sensitivity `confidential` or `restricted` MUST be governed by at least one Policy entity.

A persisted DataEntity with sensitivity `confidential` or `restricted` MUST define or reference a retention policy before Machine-Valid readiness.

---

## 14.0 Workflow Entity

The Workflow entity represents ordered behavior involving actors, services, decisions, events, and exception paths.

### 14.1 Schema

| Field           | Type   | Required    | Description                                        |
| --------------- | ------ | ----------- | -------------------------------------------------- |
| trigger         | string | REQUIRED    | Event or condition initiating the workflow         |
| actors          | array  | CONDITIONAL | Actor identifiers participating                    |
| services        | array  | CONDITIONAL | Service identifiers participating                  |
| steps           | array  | REQUIRED    | Ordered step definitions                           |
| decision-points | array  | CONDITIONAL | Branching conditions                               |
| exception-paths | array  | REQUIRED    | Failure and exception handling paths               |
| validations     | array  | CONDITIONAL | Validation identifiers verifying workflow behavior |
| terminal-states | array  | REQUIRED    | Valid end states of the workflow                   |

### 14.2 Step Schema

| Field             | Type    | Required    | Description                         |
| ----------------- | ------- | ----------- | ----------------------------------- |
| sequence          | integer | REQUIRED    | Step order                          |
| participant-id    | string  | REQUIRED    | Actor or Service responsible        |
| action            | string  | REQUIRED    | Action performed                    |
| input             | string  | CONDITIONAL | Input required                      |
| output            | string  | CONDITIONAL | Output produced                     |
| success-condition | string  | REQUIRED    | Condition for step completion       |
| failure-condition | string  | CONDITIONAL | Condition triggering exception path |

### 14.3 Decision Point Schema

| Field       | Type   | Required | Description                       |
| ----------- | ------ | -------- | --------------------------------- |
| decision-id | string | REQUIRED | Unique decision identifier        |
| condition   | string | REQUIRED | Condition evaluated               |
| true-path   | string | REQUIRED | Step or terminal state when true  |
| false-path  | string | REQUIRED | Step or terminal state when false |

### 14.4 Rules

A Workflow MUST contain at least one step.

A Workflow MUST define exception paths.

A Workflow MUST define at least one terminal state.

Every participant referenced by a Workflow MUST resolve to an Actor or Service.

Decision points MUST define explicit conditions.

A Workflow without exception paths MUST fail Machine-Valid evaluation.

---

## 15.0 Interface Entity

The Interface entity represents a communication boundary exposed or consumed by a Service.

### 15.1 Schema

| Field               | Type   | Required    | Description                                                      |
| ------------------- | ------ | ----------- | ---------------------------------------------------------------- |
| interface-type      | enum   | REQUIRED    | rest-api, grpc, message-queue, event-stream, batch-file, graphql |
| contract            | string | REQUIRED    | Inline contract or reference to contract document                |
| authentication      | string | REQUIRED    | Authentication mechanism                                         |
| authorization       | string | CONDITIONAL | Authorization policy or scope                                    |
| versioning-strategy | string | REQUIRED    | How breaking changes are handled                                 |
| exposed-by          | string | REQUIRED    | Service identifier exposing the interface                        |
| consumed-by         | array  | CONDITIONAL | Actors, services, or external systems using the interface        |
| data-entities       | array  | CONDITIONAL | DataEntity identifiers transmitted through interface             |

### 15.2 Rules

An Interface MUST define a contract.

An Interface MUST define authentication.

If authorization differs by actor or operation, the Interface MUST reference a Policy entity governing authorization.

An Interface MUST be exposed by exactly one Service unless it is an external interface represented by Context.

Interface contracts SHOULD use established formats where applicable, such as OpenAPI for REST APIs or AsyncAPI for event-driven interfaces.

Breaking-change behavior MUST be defined through the `versioning-strategy` field.

---

## 16.0 Policy Entity

The Policy entity represents a verifiable rule governing security, compliance, operations, technology standards, risk, data handling, or governance behavior. Policies constrain both the specified system and the autonomous construction process.

### 16.1 Schema

| Field              | Type    | Required    | Description                                                                 |
| ------------------ | ------- | ----------- | --------------------------------------------------------------------------- |
| policy-type        | enum    | REQUIRED    | security, compliance, operational, data-handling, technology-standard, risk |
| rule               | string  | REQUIRED    | Verifiable rule statement                                                   |
| control-reference  | string  | CONDITIONAL | External control reference such as NIST SP 800-53                           |
| enforcement-point  | enum    | REQUIRED    | authoring, planning, generation, validation, deployment, runtime            |
| applies-to         | array   | REQUIRED    | Entity identifiers governed by the policy                                   |
| violation-response | enum    | REQUIRED    | block, escalate, warn, audit-only                                           |
| waiver-permitted   | boolean | REQUIRED    | Whether waiver may be granted                                               |
| validation         | array   | CONDITIONAL | Validation identifiers proving compliance                                   |

### 16.2 Rules

A Policy MUST contain a verifiable rule.

Intent-only policy statements MUST fail semantic validation.

A Policy with `violation-response` set to `block` MUST prevent progression at its enforcement point until resolved or waived.

A security Policy SHOULD include a control-reference to NIST SP 800-53 or an equivalent control framework.

A Policy MUST reference at least one entity in `applies-to`.

If waiver-permitted is true, governance rules in ISL v1.3 determine approval requirements.

---

## 17.0 Infrastructure Entity

The Infrastructure entity represents runtime environment expectations, deployment constraints, scaling behavior, and operational resource needs.

### 17.1 Schema

| Field                      | Type   | Required    | Description                                                 |
| -------------------------- | ------ | ----------- | ----------------------------------------------------------- |
| deployment-model           | enum   | REQUIRED    | container, serverless, vm, bare-metal, hybrid               |
| runtime-platform           | string | REQUIRED    | Target runtime platform and minimum version                 |
| resource-requirements      | object | REQUIRED    | CPU, memory, storage, network expectations                  |
| scaling-model              | enum   | REQUIRED    | fixed, horizontal, vertical, auto                           |
| regions                    | array  | CONDITIONAL | Deployment regions when geographic distribution is required |
| environments               | array  | REQUIRED    | dev, test, staging, prod, or equivalent                     |
| observability-requirements | array  | REQUIRED    | Metrics, logs, traces, alerts required                      |
| availability-target        | string | CONDITIONAL | Required when availability is specified                     |
| recovery-objectives        | object | CONDITIONAL | Required for persistent or critical systems                 |

### 17.2 Rules

Every canonical model intended for Autonomous-Ready readiness MUST include at least one Infrastructure entity.

Infrastructure entities MUST define runtime platform, deployment model, resource requirements, and scaling model.

Systems with persisted data MUST define recovery objectives or explicitly justify why recovery objectives are not applicable.

Infrastructure expectations MUST be consistent with non-functional requirements and operational policies.

---

## 18.0 Validation Entity

The Validation entity represents evidence-producing criteria used to verify that requirements, policies, workflows, interfaces, data models, services, and infrastructure expectations are satisfied.

### 18.1 Schema

| Field               | Type   | Required    | Description                                                                                          |
| ------------------- | ------ | ----------- | ---------------------------------------------------------------------------------------------------- |
| validates           | array  | REQUIRED    | Entity identifiers being validated                                                                   |
| validation-type     | enum   | REQUIRED    | test, inspection, static-analysis, security-scan, policy-check, operational-check, governance-review |
| method              | string | REQUIRED    | How validation is performed                                                                          |
| pass-condition      | string | REQUIRED    | Condition required for success                                                                       |
| evidence            | string | CONDITIONAL | Evidence artifact or record expected                                                                 |
| automation-level    | enum   | REQUIRED    | manual, semi-automated, automated                                                                    |
| tool-capability     | string | CONDITIONAL | Tool capability required when automated                                                              |
| severity-on-failure | enum   | REQUIRED    | blocking, high, medium, low                                                                          |

### 18.2 Rules

A Validation entity MUST reference at least one entity through `validates`.

A Validation entity MUST define a pass-condition.

A Validation entity with automation-level `automated` SHOULD identify the tool capability required.

Every must-have Requirement MUST have at least one linked Validation entity before Autonomous-Ready readiness.

Every blocking Policy MUST have at least one linked Validation entity before Machine-Valid readiness.

Validation outcomes use the `passed`, `failed`, `warning`, `timeout`, `error`, `not-applicable` enum defined in ISL v0.1.

---

## 19.0 Traceability Identifier Format

Identifiers provide the foundation for traceability across specifications, tasks, artifacts, validations, policies, decisions, governance events, and version history. Identifier stability is mandatory: names and descriptions may change, but the identifier remains the durable semantic anchor.

The shared identifier convention, supported entity prefixes, and general format are defined in ISL v0.1 §4. This section defines the canonical model-specific application of that convention.

### 19.1 Identifier Format

Every canonical entity identifier MUST conform to the following format:

`{PREFIX}-{system-id}-{sequence}`

Where:

| Component | Description                                                              |
| --------- | ------------------------------------------------------------------------ |
| PREFIX    | Entity type prefix per the shared convention in ISL v0.1 §4              |
| system-id | System identifier from the Project entity, normalized for identifier use |
| sequence  | Zero-padded five-digit integer unique within the entity type             |

The canonical entity types map to prefixes as follows:

| Entity Type    | Prefix |
| -------------- | ------ |
| Project        | PRJ    |
| Context        | CTX    |
| Scope          | SCP    |
| Stakeholder    | STK    |
| Actor          | ACT    |
| Requirement    | REQ    |
| Capability     | CAP    |
| Service        | SVC    |
| DataEntity     | DAT    |
| Workflow       | WFL    |
| Interface      | INT    |
| Policy         | POL    |
| Infrastructure | INF    |
| Validation     | VAL    |
| ReusableAsset  | RAS    |

Example:

`REQ-acme-order-00001`

### 19.2 Identifier Rules

Identifiers MUST be immutable after assignment.

Identifiers MUST NOT be reused after deprecation.

Identifiers MUST remain stable across specification versions.

No two active entities of the same type within the same specification MAY share an identifier.

A conforming processor MUST reject a Machine-Valid candidate if any identifier violates the required format.

### 19.3 System ID Normalization

The `system-id` component MUST be derived from the Project `system-id`. The normalized system-id used in identifiers MUST:

* be lowercase
* contain only letters, numbers, and hyphens
* be no more than sixty-four characters unless the implementation explicitly supports longer identifiers
* remain stable once identifiers have been issued

---

## 20.0 Canonical Relationship Constraints

This section defines which relationships are permitted between entity types. These constraints prevent invalid semantic graphs, such as requirements implementing services or policies being implemented by data attributes without an intervening service or interface.

### 20.1 Permitted Relationship Matrix

| Source Entity  | Permitted Relationship                                      | Target Entity                                                                 |
| -------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Project        | contains                                                    | Any canonical entity                                                          |
| Context        | depends-on, constrained-by                                  | Project, Service, Interface, Infrastructure, Policy                           |
| Stakeholder    | depends-on, governs                                         | Requirement, Policy, Project                                                  |
| Actor          | participates-in, consumes                                   | Workflow, Interface                                                           |
| Requirement    | depends-on, constrained-by                                  | Requirement, Policy, Context                                                  |
| Capability     | groups                                                      | Requirement                                                                   |
| Service        | implements, exposes, manages, depends-on, reuses, extends, wraps, composes | Requirement, Capability, Interface, DataEntity, Service, Context, ReusableAsset |
| DataEntity     | depends-on, constrained-by, reuses                          | DataEntity, Policy, ReusableAsset                                             |
| Workflow       | triggered-by, consumes, produces, depends-on                | Actor, Service, DataEntity, Workflow                                          |
| Interface      | consumes, produces, constrained-by, depends-on, reuses       | Service, DataEntity, Policy, ReusableAsset                                    |
| Policy         | governs, depends-on                                         | Requirement, Service, Interface, DataEntity, Infrastructure, Validation        |
| Infrastructure | supports, constrained-by, depends-on, reuses                 | Service, Policy, ReusableAsset                                                |
| Validation     | validates, depends-on                                       | Requirement, Policy, Workflow, Interface, Service, DataEntity, Infrastructure, ReusableAsset |
| ReusableAsset  | implements, exposes, manages, supports, reuses, extends, wraps, composes, supersedes | Requirement, Capability, Interface, DataEntity, Service, Infrastructure, ReusableAsset |

### 20.2 Required Relationship Rules

The following relationships MUST exist before Machine-Valid readiness:

| Entity        | Required Relationship                                                          |
| ------------- | ------------------------------------------------------------------------------ |
| Requirement   | MUST be validated by at least one Validation entity when priority is must-have |
| Service       | MUST implement at least one Requirement or Capability                          |
| Interface     | MUST be exposed by one Service or represented as external Context              |
| Policy        | MUST govern at least one entity                                                |
| DataEntity    | MUST be managed by at least one Service when persisted                         |
| Workflow      | MUST reference at least one Actor or Service                                   |
| Validation    | MUST validate at least one entity                                              |
| ReusableAsset | MUST be validated by at least one Validation entity before approved reuse      |

### 20.3 Invalid Relationship Handling

A relationship that violates the permitted relationship matrix MUST be reported as a canonical validation error.

A processor MUST NOT silently drop invalid relationships.

A processor MAY provide repair suggestions, but the canonical model MUST NOT be marked Machine-Valid until invalid relationships are corrected.

---

## 21.0 Normalization Process

Normalization is the process by which an authored ISL specification becomes a canonical semantic model. A conforming normalization process MUST be repeatable: the same authored specification and the same ISL version MUST produce the same canonical model unless declared extensions or configuration profiles alter processing behavior.

### 21.1 Normalization Stages

Normalization MUST proceed through the following stages:

| Stage | Name                    | Purpose                                                                          |
| ----- | ----------------------- | -------------------------------------------------------------------------------- |
| 1     | Parse                   | Read authored representation and identify sections, tables, fields, and elements |
| 2     | Extract                 | Extract structured specification elements                                        |
| 3     | Classify                | Assign canonical entity types                                                    |
| 4     | Assign Identifiers      | Validate or assign traceability identifiers                                      |
| 5     | Map Fields              | Populate canonical entity fields                                                 |
| 6     | Resolve References      | Resolve identifiers and relationships                                            |
| 7     | Validate Semantics      | Apply canonical schema and relationship rules                                    |
| 8     | Produce Canonical Graph | Emit canonical model or validation failure report                                |

### 21.2 Parse Stage

The parser MUST identify all required authored sections defined by ISL v1.0.

The parser MUST preserve source location metadata sufficient to report errors back to the authored specification.

If required sections are missing, normalization MUST fail before semantic validation.

### 21.3 Extract Stage

The extractor MUST identify structured elements such as requirements, actors, services, policies, workflows, and validations.

Required fields MUST be extracted from structured fields, not inferred from prose.

Prose MAY be retained as descriptive metadata but MUST NOT replace required canonical fields.

### 21.4 Classify Stage

Each extracted element MUST be assigned one canonical entity type.

If an extracted element could map to more than one entity type, the processor MUST record an ambiguity unless the authored structure clearly resolves the type.

### 21.5 Identifier Stage

Identifiers provided in the authored specification MUST be validated against §19.0.

If Draft readiness permits provisional identifiers, the processor MAY assign temporary identifiers, but those identifiers MUST be replaced with valid traceability identifiers before Machine-Valid readiness.

### 21.6 Field Mapping Stage

Every required canonical field MUST be populated from authored content, derived metadata, or explicitly defined defaults.

The processor MUST NOT invent missing required business semantics.

### 21.7 Reference Resolution Stage

All entity references MUST resolve to known identifiers.

Unresolved references MUST be reported as canonical validation errors.

### 21.8 Semantic Validation Stage

The processor MUST validate:

* base entity schema compliance
* type-specific schema compliance
* required relationship presence
* enum values
* identifier format
* cardinality rules
* policy verifiability
* validation coverage
* readiness-relevant constraints

### 21.9 Canonical Graph Output Stage

If normalization succeeds, the processor MUST produce a canonical graph.

If normalization fails, the processor MUST produce a Normalization Report and MUST NOT emit a Machine-Valid canonical model.

---

## 22.0 Canonical Validation Rules

Canonical validation determines whether the normalized model satisfies the semantic requirements of ISL. A canonical model MUST pass all required validation rules before the specification can reach Machine-Valid readiness.

### 22.1 Required Validation Checks

A conforming canonical validator MUST verify:

* exactly one Project entity exists
* every entity satisfies the base entity schema
* every entity satisfies its type-specific schema
* every identifier conforms to the identifier format
* every required relationship exists
* every relationship target resolves
* every enum value is valid
* every must-have Requirement has Validation coverage
* every Policy contains a verifiable rule
* every Interface defines authentication and contract
* every Workflow defines steps and exception paths
* every DataEntity defines attributes and nullability
* every Service implements a Requirement or Capability
* every persisted sensitive DataEntity is governed by a Policy
* every Infrastructure entity defines deployment model, runtime platform, and resource requirements

### 22.2 Validation Outcomes

Canonical validation MUST produce one of the following outcomes, drawn from the shared validation outcome enum in ISL v0.1:

| Outcome  | Meaning                                                                  |
| -------- | ------------------------------------------------------------------------ |
| passed   | Canonical model satisfies all required rules                             |
| failed   | Canonical model has one or more blocking errors                          |
| warning  | Canonical model is valid but contains non-blocking concerns              |
| timeout  | Validation did not complete within the configured limit                  |
| error    | Validation could not run due to an internal or processing error          |

### 22.3 Blocking Conditions

The following conditions MUST block Machine-Valid readiness:

* missing Project entity
* duplicate canonical identifiers
* unresolved references
* missing required fields
* invalid enum values
* missing validation for must-have requirements
* unverifiable policies
* incomplete workflows
* missing interface contracts
* missing authentication rules
* invalid relationship types
* disconnected graph entities

---

## 23.0 Canonical Error Model

ISL v1.0 defined authored-language error classes; this document defines canonical semantic error classes produced during normalization and semantic validation. All canonical errors use the common error record structure defined in ISL v0.1 §8.

### 23.1 Canonical Error Classes

| Error Class                         | Description                                                        | Blocks Machine-Valid |
| ----------------------------------- | ------------------------------------------------------------------ | -------------------- |
| canonical-missing-entity            | Required entity is absent                                          | YES                  |
| canonical-missing-field             | Required canonical field is absent                                 | YES                  |
| canonical-invalid-type              | Entity type is invalid                                             | YES                  |
| canonical-invalid-enum              | Enum value is unsupported                                          | YES                  |
| canonical-duplicate-id              | Identifier is duplicated                                           | YES                  |
| canonical-invalid-id-format         | Identifier violates §19.0                                          | YES                  |
| canonical-unresolved-reference      | Relationship or field references missing entity                    | YES                  |
| canonical-invalid-relationship      | Relationship type is not permitted                                 | YES                  |
| canonical-missing-relationship      | Required relationship is absent                                    | YES                  |
| canonical-disconnected-entity       | Entity is not reachable from Project                               | YES                  |
| canonical-unverifiable-requirement  | Requirement lacks validation coverage                              | YES                  |
| canonical-unverifiable-policy       | Policy lacks verifiable rule or validation                         | YES                  |
| canonical-incomplete-workflow       | Workflow lacks required steps, terminal states, or exception paths | YES                  |
| canonical-incomplete-interface      | Interface lacks contract or authentication                         | YES                  |
| canonical-sensitive-data-ungoverned | Sensitive data lacks governing policy                              | YES                  |
| canonical-quality-warning           | Model is valid but below recommended quality                       | NO                   |

### 23.2 Error Handling Rules

A canonical processor MUST produce a structured error record (per ISL v0.1 §8) for every detected blocking error.

A canonical processor MUST NOT suppress blocking errors.

A canonical processor MAY continue validation after detecting errors in order to produce a complete report.

A specification with one or more blocking canonical errors MUST NOT progress to Machine-Valid readiness.

---

## 24.0 Canonical Model Versioning

### 24.1 Model Version Fields

A canonical model MUST record:

| Field                   | Type     | Required | Description                                          |
| ----------------------- | -------- | -------- | ---------------------------------------------------- |
| model-version           | semver   | REQUIRED | Version of the canonical model schema                |
| specification-version   | semver   | REQUIRED | Version of the source specification                  |
| generated-at            | ISO 8601 | REQUIRED | Time canonical model was generated                   |
| generated-by            | string   | REQUIRED | Processor or platform component that generated model |
| source-document-version | semver   | REQUIRED | Authored specification version                       |
| isl-version             | string   | REQUIRED | ISL version used                                     |

### 24.2 Entity Versioning Rules

Each canonical entity MUST carry a version.

When an entity changes semantically, its version MUST be incremented.

When an entity is replaced, the new entity MUST use a new identifier only if it represents a distinct semantic concept. If it is the same concept revised, the identifier MUST remain stable and the version MUST change.

Deprecated and superseded entities MUST remain available for traceability.

### 24.3 Model Versioning Rules

A new canonical model version MUST be created whenever the authored specification version changes.

Previous canonical model versions MUST remain queryable.

A platform MUST NOT overwrite a previous canonical model in a way that destroys traceability.

---

## 25.0 Extension Model

### 25.1 Extension Declaration Schema

| Field            | Type   | Required    | Description                                     |
| ---------------- | ------ | ----------- | ----------------------------------------------- |
| extension-id     | string | REQUIRED    | Unique extension identifier                     |
| name             | string | REQUIRED    | Human-readable extension name                   |
| version          | semver | REQUIRED    | Extension version                               |
| owner            | string | REQUIRED    | Extension owner                                 |
| extends          | array  | REQUIRED    | Entity types, fields, or relationships extended |
| compatibility    | array  | REQUIRED    | Supported ISL versions                          |
| schema-reference | string | CONDITIONAL | Reference to extension schema                   |
| validation-rules | array  | CONDITIONAL | Extension validation rules                      |

### 25.2 Extension Rules

An extension MUST NOT redefine core entity types.

An extension MUST NOT remove required core fields.

An extension MUST NOT weaken validation, governance, traceability, or readiness requirements.

An extension MUST declare compatibility with specific ISL versions.

A processor that does not support an extension MUST reject the extension or preserve it as opaque metadata while reporting unsupported semantic content.

### 25.3 Extension Field Rules

Extension fields MUST be namespaced.

Extension fields MUST NOT collide with core field names.

Extension fields MUST be ignored only when they are not required for semantic interpretation. If an extension field changes required behavior, unsupported processors MUST reject the specification.

---

## 26.0 Standards Alignment

Standards alignment improves enterprise credibility, auditability, and integration with existing governance and architecture practices. Alignment means that ISL entities and relationships MUST be mappable where applicable, and divergence MUST be explicit when required.

### 26.1 Alignment Table

| Standard          | ISL Alignment                                                                                            |
| ----------------- | -------------------------------------------------------------------------------------------------------- |
| ISO/IEC 25010     | Requirement.iso-25010-characteristic classifies non-functional requirements                              |
| NIST SP 800-53    | Policy.control-reference may reference applicable security and privacy controls                          |
| W3C PROV-DM       | Canonical entities and relationships align with provenance entity/activity/agent concepts                |
| OpenTelemetry     | Validation, Infrastructure, and later telemetry models SHOULD align with observability conventions       |
| ArchiMate / TOGAF | Service, Capability, Interface, Actor, and Stakeholder MAY be mapped to enterprise architecture concepts |

### 26.2 Standards Mapping Requirements

A canonical model intended for enterprise governance SHOULD preserve standards references in structured fields rather than prose.

Where a security or compliance policy claims alignment to an external control, the Policy entity MUST include a control-reference.

Where a non-functional requirement is classified using a quality characteristic, the classification SHOULD align to ISO/IEC 25010 unless another governance-approved quality model is used.

---

## 27.0 Conformance Requirements

Conformance is separated by artifact type because a canonical model, normalization processor, and validation engine have different obligations. Conformance to this document is required before a platform can claim semantic model support.

### 27.1 Canonical Model Conformance

A canonical model conforms to ISL v1.1 if it:

* includes exactly one Project entity
* uses only defined core entity types unless extensions are declared
* satisfies the base entity schema
* satisfies all applicable type-specific schemas
* uses valid traceability identifiers
* uses valid relationship types
* resolves all references
* satisfies required relationship constraints
* passes canonical validation

### 27.2 Normalization Processor Conformance

A normalization processor conforms to ISL v1.1 if it can:

* parse structured input from ISL v1.0 processors
* classify authored elements into canonical entity types
* validate or assign identifiers according to §19.0
* populate required canonical fields
* construct canonical relationships
* detect ambiguity and unmappable content
* produce canonical graphs
* produce structured normalization errors

### 27.3 Canonical Validator Conformance

A canonical validator conforms to ISL v1.1 if it can:

* validate all base schema requirements
* validate all type-specific schema requirements
* validate identifier format
* validate relationship constraints
* validate reference resolution
* validate required validation coverage
* detect disconnected graph entities
* produce structured canonical error records

---

## 28.0 Minimal Canonical Model Example

This section provides a compact example of canonical model content. The example is informative but demonstrates the level of structure required for canonical processing.

### 28.1 Example Entity Set

| id                | type        | name              | version | status |
| ----------------- | ----------- | ----------------- | ------- | ------ |
| PRJ-example-00001 | Project     | Example System    | 1.0.0   | active |
| ACT-example-00001 | Actor       | Order Clerk       | 1.0.0   | active |
| REQ-example-00001 | Requirement | Submit Order      | 1.0.0   | active |
| SVC-example-00001 | Service     | Order Service     | 1.0.0   | active |
| INT-example-00001 | Interface   | Order API         | 1.0.0   | active |
| VAL-example-00001 | Validation  | Submit Order Test | 1.0.0   | active |

### 28.2 Example Relationship Set

| relationship-id   | source-id         | relationship-type | target-id         |
| ----------------- | ----------------- | ----------------- | ----------------- |
| REL-example-00001 | PRJ-example-00001 | contains          | ACT-example-00001 |
| REL-example-00002 | PRJ-example-00001 | contains          | REQ-example-00001 |
| REL-example-00003 | SVC-example-00001 | implements        | REQ-example-00001 |
| REL-example-00004 | SVC-example-00001 | exposes           | INT-example-00001 |
| REL-example-00005 | VAL-example-00001 | validates         | REQ-example-00001 |
| REL-example-00006 | ACT-example-00001 | consumes          | INT-example-00001 |

---

## 29.0 Machine-Valid Readiness Dependency

ISL v1.3 defines readiness levels, but canonical validation is the semantic foundation for Machine-Valid status. A specification MUST NOT be marked Machine-Valid unless canonical normalization and validation complete without blocking errors.

### 29.1 Required Machine-Valid Conditions

Before Machine-Valid readiness, the canonical model MUST satisfy:

* all required entity schemas
* all required relationship constraints
* all identifier rules
* all reference resolution rules
* all validation coverage rules
* all policy verifiability rules
* all data entity completeness rules
* all interface completeness rules
* all workflow completeness rules

### 29.2 Readiness Failure Behavior

If canonical validation fails, readiness evaluation MUST record the failure.

The platform MUST NOT proceed to construction planning based on a failed canonical model.

The platform MAY provide advisory repair guidance to specification authors.
