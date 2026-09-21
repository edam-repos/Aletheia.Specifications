# ISL-Sage-X Integration Profile

# Specification Reuse, Execution Bridging, and Smart Construction Profile

**Status:** Draft Normative  
**Profile Version:** 0.1  
**Release:** Draft  
**Applies To:** ISL r2, Sage-X v1r0, ALETHEIA reference implementation planning  
**Document Type:** Integration Profile, Reuse Governance Specification, Execution Binding Specification

---

# 1.0 Scope

This profile defines the formal integration between the ALETHEIA Specification Language (ISL) and the Sage-X Execution Platform.

The profile exists to prevent blind or duplicate construction when an existing Sage-X code set, schema set, execution engine, control surface, registry, validation harness, or operational artifact already satisfies part of an ISL or ALETHEIA construction need.

This profile specifies:

* how ISL authored and canonical specifications are admitted into Sage-X
* how ISL canonical entities map to Sage-X Knowledge Units
* how ISL construction plans map to Sage-X execution plans
* how Sage-X artifacts are registered as reusable construction assets
* how ISL planning discovers, evaluates, selects, wraps, extends, composes, forks, or rejects existing Sage-X code
* how shared schemas and contracts are classified
* how governance decisions authorize reuse
* how traceability is preserved from ISL specification intent to Sage-X artifacts and ALETHEIA implementation outputs
* how conformance is evaluated for the bridge layer

This profile applies to:

* ISL processors
* Sage-X specification plugins
* Sage-X planning and execution engines
* ALETHEIA construction planning
* reusable asset registries
* capability registries
* artifact repositories
* governance engines
* traceability engines
* validation and certification workflows
* reference implementation repositories

This profile does not require a particular programming language, package manager, source control system, cloud provider, database, model provider, deployment topology, or transport protocol.

---

# 2.0 Purpose

The purpose of this profile is to make ISL construction reuse-aware and Sage-X execution reusable by design.

ISL defines the construction semantics for ALETHEIA. Sage-X defines a specification-agnostic execution platform capable of compiling formal specifications into Knowledge Units, generating execution plans, executing governed steps, recording state, validating outputs, preserving traceability, and recovering from failure.

The two systems intersect in several enterprise-critical areas:

* specification compilation
* canonical knowledge representation
* construction planning
* execution planning
* execution state
* trace events
* validation results
* checkpoints
* failure records
* governance decisions
* capability and tool registration
* artifact metadata
* reusable asset admission
* observability and audit evidence

Without an integration profile, ISL construction may generate new code for behaviors already implemented and validated in Sage-X. That creates duplicated engines, inconsistent schemas, governance drift, unnecessary maintenance burden, and unclear provenance.

This profile requires ISL construction to inspect existing Sage-X assets before generation, evaluate their fitness against ISL construction intent, reuse or adapt them where appropriate, and record evidence for every reuse decision.

---

# 3.0 Normative References

The following specifications are required to interpret this profile.

| Reference | Purpose |
| --------- | ------- |
| ISL r2 v0.0 | Corpus-level standard, conformance, system integrity rules |
| ISL r2 v0.1 | Schema and conformance artifact model |
| ISL r2 v1.0 | Authored ISL language surface |
| ISL r2 v1.1 | Canonical semantic model |
| ISL r2 v1.2 | ASC execution model |
| ISL r2 v1.3 | Specification readiness levels |
| ISL r2 v1.4 | Traceability model |
| ISL r2 v1.5 | Construction planning model |
| ISL r2 v1.6 | Tool integration model |
| ISL r2 v1.7 | Governance and control model |
| ISL r2 v1.8 | Reusable asset and construction reuse model |
| ISL r2 v2.0 | ALETHEIA platform architecture model |
| ISL r2 v2.2 | State and memory model |
| ISL r2 v2.3 | Artifact repository model |
| ISL r2 v2.4 | Execution runtime model |
| ISL r2 v2.6 | Observability and telemetry model |
| ISL r2 v3.1 | Reference repository layout and service topology |
| ISL r2 v3.6 | Autonomous development loop |
| ISL r2 v3.7 | Autonomous SDLC model |
| Sage-X v1r0 Architecture | Specification-agnostic execution platform architecture |
| Sage-X v1r0 Execution State Machine | Formal execution state behavior |
| Sage-X v1r0 Trace and Observability | Trace and observability behavior |
| Sage-X v1r0 Execution API and Control Surface | External execution, planning, governance, and observability interfaces |
| Sage-X v1r0 System Assembly | Integrated operational model |
| Sage-X v1r0 Certification and Conformance | Certification expectations |
| Sage-X v1r0 Reference Implementation Blueprint | Logical implementation modules |
| Sage-X v1r0 Specification Plugin Interface | External specification language integration |
| Sage-X v1r0 Capability Registry | Capability registration, discovery, and invocation |
| Sage-X v1r0 Security Model | Execution security and isolation |
| Sage-X v1r0 Governance Model | Policy, approval, override, and enforcement behavior |
| Sage-X v1r0 Failure and Recovery Model | Failure records, rollback, branch, and recovery behavior |
| Sage-X v1r0 Deployment Architecture | Runtime deployment and process boundaries |
| Sage-X v1r0 Observability Model | Execution trace and telemetry evidence |

---

# 4.0 Normative Language

The key words MUST, MUST NOT, SHALL, SHALL NOT, SHOULD, SHOULD NOT, MAY, and OPTIONAL are to be interpreted as normative requirement levels.

* MUST and SHALL indicate mandatory requirements.
* MUST NOT and SHALL NOT indicate mandatory prohibitions.
* SHOULD indicates recommended behavior. Deviations MUST be justified.
* MAY indicates permitted behavior.

