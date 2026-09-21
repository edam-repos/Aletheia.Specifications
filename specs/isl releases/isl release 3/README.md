# ISL Release 3 (r3)

This directory contains **ISL release 3 (r3)** — a consolidated, simplified, and corrected version of the ALETHEIA Specification Language corpus.

**Start here:** if you want the essentials without reading the full corpus, read **`isl r3 - Compact Guide.md`** — the fundamentals, the message, and a reading map to every source document. The full corpus follows below for authoritative detail.

r3 consolidates the r2 corpus: it merges overlapping documents, strips boilerplate and meta-commentary, extracts shared conventions into a single common document, resolves cross-document contradictions, and fixes the JSON schema duplication. The substantive normative content of r2 is preserved; the structure is simplified.

---

## Corpus Structure

| Layer | Document | Consolidates (r2) |
| ----- | -------- | ----------------- |
| **Foundation** | `v0.0 ALETHEIA Specification Language` | v0.0 |
| | `v0.1 Common Conventions` | **new** — shared vocabulary, enums, error/telemetry models, conformance framework |
| | `v0.2 Schema and Conformance Artifact Model` | v0.1 |
| | `v0.3 Enterprise Project, Program, and Portfolio Integration` | **new** — enterprise PM domains (portfolio/program, cost, schedule, resource, comms, benefits, adoption, service transition, procurement, lessons learned, approval capacity) |
| | `isl-r3-common.schema.json` | **new** — shared schema definitions |
| | `isl-r3-language.schema.json` | v0.1.1 (fixed) |
| | `isl-r3-canonical.schema.json` | v0.1.2 (fixed) |
| | `isl-r3-enterprise.schema.json` | **new** — v0.3 enterprise PPM control records |
| **Language** | `v1.0 The Specification Language` | v1.0 |
| | `v1.1 The Canonical Semantic Model` | v1.1 |
| | `v1.2 The Execution Model` | v1.2 + v1.6 |
| | `v1.3 Readiness and Governance Model` | v1.3 + v1.7 |
| | `v1.4 The Traceability Model` | v1.4 |
| | `v1.5 Construction Planning and Reuse Model` | v1.5 + v1.8 |
| **Platform** | `v2.0 Platform Architecture and Execution Runtime` | v2.0 + v2.4 |
| | `v2.1 Agent and Model Integration` | v2.1 + v2.5 |
| | `v2.2 State, Memory, and Artifact Repository` | v2.2 + v2.3 |
| | `v2.3 Observability, Telemetry, and Deployment` | v2.6 + v2.7 |
| **Execution** | `v3.0 Reference Implementation Architecture and Repository Layout` | v3.0 + v3.1 |
| | `v3.1 Reference Construction Scenario` | v3.2 + v3.3 |
| | `v3.2 Integration Components` | v3.4 + v3.8 + v3.9 |
| | `v3.3 Runtime Orchestration and Autonomous Development Loop` | v3.5 + v3.6 |
| | `v3.4 Autonomous SDLC and Platform Security` | v3.7 + v3.10 |
| **Ecosystem** | `v4.0 Platform Vision and Ecosystem` | v4.0 |
| **Companion** | `Companion - Human Oversight and Project Management` | **new** — informative guide to human roles and canonical entities |

---

## Key Changes from r2

### 1. Shared conventions extracted (v0.1)
All documents now reference **ISL v0.1 Common Conventions** for:
* normative language (MUST/SHOULD/MAY) and field requirement levels
* identifier conventions and entity-type prefixes
* shared enums (readiness, risk tier, artifact state, governance decision, validation outcome, reuse outcome, execution status, etc.)
* the common error model and error-class taxonomy
* the common telemetry model
* the conformance framework (dimensions, levels, profiles)
* cross-cutting invariants

Domain documents no longer redefine these.

### 2. Boilerplate and meta-commentary removed
* "Purpose" sections that merely restated scope were removed.
* "Summary" sections and "This revision strengthens…" sentences were removed.
* Editor notes and process artifacts (e.g., "ISL-007", "The original v2.2 model…") were removed.
* Repeated "Normative References" / "Terms and Definitions" / "Design Principles" sections were replaced with references to ISL v0.1.

### 3. Contradictions resolved
* **One artifact-state enum** (v0.1 §6.7) used everywhere.
* **One governance-decision enum** (v0.1 §6.6) used everywhere.
* **One validation-outcome enum** (v0.1 §6.5) used everywhere.
* **"Execution Runtime"** is the single name for the execution engine (was "Construction Engine" in some r2 docs).
* **"Model Integration Layer" / "Tool Integration Layer"** are the single names (were "Model Gateway" / "Tool Gateway" in some r2 docs).
* **Construction Boundaries and Connection Contexts** are defined authoritatively in v1.5 and referenced elsewhere (they were scattered and undefined in r2).
* The **circular preconditions** and **record-schema mismatches** identified in the r2 review were reconciled.

### 4. JSON schemas fixed
* **`isl-r3-common.schema.json`** — new shared schema holding the primitive types and enums referenced by both other schemas.
* **`isl-r3-canonical.schema.json`** — the canonical model is defined **once** here.
* **`isl-r3-language.schema.json`** — validates authored specifications and conformance evidence; it **`$ref`s** the canonical schema for canonical models instead of embedding a divergent copy (the r2 defect).
* **`isl-r3-enterprise.schema.json`** — **new** — validates the v0.3 enterprise PPM control records (cost ledger, schedule baseline, benefits plan/realization, service-transition handoff, approval-queue metrics, portfolio inventory, org conformance declaration).
* The conformance evidence record now enforces the REQUIRED `evidence-type` and `subject-id` fields.

---

## Reading Order

1. `v0.0` — umbrella standard
2. `v0.1` — common conventions (read before any other document)
3. `v0.2` — schema and conformance artifact model
4. `v0.3` — enterprise project, program, and portfolio integration
5. `v1.x` — language layer
6. `v2.x` — platform layer
7. `v3.x` — execution layer
8. `v4.0` — vision and ecosystem
9. `Companion - Human Oversight and Project Management` — plain-language guide to the human roles and canonical entities (informative)

---

## Worked Example

A concrete worked example of the companion applied to a real project lives in the repository's `scenarios` folder: *SCEN-0001* — an RFP-style communicable-disease case-management web-app with human-only and AI-assisted delivery baselines, a risk register, a one-page comparison, and a deliverable/acceptance checklist. It is the companion's argument made concrete: the same discipline, the same budget, and the pairing's advantage showing up in completeness and quality rather than speed or cost.

---

## Status

This is a consolidation release. The r2 documents remain in place under `../isl release 2/` for reference. r3 is the live corpus.
