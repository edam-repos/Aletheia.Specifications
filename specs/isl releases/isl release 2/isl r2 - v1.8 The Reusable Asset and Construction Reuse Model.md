# ALETHEIA Specification Language (ISL) v1.8

# The Reusable Asset and Construction Reuse Model

**Status:** Normative
**Release:** r2
**Depends On:** ISL r2 v0.0, ISL r2 v1.0, ISL r2 v1.1, ISL r2 v1.2, ISL r2 v1.3, ISL r2 v1.4, ISL r2 v1.5, ISL r2 v1.6, ISL r2 v1.7
**Document Type:** Language Layer Reuse, Asset Registry, and Construction Reuse Specification

---

## 1.0 Scope

This document defines the reusable asset model for ISL-compliant autonomous software construction. It establishes reusable assets as first-class specification, planning, traceability, governance, and validation objects.

This document applies to:

* reusable libraries, modules, services, components, utilities, schemas, contracts, templates, infrastructure modules, validation harnesses, policy modules, and generated artifacts promoted for reuse
* construction planning decisions that determine whether to reuse, extend, compose, wrap, or generate assets
* traceability links between newly constructed artifacts and previously validated assets
* governance decisions for asset promotion, approval, deprecation, retirement, and cross-project reuse
* enterprise impact analysis when reusable assets change

This document does not require a specific package manager, programming language, repository technology, artifact store, or model provider.

---

## 2.0 Purpose

The purpose of this model is to make reuse the default posture of autonomous construction. A conforming ALETHEIA Engine MUST avoid unnecessary duplication and MUST prefer approved, validated, traceable reusable assets when they satisfy the construction intent.

Autonomous construction that generates new code without first checking available reusable assets creates avoidable maintenance cost, governance risk, architectural drift, and duplicated logic. Enterprise construction requires the platform to know what has already been built, whether it is still valid, whether it is safe to reuse, and what downstream systems depend on it.

---

## 3.0 Reuse Design Principles

### 3.1 Reuse Before Generation

For implementation, schema, interface, contract, infrastructure, validation, and policy tasks, the Planning Engine MUST perform reuse discovery before full generation is authorized.

Generation is permitted only when reuse discovery determines that no suitable approved asset exists, or when governance approves delta generation, wrapping, extension, or replacement.

### 3.2 Reuse Is Governed

Reusable assets are not informal snippets. Promotion, dependency creation, deprecation, retirement, and cross-boundary or cross-project use MUST be governed according to asset risk, sensitivity, validation status, ownership, and enterprise policy.

### 3.3 Reuse Is Traceable

Every artifact that reuses, extends, wraps, adapts, or composes a reusable asset MUST record traceability to that asset and to the reuse decision that authorized the dependency.

### 3.4 Reuse Must Not Increase Drift

Reuse MUST preserve the active Construction Boundary. A reusable asset MUST NOT introduce behavior, dependencies, data exposure, assumptions, or architectural responsibilities outside the authorized boundary unless a valid Connection Context and governance decision authorize the crossing.

### 3.5 Reuse Fitness Is Explicit

The platform MUST assess asset fitness using structured criteria. A semantic match MUST NOT be accepted solely because an asset name or textual description appears similar to the requirement.

---

## 4.0 Terms and Definitions

**Reusable Asset**
A validated artifact, module, contract, template, policy, validation harness, or other construction object approved for possible use in future construction.

**Reusable Asset Registry**
The governed catalog of reusable assets available to Planning Engines, Runtime Orchestrators, agents, tools, and governance workflows.

**Reuse Discovery**
The required planning activity that searches for existing assets before generation.

**Reuse Fitness Assessment**
A structured record evaluating whether a candidate asset can satisfy, partially satisfy, or fail to satisfy a construction need.

**Reuse Decision**
The recorded decision to reuse, partially reuse, wrap, extend, compose, reject, or generate new assets.

**Delta Generation**
Generation of only the missing or adapting logic required after a reusable asset partially satisfies the construction need.

---

## 5.0 Reusable Asset Registry

The platform MUST provide a Reusable Asset Registry or an implementation-equivalent controlled interface.

The registry MUST support:

* asset registration
* semantic search
* capability tagging
* version lookup
* provenance lookup
* validation status lookup
* sensitivity and trust classification
* dependency and consumer lookup
* promotion, deprecation, retirement, and supersession workflows