All mandatory requirements in this profile are enforceable for implementations claiming conformance to the applicable conformance class.

---

# 5.0 Terms and Definitions

**ISL**
The ALETHEIA Specification Language, including its authored representation, canonical semantic model, planning model, governance model, traceability model, execution model, and platform architecture requirements.

**Sage-X**
The specification-agnostic execution platform that compiles specifications into Knowledge Units, generates execution plans, executes governed steps, validates results, records state, and preserves traceability.

**Integration Profile**
A governed set of mapping, reuse, execution, evidence, and conformance rules that define how ISL and Sage-X interoperate.

**ISL-Sage-X Bridge**
The combined plugin, registry, mapping, planning, governance, and traceability behavior required to connect ISL semantics to Sage-X execution.

**ISL Specification Plugin**
A Sage-X specification plugin that ingests ISL authored or canonical specifications and produces Sage-X Knowledge Units with traceable ISL origin metadata.

**Knowledge Unit**
The Sage-X atomic structured representation derived from a specification element.

**Canonical Entity**
An ISL semantic object produced through ISL normalization.

**Construction Need**
A planned ISL requirement for code, schema, interface, contract, engine behavior, console behavior, test, validation harness, policy, documentation, infrastructure, or operational artifact.

**Sage-X Asset**
A code module, schema, contract, service, engine, adapter, capability, test harness, validation tool, trace schema, state model, governance component, or deployment artifact produced by or for Sage-X.

**Bridge Asset**
A Sage-X asset admitted into ISL construction planning as a candidate reusable asset.

**Reuse Fitness**
The evaluated degree to which a Sage-X asset satisfies an ISL construction need.

**Reuse Mode**
The authorized treatment of a candidate asset: use-as-is, wrap, extend, compose, fork, generate-new, reject, or escalate.

**Shared Contract**
A schema, interface, event, record, or API contract that is usable by both Sage-X and ISL/ALETHEIA without semantic contradiction.

**ISL-Specific Extension**
Additional behavior or metadata required by ISL that is layered over a Sage-X asset without changing the Sage-X core contract.

**Semantic Ownership**
The system that defines the meaning of an artifact or concept. ISL owns ALETHEIA construction semantics. Sage-X owns generic execution mechanics.

---

# 6.0 Integration Principles

## 6.1 Reuse Before Construction

An ISL Planning Engine claiming conformance to this profile MUST search for reusable Sage-X assets before authorizing new construction for any overlapping execution, planning, validation, traceability, governance, state, registry, or observability function.

Generation MUST NOT be treated as the default action when an existing Sage-X asset may satisfy the construction need.

## 6.2 ISL Owns Construction Semantics

ISL SHALL remain the semantic authority for ALETHEIA autonomous software construction.

Sage-X MUST NOT reinterpret ISL requirements, readiness levels, canonical entities, construction boundaries, governance gates, or traceability semantics unless authorized through an ISL specification plugin or ISL extension profile.

## 6.3 Sage-X Owns Execution Mechanics

Sage-X SHALL remain the execution authority for generic specification-agnostic planning, execution state management, step orchestration, checkpointing, validation progression, failure recovery, governed control surface behavior, and capability invocation when those functions are reused by ISL.

ISL-specific construction behavior MUST be expressed through plugin metadata, profile configuration, governed adapters, or ISL-aware planning rules rather than by silently modifying Sage-X core execution mechanics.

## 6.4 Shared Contracts Must Be Explicit

A contract MUST NOT be assumed shared merely because ISL and Sage-X use similar terms.

Each shared contract MUST have an explicit classification record indicating:

* semantic owner
* supported versions
* field-level compatibility
* extension rules
* validation rules
* governance approval state

## 6.5 Reuse Requires Evidence

No Sage-X asset SHALL be reused by ISL construction unless the reuse decision is supported by a recorded fitness assessment, validation evidence, provenance evidence, compatibility evidence, and governance outcome.

## 6.6 Adapters Are Preferable to Semantic Contamination

When Sage-X behavior is technically reusable but its model does not directly satisfy ISL semantics, an adapter, wrapper, or extension layer SHOULD be preferred over mutating the Sage-X core.

## 6.7 Forking Is Exceptional

Forking a Sage-X asset for ISL use SHOULD be treated as an exceptional action requiring governance approval, rationale, migration plan, and long-term ownership assignment.

## 6.8 Traceability Is Mandatory

Every reused, wrapped, extended, composed, forked, or rejected Sage-X asset MUST be traceable to:

* the ISL canonical entity or construction task that required it
* the Sage-X asset record
* the reuse fitness assessment
* the reuse decision
* the validation evidence
* the governance decision where applicable

---

# 7.0 Architectural Positioning

## 7.1 Domain Relationship

ISL and Sage-X are complementary systems.

| Domain | Primary Role |
| ------ | ------------ |
| ISL | Defines specification, semantic, planning, governance, artifact, lifecycle, and autonomous construction meaning for ALETHEIA |
| Sage-X | Provides generic execution substrate for specification compilation, planning, step execution, state, traceability, governance, capability invocation, and recovery |
| Bridge Profile | Defines how ISL semantics are compiled into Sage-X execution artifacts and how Sage-X assets are reused by ISL construction |

## 7.2 Reference Flow

A conforming bridge SHOULD support the following reference flow:

1. ISL authored specification is received.
2. ISL processor validates the authored structure.
3. ISL semantic engine normalizes the authored specification into canonical entities.
4. ISL-Sage-X specification plugin maps canonical entities into Sage-X Knowledge Units.
5. Sage-X planning generates or participates in an execution plan.
6. ISL planning performs reuse discovery against the Bridge Asset Registry.
7. Reuse fitness assessments are created for candidate Sage-X assets.
8. Governance evaluates reuse decisions where required.
9. Sage-X executes approved plans or plan segments.
10. ALETHEIA artifacts are generated, reused, wrapped, extended, composed, or rejected.
11. Validation results, trace events, checkpoints, failures, governance decisions, and artifact metadata are recorded.
12. Completion evidence is returned to ISL/ALETHEIA lifecycle state.

## 7.3 Control Boundary

All bridge activity MUST pass through controlled interfaces.

The bridge MUST NOT:

* directly mutate Sage-X execution state outside the Sage-X control surface
* directly mutate ISL canonical models after normalization without ISL governance
* bypass ISL readiness gates
* bypass Sage-X validation checkpoints
* admit untraced artifacts
* accept ungoverned reuse of restricted assets
* convert informal prompts into executable construction tasks without ISL normalization or governance

---

# 8.0 Conceptual Crosswalk

The bridge MUST maintain a versioned crosswalk between ISL concepts and Sage-X concepts.

| ISL Concept | Sage-X Concept | Relationship |
| ----------- | -------------- | ------------ |
| Authored ISL Specification | Specification Input | ISL authored document is ingested through the ISL plugin |
| ISL Canonical Entity | Knowledge Unit | Canonical entities map to Knowledge Units or Knowledge Unit groups |
| ISL Canonical Graph | Knowledge Repository and Semantic Index | Graph structure is preserved as dependency and metadata relationships |
| Readiness Record | Planning and Execution Eligibility Metadata | Readiness gates constrain plan generation and execution admission |
| Construction Plan | Execution Plan | Construction tasks map to execution steps or step groups |
| Construction Task | Execution Step | ISL task semantics are carried as step metadata |
| Verification Task | Validation Step | Deterministic validation is represented as execution validation |
| Repair Task | Recovery or Repair Execution Branch | Repair behavior maps to governed recovery or branch execution |
| Tool Integration Model | Capability Registry | Tools and model-backed capabilities are registered as capabilities |
| Governance Gate | Governance Control | Approval, waiver, override, and policy checks map across both systems |
| Traceability Graph | Execution Trace and Trace Graph | Trace records link ISL intent, Sage-X execution, and artifacts |
| Artifact Repository Record | Execution Output and Artifact Metadata | Generated or reused outputs are recorded with provenance |
| Reusable Asset Registry | Bridge Asset Registry and Capability Registry | Existing Sage-X code and contracts become candidate reusable assets |
| Execution State | Execution State | Shared or mapped state models must preserve versioned semantics |
| Checkpoint | Checkpoint | Checkpoints are reusable when rollback semantics are compatible |
| Failure Record | Failure Record | Failures must preserve origin, task, step, and recovery metadata |
| Observability Event | Trace or Observability Event | Events must be mappable to both telemetry models |

---

# 9.0 Semantic Ownership Model

## 9.1 Ownership Classes

Each bridge artifact MUST declare one of the following semantic ownership classes.

| Ownership Class | Meaning |
| --------------- | ------- |
| sagex-owned | Sage-X defines the artifact semantics |
| isl-owned | ISL defines the artifact semantics |
| shared-owned | Semantics are formally shared by this profile |
| isl-extension | Artifact extends Sage-X with ISL-specific metadata or behavior |
| sagex-adapter | Artifact adapts Sage-X behavior for an external consumer |
| bridge-owned | Artifact exists only to connect ISL and Sage-X |

## 9.2 Ownership Rules

A sagex-owned artifact MAY be reused by ISL only if ISL-specific semantics are represented through metadata, adapter behavior, or extension records.

An isl-owned artifact MUST NOT be replaced by a Sage-X artifact unless the Sage-X artifact is proven semantically equivalent and governance approves the substitution.

A shared-owned artifact MUST maintain a compatibility matrix and field-level extension rules.

A bridge-owned artifact MUST have a named owner responsible for versioning, validation, documentation, and deprecation.

---

# 10.0 Bridge Asset Registry

## 10.1 Registry Requirement

A conforming integration MUST provide a Bridge Asset Registry or an implementation-equivalent controlled interface.

The Bridge Asset Registry MAY be implemented as:

* an extension of the ISL Reusable Asset Registry
* an extension of the Sage-X Capability Registry
* a separate bridge catalog
* a federated view over source repositories, package repositories, schema registries, test systems, and artifact repositories

Regardless of implementation form, the registry MUST expose the required records and behaviors defined in this profile.

## 10.2 Bridge Asset Types

The registry MUST support the following bridge asset types.

| Asset Type | Description |
| ---------- | ----------- |
| source-module | Reusable code module |
| service | Deployable service or process |
| library | Reusable package or library |
| schema | JSON Schema, contract schema, or validation schema |
| api-contract | Request, response, or control surface contract |
| event-contract | Trace, telemetry, state, or governance event contract |
| state-model | Execution, artifact, repository, or lifecycle state model |
| execution-engine | Execution core or step execution behavior |
| planning-engine | Planning behavior or plan-generation logic |
| validation-harness | Tests, validators, scanners, or compliance checks |
| governance-policy | Policy module, approval rule, waiver rule, or override constraint |
| capability-adapter | Adapter for model, tool, repository, identity, telemetry, or external system capability |
| console-component | UI, CLI, dashboard, or control surface component |
| observability-adapter | Trace, logging, metrics, or dashboard integration |
| deployment-module | Infrastructure, runtime, container, or deployment artifact |
| documentation-template | Reusable documentation or evidence template |

