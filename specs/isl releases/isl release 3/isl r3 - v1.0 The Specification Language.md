# ALETHEIA Specification Language (ISL) v1.0

# The Specification Language

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1
**Supersedes:** ISL r2 v1.0 ASC Language
**Document Type:** Language Specification

---

## 1.0 Scope

This document defines the ALETHEIA Specification Language (ISL) as the authored specification language used to describe software systems for autonomous construction. It establishes the required structure, authored representation rules, minimum content requirements, and validation expectations for ISL specifications before they are normalized into the canonical semantic model defined in ISL v1.1.

This document governs the **language surface** of ISL. It defines what a valid authored ISL specification MUST contain, how specification sections MUST be organized, and how human-authored content MUST map to machine-interpretable structures. It does not define the full internal canonical entity schema, execution lifecycle, construction task graph, or platform runtime behavior; those responsibilities are assigned to later ISL documents.

Normative language, field requirement levels, identifiers, and shared enums are defined in ISL v0.1. A conforming ISL document MUST NOT redefine them.

An ISL v1.0 conforming specification MUST be capable of being:

* read and reviewed by human stakeholders
* parsed into structured sections
* normalized into the ISL canonical semantic model
* evaluated for readiness progression
* traced to generated artifacts in later construction phases

An ISL v1.0 conforming specification SHALL treat the specification as the controlling artifact for planning, execution, validation, traceability, and governance.

---

## 2.0 ISL Representations

A single ISL specification may exist in multiple forms, but all valid representations MUST resolve to the same canonical semantic structure.

### 2.1 Representation Types

| Representation | Format              | Primary Use                                 | Normative Role                       |
| -------------- | ------------------- | ------------------------------------------- | ------------------------------------ |
| Authored       | Structured Markdown | Human authoring and review                  | Primary human-facing representation  |
| Canonical      | JSON                | Platform processing and validation          | Authoritative machine representation |
| Exchange       | YAML                | Toolchain exchange and pipeline integration | Interoperability representation      |

The canonical JSON representation SHALL govern when two representations disagree.

### 2.2 Authored Markdown Representation

The authored representation MUST use structured Markdown for human authoring, review, governance approval, and version control.

Authored Markdown MUST follow these rules:

* Top-level sections MUST use numbered headings.
* Required sections MUST appear in the order defined by this document.
* Structured elements MUST be represented using tables or labeled field lists.
* Each specification element requiring traceability MUST include an identifier.
* Prose MAY explain intent but MUST NOT replace required structured fields.

### 2.3 Canonical JSON Representation

The canonical JSON representation is the authoritative internal form used by conforming ISL processors. It is derived from the authored specification through normalization.

The canonical representation MUST:

* conform to the canonical semantic model defined in ISL v1.1
* preserve all identifiers from the authored specification
* preserve all relationships required for traceability
* reject ambiguous or unmappable authored content
* record normalization errors when mapping fails

### 2.4 YAML Exchange Representation

The YAML exchange representation MAY be used for toolchain integration, pipeline transfer, or configuration-based workflows. YAML MUST NOT introduce semantics that are absent from the canonical JSON representation. A YAML exchange document MUST be transformable into canonical JSON without semantic loss.

### 2.5 Graphical and Visual Representations

Graphical editors, diagramming tools, or visual modeling environments MAY be used as authoring surfaces if they produce output that conforms to the authored or canonical representation rules. A graphical representation SHALL NOT be considered authoritative unless it can be exported into a valid ISL representation.

---

## 3.0 Authored Markdown Syntax

The authored syntax uses numbered sections, tables, enumerated values, and stable identifiers rather than a custom programming-language grammar.

### 3.1 Document Header Syntax

Every authored ISL specification MUST begin with a document header including:

| Field                 | Required | Description                           |
| --------------------- | -------- | ------------------------------------- |
| Document Title        | REQUIRED | Human-readable specification title    |
| System ID             | REQUIRED | Stable system identifier              |
| Specification Version | REQUIRED | Semantic version of the specification |
| ISL Version           | REQUIRED | ISL language version used             |
| Status                | REQUIRED | Current readiness status              |
| Owner                 | REQUIRED | Responsible organizational unit       |
| Last Modified         | REQUIRED | ISO 8601 date                         |

Recommended authored Markdown form:

```
# System Name Specification

**System ID:** example-system
**Specification Version:** 1.0.0
**ISL Version:** 1.0
**Status:** Draft
**Owner:** Example Organization
**Last Modified:** 2026-05-02
```

### 3.2 Top-Level Section Syntax

Each required ISL section MUST be represented as a numbered second-level Markdown heading:

```
## 1. System Identity and Context
## 2. Business Objectives and Scope
## 3. Actors and Stakeholders
## 4. Functional Requirements
## 5. Non-Functional Requirements
## 6. Data Model and Information Structures
## 7. Service Boundaries and Interfaces
## 8. Workflows and Behavioral Processes
## 9. Security and Policy Constraints
## 10. Operational and Deployment Expectations
## 11. Validation and Acceptance Criteria
## 12. Change History
```

A conforming parser MUST reject a Machine-Valid candidate specification if any REQUIRED section is missing.

### 3.3 Structured Element Syntax

Structured elements SHOULD be represented as Markdown tables when multiple elements of the same type appear in a section. A structured element table MUST include:

* an identifier column when the element maps to a canonical entity
* a name or statement column when the element has human-readable content
* a type or classification column when the element is typed
* a validation or reference column when the element must be verified

Field-list syntax MAY be used when a section contains a single complex element.

### 3.4 Required Field Syntax

Required fields MUST be explicitly labeled. A parser MUST NOT infer a required field from surrounding prose unless the field appears in a defined structured location.

Valid field-list form:

```
* **id:** REQ-example-00001
* **statement:** The system MUST authenticate users before granting access.
* **priority:** must-have
* **source:** STK-example-00001
* **validation:** VAL-example-00001
```

### 3.5 Controlled Vocabulary Syntax

Where this document defines enum values, authored specifications MUST use the exact lowercase enum value unless a later ISL document explicitly permits aliases. All shared enums (for example requirement priority: `must-have`, `should-have`, `nice-to-have`) are defined in ISL v0.1. A conforming processor MUST reject unrecognized enum values during Machine-Valid evaluation.

### 3.6 Prose Usage

