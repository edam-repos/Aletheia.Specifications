# ISL r3 — Compact Guide

**The ALETHEIA Specification Language, release 3 — fundamentals, message, and a reading map, in one place.**

| | |
| --- | --- |
| **What this is** | A compact walkthrough of the ISL r3 corpus. It does not replace any document — it captures the essentials and points you to the right source for authoritative detail. |
| **Status** | Informative overview. For normative requirements, read the cited document. |
| **Coverage** | ISL r3 v0.0–v4.0, the Companion, and the four r3 JSON schemas. |

---

## 1. What ISL r3 is

ISL r3 is a **specification-driven system-construction corpus**. It defines how to express a system as a structured, machine-interpretable **specification**; how to transform that specification into **validated artifacts** (code, configuration, tests, infrastructure); and how to govern the whole process under **human oversight**.

Three ideas carry the entire corpus:

1. **Specification first.** A system is authored once as a structured specification, not assembled ad hoc (*v1.0*).
2. **Deterministic validation.** Artifacts are not trusted because they were generated — they are trusted because they were validated by repeatable tools (*v0.0, v1.2*).
3. **Human-governed and traceable.** Every step ties back to requirements and evidence, and human judgment sits at the gates (*v1.3, v1.4, Companion*).

---

## 2. The fundamentals, by layer

### Foundation
- **v0.0 — Foundational Standard / System Architecture / Conformance Framework.** The umbrella standard. Governs the structure, layering (v0.0–v4.0), and interpretation of the whole corpus; defines the system model, cross-layer contracts, integrity rules, the conformance framework, and alignment to ISO/IEC 25010, NIST SP 800-53, W3C PROV-DM, and OpenTelemetry.
- **v0.1 — Common Conventions.** The single source of truth for normative language (MUST/SHOULD/MAY), field requirement levels (REQUIRED/CONDITIONAL/OPTIONAL/DERIVED), identifier conventions, design principles, the common error model, the common telemetry model, shared enums, and the conformance framework. No r3 document may redefine these.
- **v0.2 — Schema and Conformance Artifact Model.** Normative position of the JSON schemas, the required r3 schema artifacts, schema format rules, and validation stages.
- **v0.3 — Enterprise Project, Program, and Portfolio Integration.** The enterprise-PM domains: portfolio/program, cost & financial, schedule, resource, communications, benefits/value, organizational change & adoption, service transition, procurement/supply chain, lessons learned, and approval capacity / human bottleneck management — plus the paired-delivery model of the Senior Developer and the AI Coder.

### Language layer (authoring and semantics)
- **v1.0 — The Specification Language.** How you author a specification: the required 12-section structure (identity, objectives, actors, functional & non-functional requirements, data model, service boundaries, workflows, security, operations, validation/acceptance, change history), the authored-markdown syntax, canonical mapping, extension rules, and the minimum-complete-specification checklist.
- **v1.1 — The Canonical Semantic Model.** The normalized, machine-readable form of a specification: a canonical graph of entities (project, context, stakeholder, actor, requirement, capability, service, data entity, workflow, interface, policy, infrastructure, validation) and relationships, with normalization and validation rules.
- **v1.2 — The Execution Model.** How a specification is executed into artifacts: a nine-phase model (specification interpretation → construction-planning confirmation → artifact generation → deterministic validation → repair cycles → test generation/execution → security & policy validation → artifact consolidation → deployment preparation), tool registry/trust profiles, sandboxing, and traceability during execution.
- **v1.3 — Readiness and Governance Model.** The governance spine: lifecycle principles, roles and separation of duties, readiness levels, policy governance, risk tiers, approval gates, waivers, overrides, escalation, evidence packages, and audit logging.
- **v1.4 — The Traceability Model.** Enforced linkage between specifications, tasks, artifacts, and validation outcomes: traceability identifiers, the traceability graph, node/edge types, bidirectional traceability, impact analysis, and snapshots.
- **v1.5 — Construction Planning and Reuse Model.** How construction is planned: construction task definition and classification, the construction task graph, dependency resolution, **construction boundaries and connection contexts**, repair-task generation, and the reusable-asset registry.

### Platform layer (the runtime)
- **v2.0 — Platform Architecture and Execution Runtime.** The platform's physical and logical architecture and the execution runtime that hosts the language and execution layers.
- **v2.1 — Agent and Model Integration.** How agents collaborate and how models are integrated behind a common abstraction (the Model Integration Layer).
- **v2.2 — State, Memory, and Artifact Repository.** State categories and the state event model, the artifact lifecycle, repository structure/admission/promotion/versioning, and working vs. persistent memory.
- **v2.3 — Observability, Telemetry, and Deployment.** Telemetry signal types and event schema, the telemetry event catalog, dashboards/alerting, and deployment profiles, topologies, scaling, HA/DR, and secrets/network/config.