## 10.3 Bridge Asset Record Schema

Each bridge asset record MUST include the following fields.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| bridge-asset-id | string | REQUIRED | Unique bridge asset identifier |
| source-system | enum | REQUIRED | sagex, isl, aletheia, external |
| asset-type | enum | REQUIRED | Asset type from section 10.2 |
| semantic-owner | enum | REQUIRED | Ownership class from section 9.1 |
| name | string | REQUIRED | Human-readable name |
| description | string | REQUIRED | Functional and semantic description |
| version | semver | REQUIRED | Asset version |
| repository-reference | string | CONDITIONAL | Source repository, package, or artifact reference |
| immutable-reference | string | CONDITIONAL | Commit, digest, artifact hash, package lock, or equivalent |
| public-interface | array | REQUIRED | Exposed interfaces, schemas, or operations |
| consumed-interfaces | array | CONDITIONAL | Required dependencies or upstream contracts |
| capability-tags | array | REQUIRED | Capability tags used for discovery |
| applicable-isl-entity-types | array | CONDITIONAL | ISL entity types the asset may satisfy |
| applicable-sagex-artifact-types | array | CONDITIONAL | Sage-X artifacts the asset may satisfy |
| validation-status | enum | REQUIRED | unvalidated, validated, warning, failed, expired, superseded |
| validation-evidence-refs | array | CONDITIONAL | Evidence references supporting validation status |
| security-status | enum | REQUIRED | not-evaluated, passed, warning, failed, expired |
| governance-status | enum | REQUIRED | unreviewed, approved, restricted, prohibited, deprecated |
| sensitivity-classification | enum | REQUIRED | public, internal, confidential, restricted |
| reuse-scope | enum | REQUIRED | local, project, workspace, tenant, enterprise, public |
| compatibility-profile | array | REQUIRED | Supported ISL, Sage-X, runtime, schema, and platform versions |
| extension-policy | enum | REQUIRED | closed, metadata-only, adapter-only, extension-allowed, fork-allowed |
| owner | string | REQUIRED | Owning team, role, or authority |
| registered-at | ISO 8601 | REQUIRED | Registration timestamp |
| supersedes | array | OPTIONAL | Prior bridge asset identifiers |
| superseded-by | string | OPTIONAL | Replacement bridge asset identifier |

## 10.4 Registry Rules

The registry MUST NOT mark an asset reusable unless validation-status is `validated` or governance-status explicitly permits restricted reuse.

The registry MUST NOT present prohibited assets as candidates for automated selection.

The registry MUST expose enough metadata for automated discovery, human review, audit, and impact analysis.

Each registered asset MUST be traceable to its source repository, source artifact, or authoritative origin.

Every registry update MUST produce an audit event.

---

# 11.0 ISL Specification Plugin for Sage-X

## 11.1 Plugin Requirement

A conforming bridge MUST provide an ISL Specification Plugin or an implementation-equivalent component that satisfies the Sage-X Specification Plugin Interface.

The plugin MUST support at least one of the following input forms:

* authored ISL Markdown validated under ISL v1.0
* canonical ISL JSON validated under ISL v1.1
* ISL exchange YAML transformable into canonical JSON

## 11.2 Plugin Responsibilities

The ISL Specification Plugin MUST:

* ingest ISL specification input
* validate input format
* preserve ISL version metadata
* preserve ISL source identifiers
* map canonical entities to Knowledge Units
* preserve dependency relationships
* preserve readiness metadata
* expose ISL governance constraints as execution eligibility metadata
* produce structured errors for unmappable content
* produce deterministic output for identical input and plugin version

## 11.3 Knowledge Unit Mapping Rules

Each mapped Knowledge Unit MUST include:

| Field | Required | Description |
| ----- | -------- | ----------- |
| knowledge-unit-id | YES | Stable Sage-X Knowledge Unit identifier |
| source-system | YES | MUST be `isl` |
| source-specification-id | YES | ISL specification identifier |
| source-specification-version | YES | ISL specification version |
| source-entity-id | YES | ISL canonical entity identifier |
| source-entity-type | YES | ISL canonical entity type |
| semantic-owner | YES | MUST identify ISL ownership unless shared |
| content-hash | YES | Deterministic hash of mapped content |
| dependency-refs | YES | Knowledge Unit or canonical entity dependencies |
| governance-refs | CONDITIONAL | Required when policies, approvals, waivers, or constraints apply |
| traceability-ref | YES | Traceability node or pending traceability reference |
| readiness-state | CONDITIONAL | Required when readiness controls execution eligibility |
| mapping-profile-version | YES | Version of this profile used for mapping |

## 11.4 Mapping Failure Rules

The plugin MUST reject unmappable required canonical entities.

The plugin MAY produce partial advisory mappings only when the output is marked non-executable.

The plugin MUST NOT silently discard ISL canonical fields that affect construction, governance, validation, security, traceability, reuse, or artifact generation.

---

# 12.0 Planning Bridge

## 12.1 Construction Plan to Execution Plan Mapping

An ISL construction plan MAY be executed by Sage-X when each executable construction task has a valid mapping to one or more Sage-X execution steps.

The mapping MUST preserve:

* task identifier
* source canonical entity identifiers
* dependency order
* expected outputs
* validation requirements
* required capabilities
* governance constraints
* construction boundary references
* connection context references
* reuse decisions
* traceability references

## 12.2 Execution Step Metadata

Each Sage-X execution step derived from an ISL construction task MUST include:

| Field | Required | Description |
| ----- | -------- | ----------- |
| execution-step-id | YES | Sage-X step identifier |
| construction-task-id | YES | ISL task identifier |
| source-entity-ids | YES | ISL canonical entities satisfied by the step |
| step-type | YES | generation, validation, reuse, wrap, extend, compose, governance, repair, packaging, deployment-prep |
| required-capability-refs | CONDITIONAL | Required capabilities or tools |
| expected-output-refs | CONDITIONAL | Expected artifacts or records |
| validation-boundary | YES | Validation requirement before progression |
| governance-boundary | CONDITIONAL | Approval, waiver, override, escalation, or policy reference |
| reuse-decision-id | CONDITIONAL | Required when step uses an existing Sage-X or bridge asset |
| traceability-node-id | YES | Traceability node for the step |

## 12.3 Planning Admission Rules

The bridge MUST NOT submit an ISL-derived plan to Sage-X execution unless:

* ISL planning preconditions are satisfied
* required Knowledge Units are available
* dependencies are resolved
* reuse discovery has completed for applicable tasks
* governance constraints have been attached
* validation checkpoints are defined
* traceability nodes are initialized
* required capabilities are registered or escalation has occurred

---

# 13.0 Smart Reuse Model

## 13.1 Required Reuse Discovery

For any ISL construction task that intersects with Sage-X execution, state, traceability, validation, governance, planning, capability, schema, API, console, or deployment behavior, the Planning Engine MUST perform bridge reuse discovery before generating new code.

## 13.2 Reuse Discovery Inputs

Bridge reuse discovery MUST use:

| Input | Required | Description |
| ----- | -------- | ----------- |
| construction-task-id | YES | Task requiring construction |
| source-entity-ids | YES | ISL canonical entities the task satisfies |
| required-capability-tags | YES | Semantic capability needs |
| expected-artifact-types | YES | Required output types |
| target-module-boundary | YES | Intended ISL/ALETHEIA module or service |
| target-runtime-profile | YES | Runtime, language, deployment, or platform constraints |
| governance-profile-id | YES | Governance policy controlling reuse |
| construction-boundary-id | CONDITIONAL | Required where ISL construction boundaries apply |
| connection-context-ids | CONDITIONAL | Required for cross-boundary reuse |
| sensitivity-classification | YES | Data and artifact classification |
| compatibility-profile | YES | ISL, Sage-X, schema, and runtime version requirements |

## 13.3 Candidate Selection

Candidate selection MUST search:

* Bridge Asset Registry
* ISL Reusable Asset Registry
* Sage-X Capability Registry
* approved schema repositories
* approved source repositories
* approved package repositories
* approved validation harness catalogs
* approved governance policy catalogs

Candidate selection SHOULD support semantic search, tag search, interface search, schema compatibility search, and traceability search.

## 13.4 Reuse Fitness Assessment

Each candidate asset MUST receive a Bridge Reuse Fitness Assessment.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| bridge-reuse-assessment-id | string | REQUIRED | Unique assessment identifier |
| construction-task-id | string | REQUIRED | ISL construction task |
| candidate-bridge-asset-id | string | REQUIRED | Candidate asset |
| semantic-match | enum | REQUIRED | full, partial, weak, none, conflicting, uncertain |
| interface-match | enum | REQUIRED | compatible, adapter-required, incompatible, unknown |
| schema-match | enum | REQUIRED | compatible, extension-required, incompatible, unknown |
| behavior-match | enum | REQUIRED | equivalent, subset, superset, divergent, unknown |
| validation-match | enum | REQUIRED | sufficient, additional-validation-required, insufficient, expired |
| governance-match | enum | REQUIRED | approved, approval-required, waiver-required, prohibited |
| security-match | enum | REQUIRED | passed, warning, failed, expired, not-evaluated |
| boundary-match | enum | REQUIRED | within-boundary, connection-context-required, outside-boundary, unknown |
| extension-cost | enum | REQUIRED | none, low, medium, high, prohibitive, unknown |
| lifecycle-risk | enum | REQUIRED | low, medium, high, critical, unknown |
| recommended-reuse-mode | enum | REQUIRED | use-as-is, wrap, extend, compose, fork, generate-new, reject, escalate |
| rationale | string | REQUIRED | Explanation of recommendation |
| assessed-by | string | REQUIRED | Component, agent, tool, or reviewer |
| assessed-at | ISO 8601 | REQUIRED | Assessment timestamp |

## 13.5 Reuse Modes

| Reuse Mode | Meaning | Governance Requirement |
| ---------- | ------- | ---------------------- |
| use-as-is | Asset satisfies need without modification | Required when asset is restricted or sensitive |
| wrap | Asset is used through an adapter preserving original semantics | Required when wrapper affects behavior or security |
| extend | Asset is extended through approved extension points | Required unless extension policy pre-approves |
| compose | Multiple assets jointly satisfy the need | Required when composition crosses boundaries |
| fork | Asset is copied and independently evolved | Always required |
| generate-new | New asset is generated because no suitable candidate exists | Required when high-risk duplication may occur |
| reject | Asset is not used due to mismatch or policy | Evidence required |
| escalate | Human or governance review required | Always required before continuation |

## 13.6 Reuse Decision Record