Prose MAY be used to explain rationale, context, assumptions, design intent, or business background. Prose MUST NOT be used as the only representation of required specification content. If prose conflicts with structured fields, the structured fields SHALL govern.

---

## 4.0 Required Specification Structure

The following sections form the minimum complete language surface required to describe a system for review, semantic normalization, and eventual autonomous construction:

| Section | Title                                   | Requirement Level | Canonical Mapping                    |
| ------- | --------------------------------------- | ----------------- | ------------------------------------ |
| 1       | System Identity and Context             | REQUIRED          | Project, Context                     |
| 2       | Business Objectives and Scope           | REQUIRED          | Requirement, Capability, Context     |
| 3       | Actors and Stakeholders                 | REQUIRED          | Actor, Stakeholder                   |
| 4       | Functional Requirements                 | REQUIRED          | Requirement                          |
| 5       | Non-Functional Requirements             | REQUIRED          | Requirement, Policy                  |
| 6       | Data Model and Information Structures   | REQUIRED          | DataEntity                           |
| 7       | Service Boundaries and Interfaces       | REQUIRED          | Service, Interface                   |
| 8       | Workflows and Behavioral Processes      | CONDITIONAL       | Workflow                             |
| 9       | Security and Policy Constraints         | REQUIRED          | Policy                               |
| 10      | Operational and Deployment Expectations | REQUIRED          | Infrastructure, Policy               |
| 11      | Validation and Acceptance Criteria      | REQUIRED          | Validation                           |
| 12      | Change History                          | REQUIRED          | Project metadata, governance records |

A specification MAY include appendices, examples, diagrams, and explanatory material, but such material MUST NOT replace required sections.

### 4.1 Control Sections

A specification MAY include structured control sections for reuse-first construction and context drift control. These sections become REQUIRED when the specified system, construction plan, or deployment profile relies on reusable assets, boundary-scoped construction, cross-boundary context transfer, local resource minimization, cloud model routing, or enterprise conformance.

| Control Section                         | Requirement Level | Canonical Mapping                                        |
| --------------------------------------- | ----------------- | -------------------------------------------------------- |
| Reusable Assets and Reuse Preferences   | CONDITIONAL       | ReusableAsset, Requirement, Service, Interface           |
| Construction Boundaries                 | CONDITIONAL       | ConstructionBoundary planning records and task scope     |
| Connection Contexts                     | CONDITIONAL       | ConnectionContext planning records and traceability      |

Reusable Assets and Reuse Preferences MUST identify approved reusable assets, candidate asset constraints, reuse prohibitions, promotion preferences, or reuse exception requirements when reuse is enabled or expected.

Construction Boundaries MUST identify the semantic, architectural, execution, artifact, data, and model-context scope within which autonomous construction must remain when boundary-scoped work applies.

Connection Contexts MUST identify the minimal authorized context transfer between boundaries, including allowed artifacts, decisions, policies, sensitivity, expiration, and drift checks when any boundary crossing is expected.

A specification that includes these sections MUST preserve enough structured information for planning, reuse discovery, boundary validation, and enterprise conformance evidence.

### 4.2 Reusable Asset Authoring Fields

When the Reusable Assets and Reuse Preferences section is present, each structured reusable asset reference or preference MUST include the applicable fields below.

| Field              | Type   | Required    | Description                                                              |
| ------------------ | ------ | ----------- | ------------------------------------------------------------------------ |
| reusable-asset-id  | string | CONDITIONAL | Required when an existing registered asset is referenced                 |
| asset-type         | enum   | REQUIRED    | library, module, component, utility, service-contract, schema, template, infrastructure-module, validation-harness, policy-module, agent-pattern, or approved extension |
| intended-use       | enum   | REQUIRED    | reuse-full, reuse-partial, wrap, extend, compose, promote-candidate, prohibit |
| source-entity-ids  | array  | REQUIRED    | Requirements, services, interfaces, policies, or validations the asset relates to |
| allowed-scope      | enum   | REQUIRED    | project, workspace, tenant, enterprise, public, prohibited              |
| required-validation | array | REQUIRED    | Validation evidence required before use or promotion                     |
| governance-condition | string | CONDITIONAL | Approval, waiver, ownership, or policy condition required before use     |
| rationale          | string | REQUIRED    | Reason the asset is preferred, constrained, promoted, or prohibited      |

### 4.3 Construction Boundary Authoring Fields

When the Construction Boundaries section is present, each boundary MUST include the applicable fields below.

| Field                  | Type   | Required    | Description                                                             |
| ---------------------- | ------ | ----------- | ----------------------------------------------------------------------- |
| boundary-id            | string | REQUIRED    | Stable Construction Boundary identifier                                 |
| boundary-name          | string | REQUIRED    | Human-readable boundary name                                            |
| boundary-type          | enum   | REQUIRED    | Boundary type compatible with construction-boundary planning            |
| included-entity-ids    | array  | REQUIRED    | Specification entities included in the boundary                         |
| excluded-scope         | array  | REQUIRED    | Explicitly excluded entities, responsibilities, assumptions, or artifact areas |
| expected-artifact-types | array | CONDITIONAL | Artifact categories expected from the boundary                          |
| allowed-reusable-assets | array | CONDITIONAL | Reusable assets permitted inside the boundary                           |
| prohibited-context     | array  | CONDITIONAL | Context, data, assumptions, dependencies, or policies that MUST NOT enter the boundary |
| connection-context-ids | array  | REQUIRED    | Connection Contexts authorized for boundary crossing                    |
| drift-handling         | enum   | REQUIRED    | block, repair, replan, escalate                                         |

### 4.4 Connection Context Authoring Fields

When the Connection Contexts section is present, each Connection Context MUST include the applicable fields below.

| Field                   | Type   | Required    | Description                                                              |
| ----------------------- | ------ | ----------- | ------------------------------------------------------------------------ |
| connection-context-id   | string | REQUIRED    | Stable Connection Context identifier                                     |
| from-boundary-id        | string | REQUIRED    | Boundary providing context                                               |
| to-boundary-id          | string | REQUIRED    | Boundary receiving context                                               |
| context-purpose         | string | REQUIRED    | Reason the context transfer is authorized                                |
| allowed-elements        | array  | REQUIRED    | Minimal artifacts, decisions, entities, validations, policies, or summaries allowed to cross |
| prohibited-elements     | array  | CONDITIONAL | Elements that MUST NOT cross                                             |
| sensitivity-classification | enum | REQUIRED    | public, internal, confidential, restricted                              |
| minimization-rule       | string | REQUIRED    | Rule proving the transfer is limited to necessary context                |
| validation-rule         | string | REQUIRED    | Check proving the context is valid before use                            |
| expiration-rule         | string | CONDITIONAL | Time, event, version, or governance condition that ends validity         |

