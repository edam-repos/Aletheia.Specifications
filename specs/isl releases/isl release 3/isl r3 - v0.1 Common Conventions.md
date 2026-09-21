# ALETHEIA Specification Language (ISL) v0.1

# Common Conventions

**Status:** Normative
**Release:** r3
**Depends On:** ISL v0.0
**Document Type:** Foundation Layer — Shared Conventions, Vocabulary, and Common Models

---

## 1.0 Scope

This document defines the conventions, vocabulary, and common models shared by every ISL r3 document. It is the single source of truth for normative language, field requirement levels, design principles, the common error model, the common telemetry model, the conformance framework, and the shared enums used across the corpus.

A conforming ISL document MUST NOT redefine a convention, enum, or common model defined here. Domain documents MAY extend the common models only where this document explicitly permits extension.

---

## 2.0 Normative Language

The following terms carry precise normative meaning throughout the ISL corpus:

| Term | Meaning |
| ---- | ------- |
| **MUST** / **SHALL** | Mandatory requirement |
| **MUST NOT** / **SHALL NOT** | Mandatory prohibition |
| **SHOULD** | Recommended practice; deviations MUST be justified |
| **MAY** | Permitted behavior |

Normative requirements apply to both specification authors and conforming ISL tooling unless explicitly scoped to one or the other. All normative statements are enforceable unless explicitly stated otherwise.

---

## 3.0 Field Requirement Levels

ISL uses the following field requirement levels:

| Level | Meaning |
| ----- | ------- |
| **REQUIRED** | Field or section MUST be present |
| **CONDITIONAL** | Field or section MUST be present when the stated condition applies |
| **OPTIONAL** | Field or section MAY be present |
| **DERIVED** | Field is produced during normalization or platform processing |

---

## 4.0 Identifier Conventions

Identifiers in ISL MUST be stable, unique within their entity type, and suitable for traceability.

### 4.1 Identifier Format

Identifiers MUST match the pattern `^[A-Za-z0-9][A-Za-z0-9_.:-]*$`.

### 4.2 Entity Type Prefixes

Canonical entity identifiers MUST use a type prefix:

| Prefix | Entity Type |
| ------ | ----------- |
| `PRJ` | Project |
| `CTX` | Context |
| `REQ` | Requirement |
| `NFR` | Non-Functional Requirement |
| `ACT` | Actor |
| `STK` | Stakeholder |
| `OBJ` | Business Objective |
| `SCP` | Scope |
| `DAT` | Data Entity |
| `INF` | Infrastructure |
| `SVC` | Service |
| `CAP` | Capability |
| `INT` | Interface |
| `POL` | Policy |
| `VAL` | Validation |
| `WFL` | Workflow |
| `RAS` | Reusable Asset |
| `TSK` | Construction Task |
| `ART` | Artifact |
| `VRS` | Validation Result |
| `RPR` | Repair Record |
| `DEC` | Decision Record |
| `TIV` | Tool Invocation |
| `MIV` | Model Invocation |
| `GOV` | Governance Event |
| `IAN` | Impact Analysis |
| `TRS` | Traceability Snapshot |
| `DPA` | Deployment Artifact |
| `EXE` | Execution Record |
| `RSA` | Reusable Asset |
| `RSD` | Reuse Decision |
| `RFA` | Reuse Fitness Assessment |
| `BND` | Construction Boundary |
| `CNX` | Connection Context |
| `BCR` | Boundary Continuation Record |

An authored specification MAY use provisional identifiers during Draft readiness, but MUST use valid traceability identifiers before reaching Machine-Valid readiness.

---

## 5.0 Date, Time, and Version Format

Date and time values in ISL MUST use ISO 8601 format.

Version values MUST use semantic versioning:

| Component | Meaning |
| --------- | ------- |
| major | Breaking change |
| minor | Backward-compatible functional addition |
| patch | Correction or clarification |

---

## 6.0 Shared Enums