Each applicable construction task MUST produce a Bridge Reuse Decision Record.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| bridge-reuse-decision-id | string | REQUIRED | Unique decision identifier |
| construction-task-id | string | REQUIRED | Task governed by decision |
| selected-reuse-mode | enum | REQUIRED | Authorized reuse mode |
| selected-asset-ids | array | CONDITIONAL | Required when any asset is selected |
| rejected-asset-ids | array | CONDITIONAL | Candidate assets rejected |
| assessment-refs | array | REQUIRED | Fitness assessment references |
| governance-decision-ref | string | CONDITIONAL | Required where approval, waiver, override, or escalation applies |
| validation-evidence-refs | array | REQUIRED | Evidence supporting decision |
| traceability-node-id | string | REQUIRED | Traceability node for decision |
| rationale | string | REQUIRED | Human-readable decision rationale |
| decided-by | string | REQUIRED | Planner, governance engine, human reviewer, or system |
| decided-at | ISO 8601 | REQUIRED | Decision timestamp |

## 13.7 Generation Gate

A generation step MUST NOT be dispatched when bridge reuse discovery is applicable unless a Bridge Reuse Decision Record authorizes `generate-new`, `fork`, `extend`, `wrap`, or `compose`.

---

# 14.0 Shared Contract Classification

## 14.1 Contract Classes

Each overlapping schema, event, state record, API, or artifact contract MUST be classified.

| Contract Class | Meaning |
| -------------- | ------- |
| shared-contract | Same contract is valid for both ISL/ALETHEIA and Sage-X |
| mapped-contract | Contract fields are mapped between distinct ISL and Sage-X forms |
| extension-contract | Sage-X contract is extended with ISL-specific fields |
| adapter-contract | Adapter transforms between ISL and Sage-X contract forms |
| incompatible-contract | Contract cannot be safely shared or mapped |

## 14.2 Required Contract Classification Record

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| contract-classification-id | string | REQUIRED | Unique classification identifier |
| contract-name | string | REQUIRED | Contract name |
| isl-contract-ref | string | CONDITIONAL | ISL contract or schema reference |
| sagex-contract-ref | string | CONDITIONAL | Sage-X contract or schema reference |
| contract-class | enum | REQUIRED | Classification from section 14.1 |
| semantic-owner | enum | REQUIRED | Ownership class |
| field-mapping-ref | string | CONDITIONAL | Required for mapped or adapter contracts |
| extension-rules | string | CONDITIONAL | Required for extension contracts |
| validation-rules-ref | string | REQUIRED | Validation rule reference |
| compatibility-profile | array | REQUIRED | Supported versions |
| governance-status | enum | REQUIRED | unreviewed, approved, restricted, prohibited, deprecated |
| classified-by | string | REQUIRED | Component, reviewer, or authority |
| classified-at | ISO 8601 | REQUIRED | Timestamp |

## 14.3 Contract Rules

A shared-contract MUST NOT contain fields with divergent meaning across ISL and Sage-X.

A mapped-contract MUST have deterministic field mapping.

An extension-contract MUST preserve base contract validity.

An adapter-contract MUST be validated bidirectionally when reverse mapping is required.

An incompatible-contract MUST NOT be reused without fork or replacement governance.

---

# 15.0 Governance Model

## 15.1 Governance Enforcement Points

The bridge MUST enforce governance at:

* asset registration
* contract classification
* ISL plugin version registration
* Knowledge Unit mapping
* reuse discovery completion
* reuse decision approval
* fork authorization
* restricted asset use
* sensitive data exposure
* execution plan admission
* validation waiver
* recovery branch creation
* artifact promotion
* shared contract version changes

## 15.2 Governance Decision Outcomes

Governance decisions MUST use one of the following outcomes.

| Outcome | Meaning |
| ------- | ------- |
| approved | Action may proceed |
| approved-with-conditions | Action may proceed only if conditions are satisfied |
| approval-required | Human or governance authority approval required |
| waiver-required | Waiver required before continuation |
| rejected | Action must not proceed |
| escalated | Higher authority or additional review required |
| deferred | Action paused pending evidence |

## 15.3 Fork Governance

Forking a Sage-X asset for ISL use MUST require:

* business or technical rationale
* failed or insufficient reuse assessment
* ownership assignment
* divergence risk classification
* migration or convergence plan
* validation plan
* traceability links to original asset
* deprecation or synchronization policy where applicable

## 15.4 Governance Evidence

Each governed bridge decision MUST produce evidence containing:

* decision identifier
* decision outcome
* affected assets
* affected ISL tasks or entities
* policy references
* approver or authority
* conditions or waivers
* timestamp
* audit reference

---

# 16.0 Security and Isolation

## 16.1 Security Principles

The bridge MUST treat all specification input, registry metadata, generated output, imported source code, and external capability responses as untrusted until validated.

## 16.2 Required Security Controls

A conforming bridge MUST:

* validate ISL input before mapping to Knowledge Units
* validate Sage-X asset metadata before registry admission
* prevent execution of embedded code in specifications
* enforce access controls for restricted assets
* prevent sensitive assets from crossing unauthorized boundaries
* preserve execution isolation for Sage-X steps
* prevent direct state mutation outside governed interfaces
* require security validation before unrestricted reuse
* record security-relevant reuse and execution events

## 16.3 Restricted Asset Rule

A restricted asset MUST NOT be selected automatically unless:

* the construction task sensitivity classification allows it
* the reuse scope permits the target context
* governance approves the selection
* traceability records the decision
* access controls are enforced at execution and repository boundaries

---

# 17.0 Traceability Requirements

## 17.1 Required Traceability Chain

For every reused or generated bridge artifact, the system MUST support the following traceability chain:

ISL source element -> ISL canonical entity -> Sage-X Knowledge Unit -> ISL construction task -> Sage-X execution step -> bridge reuse decision -> selected asset or generated artifact -> validation result -> governance event where applicable -> repository or package record.

## 17.2 Required Traceability Edges