---

## 5.0 Section 1 — System Identity and Context

This section identifies the system being specified and establishes its operating context. It MUST be complete before a specification can progress beyond Draft readiness.

### 5.1 Required Fields

| Field            | Type   | Required    | Description                                        |
| ---------------- | ------ | ----------- | -------------------------------------------------- |
| system-id        | string | REQUIRED    | Stable identifier for the system                   |
| name             | string | REQUIRED    | Human-readable system name                         |
| version          | semver | REQUIRED    | Specification version                              |
| domain           | string | REQUIRED    | Business or technical domain                       |
| owner            | string | REQUIRED    | Responsible organization or unit                   |
| description      | string | REQUIRED    | Purpose of the system                              |
| isl-version      | string | REQUIRED    | ISL version used to author the specification       |
| readiness-level  | enum   | REQUIRED    | draft, reviewable, machine-valid, autonomous-ready |
| external-context | array  | CONDITIONAL | External systems, constraints, or dependencies     |

### 5.2 Rules

The system-id MUST remain stable across specification versions unless the system is formally replaced by a new system. The name MAY change for readability, but the system-id MUST remain the traceability anchor.

The description MUST explain the purpose of the system in two to five sentences. Marketing language, aspirational claims, or vague statements MUST NOT substitute for a clear statement of purpose.

### 5.3 Minimum Authored Form

| Field           | Value                                                                                |
| --------------- | ------------------------------------------------------------------------------------ |
| system-id       | example-system                                                                       |
| name            | Example System                                                                       |
| version         | 1.0.0                                                                                |
| domain          | order-management                                                                     |
| owner           | Enterprise Architecture                                                              |
| description     | The system manages order intake, validation, and status tracking for internal users. |
| isl-version     | 1.0                                                                                  |
| readiness-level | draft                                                                                |

---

## 6.0 Section 2 — Business Objectives and Scope

This section defines why the system exists and what boundaries constrain its responsibility.

### 6.1 Business Objective Fields

| Field     | Type   | Required | Description                          |
| --------- | ------ | -------- | ------------------------------------ |
| id        | string | REQUIRED | Objective identifier                 |
| statement | string | REQUIRED | Measurable outcome statement         |
| metric    | string | REQUIRED | Measurement used to evaluate success |
| target    | string | REQUIRED | Required target value                |
| source    | string | REQUIRED | Stakeholder or business driver       |
| priority  | enum   | REQUIRED | must-have, should-have, nice-to-have |

### 6.2 Scope Fields

| Field        | Type  | Required    | Description                             |
| ------------ | ----- | ----------- | --------------------------------------- |
| in-scope     | array | REQUIRED    | Responsibilities included in the system |
| out-of-scope | array | REQUIRED    | Responsibilities explicitly excluded    |
| assumptions  | array | CONDITIONAL | Assumptions required for interpretation |
| dependencies | array | CONDITIONAL | External dependencies affecting scope   |

### 6.3 Rules

Each business objective MUST be measurable. Statements such as "improve user experience," "increase efficiency," or "modernize the platform" MUST NOT be accepted unless accompanied by measurable targets.

Each specification MUST include at least one explicit scope exclusion before reaching Reviewable readiness. Implicit exclusions MUST NOT be assumed.

### 6.4 Minimum Authored Form

| id                | statement                             | metric             | target                            | source            | priority  |
| ----------------- | ------------------------------------- | ------------------ | --------------------------------- | ----------------- | --------- |
| OBJ-example-00001 | Reduce manual order validation effort | manual review rate | less than 10% of submitted orders | STK-example-00001 | must-have |

Scope:

* **in-scope:** order intake, order validation, order status tracking
* **out-of-scope:** payment processing, shipment execution
* **assumptions:** customer identity is provided by an external identity provider

---

## 7.0 Section 3 — Actors and Stakeholders

This section identifies the participants relevant to the system. Actors interact directly with the system at runtime. Stakeholders have an interest in the system's outcomes, governance, funding, compliance, or operation.

### 7.1 Actor Fields

| Field           | Type   | Required    | Description                             |
| --------------- | ------ | ----------- | --------------------------------------- |
| id              | string | REQUIRED    | Actor identifier                        |
| name            | string | REQUIRED    | Human-readable actor name               |
| actor-type      | enum   | REQUIRED    | human, system, process, external        |
| description     | string | REQUIRED    | Runtime role of the actor               |
| permissions     | array  | REQUIRED    | Operations the actor may perform        |
| interfaces-used | array  | CONDITIONAL | Interface identifiers used by the actor |

### 7.2 Stakeholder Fields

| Field              | Type    | Required | Description                                                    |
| ------------------ | ------- | -------- | -------------------------------------------------------------- |
| id                 | string  | REQUIRED | Stakeholder identifier                                         |
| name               | string  | REQUIRED | Human-readable stakeholder name                                |
| role               | string  | REQUIRED | Organizational or governance role                              |
| concerns           | array   | REQUIRED | Outcomes or qualities the stakeholder cares about              |
| approval-authority | boolean | REQUIRED | Whether this stakeholder can approve specification progression |

### 7.3 Rules

Every functional requirement MUST reference at least one actor, stakeholder, or business objective as its source.

An actor MUST NOT be assigned permissions that are not later reflected in interface, workflow, or policy definitions.

A stakeholder with approval-authority set to true MUST correspond to a governance role defined under ISL v1.3 before the specification can reach Machine-Valid readiness.

### 7.4 Minimum Authored Form

Actors:

| id                | name        | actor-type | description                                  | permissions                     |
| ----------------- | ----------- | ---------- | -------------------------------------------- | ------------------------------- |
| ACT-example-00001 | Order Clerk | human      | Internal user who submits and reviews orders | create-order, view-order-status |

Stakeholders:

| id                | name               | role           | concerns                         | approval-authority |
| ----------------- | ------------------ | -------------- | -------------------------------- | ------------------ |
| STK-example-00001 | Operations Manager | Business Owner | order accuracy, processing speed | true               |

---

## 8.0 Section 4 — Functional Requirements

This section defines the behaviors the system must provide. Functional requirements MUST be specific, active-voice, and verifiable.

### 8.1 Required Fields

| Field      | Type   | Required    | Description                                 |
| ---------- | ------ | ----------- | ------------------------------------------- |
| id         | string | REQUIRED    | Requirement identifier                      |
| statement  | string | REQUIRED    | Active-voice behavioral requirement         |
| priority   | enum   | REQUIRED    | must-have, should-have, nice-to-have        |
| source     | string | REQUIRED    | Actor, stakeholder, or objective identifier |
| validation | string | REQUIRED    | Validation identifier or method             |
| capability | string | CONDITIONAL | Capability grouping identifier              |
| rationale  | string | OPTIONAL    | Explanation of why the requirement exists   |

### 8.2 Requirement Statement Rules

A functional requirement statement MUST:

* describe one behavior only
* use active voice
* identify the system responsibility
* avoid implementation details unless architecturally required
* avoid ambiguous terms such as fast, easy, robust, seamless, normal, appropriate, or user-friendly unless quantified elsewhere

A functional requirement statement MUST NOT use:

* should
* might
* could
* as needed
* where appropriate
* etc.

### 8.3 Priority Rules

| Priority     | Meaning                                          |
| ------------ | ------------------------------------------------ |
| must-have    | Required for system acceptance                   |
| should-have  | Expected unless formally deferred                |
| nice-to-have | Optional enhancement not required for acceptance |

A must-have functional requirement MUST have at least one linked Validation entity before the specification can reach Autonomous-Ready readiness.

### 8.4 Minimum Authored Form

| id                | statement                                                                                                 | priority  | source            | validation        |
| ----------------- | --------------------------------------------------------------------------------------------------------- | --------- | ----------------- | ----------------- |
| REQ-example-00001 | The system MUST allow an Order Clerk to submit a new order with customer, item, and quantity information. | must-have | ACT-example-00001 | VAL-example-00001 |

---

## 9.0 Section 5 — Non-Functional Requirements

This section defines quality attributes, constraints, and measurable expectations that govern how the system must behave. Non-functional requirements MUST be measurable wherever measurement is possible. A non-functional requirement that cannot be measured MUST define a reviewable compliance method.

### 9.1 Required Fields

| Field          | Type   | Required    | Description                                  |
| -------------- | ------ | ----------- | -------------------------------------------- |
| id             | string | REQUIRED    | Requirement identifier                       |
| characteristic | enum   | REQUIRED    | ISO/IEC 25010-aligned quality characteristic |
| statement      | string | REQUIRED    | Quality or constraint statement              |
| metric         | string | CONDITIONAL | Measurement used to verify the requirement   |
| target         | string | CONDITIONAL | Required target value                        |
| source         | string | REQUIRED    | Stakeholder, policy, or objective identifier |
| validation     | string | REQUIRED    | Validation identifier or method              |

### 9.2 Quality Characteristics

ISL specifications SHOULD classify non-functional requirements using ISO/IEC 25010-aligned characteristics.

| Characteristic         | Example Measures                               |
| ---------------------- | ---------------------------------------------- |
| functional-suitability | completeness, correctness                      |
| performance-efficiency | response time, throughput, latency             |
| compatibility          | interoperability, coexistence                  |
| usability              | task completion rate, accessibility compliance |
| reliability            | availability, error rate, recovery time        |
| security               | authentication, authorization, encryption      |
| maintainability        | code coverage, complexity, modularity          |
| portability            | supported runtime environments                 |

### 9.3 Rules

A non-functional requirement MUST NOT use subjective quality statements without a measurable or reviewable target.

Examples of invalid statements:

* The system must be fast.
* The system must be secure.
* The system must be easy to maintain.

Examples of valid statements:

* The system MUST respond to 95% of order status requests within 500 milliseconds under normal operating load.
* The system MUST encrypt customer identifiers at rest using an approved enterprise encryption standard.
* The system MUST maintain unit test coverage of at least 80% for generated business logic.

### 9.4 Minimum Authored Form

| id                | characteristic         | statement                                                                           | metric      | target | source            | validation        |
| ----------------- | ---------------------- | ----------------------------------------------------------------------------------- | ----------- | ------ | ----------------- | ----------------- |
| REQ-example-00002 | performance-efficiency | The system MUST respond to order status requests within the defined latency target. | p95 latency | 500 ms | STK-example-00001 | VAL-example-00002 |

---

## 10.0 Section 6 — Data Model and Information Structures

This section defines the information managed by the system.

### 10.1 Data Entity Fields

| Field         | Type   | Required    | Description                                 |
| ------------- | ------ | ----------- | ------------------------------------------- |
| id            | string | REQUIRED    | Data entity identifier                      |
| name          | string | REQUIRED    | Human-readable entity name                  |
| description   | string | REQUIRED    | Purpose of the data entity                  |
| attributes    | array  | REQUIRED    | Attribute definitions                       |
| relationships | array  | CONDITIONAL | Relationships to other data entities        |
| owner-service | string | CONDITIONAL | Service responsible for managing the entity |

### 10.2 Attribute Fields

| Field       | Type    | Required    | Description                              |
| ----------- | ------- | ----------- | ---------------------------------------- |
| name        | string  | REQUIRED    | Attribute name                           |
| data-type   | string  | REQUIRED    | Primitive, structured, or reference type |
| nullable    | boolean | REQUIRED    | Whether null is permitted                |
| constraints | array   | CONDITIONAL | Validation constraints                   |
| description | string  | CONDITIONAL | Attribute purpose                        |

### 10.3 Relationship Fields

| Field             | Type    | Required | Description                           |
| ----------------- | ------- | -------- | ------------------------------------- |
| target            | string  | REQUIRED | Target DataEntity identifier          |
| relationship-type | string  | REQUIRED | Nature of the relationship            |
| cardinality       | enum    | REQUIRED | one-to-one, one-to-many, many-to-many |
| required          | boolean | REQUIRED | Whether the relationship is mandatory |

### 10.4 Rules

Each data entity MUST include at least one attribute. Each attribute MUST declare a data-type and nullability.