The following enums are defined once here and referenced by all ISL documents. Domain documents MUST NOT redefine them.

### 6.1 Readiness Level

`draft`, `reviewable`, `machine-valid`, `autonomous-ready`

### 6.2 Risk Tier

`low`, `standard`, `high`, `critical`

### 6.3 Sensitivity Classification

`public`, `internal`, `confidential`, `restricted`

### 6.4 Conformance Profile

`core`, `team`, `enterprise`, `regulated`

### 6.5 Validation Outcome

`passed`, `failed`, `warning`, `timeout`, `error`, `not-applicable`

### 6.6 Governance Decision

`allow`, `warn`, `block`, `escalate`, `approval-required`, `waiver-required`, `override-required`

### 6.7 Artifact State

`candidate`, `pending`, `valid`, `failed`, `repaired`, `escalated`, `waived`, `stable`, `packaged`, `superseded`, `deprecated`, `non-deployable`, `archived`, `deleted`

### 6.8 Artifact Derivation Type

`direct`, `derived`, `inferred`, `repair`, `reused`, `reuse-delta`, `wrapper`, `extension`, `composition`

### 6.9 Requirement Priority

`must-have`, `should-have`, `nice-to-have`

### 6.10 Reuse Outcome

`reuse-full`, `reuse-partial`, `wrap`, `extend`, `compose`, `promote-candidate`, `reject-reuse`

### 6.11 Execution Status

`initialized`, `admission-checking`, `admitted`, `preparing`, `running`, `pausing`, `paused`, `recovering`, `draining`, `completed`, `failed`, `halted`, `cancelled`, `escalated`

---

## 7.0 Design Principles

The following principles govern all ISL documents. They are defined once here and referenced by domain documents.

1. **Specification Authority** — The specification is the authoritative source of truth. No generated artifact MAY supersede, contradict, or silently extend it.
2. **Deterministic Validation** — Generated artifacts MUST NOT be trusted until validated by deterministic tools. Reasoning may generate, but tools establish trust.
3. **Traceability Enforcement** — Every meaningful output MUST be traceable to the specification entity that caused it.
4. **Governed Execution** — Autonomy MUST NOT bypass governance, security, or validation controls.
5. **Separation of Concerns** — Each layer and subsystem has a defined responsibility; no layer MAY bypass another layer's responsibilities.
6. **Reuse-First Construction** — Construction MUST prefer approved, validated, traceable reusable assets before generating new artifacts.
7. **Explicit Semantics** — Core entity type and identity MUST NOT be inferred from prose; they MUST be explicit and machine-checkable.
8. **Local-First** — The platform MUST support local-first execution while remaining deployable to team, enterprise, and hybrid modes.

---

## 8.0 Common Error Model

All ISL documents use a single error record structure. Domain documents MAY define extension error classes but MUST use this base structure.

### 8.1 Error Record

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| error-id | string | REQUIRED | Stable error identifier |
| error-class | string | REQUIRED | Error class (see 8.2) |
| severity | enum | REQUIRED | `info`, `warning`, `error`, `critical` |
| message | string | REQUIRED | Human-readable description |
| required-action | string | REQUIRED | Action required to resolve |
| recorded-at | timestamp | REQUIRED | ISO 8601 timestamp |
| target-id | string | CONDITIONAL | Identifier of the affected entity |
| evidence | array | CONDITIONAL | Supporting evidence references |

### 8.2 Error Class Taxonomy

The corpus uses a single high-level error taxonomy. Each domain registers a small set of extension classes under these categories:

| Category | Description |
| -------- | ----------- |
| `validation` | Structural, semantic, or reference validation failure |
| `governance` | Policy, approval, waiver, or override failure |
| `traceability` | Missing or inconsistent traceability |
| `repository` | Artifact repository operation failure |
| `runtime` | Scheduling, execution, or recovery failure |
| `tool` | Tool invocation, timeout, or result failure |
| `model` | Model invocation, output, or normalization failure |
| `agent` | Agent invocation, context, or output failure |
| `security` | Identity, authorization, secret, or isolation failure |
| `observability` | Telemetry, monitoring, or alerting failure |
| `deployment` | Deployment, scaling, or environment failure |
| `reuse` | Reuse discovery, fitness, or decision failure |