The registry MAY be local, embedded, cloud-hosted, federated, or backed by an enterprise catalog. Regardless of implementation form, the registry MUST expose the same governed lookup, provenance, validation, and decision behavior required by this document.

### 5.1 Asset Types

The registry MUST support at least the following asset types.

| Asset Type            | Description                                                   |
| --------------------- | ------------------------------------------------------------- |
| library               | Reusable code library or package                              |
| module                | Reusable implementation module                                |
| component             | Reusable application component                                |
| utility               | Reusable helper or utility                                    |
| service-contract      | API, event, or service contract                               |
| schema                | Data, configuration, or validation schema                     |
| template              | Controlled generation template                                |
| infrastructure-module | Infrastructure, deployment, or platform configuration module  |
| validation-harness    | Tests, fixtures, scanners, or verification harnesses          |
| policy-module         | Reusable policy, control, or governance rule                  |
| agent-pattern         | Reusable bounded agent instruction or output contract pattern |

### 5.2 Asset Record Schema

Each reusable asset record MUST include:

| Field                      | Type     | Required    | Description                                                              |
| -------------------------- | -------- | ----------- | ------------------------------------------------------------------------ |
| asset-id                   | string   | REQUIRED    | Unique reusable asset identifier                                         |
| asset-type                 | enum     | REQUIRED    | Asset type from the registry asset type catalog                          |
| name                       | string   | REQUIRED    | Human-readable asset name                                                |
| description                | string   | REQUIRED    | Semantic description of asset capability                                 |
| version                    | semver   | REQUIRED    | Asset version                                                            |
| capability-tags            | array    | REQUIRED    | Capability tags derived from canonical entities and validated behavior    |
| satisfied-entity-types     | array    | REQUIRED    | Canonical entity types the asset can satisfy                             |
| provenance-reference       | string   | REQUIRED    | Specification, artifact, import, or governance source                    |
| validation-status          | enum     | REQUIRED    | unvalidated, validated, warning, failed, expired, superseded             |
| validation-evidence        | array    | CONDITIONAL | Required when validation-status is validated, warning, failed, or expired |
| traceability-snapshot-id   | string   | REQUIRED    | Traceability snapshot proving origin and validation                       |
| sensitivity-classification | enum     | REQUIRED    | public, internal, confidential, restricted                               |
| trust-level                | enum     | REQUIRED    | approved, restricted, experimental, prohibited                           |
| owner                      | string   | REQUIRED    | Owning role, team, project, or governance authority                      |
| reuse-scope                | enum     | REQUIRED    | project, workspace, tenant, enterprise, public                           |
| deprecation-state          | enum     | REQUIRED    | active, deprecated, superseded, retired                                  |
| compatibility              | array    | CONDITIONAL | Supported platforms, languages, contracts, runtimes, or ISL versions     |
| immutable-identity-hash    | string   | CONDITIONAL | Stable integrity marker when artifact content is available               |
| consumer-count             | integer  | CONDITIONAL | Number of known active consumers when dependency tracking is available   |
| registered-at              | ISO 8601 | REQUIRED    | Registration timestamp                                                   |

### 5.3 Registry Rules

The registry MUST NOT list an asset as approved unless validation evidence and traceability evidence are available.

An asset with validation-status `failed`, `expired`, or deprecation-state `retired` MUST NOT be selected for new construction unless governance grants an explicit exception.

An asset with trust-level `experimental` MAY be selected only when runtime policy permits experimental dependencies.

Restricted assets MUST NOT be exposed across project, tenant, or environment boundaries without governed authorization.

The registry MUST distinguish an artifact lifecycle state from a reusable asset lifecycle state. Promotion to the registry creates or updates an asset record; it MUST NOT erase the original artifact metadata, artifact validation evidence, artifact version history, or traceability links.

Enterprise registries MUST support deterministic export of asset records, dependency relationships, governance decisions, validation evidence references, and consumer impact data.

---

## 6.0 Reuse Discovery

Reuse Discovery is a mandatory planning activity for tasks that may produce or select implementation artifacts.

### 6.1 Reuse Discovery Applicability

Reuse Discovery MUST run for task types including:

* implementation
* schema
* interface
* contract
* infrastructure
* policy
* validation
* documentation-template
* agent-pattern