Relationships MUST declare cardinality explicitly. Implicit cardinality MUST NOT be assumed.

A data entity that stores sensitive, regulated, or personal information MUST be governed by at least one Policy entity before the specification can reach Machine-Valid readiness.

### 10.5 Minimum Authored Form

Data Entities:

| id                | name  | description                                       | owner-service     |
| ----------------- | ----- | ------------------------------------------------- | ----------------- |
| DAT-example-00001 | Order | Represents an order submitted by an internal user | SVC-example-00001 |

Attributes:

| entity-id         | name       | data-type | nullable | constraints                    |
| ----------------- | ---------- | --------- | -------- | ------------------------------ |
| DAT-example-00001 | orderId    | string    | false    | unique                         |
| DAT-example-00001 | customerId | string    | false    | required                       |
| DAT-example-00001 | status     | enum      | false    | submitted, validated, rejected |

---

## 11.0 Section 7 — Service Boundaries and Interfaces

This section defines the structural components that provide system behavior and the interfaces through which actors, services, or external systems interact.

### 11.1 Service Fields

| Field          | Type   | Required    | Description                                        |
| -------------- | ------ | ----------- | -------------------------------------------------- |
| id             | string | REQUIRED    | Service identifier                                 |
| name           | string | REQUIRED    | Human-readable service name                        |
| responsibility | string | REQUIRED    | Single responsibility statement                    |
| capabilities   | array  | CONDITIONAL | Capability identifiers implemented by the service  |
| requirements   | array  | REQUIRED    | Requirement identifiers implemented by the service |
| data-entities  | array  | CONDITIONAL | DataEntity identifiers managed by the service      |
| dependencies   | array  | CONDITIONAL | Service or external dependencies                   |
| interfaces     | array  | REQUIRED    | Interface identifiers exposed by the service       |

### 11.2 Interface Fields

| Field               | Type   | Required    | Description                                                      |
| ------------------- | ------ | ----------- | ---------------------------------------------------------------- |
| id                  | string | REQUIRED    | Interface identifier                                             |
| name                | string | REQUIRED    | Human-readable interface name                                    |
| interface-type      | enum   | REQUIRED    | rest-api, grpc, message-queue, event-stream, batch-file, graphql |
| exposed-by          | string | REQUIRED    | Service identifier exposing the interface                        |
| used-by             | array  | CONDITIONAL | Actor or service identifiers using the interface                 |
| contract            | string | REQUIRED    | Inline or referenced interface contract                          |
| authentication      | string | REQUIRED    | Authentication mechanism                                         |
| authorization       | string | CONDITIONAL | Authorization rule or policy reference                           |
| versioning-strategy | string | REQUIRED    | Interface versioning approach                                    |

### 11.3 Rules

A service MUST have one clearly stated responsibility. A service MUST NOT overlap responsibility with another service in the same specification unless the overlap is explicitly justified as redundancy, migration support, or failover behavior.

Each service MUST expose or consume at least one interface unless it is explicitly marked as internal infrastructure or background processing.

Each interface MUST define authentication before the specification can reach Machine-Valid readiness.

### 11.4 Minimum Authored Form

Services:

| id                | name          | responsibility                                            | requirements      | interfaces        |
| ----------------- | ------------- | --------------------------------------------------------- | ----------------- | ----------------- |
| SVC-example-00001 | Order Service | Manages order submission, validation, and status tracking | REQ-example-00001 | INT-example-00001 |

Interfaces:

| id                | name      | interface-type | exposed-by        | contract                             | authentication            | versioning-strategy |
| ----------------- | --------- | -------------- | ----------------- | ------------------------------------ | ------------------------- | ------------------- |
| INT-example-00001 | Order API | rest-api       | SVC-example-00001 | OpenAPI reference or inline contract | enterprise-identity-token | URI versioning      |

---

## 12.0 Section 8 — Workflows and Behavioral Processes

This section defines multi-step behavior involving actors, services, decisions, and exception paths.

### 12.1 When Workflows Are Required

A workflow section MUST be included when the system includes behavior involving any of the following:

* more than one actor
* more than one service
* ordered multi-step processing
* decision points
* approval paths
* exception handling
* asynchronous events
* state transitions

### 12.2 Workflow Fields

| Field           | Type   | Required    | Description                                 |
| --------------- | ------ | ----------- | ------------------------------------------- |
| id              | string | REQUIRED    | Workflow identifier                         |
| name            | string | REQUIRED    | Human-readable workflow name                |
| trigger         | string | REQUIRED    | Event or condition that starts the workflow |
| participants    | array  | REQUIRED    | Actors and services involved                |
| steps           | array  | REQUIRED    | Ordered workflow steps                      |
| decision-points | array  | CONDITIONAL | Branching conditions                        |
| exception-paths | array  | REQUIRED    | Failure and exception handling              |
| validation      | string | REQUIRED    | Validation identifier or method             |

### 12.3 Workflow Step Fields

| Field             | Type    | Required    | Description                         |
| ----------------- | ------- | ----------- | ----------------------------------- |
| sequence          | integer | REQUIRED    | Step order                          |
| actor-or-service  | string  | REQUIRED    | Responsible participant             |
| action            | string  | REQUIRED    | Action performed                    |
| input             | string  | CONDITIONAL | Input required                      |
| output            | string  | CONDITIONAL | Output produced                     |
| success-condition | string  | REQUIRED    | Condition for successful completion |

### 12.4 Rules

A workflow MUST include at least one step.

A workflow with no exception paths MUST NOT be considered complete.

Decision points MUST include explicit conditions. A decision point MUST NOT rely on unstated judgment or implicit interpretation.

### 12.5 Minimum Authored Form

Workflows:

| id                | name         | trigger                   | participants                         | validation        |
| ----------------- | ------------ | ------------------------- | ------------------------------------ | ----------------- |
| WFL-example-00001 | Submit Order | Order Clerk submits order | ACT-example-00001, SVC-example-00001 | VAL-example-00003 |

Steps:

| workflow-id       | sequence | actor-or-service  | action               | success-condition                |
| ----------------- | -------- | ----------------- | -------------------- | -------------------------------- |
| WFL-example-00001 | 1        | ACT-example-00001 | Submit order request | Request contains required fields |
| WFL-example-00001 | 2        | SVC-example-00001 | Validate order       | Order is accepted or rejected    |