The bridge traceability graph MUST support the following relationship types.

| Edge Type | Source | Target |
| --------- | ------ | ------ |
| maps-to-knowledge-unit | ISL canonical entity | Sage-X Knowledge Unit |
| planned-as | ISL canonical entity | ISL construction task |
| executes-as | ISL construction task | Sage-X execution step |
| considers-asset | Construction task | Bridge asset |
| selects-asset | Reuse decision | Bridge asset |
| rejects-asset | Reuse decision | Bridge asset |
| governed-by | Task, step, asset, or decision | Governance decision |
| validates | Validation result | Asset, artifact, step, or task |
| produces | Execution step | Artifact or record |
| reuses | Artifact or task | Bridge asset |
| wraps | Artifact or task | Bridge asset |
| extends | Artifact or task | Bridge asset |
| forks-from | Artifact or task | Bridge asset |
| supersedes | Bridge asset | Bridge asset |

## 17.3 Traceability Integrity Rule

An artifact constructed through the bridge MUST NOT be promoted as stable unless all required traceability edges are present or a governance-approved waiver exists.

---

# 18.0 Validation and Evidence

## 18.1 Validation Stages

The bridge MUST support the following validation stages.

| Stage | Input | Output |
| ----- | ----- | ------ |
| ISL Input Validation | Authored or canonical ISL input | ISL validation result |
| Plugin Mapping Validation | ISL canonical entities and mapping rules | Knowledge Unit validation result |
| Contract Classification Validation | Candidate shared or mapped contracts | Contract classification result |
| Asset Admission Validation | Candidate Sage-X assets | Bridge asset validation result |
| Reuse Fitness Validation | Candidate assets and construction tasks | Fitness assessment result |
| Plan Admission Validation | ISL task graph and Sage-X execution plan | Plan admission result |
| Execution Validation | Execution step outputs | Step validation result |
| Traceability Validation | Trace graph records | Traceability integrity result |
| Promotion Validation | Final artifacts and evidence | Promotion eligibility result |

## 18.2 Evidence Bundle

Each bridge execution run MUST produce a Bridge Evidence Bundle.

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| bridge-evidence-bundle-id | string | REQUIRED | Unique evidence bundle identifier |
| profile-version | string | REQUIRED | Integration profile version |
| isl-specification-ref | string | REQUIRED | ISL specification reference |
| sagex-execution-ref | string | CONDITIONAL | Sage-X execution reference |
| plugin-version | string | REQUIRED | ISL plugin version |
| knowledge-unit-refs | array | REQUIRED | Produced or used Knowledge Units |
| plan-refs | array | REQUIRED | ISL and Sage-X plan references |
| bridge-asset-refs | array | CONDITIONAL | Assets considered or used |
| reuse-assessment-refs | array | CONDITIONAL | Fitness assessments |
| reuse-decision-refs | array | CONDITIONAL | Reuse decisions |
| contract-classification-refs | array | CONDITIONAL | Contract classification records |
| governance-decision-refs | array | CONDITIONAL | Governance records |
| validation-result-refs | array | REQUIRED | Validation results |
| traceability-snapshot-ref | string | REQUIRED | Traceability snapshot |
| artifact-refs | array | CONDITIONAL | Generated, reused, wrapped, extended, or forked artifacts |
| completion-status | enum | REQUIRED | completed, completed-with-waivers, failed, blocked, cancelled |
| created-at | ISO 8601 | REQUIRED | Evidence bundle creation timestamp |

---

# 19.0 Versioning and Compatibility

## 19.1 Version Declaration

Every bridge component MUST declare:

* supported ISL versions
* supported Sage-X versions
* supported profile version
* supported schema versions
* supported contract classifications

## 19.2 Compatibility Matrix

A conforming bridge MUST maintain a compatibility matrix containing:

| Field | Required |
| ----- | -------- |
| matrix-id | YES |
| profile-version | YES |
| supported-isl-versions | YES |
| supported-sagex-versions | YES |
| supported-plugin-versions | YES |
| supported-contract-classes | YES |
| known-incompatibilities | YES |
| migration-guidance | CONDITIONAL |
| approved-by | CONDITIONAL |
| updated-at | YES |

## 19.3 Breaking Change Rules

A breaking change to a shared contract, mapping rule, plugin output, reuse decision schema, or traceability edge type MUST:

* increment the applicable major version
* update the compatibility matrix
* produce migration guidance
* identify affected assets and consumers
* trigger impact analysis
* require governance approval

---

# 20.0 Operational Model

## 20.1 Bridge Runtime Responsibilities

The bridge runtime or equivalent orchestration layer MUST:

* coordinate ISL plugin execution
* coordinate reuse discovery
* call registries through controlled interfaces
* produce reuse assessments
* request governance decisions
* submit eligible plans to Sage-X
* collect execution results
* update traceability
* update artifact and asset records
* produce evidence bundles

## 20.2 Failure Behavior

The bridge MUST fail closed when:

* ISL input cannot be validated
* required canonical entities cannot be mapped
* required capability is missing
* reuse discovery cannot complete
* governance decision is rejected or deferred
* restricted asset authorization is missing
* traceability cannot be recorded
* validation evidence is missing for stable promotion

## 20.3 Recovery Behavior

Bridge recovery MUST preserve:

* prior valid ISL canonical state
* prior valid Knowledge Units
* prior valid execution checkpoints
* reuse decisions already committed
* governance records
* traceability history
* failure origin

Recovery MUST NOT erase failed reuse or mapping attempts from audit history.

---

# 21.0 Console and Developer Experience Requirements