A platform MAY skip Reuse Discovery only when the task type is explicitly declared non-reusable by policy or when governance authorizes emergency generation.

### 6.2 Reuse Discovery Inputs

Reuse Discovery MUST use:

| Input                     | Required | Description                                                       |
| ------------------------- | -------- | ----------------------------------------------------------------- |
| source-entity-ids         | YES      | Canonical entities the task must satisfy                          |
| construction-boundary-id  | YES      | Active Construction Boundary                                      |
| connection-context-ids    | YES      | Authorized cross-boundary contexts                                |
| task-capability-tags      | YES      | Required semantic capabilities                                    |
| required-artifact-types   | YES      | Expected artifact categories                                      |
| target-platform-profile   | YES      | Language, runtime, deployment, or platform constraints            |
| governance-profile-id     | YES      | Policy profile controlling reuse                                  |
| sensitivity-classification| YES      | Data and artifact sensitivity for the task                        |

### 6.3 Reuse Discovery Outcomes

Reuse Discovery MUST produce one of the following outcomes.

| Outcome        | Meaning                                                                 |
| -------------- | ----------------------------------------------------------------------- |
| reuse-full     | Existing asset fully satisfies the task intent                          |
| reuse-partial  | Existing asset partially satisfies the task intent and delta is needed  |
| wrap           | Existing asset can be used through an adapter or wrapper                |
| extend         | Existing asset can be extended under governed compatibility rules       |
| compose        | Multiple assets can jointly satisfy the task intent                     |
| generate-new   | No suitable approved asset exists                                       |
| reject-reuse   | Candidate asset exists but must not be used due to policy or mismatch   |
| escalate       | Reuse decision requires governance or human review                      |

The Runtime MUST NOT dispatch a generation task until a Reuse Discovery outcome has been recorded for applicable tasks.

---

## 7.0 Reuse Fitness Assessment

Each candidate asset considered during Reuse Discovery MUST produce a Reuse Fitness Assessment.

### 7.1 Assessment Record Schema

| Field                         | Type     | Required    | Description                                                           |
| ----------------------------- | -------- | ----------- | --------------------------------------------------------------------- |
| reuse-fitness-assessment-id   | string   | REQUIRED    | Unique assessment identifier                                          |
| task-id                       | string   | REQUIRED    | Construction task being assessed                                      |
| source-entity-ids             | array    | REQUIRED    | Canonical entities the task must satisfy                              |
| candidate-asset-id            | string   | REQUIRED    | Candidate reusable asset                                              |
| coverage-level                | enum     | REQUIRED    | full, partial, none, conflicting, uncertain                           |
| coverage-rationale            | string   | REQUIRED    | Explanation of coverage judgment                                      |
| interface-compatibility       | enum     | REQUIRED    | compatible, adapter-required, incompatible, unknown                   |
| version-compatibility         | enum     | REQUIRED    | compatible, upgrade-required, downgrade-required, incompatible, unknown |
| security-validation-status    | enum     | REQUIRED    | passed, warning, failed, expired, not-evaluated                       |
| boundary-compatibility        | enum     | REQUIRED    | within-boundary, connection-context-required, incompatible, unknown    |
| sensitivity-compatibility     | enum     | REQUIRED    | compatible, restricted, incompatible, unknown                         |
| recommended-action            | enum     | REQUIRED    | reuse-full, reuse-partial, wrap, extend, compose, generate-new, reject, escalate |
| assessed-by                   | string   | REQUIRED    | Planner, agent, tool, or governance component                         |
| assessed-at                   | ISO 8601 | REQUIRED    | Assessment timestamp                                                  |

### 7.2 Assessment Rules

A candidate asset MUST NOT receive coverage-level `full` unless it satisfies all must-have requirements linked to the task.

An asset requiring a Connection Context MUST NOT be selected unless the Connection Context exists and has passed validation.

An asset with failed or expired security validation MUST produce recommended-action `reject` or `escalate`.

An uncertain assessment MUST NOT authorize full reuse.

---

## 8.0 Reuse Decision Record

Reuse Discovery MUST produce a Reuse Decision Record for each applicable task.