Exception Paths:

| workflow-id       | condition              | handling                                   |
| ----------------- | ---------------------- | ------------------------------------------ |
| WFL-example-00001 | Required field missing | Reject request and return validation error |

---

## 13.0 Section 9 — Security and Policy Constraints

This section defines rules that constrain system behavior for security, compliance, operational control, data protection, and governance. Policies MUST be expressed as rules, not aspirations.

### 13.1 Policy Fields

| Field              | Type   | Required    | Description                                                                 |
| ------------------ | ------ | ----------- | --------------------------------------------------------------------------- |
| id                 | string | REQUIRED    | Policy identifier                                                           |
| name               | string | REQUIRED    | Human-readable policy name                                                  |
| policy-type        | enum   | REQUIRED    | security, compliance, operational, data-handling, technology-standard, risk |
| rule               | string | REQUIRED    | Verifiable policy rule                                                      |
| enforcement-point  | enum   | REQUIRED    | authoring, planning, generation, validation, deployment, runtime            |
| applies-to         | array  | REQUIRED    | Entity identifiers governed by the policy                                   |
| control-reference  | string | CONDITIONAL | External control reference                                                  |
| violation-response | enum   | REQUIRED    | block, escalate, warn, audit-only                                           |
| validation         | string | REQUIRED    | Validation identifier or method                                             |

### 13.2 Rules

Each policy MUST contain a verifiable rule.

Intent-only policy statements MUST be rejected during Machine-Valid evaluation.

Where applicable, security policies SHOULD reference NIST SP 800-53 or an equivalent enterprise control framework.

A policy with violation-response set to block MUST prevent readiness progression, construction, deployment, or runtime continuation at its enforcement point until resolved or waived under governance rules.

### 13.3 Invalid and Valid Policy Statements

Invalid:

* The system should be secure.
* Access should be controlled appropriately.
* Sensitive data should be protected.

Valid:

* The system MUST require authenticated identity tokens for all order submission requests.
* The system MUST encrypt customer identifiers at rest.
* The system MUST reject requests from actors lacking create-order permission.

### 13.4 Minimum Authored Form

| id                | name                           | policy-type | rule                                                                        | enforcement-point | applies-to        | violation-response | validation        |
| ----------------- | ------------------------------ | ----------- | --------------------------------------------------------------------------- | ----------------- | ----------------- | ------------------ | ----------------- |
| POL-example-00001 | Authenticated Order Submission | security    | The system MUST require authenticated identity tokens for order submission. | validation        | INT-example-00001 | block              | VAL-example-00004 |

---

## 14.0 Section 10 — Operational and Deployment Expectations

This section defines the environment in which the system is expected to operate. It provides structured expectation for the platform to generate, validate, or prepare deployment artifacts later in the lifecycle.

### 14.1 Required Fields

| Field                 | Type   | Required    | Description                                      |
| --------------------- | ------ | ----------- | ------------------------------------------------ |
| runtime-platform      | string | REQUIRED    | Target runtime platform and minimum version      |
| deployment-model      | enum   | REQUIRED    | container, serverless, vm, bare-metal, hybrid    |
| environments          | array  | REQUIRED    | Target environments such as dev, test, prod      |
| resource-requirements | object | REQUIRED    | CPU, memory, storage, network expectations       |
| scaling-model         | enum   | REQUIRED    | fixed, horizontal, vertical, auto                |
| availability-target   | string | CONDITIONAL | Required when availability is a stated objective |
| monitoring            | array  | REQUIRED    | Metrics, logs, traces, or alerts required        |
| backup-recovery       | string | CONDITIONAL | Required when persistent data is managed         |
| operational-owner     | string | REQUIRED    | Team or role responsible for operation           |

### 14.2 Rules

Operational expectations MUST define the target runtime platform and deployment model.

Systems managing persistent data MUST define backup or recovery expectations.

Systems with availability or reliability requirements MUST define monitoring and alerting expectations.

Deployment expectations MUST NOT contradict infrastructure constraints defined in the canonical Infrastructure entity.

### 14.3 Minimum Authored Form

| Field                 | Value                                            |
| --------------------- | ------------------------------------------------ |
| runtime-platform      | .NET 8 or later                                  |
| deployment-model      | container                                        |
| environments          | dev, test, prod                                  |
| resource-requirements | 1 CPU minimum, 512 MB memory minimum             |
| scaling-model         | horizontal                                       |
| monitoring            | request latency, error rate, health check status |
| operational-owner     | Platform Operations                              |

---

## 15.0 Section 11 — Validation and Acceptance Criteria

This section defines how the specification proves that the constructed system satisfies the stated requirements, policies, workflows, and operational expectations.

### 15.1 Validation Fields

| Field            | Type   | Required    | Description                                                                                          |
| ---------------- | ------ | ----------- | ---------------------------------------------------------------------------------------------------- |
| id               | string | REQUIRED    | Validation identifier                                                                                |
| name             | string | REQUIRED    | Human-readable validation name                                                                       |
| validates        | array  | REQUIRED    | Requirement, policy, workflow, interface, or infrastructure identifiers                              |
| validation-type  | enum   | REQUIRED    | test, inspection, static-analysis, security-scan, policy-check, operational-check, governance-review |
| method           | string | REQUIRED    | How validation is performed                                                                          |
| pass-condition   | string | REQUIRED    | Condition required for success                                                                       |
| evidence         | string | CONDITIONAL | Evidence artifact or record expected                                                                 |
| automation-level | enum   | REQUIRED    | manual, semi-automated, automated                                                                    |

### 15.2 Rules

Each must-have functional requirement MUST be linked to at least one validation criterion.

Each policy with violation-response set to block MUST be linked to at least one validation criterion.

Each acceptance criterion MUST define a pass-condition.

Acceptance criteria without linked specification identifiers MUST be rejected during Machine-Valid evaluation.

### 15.3 Minimum Authored Form

| id                | name              | validates         | validation-type | method                                      | pass-condition                    | automation-level |
| ----------------- | ----------------- | ----------------- | --------------- | ------------------------------------------- | --------------------------------- | ---------------- |
| VAL-example-00001 | Submit Order Test | REQ-example-00001 | test            | Execute API test for valid order submission | API returns accepted order status | automated        |

---

## 16.0 Section 12 — Change History