---

## 9.0 Common Telemetry Model

All ISL documents use a single telemetry event structure. The full event catalog is defined in ISL v2.3 (Observability, Telemetry, and Deployment); domain documents reference it rather than redefining it.

### 9.1 Telemetry Event

| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| event-id | string | REQUIRED | Stable event identifier |
| event-type | string | REQUIRED | Event category |
| correlation-id | string | REQUIRED | Correlation identifier linking related events |
| occurred-at | timestamp | REQUIRED | ISO 8601 timestamp |
| source | string | REQUIRED | Component that emitted the event |
| subject-id | string | CONDITIONAL | Affected entity identifier |
| payload | object | CONDITIONAL | Structured event data |

Telemetry MUST include correlation identifiers. Telemetry does not replace audit logs or traceability records.

---

## 10.0 Conformance Framework

### 10.1 Conformance Dimensions

| Dimension | Description |
| --------- | ----------- |
| Language | v1.x compliance |
| Platform | v2.x compliance |
| Execution | v3.x compliance |
| Governance | cross-layer enforcement |

### 10.2 Conformance Levels

| Level | Description |
| ----- | ----------- |
| L0 | Specification parsing |
| L1 | Semantic validation |
| L2 | Planning and analysis |
| L3 | Full autonomous construction |

### 10.3 Conformance Profiles

| Profile | Purpose | Minimum Capability |
| ------- | ------- | ------------------ |
| Core | Local or single-project construction | Parse, normalize, plan, execute, validate, trace, and govern according to declared subsystem support |
| Team | Shared construction for multiple users or projects | Core plus shared repository control, durable state, access control, and auditable governance records |
| Enterprise | Organization-scale governed construction | Team plus reusable asset governance, Construction Boundary enforcement, Connection Context validation, operational monitoring, recovery, schema evidence, and conformance test evidence |
| Regulated | High or Critical risk construction | Enterprise plus stronger identity, retention, segregation of duties, waiver control, security evidence, and audit export requirements |

An implementation MUST NOT claim a profile unless every mandatory requirement for that profile is implemented, tested, and evidenced.

### 10.4 Common Conformance Requirements

A conforming implementation MUST:

* enforce readiness gating
* enforce deterministic validation
* enforce reuse-first construction planning
* enforce Construction Boundary and Connection Context controls
* enforce traceability closure
* enforce governance controls
* preserve audit and evidence records

---

## 11.0 Cross-Cutting Invariants

The following invariants hold across the entire corpus:

* Construction MUST NOT begin without readiness validation.
* Generation MUST NOT begin for applicable tasks without reuse discovery.
* Artifacts MUST NOT be accepted without deterministic validation.
* All system elements MUST be traceable.
* Execution MUST operate under governance constraints.
* Construction Boundary crossings MUST occur only through valid Connection Contexts.
* Governance audit records MUST be immutable or correction-only.
* Secrets MUST NOT be written to logs, telemetry, specifications, or context.
* A tool timeout MUST NOT be treated as success.
* Expired approvals, waivers, or overrides MUST NOT authorize progression.

---

## 12.0 Conformance to This Document

An implementation conforms to this document if it uses the shared enums, error model, telemetry model, and conventions defined here without redefining them, and if it enforces the cross-cutting invariants.

---

## 13.0 Summary

This document is the shared foundation of the ISL r3 corpus. It defines normative language, field levels, identifiers, shared enums, design principles, the common error and telemetry models, the conformance framework, and cross-cutting invariants. All other ISL r3 documents reference this document rather than redefining these conventions.
