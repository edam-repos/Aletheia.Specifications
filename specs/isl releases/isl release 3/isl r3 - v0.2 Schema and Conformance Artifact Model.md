# ALETHEIA Specification Language (ISL) v0.2

# Schema and Conformance Artifact Model

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v1.0, ISL v1.1
**Document Type:** Foundation Layer — Schema, Validation, and Conformance Artifact Specification

---

## 1.0 Scope

This document defines the schema and conformance artifact model for ISL r3. It establishes how machine-readable schemas, validation reports, parser fixtures, canonical model validation, and enterprise conformance evidence relate to the normative ISL documentation set.

This document does not replace the normative prose specifications. Machine-readable schemas are conformance artifacts derived from the normative specifications and MUST remain consistent with them.

---

## 2.0 Purpose

ISL r3 requires machine-readable schema artifacts so that an ALETHEIA Engine can validate specifications, canonical models, and conformance evidence consistently. The purpose of this model is to ensure that:

* authored specifications can be structurally validated before semantic normalization
* canonical models can be validated before construction planning
* reuse, Construction Boundary, and Connection Context controls can be checked by tools
* enterprise conformance claims are backed by reproducible evidence
* schema changes are versioned, traceable, and governed

---

## 3.0 Terms and Definitions

**Schema Artifact** — A machine-readable file that defines the valid structure of an ISL representation.

**Authored ISL Schema** — A schema that validates the structured form authored by humans or tools before semantic normalization.

**Canonical Schema** — A schema that validates the normalized canonical semantic model produced by the ALETHEIA Engine.

**Conformance Artifact** — Any schema, test fixture, validation report, conformance matrix, or evidence record used to prove implementation behavior.

**Validation Fixture** — A positive or negative test input used to verify parser, schema, normalization, or conformance behavior.

**Schema Binding** — The explicit association between a normative ISL document section and a machine-readable schema definition.

---

## 4.0 Normative Position of Schemas

Machine-readable schemas are required r3 conformance artifacts.

A schema artifact MUST NOT weaken, remove, reinterpret, or contradict a normative ISL requirement. If a conflict exists between normative prose and a schema artifact, the normative prose controls until the conflict is resolved through governed specification maintenance.

A conforming implementation MUST identify the schema versions used during authored validation, canonical validation, and conformance evidence validation.

---

## 5.0 Required Release 3 Schema Artifacts

An ISL r3 schema set MUST include at least the following artifacts.

| Schema Artifact | Required | Purpose |
| --------------- | -------- | ------- |
| Common Schema | YES | Shared primitive and domain definitions referenced by all schemas |
| Authored ISL Schema | YES | Validate authored ISL r3 structured input before normalization |
| Canonical ISL Schema | YES | Validate normalized canonical semantic model before planning |
| Conformance Evidence Schema | YES | Validate Enterprise and Regulated conformance evidence sets |
| Validation Report Schema | SHOULD | Validate parser, schema, normalization, and lifecycle validation reports |
| Schema Index | SHOULD | List schema artifact identifiers, versions, hashes, and supported ISL releases |

For the r3 baseline, the following schema files are expected:

| File | Role |
| ---- | ---- |
| `isl-r3-common.schema.json` | Shared primitive and domain definitions |
| `isl-r3-language.schema.json` | Authored specification and conformance evidence schema |
| `isl-r3-canonical.schema.json` | Canonical semantic model schema |
| `isl-r3-enterprise.schema.json` | Enterprise project, program, and portfolio control records (cost ledger, schedule baseline, benefits, service-transition handoff, approval-queue metrics, portfolio inventory, org conformance declaration) |

The canonical model is defined **once** in the canonical schema. The language schema references it via `$ref` rather than embedding a copy. The enterprise schema references the common schema for shared primitives and enums and is defined by ISL v0.3 §15.

---

## 6.0 Schema Format Requirements

ISL r3 schema artifacts SHOULD use JSON Schema 2020-12.

When JSON Schema is used, each schema artifact MUST include:

| Field | Requirement |
| ----- | ----------- |
| `$schema` | MUST identify JSON Schema draft version |
| `$id` | MUST provide a stable schema identifier |
| `title` | MUST identify the schema purpose |
| `description` | MUST summarize validation scope |
| `$defs` | SHOULD define reusable schema structures |

Schema artifacts MUST be deterministic, text-based, version controlled, and suitable for automated validation.

---

## 7.0 Common Schema Requirements

The Common Schema defines shared primitive and domain definitions referenced by the language and canonical schemas. It MUST define:

* primitive types: `Identifier`, `NonEmptyString`, `SemVer`, `Timestamp`, `ReferenceArray`, `ExtensionObject`
* shared enums: `ReadinessLevel`, `ConformanceProfile`, `RiskTier`, `SensitivityClassification`, `ValidationStatus`, `GovernanceStatus`, `ArtifactState`, `DerivationType`, `ReuseOutcome`, `ExecutionStatus`
* shared domain types: `ReusableAssetType`, `ReuseOutcome`

The Common Schema MUST be the single source of truth for these definitions. The language and canonical schemas MUST reference them via `$ref` and MUST NOT redefine them.

---

## 8.0 Authored ISL Schema Requirements

The Authored ISL Schema validates the structured representation before semantic normalization. It MUST validate:

* release identifier
* ISL version identifier
* specification metadata
* system identity
* readiness level
* business objectives
* actors and stakeholders
* functional and non-functional requirements
* data model structures
* service boundaries and interfaces
* workflows when present
* security and policy constraints
* operational expectations
* validation and acceptance criteria
* change history
* applicable control sections (reusable assets, Construction Boundaries, Connection Contexts)