| Field                       | Type     | Required    | Description                                                     |
| --------------------------- | -------- | ----------- | --------------------------------------------------------------- |
| reuse-decision-id           | string   | REQUIRED    | Unique reuse decision identifier                                |
| task-id                     | string   | REQUIRED    | Task governed by the decision                                   |
| construction-boundary-id    | string   | REQUIRED    | Active Construction Boundary                                    |
| selected-asset-ids          | array    | CONDITIONAL | Assets selected for reuse, composition, extension, or wrapping   |
| outcome                     | enum     | REQUIRED    | Outcome from the Reuse Discovery outcome catalog                |
| decision-rationale          | string   | REQUIRED    | Reason for the selected outcome                                 |
| delta-generation-required   | boolean  | REQUIRED    | Whether generation remains required                             |
| delta-generation-rationale  | string   | CONDITIONAL | Required when delta-generation-required is true                 |
| governance-decision-id      | string   | CONDITIONAL | Required when governance approval was needed                    |
| traceability-link-ids       | array    | REQUIRED    | Traceability links created or required by the decision          |
| decided-by                  | string   | REQUIRED    | Planner, runtime, agent, tool, or governance component          |
| decided-at                  | ISO 8601 | REQUIRED    | Decision timestamp                                              |

Generation MUST NOT proceed unless the Reuse Decision Record authorizes `generate-new`, `reuse-partial`, `wrap`, `extend`, or `compose` with delta generation.

A Reuse Decision Record that authorizes `generate-new` MUST include a rationale proving that applicable registry discovery was performed and that candidate assets were absent, rejected, incompatible, prohibited, or insufficient.

An implementation MUST NOT record `generate-new` merely because generation is easier, faster, cheaper, or preferred by a model response.

---

## 9.0 Generation Constraints After Reuse Discovery

Implementation agents, generators, and templates MUST respect the Reuse Decision Record.

An agent MUST NOT generate a full replacement implementation when outcome is `reuse-full`.

When outcome is `reuse-partial`, `wrap`, `extend`, or `compose`, generation MUST be limited to the recorded delta, adapter, extension, or composition logic.

Generated deltas MUST preserve traceability to both the source specification entities and the reused assets.

---

## 10.0 Asset Promotion

After a new artifact passes validation and traceability closure, the platform SHOULD evaluate whether it is eligible for promotion to the Reusable Asset Registry.

### 10.1 Promotion Criteria

An artifact SHOULD be considered for promotion when:

* it is stable
* it passed required validation
* it has complete traceability
* it provides a generalizable capability
* it is not tightly coupled to a single specification's private data or policy context
* security validation has passed or governed warnings are accepted
* ownership and reuse scope are declared
* governance approves promotion where required

### 10.2 Promotion Rules

Promotion MUST create or update a reusable asset record.

Promotion MUST identify the owner responsible for future maintenance.

Promotion MUST identify the allowed reuse scope.

Promotion MUST preserve provenance to the specification and artifacts that produced the asset.

The promoting owner MUST acknowledge that other systems may take a dependency on the asset when reuse-scope exceeds the originating project.

---

## 11.0 Asset Deprecation, Supersession, and Retirement

Deprecating, superseding, or retiring a reusable asset is a governed lifecycle action.

Deprecation MUST trigger impact analysis for known consumers.

Supersession MUST identify the replacement asset where one exists.

Retirement MUST NOT occur while active consumers depend on the asset unless governance approves a migration, isolation, or exception plan.

An asset marked retired MUST NOT be selected for new construction.

---

## 12.0 Traceability Requirements

The traceability model MUST support reuse-aware relationships.

At minimum, the traceability graph MUST support the following edge types.

| Edge Type | Source | Target | Meaning |
| --------- | ------ | ------ | ------- |
| reuses    | Artifact, Task, or Boundary | ReusableAsset | Current construction directly uses the asset |
| extends   | Artifact, Task, or Boundary | ReusableAsset | Current construction extends the asset |
| wraps     | Artifact, Task, or Boundary | ReusableAsset | Current construction adapts the asset through a wrapper |
| composes  | Artifact, Task, or Boundary | ReusableAsset | Current construction composes the asset with other assets |

Reuse traceability MUST be created before a reused asset is admitted as part of a stable output.

Impact analysis MUST traverse reuse edges.

---

## 13.0 Duplication Detection

Enterprise and Regulated profiles MUST require duplication detection as part of static analysis, semantic analysis, repository validation, or an implementation-equivalent validation mechanism.