### Execution layer (reference implementation)
- **v3.0 — Reference Implementation Architecture and Repository Layout.** The concrete reference-implementation layers, modules, services, and repository layout.
- **v3.1 — Reference Construction Scenario.** A defined, runnable reference scenario: inputs, target system, minimum platform components, construction pipeline, run record, and success/failure criteria.
- **v3.2 — Integration Components.** The shared component skeleton, agent implementations, the model-interaction protocol, and tool plugins.
- **v3.3 — Runtime Orchestration and the Autonomous Development Loop.** The construction flow and its loop lifecycle, stage gates, orchestration components, scheduling/dispatch, convergence, change-driven re-entry, and local/distributed modes.
- **v3.4 — Autonomous SDLC and Platform Security.** Two halves: the autonomous SDLC (lifecycle phases, phase-gate model, roles, quality model, audit) and the platform security model (trust zones, identity/authorization, data classification, secrets, prompt/context-injection defense, model/agent/tool security, generated-code execution, supply chain, incident response).

### Ecosystem
- **v4.0 — Platform Vision and Ecosystem.** The long-term vision: architecture layers, ecosystem structure, participant roles, value model, interoperability, enterprise adoption, maturity, and risk.

### Human oversight
- **Companion — Human Oversight and Project Management.** The plain-language bridge to a PM: human roles, the project lifecycle, the accountable pairing of the Senior Developer and the AI Coder, costing, and the governance axioms.

### Schemas
- **`isl-r3-common.schema.json`, `isl-r3-language.schema.json`, `isl-r3-canonical.schema.json`, `isl-r3-enterprise.schema.json`.** The machine-readable validators for shared primitives, authored specifications, the canonical model, and enterprise control records.

---

## 3. The message

ISL r3 is not "AI writes everything and humans step aside." Its message is the opposite:

- **Generation is not delivery.** An artifact is a draft until it is deterministically validated and human-governed. The platform automates the mechanical work; the human stays the accountable owner.
- **Traceability is non-negotiable.** Every requirement, task, artifact, and validation outcome is linked and auditable.
- **Governance is built in, not bolted on.** Readiness levels, risk tiers, approval gates, and evidence packages enforce judgment at the right points.
- **The whole corpus is one system.** v0.0 through v4.0, the Companion, and the schemas behave as a unified, enforceable whole — and the Companion makes it runnable by the people who actually run projects.

---

## 4. Reading map — where to go deeper

| If you need… | Read | Read more in |
| --- | --- | --- |
| The governing framework and conformance rules | v0.0 | — |
| The shared vocabulary, normative language, enums, error & telemetry models | v0.1 | v0.0, v2.3 |
| The JSON schemas and validation stages | v0.2 | the four `isl-r3-*.schema.json` |
| Enterprise PM / cost / schedule / portfolio / procurement / approval capacity | v0.3 | Companion §10, `isl-r3-enterprise.schema.json` |
| How to author a specification (the 12 sections, markdown syntax) | v1.0 | v0.1, v1.1 |
| The normalized semantic model and its entities | v1.1 | v0.1, `isl-r3-canonical.schema.json` |
| The phase-by-phase execution of a specification into artifacts | v1.2 | v1.5, v3.3 |
| Readiness levels, gates, waivers, escalation, evidence | v1.3 | v0.3, Companion §4 |
| Enforced traceability and impact analysis | v1.4 | v1.0, v1.1 |
| Construction planning, task graphs, construction boundaries, reuse | v1.5 | v1.2, v3.3 |
| The platform runtime architecture | v2.0 | v3.0 |
| Agent collaboration and model abstraction | v2.1 | v3.2 |
| State, memory, and the artifact repository | v2.2 | v3.0 |
| Telemetry, observability, deployment, HA/DR | v2.3 | v0.1 |
| The concrete reference implementation and repo layout | v3.0 | v2.0 |
| A runnable reference scenario and success criteria | v3.1 | v3.3 |
| Building agents, model-interaction protocol, tool plugins | v3.2 | v2.1 |
| The autonomous development loop and orchestration | v3.3 | v1.2, v1.5 |
| The autonomous SDLC and platform security model | v3.4 | v0.0, v1.3 |
| The platform vision, ecosystem, and adoption | v4.0 | v0.0 |
| Human roles, PM application, and the pairing's costing | Companion | v0.3, v1.3 |
| Machine validation of authored specs / canonical models / enterprise records | the four JSON schemas | v0.2 |

---

*For normative requirements, always cite and read the underlying document; this guide is a map, not a substitute.*