An authored specification MUST NOT progress to semantic normalization when required authored schema validation fails.

The Authored ISL Schema MUST reference the canonical model via `$ref` to the canonical schema when a canonical model is embedded or validated, rather than re-implementing it.

---

## 9.0 Canonical Schema Requirements

The Canonical Schema validates the normalized model produced by the ALETHEIA Engine. It MUST validate:

* canonical model identity
* source specification reference
* normalization record
* readiness level
* conformance profile
* canonical entity sets
* canonical relationship records
* traceability snapshot
* planning model
* construction task records
* reuse model and reusable asset records
* Reuse Fitness Assessments and Reuse Decision Records
* Construction Boundary and Connection Context records
* artifact records
* validation results
* governance decisions
* evidence records

A canonical model MUST NOT be accepted for construction planning when canonical schema validation fails.

The Canonical Schema is the **single authoritative definition** of the canonical model. No other schema MAY embed a divergent copy.

---

## 10.0 Conformance Evidence Schema Requirements

Enterprise and Regulated implementations MUST produce conformance evidence. The Conformance Evidence Schema MUST validate evidence for:

* conformance matrix
* schema artifacts
* parser tests
* lifecycle tests
* reuse tests
* Construction Boundary tests
* Connection Context tests
* security tests
* audit export

Evidence records MUST identify:

| Field | Requirement |
| ----- | ----------- |
| evidence-id | REQUIRED |
| evidence-type | REQUIRED |
| subject-id | REQUIRED |
| location | REQUIRED |
| status | REQUIRED |
| produced-at | CONDITIONAL |
| hash | CONDITIONAL |

An implementation MUST NOT claim Enterprise or Regulated conformance when required evidence records fail schema validation.

---

## 11.0 Validation Stages

| Stage | Input | Output | Blocks Progression |
| ----- | ----- | ------ | ------------------ |
| Authored Structural Validation | Authored ISL specification | Authored validation report | YES |
| Semantic Normalization Validation | Canonical model | Canonical validation report | YES |
| Planning Validation | Canonical model and construction plan | Planning validation report | YES |
| Reuse Validation | Reuse assessments and decisions | Reuse validation report | YES |
| Boundary Validation | Construction Boundaries and Connection Contexts | Boundary validation report | YES |
| Artifact Validation | Generated or reused artifacts | Artifact validation evidence | YES |
| Conformance Validation | Evidence set | Conformance validation report | YES for Enterprise and Regulated claims |

Validation reports MUST identify blocking errors, warnings, waived findings, and evidence references.

---

## 12.0 Parser and Fixture Requirements

Schema artifacts MUST be accompanied by validation fixtures. At minimum, fixture sets SHOULD include:

* valid authored specification fixture
* invalid authored specification fixture missing required sections
* invalid authored specification fixture with bad identifiers
* valid canonical model fixture
* invalid canonical model fixture with unresolved relationships
* reuse-before-generation fixture
* duplicate-generation prevention fixture
* valid Construction Boundary fixture
* invalid Connection Context fixture
* Enterprise conformance evidence fixture

Enterprise and Regulated implementations MUST execute fixture tests as part of conformance validation.

---

## 13.0 Schema Versioning

Each schema artifact MUST be versioned. Schema versioning MUST support patch, minor, and major updates. A schema change MUST identify:

| Field | Requirement |
| ----- | ----------- |
| schema-id | REQUIRED |
| prior-version | CONDITIONAL |
| new-version | REQUIRED |
| affected-isl-documents | REQUIRED |
| compatibility-impact | REQUIRED |
| migration-guidance | CONDITIONAL |

Schema changes that affect Enterprise or Regulated conformance MUST be governed.

---

## 14.0 Traceability Between Schemas and Normative Documents

Schema definitions MUST be traceable to normative ISL documents. Traceability SHOULD identify source ISL document, section number, schema definition name, requirement or field represented, and validation fixture coverage.

---

## 15.0 Extension Rules

Implementations MAY define schema extensions. Schema extensions MUST NOT redefine standard ISL fields, weaken required r3 validation, bypass reuse-before-generation controls, bypass Construction Boundary or Connection Context controls, or obscure audit or traceability evidence. Extension fields MUST be namespaced or otherwise distinguishable from standard ISL fields.

---

## 16.0 Governance Requirements

Schema artifacts are governed construction assets. Governance MUST control schema publication, version promotion, deprecation, supersession, compatibility claims, waivers, and use in Enterprise and Regulated conformance. Expired, superseded, or revoked schemas MUST NOT be used for new Enterprise or Regulated conformance claims unless governance explicitly authorizes a transition period.

---

## 17.0 Conformance Requirements

An implementation conforms to this document if it provides required r3 schema artifacts, validates authored specifications before normalization, validates canonical models before construction planning, validates conformance evidence for Enterprise and Regulated claims, maintains schema version records, preserves schema validation evidence, executes required validation fixtures, and governs schema changes and waivers.

An implementation MUST NOT treat successful schema validation as sufficient for construction readiness. Schema validation proves structural validity; readiness, semantic validity, governance authorization, traceability closure, reuse enforcement, and deterministic validation remain required.

---

## 18.0 Summary

ISL r3 requires machine-readable schema and conformance artifacts so that autonomous software construction can be validated repeatedly, audited reliably, and governed at enterprise quality. The schema model makes authored validation, canonical validation, reuse controls, Construction Boundary controls, Connection Context validation, and Enterprise conformance evidence executable by tools while preserving the normative authority of the ISL documentation set.