A significant duplication finding between a new artifact and an approved reusable asset MUST route the task back through Reuse Discovery unless governance accepts the duplication with a documented rationale.

Duplication findings MUST reference candidate asset identifiers when known.

Enterprise and Regulated profiles MUST define duplication thresholds, matching methods, and exception handling in governance policy.

A duplicated artifact that bypassed required Reuse Discovery MUST NOT be promoted to stable state until Reuse Discovery is completed or governance records a waiver that explains why duplication is accepted.

---

## 14.0 Construction Boundary and Connection Context Rules

Reuse MUST operate inside the active Construction Boundary.

A reused asset that introduces external behavior, shared state, cross-boundary assumptions, external services, sensitive data access, or downstream dependencies MUST be authorized through a Connection Context.

The Connection Context MUST identify:

* asset identifiers
* source and target boundaries
* allowed artifacts and interfaces
* assumptions transferred
* validation evidence transferred
* sensitivity classification
* trust level
* expiration or supersession conditions

If a reusable asset cannot be summarized or bounded within the active Construction Boundary and authorized Connection Contexts, the Reuse Decision MUST escalate.

For local or resource-constrained model profiles, the platform MUST prefer asset summaries, contracts, metadata, test evidence, and selected excerpts before transferring full asset content into model context.

A Connection Context MUST NOT authorize broad transfer such as "all prior code", "entire project history", or "full repository context" unless governance records why no smaller context package can satisfy the task.

---

## 15.0 Governance Requirements

Governance MUST control:

* registry write access
* asset promotion
* cross-project reuse
* cross-tenant reuse
* restricted asset reuse
* deprecation and retirement
* exceptions to reuse-before-generation
* reuse of assets with expired, warning, or experimental status

Governance decisions affecting reusable assets MUST be auditable.

---

## 16.0 Conformance Requirements

### 16.1 Registry Conformance

A Reusable Asset Registry conforms to ISL v1.8 if it:

* records required asset metadata
* preserves provenance and traceability
* exposes semantic asset lookup
* records validation and trust state
* supports dependency and consumer lookup
* enforces governance-controlled promotion and deprecation

### 16.2 Planning Conformance

A Planning Engine conforms to ISL v1.8 if it:

* performs Reuse Discovery for applicable tasks
* produces Reuse Fitness Assessments
* produces Reuse Decision Records
* blocks generation without a valid reuse decision
* supports reuse-full, reuse-partial, wrap, extend, compose, generate-new, reject-reuse, and escalate outcomes

### 16.3 Runtime Conformance

A Runtime conforms to ISL v1.8 if it:

* refuses to dispatch applicable generation tasks without Reuse Discovery
* limits generation to authorized deltas when reuse is partial
* records reuse traceability before stable promotion
* routes duplication findings back through Reuse Discovery
* enforces governance decisions for asset reuse

### 16.4 Enterprise Conformance

An enterprise implementation conforms to ISL v1.8 if it:

* governs registry curation
* performs reuse-aware impact analysis
* prevents unmanaged cross-project or cross-tenant reuse
* validates reusable assets before approval
* monitors asset deprecation, supersession, and retirement
* defines duplication thresholds and routes significant duplication findings through Reuse Discovery
* exports reusable asset, reuse decision, and consumer impact evidence for audit
* enforces minimized context transfer for reusable assets in local, cloud, and hybrid model profiles

### 16.5 Regulated Conformance

A regulated implementation conforms to ISL v1.8 if it satisfies Enterprise Conformance and additionally:

* requires explicit governance approval for restricted, cross-tenant, retired, deprecated, warning-status, or experimental asset use
* preserves immutable evidence references for every promoted asset and every reuse-derived deployable artifact
* prevents deployment-capable packaging when reuse provenance is incomplete unless a valid governance waiver marks the package risk and approver
* supports audit export of asset lineage from source specification to reusable asset to consuming artifact

---

## 17.0 Summary

ISL v1.8 makes reuse a first-class construction control. A conforming ALETHEIA Engine MUST discover and assess reusable assets before generating new implementation artifacts. Reusable assets MUST be validated, traceable, governed, versioned, and compatible with Construction Boundaries and Connection Contexts.

This model reduces duplicated code, improves maintainability, strengthens enterprise governance, and helps autonomous construction behave like a controlled engineering system rather than an unbounded generator.

---