An enterprise implementation SHOULD provide console or CLI support for:

* viewing ISL to Sage-X mappings
* browsing Bridge Asset Registry records
* reviewing reuse assessments
* approving or rejecting governed reuse decisions
* inspecting shared contract classifications
* viewing traceability chains
* comparing generated-new versus reuse options
* identifying forked assets and divergence risk
* exporting evidence bundles

Console behavior MUST NOT bypass the control surface, governance engine, registry rules, or traceability requirements.

---

# 22.0 Minimal Reference Implementation Profile

## 22.1 Required Components

A minimal conforming reference implementation MUST include:

* ISL Specification Plugin for Sage-X
* Bridge Asset Registry
* bridge reuse discovery operation
* Bridge Reuse Fitness Assessment record
* Bridge Reuse Decision Record
* contract classification record
* plan admission validation
* traceability chain creation
* evidence bundle creation

## 22.2 Required Demonstration Scenario

The reference scenario MUST demonstrate:

1. Registration of at least one existing Sage-X schema or module as a Bridge Asset.
2. Ingestion of an ISL canonical or authored specification.
3. Mapping of at least one ISL canonical entity to a Sage-X Knowledge Unit.
4. Planning of an ISL construction task that overlaps with a Sage-X asset.
5. Discovery of the Sage-X asset.
6. Creation of a reuse fitness assessment.
7. Creation of a reuse decision.
8. Execution of a plan step using, wrapping, extending, or composing the Sage-X asset.
9. Validation of the resulting artifact or behavior.
10. Creation of a traceability chain and evidence bundle.

## 22.3 Reference Success Criteria

The scenario succeeds only if:

* no generation occurs before reuse discovery
* the reused asset has validation evidence
* the reuse decision is traceable
* execution does not bypass Sage-X state controls
* ISL semantic ownership is preserved
* final evidence is sufficient for audit review

---

# 23.0 Conformance Classes

## 23.1 Class A: Mapping Conformance

An implementation conforms to Class A if it:

* ingests valid ISL input
* maps ISL canonical entities to Knowledge Units
* preserves source identifiers
* produces deterministic mapping output
* records mapping traceability

## 23.2 Class B: Reuse Discovery Conformance

An implementation conforms to Class B if it satisfies Class A and:

* registers bridge assets
* performs reuse discovery before generation
* produces reuse fitness assessments
* produces reuse decision records
* blocks generation when required reuse decisions are missing

## 23.3 Class C: Execution Bridge Conformance

An implementation conforms to Class C if it satisfies Class B and:

* maps ISL construction tasks to Sage-X execution steps
* submits eligible plans through controlled interfaces
* preserves execution state, validation checkpoints, and failure behavior
* records execution traceability
* returns execution results to ISL lifecycle state

## 23.4 Class D: Enterprise Governance Conformance

An implementation conforms to Class D if it satisfies Class C and:

* enforces governance at all required bridge points
* classifies shared contracts
* controls restricted asset reuse
* governs forks and extensions
* maintains compatibility matrices
* produces complete evidence bundles
* supports audit and impact analysis

## 23.5 Claiming Conformance

A conformance claim MUST identify:

* claimed conformance class
* ISL versions supported
* Sage-X versions supported
* profile version supported
* implemented components
* known limitations
* validation evidence
* governance approval status

---

# 24.0 Non-Conforming Behaviors

The following behaviors are non-conforming:

* generating ISL/ALETHEIA code without required bridge reuse discovery
* treating Sage-X assets as reusable without registry metadata
* reusing a Sage-X asset without validation evidence
* bypassing ISL readiness gates
* bypassing Sage-X execution state controls
* treating similar names as semantic equivalence
* silently discarding ISL canonical metadata during mapping
* changing Sage-X core behavior to satisfy ISL semantics without an adapter, extension, or governance decision
* promoting untraced bridge artifacts
* forking Sage-X assets without governance approval
* sharing contracts whose field semantics differ between ISL and Sage-X
* accepting restricted assets across unauthorized boundaries

---

# 25.0 Enterprise Quality Checklist

An enterprise-quality bridge implementation SHOULD be able to answer the following questions with evidence:

* Which ISL entities caused this Sage-X execution step to exist?
* Which existing Sage-X assets were considered before new code was generated?
* Why was the selected asset reused, wrapped, extended, composed, forked, or rejected?
* Which validation evidence proves the asset was safe to reuse?
* Which governance authority approved restricted reuse, extension, fork, or waiver?
* Which shared contracts are truly shared and which are mapped or adapted?
* Which artifacts depend on this Sage-X asset?
* What breaks if the Sage-X asset changes?
* Can the full execution and reuse history be reconstructed?
* Can an auditor trace final ALETHEIA code back to ISL intent and Sage-X source assets?

If the implementation cannot answer these questions, it SHOULD NOT claim Class D Enterprise Governance Conformance.

---

# 26.0 Summary

This profile defines the ISL-Sage-X bridge required for smart autonomous construction.

ISL supplies the construction semantics, readiness rules, governance meaning, planning obligations, traceability expectations, artifact lifecycle, and ALETHEIA platform intent.

Sage-X supplies the specification-agnostic execution substrate, including Knowledge Units, execution plans, state machine behavior, control surfaces, validation progression, trace recording, capability abstraction, governance enforcement, and recovery mechanics.

The bridge prevents blind construction by requiring ISL planning to discover, evaluate, govern, and trace existing Sage-X assets before generating new code. It turns the Sage-X code set into reusable construction material for ISL/ALETHEIA while preserving semantic ownership, execution safety, auditability, and enterprise-quality evidence.