This section records the version history of the specification. A conforming platform MUST NOT process a specification that lacks a valid version identifier and change history after Draft readiness.

### 16.1 Required Fields

| Field             | Type     | Required | Description                                |
| ----------------- | -------- | -------- | ------------------------------------------ |
| version           | semver   | REQUIRED | Specification version                      |
| date              | ISO 8601 | REQUIRED | Date of change                             |
| author            | string   | REQUIRED | Person, role, or system making the change  |
| change-summary    | string   | REQUIRED | Summary of the change                      |
| affected-sections | array    | REQUIRED | Sections modified                          |
| readiness-impact  | enum     | REQUIRED | none, regression-required, review-required |

### 16.2 Rules

Every specification revision MUST increment the semantic version.

A change to requirements, policies, interfaces, data entities, or operational expectations MUST trigger readiness impact analysis as defined by ISL v1.3.

A specification version authorized for autonomous construction MUST be recorded by the governance system.

### 16.3 Minimum Authored Form

| version | date       | author                  | change-summary                 | affected-sections | readiness-impact |
| ------- | ---------- | ----------------------- | ------------------------------ | ----------------- | ---------------- |
| 1.0.0   | 2026-05-02 | Enterprise Architecture | Initial specification baseline | all               | review-required  |

---

## 17.0 Canonical Mapping Requirements

This section defines the mapping obligation between authored ISL sections and canonical semantic entities. It does not define the full canonical schema; that responsibility belongs to ISL v1.1.

### 17.1 Section-to-Entity Mapping

| Authored Section                        | Canonical Entity Types               |
| --------------------------------------- | ------------------------------------ |
| System Identity and Context             | Project, Context                     |
| Business Objectives and Scope           | Requirement, Capability, Context     |
| Actors and Stakeholders                 | Actor, Stakeholder                   |
| Functional Requirements                 | Requirement                          |
| Non-Functional Requirements             | Requirement, Policy                  |
| Data Model and Information Structures   | DataEntity                           |
| Service Boundaries and Interfaces       | Service, Interface                   |
| Workflows and Behavioral Processes      | Workflow                             |
| Security and Policy Constraints         | Policy                               |
| Operational and Deployment Expectations | Infrastructure, Policy               |
| Validation and Acceptance Criteria      | Validation                           |
| Reusable Assets and Reuse Preferences   | ReusableAsset                        |
| Construction Boundaries                 | ConstructionBoundary planning records |
| Connection Contexts                     | ConnectionContext planning records   |
| Change History                          | Project metadata, governance record  |

### 17.2 Mapping Rules

A conforming ISL processor MUST map every required structured element to at least one canonical entity or canonical relationship.

If an authored element cannot be mapped, the processor MUST record a normalization error.

If a required relationship cannot be resolved, the specification MUST NOT reach Machine-Valid readiness.

If authored reuse or boundary control sections exist, a conforming ISL processor MUST preserve their structured identifiers and references for Construction Planning and Reuse Discovery.

### 17.3 Ambiguity Handling

When authored prose is ambiguous, the processor MUST NOT infer hidden requirements, policies, services, or validations.

Ambiguities MUST be recorded and resolved before the specification progresses to Autonomous-Ready readiness.

---

## 18.0 Structural Validation Rules

This section defines the minimum validation rules that apply to authored ISL specifications. Structural validation is language-level and separate from deeper semantic validation performed by ISL v1.1.

### 18.1 Required Structural Checks

A conforming ISL processor MUST verify:

* all required sections are present
* required sections appear in the defined order
* all required fields are populated
* all identifiers are unique within their entity type
* all enum values are valid
* all references point to defined identifiers
* all must-have requirements have validation references
* all policies have verifiable rules
* all workflows have exception paths when workflows are present
* all data relationships define cardinality
* reuse control sections are present when reuse is enabled or enterprise conformance is claimed
* Construction Boundary records are present when boundary-scoped construction applies
* Connection Context records are present when cross-boundary transfer applies
* Connection Context minimization and drift rules are populated when Connection Context records exist

### 18.2 Failure Behavior

If structural validation fails, the processor MUST produce a validation report identifying:

| Field           | Description                                |
| --------------- | ------------------------------------------ |
| error-id        | Unique validation error identifier         |
| section         | Section where the failure occurred         |
| element-id      | Related element identifier, when available |
| severity        | error, warning, advisory                   |
| message         | Human-readable explanation                 |
| required-action | Action required to resolve the issue       |

A specification with one or more structural validation errors MUST NOT reach Machine-Valid readiness. The base error record structure is defined in ISL v0.1.

---

## 19.0 Language-Level Error Classes

This section defines language-level error classes that occur during authoring, parsing, and structural validation.

| Error Class              | Description                                         | Blocks Machine-Valid |
| ------------------------ | --------------------------------------------------- | -------------------- |
| missing-section          | Required section is absent                          | YES                  |
| missing-field            | Required field is absent                            | YES                  |
| invalid-enum             | Field contains unsupported enum value               | YES                  |
| duplicate-identifier     | Identifier is reused incorrectly                    | YES                  |
| unresolved-reference     | Referenced element does not exist                   | YES                  |
| ambiguous-statement      | Required content is not clear enough to normalize   | YES                  |
| unverifiable-requirement | Requirement lacks validation method                 | YES                  |
| unverifiable-policy      | Policy lacks enforceable rule or validation         | YES                  |
| incomplete-workflow      | Workflow lacks steps or exception paths             | YES                  |
| advisory-quality         | Content is valid but lower quality than recommended | NO                   |

---

## 20.0 Readiness Relationship

This section defines how ISL v1.0 relates to the readiness model defined in ISL v1.3. ISL v1.0 does not itself assign final readiness levels, but it defines the authored content that readiness evaluation depends on. Readiness level values are defined in ISL v0.1.

### 20.1 Draft Readiness

A Draft specification MAY contain incomplete sections, provisional identifiers, and unresolved questions. A Draft specification SHOULD still use the required section structure to reduce future normalization effort.

### 20.2 Reviewable Readiness

To become Reviewable, a specification MUST include:

* complete System Identity and Context
* at least one measurable business objective
* at least one explicit scope exclusion
* at least one actor or stakeholder
* at least one functional requirement
* a valid semantic version
* a declared ISL version

### 20.3 Machine-Valid Readiness

To become Machine-Valid, a specification MUST satisfy all required structural validation rules in this document and all canonical normalization rules in ISL v1.1.

### 20.4 Autonomous-Ready Readiness

To become Autonomous-Ready, a specification MUST satisfy Machine-Valid readiness and all governance, traceability, validation, and authorization requirements defined by later ISL documents.

---

## 21.0 Extension Rules

Extensions are permitted only when they preserve the meaning of core ISL constructs and remain compatible with canonical normalization.

### 21.1 Extension Declaration

An extension MUST declare:

| Field         | Type   | Required | Description                       |
| ------------- | ------ | -------- | --------------------------------- |
| extension-id  | string | REQUIRED | Unique extension identifier       |
| name          | string | REQUIRED | Human-readable extension name     |
| version       | semver | REQUIRED | Extension version                 |
| owner         | string | REQUIRED | Extension owner                   |
| applies-to    | array  | REQUIRED | Sections or entity types extended |
| compatibility | string | REQUIRED | Supported ISL versions            |

### 21.2 Extension Constraints

An extension MUST NOT:

* remove required ISL sections
* redefine core enum values
* weaken validation requirements
* bypass readiness gates
* bypass governance controls
* break canonical mapping

### 21.3 Extension Processing

A conforming processor MAY reject specifications using unsupported extensions. A processor that accepts an extension MUST preserve extension data during normalization or explicitly record unsupported fields as validation errors.

---

## 22.0 Minimum Complete Specification Checklist

A specification MUST satisfy all applicable checklist items before Machine-Valid readiness.

| Item                                                              | Required |
| ----------------------------------------------------------------- | -------- |
| System Identity block is complete                                 | YES      |
| Specification version uses semantic versioning                    | YES      |
| ISL version is declared                                           | YES      |
| At least one measurable business objective exists                 | YES      |
| At least one explicit scope exclusion exists                      | YES      |
| Actors and stakeholders are identified                            | YES      |
| Functional requirements are uniquely identified                   | YES      |
| Functional requirements use active voice                          | YES      |
| Functional requirements avoid ambiguous language                  | YES      |
| Non-functional requirements use measurable targets where possible | YES      |
| Non-functional requirements are quality-classified                | YES      |
| Data entities define attributes, types, and constraints           | YES      |
| Data relationships define cardinality                             | YES      |
| Services define single responsibilities                           | YES      |
| Interfaces define contracts and authentication                    | YES      |
| Workflows define exception paths when workflows exist             | YES      |
| Policies are expressed as verifiable rules                        | YES      |
| Operational expectations define runtime and deployment model      | YES      |
| Acceptance criteria are linked to requirements or policies        | YES      |
| Change history is present                                         | YES      |

---

## 23.0 Minimal Example Specification Fragment

This section provides a minimal authored example to clarify the expected language style. Examples are informative unless explicitly stated otherwise; however, the structure demonstrated here reflects normative syntax expectations.

```
# Example System Specification

**System ID:** example-system
**Specification Version:** 1.0.0
**ISL Version:** 1.0
**Status:** Draft
**Owner:** Enterprise Architecture
**Last Modified:** 2026-05-02

## 1. System Identity and Context

| Field           | Value                                                                                |
| --------------- | ------------------------------------------------------------------------------------ |
| system-id       | example-system                                                                       |
| name            | Example System                                                                       |
| version         | 1.0.0                                                                                |
| domain          | order-management                                                                     |
| owner           | Enterprise Architecture                                                              |
| description     | The system manages order intake, validation, and status tracking for internal users. |
| isl-version     | 1.0                                                                                  |
| readiness-level | draft                                                                                |

## 2. Business Objectives and Scope

| id                | statement                             | metric             | target        | source            | priority  |
| ----------------- | ------------------------------------- | ------------------ | ------------- | ----------------- | --------- |
| OBJ-example-00001 | Reduce manual order validation effort | manual review rate | less than 10% | STK-example-00001 | must-have |

* **in-scope:** order intake, order validation, order status tracking
* **out-of-scope:** payment processing, shipment execution

## 3. Actors and Stakeholders

| id                | name        | actor-type | description                                  | permissions                     |
| ----------------- | ----------- | ---------- | -------------------------------------------- | ------------------------------- |
| ACT-example-00001 | Order Clerk | human      | Internal user who submits and reviews orders | create-order, view-order-status |

| id                | name               | role           | concerns                         | approval-authority |
| ----------------- | ------------------ | -------------- | -------------------------------- | ------------------ |
| STK-example-00001 | Operations Manager | Business Owner | order accuracy, processing speed | true               |

## 4. Functional Requirements

| id                | statement                                                                                                 | priority  | source            | validation        |
| ----------------- | --------------------------------------------------------------------------------------------------------- | --------- | ----------------- | ----------------- |
| REQ-example-00001 | The system MUST allow an Order Clerk to submit a new order with customer, item, and quantity information. | must-have | ACT-example-00001 | VAL-example-00001 |

## 11. Validation and Acceptance Criteria

| id                | name              | validates         | validation-type | method                                      | pass-condition                    | automation-level |
| ----------------- | ----------------- | ----------------- | --------------- | ------------------------------------------- | --------------------------------- | ---------------- |
| VAL-example-00001 | Submit Order Test | REQ-example-00001 | test            | Execute API test for valid order submission | API returns accepted order status | automated        |
```

---

## 24.0 Conformance Requirements

Conformance is separated by role because a document, parser, and platform do not satisfy the same obligations.

### 24.1 Authored Specification Conformance

An authored specification conforms to ISL v1.0 if it:

* includes all required sections
* uses the required section order
* provides all required fields
* uses valid enum values
* links requirements to validation criteria
* expresses policies as verifiable rules
* declares version and ISL version
* can be normalized into ISL v1.1 canonical form

### 24.2 Parser Conformance

An ISL parser conforms to ISL v1.0 if it can:

* identify all required sections
* extract structured fields from authored Markdown
* validate required fields and enum values
* detect unresolved references
* report language-level errors using defined error classes
* produce canonical input suitable for ISL v1.1 normalization

### 24.3 Authoring Tool Conformance

An ISL authoring tool conforms to ISL v1.0 if it can:

* guide authors through required sections
* prevent or flag missing required fields
* validate enum values
* detect duplicate identifiers
* warn about ambiguous requirement language
* produce valid authored Markdown or canonical JSON
