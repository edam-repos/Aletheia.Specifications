# ALETHEIA Specification Language (ISL) v0.3

# Enterprise Project, Program, and Portfolio Integration

**Status:** Normative (enterprise conformance profiles)
**Release:** r3
**Depends On:** ISL v0.0, ISL v0.1, ISL v0.2, ISL v1.0, ISL v1.1, ISL v1.3, ISL v1.5, ISL v2.0, ISL v3.x
**Companion:** `Companion - Human Oversight and Project Management`
**Document Type:** Cross-Layer Enterprise Project Management Integration Model

---

## 1.0 Scope and Governing Intent

The ISL r3 corpus defines a platform that can interpret a specification and autonomously construct, validate, repair, and prepare a system for deployment. That platform solves the *engineering delivery* problem. It does not, by itself, solve the *enterprise project management* problem — the disciplines a Project Management Office (PMO) uses to govern cost, schedule, resources, benefits, communications, adoption, and the accountability of the humans who oversee the work.

This document is the enterprise project, program, and portfolio (PPM) integration model. It is the bridge between the autonomous platform and the mainstream project-management practice documented in PMI's *PMBOK® Guide*, PRINCE2, and ISO 21500. It closes the gaps a project manager notices when they first meet ISL: where the budget is, what the schedule is tracked against, who will staff the gates, how progress is communicated, how value is realized after go-live, and how the delivered system moves into operations.

### 1.1 Three Responsibility Domains

Every requirement in this document is owned by exactly one domain:

| Domain | What it means | Example |
| --- | --- | --- |
| **Platform** | What conforming ISL r3 tooling MUST or SHOULD enforce, record, or automate | Recording a cost estimate against a task; exposing schedule progress |
| **Specification / System Owner** | What the author of the ISL specification and the owner of the constructed system MUST or SHOULD declare | Declaring a budget objective; naming the service owner |
| **Organization / PMO** | Enterprise practices outside the platform that the organization MUST or SHOULD operate and that the platform integrates with or records | Running portfolio prioritization; building a communications plan |

A conforming Enterprise or Regulated implementation (ISL v0.1 §10) MUST clearly distinguish, in its evidence, which of these three domains satisfies each requirement. A platform MUST NOT claim to implement an Organization/PMO-owned requirement; that would be a conformance failure.

### 1.2 Relationship to the Companion

The companion document `Companion - Human Oversight and Project Management` is the plain-language, project-manager-facing map of this model. It is informative. This document is the normative statement and the authoritative reference. Where they differ, this document governs.

### 1.3 Normative Language and Standards Alignment

This document uses the shared ISL r3 normative language and shared enums from ISL v0.1. It references external practice families — the PMI PMBOK process groups and knowledge areas, the PRINCE2 themes and controls, ISO 21500, and common IT service management (ITSM/ITIL) practice — as *orientation* for how the organization owns each domain. It does not mandate a specific methodology; it defines the control surfaces the platform provides regardless of the methodology chosen. The external references are informational and do not create conformance obligations on the platform beyond what this document states in normative terms.

---

## 2.0 Portfolio and Program Integration

### 2.1 Portfolio Context (Organization / PMO)

ISL r3 governs a single autonomous construction effort (a "system project"). Enterprise PM operates across a portfolio of such efforts. The organization MUST:

* maintain a portfolio intake that records each ISL system project (its specification, system identity, sponsor, objectives, and risk tier);
* prioritize ISL system projects against business value, cost, risk, and strategic alignment, using the shared Risk Tier and the declared objectives as inputs;
* identify cross-project dependencies (systems that must interoperate, share reusable assets, or sequence deployment);
* reconcile shared-resource demand (reviewers, approvers, reusable-asset governance) across concurrent systems.

### 2.2 Platform Support (Platform)

A conforming platform MUST NOT assume it operates in isolation. It MUST support a portfolio view that:

* exposes each system project's identity, spec version, readiness level, risk tier, and progress in a machine-readable form that a portfolio tool can consume;
* records inter-system dependencies declared in the specification (e.g., systems whose interfaces or shared data the constructed system depends on);
* identifies the reusable assets one system exposes for others, so portfolio-level reuse can be governed;
* provides an exportable portfolio inventory (system, owner, state, risk) suitable for intake and demand reporting.

A platform SHOULD allow a specification to reference the portfolio project it belongs to and the programs it participates in, recorded as attributes on the Project entity.

### 2.3 Program Context (Organization / PMO)

When two or more ISL system projects are coordinated under one program, the organization MUST designate a program sponsor and record program-level dependencies, shared milestones, and shared governance. The platform MUST, where program linkage is declared, carry the program identifier in the Project entity and in governance and audit records so that program-level oversight is possible.

---

## 3.0 Cost and Financial Management

### 3.1 Cost Baseline (Specification / System Owner)

The system owner MUST declare a financial budget for autonomous construction in the specification (or link the Project entity to the portfolio financial record). Budget is a declared constraint, not a platform-invented value.

### 3.2 Cost Recording (Platform)

A conforming platform MUST, for each construction task it executes:

* record a task effort estimate derived from the task's scope and from actual execution telemetry;
* accumulate executed effort and cost against the declared budget by task, by Construction Boundary (work package), and for the whole system project;
* expose cost-progress data (planned vs. actual cost) in a machine-readable form for Earned Value Management (EVM) and financial reporting;
* emit a control event when actual cost crosses a configurable threshold relative to budget (e.g., `warn` at 80%, `block`-eligible response at 100%, per policy).

### 3.2.1 Cost Basis (platform-enforced, organization-defined parameters)

A platform MUST NOT record a cost it cannot explain. A conforming platform MUST apply an explicit **cost basis** that converts construction effort into cost, and MUST record that basis with every cost ledger:

| Field | Meaning | Owner |
| --- | --- | --- |
| `costing-method` | How cost is derived: `rate-per-effort-unit` (default), `fixed-task-cost`, or `blended-rate` | Organization supplies; Platform records |
| `effort-unit` | Unit of effort (`hour`, `point`, or `task`) | Organization supplies; Platform records |
| `rate-per-effort-unit` | Currency per effort unit, REQUIRED for `rate-per-effort-unit` and `blended-rate` | Organization supplies |
| `currency` | ISO currency code | Organization supplies |
| `rate-source` | Reference to the approved rate/agreement | Organization supplies |
| `effective-from` | Time the rate takes effect | Organization supplies |

The platform MUST apply the cost basis consistently: `cost = effort × rate-per-effort-unit` for the default method. A cost ledger without a defined, current cost basis MUST be marked incomplete and MUST NOT be used for financial reporting. Where the organization costs effort outside the platform, the platform MUST export the raw effort ledger (§3.2.2) so the organization's costing system can apply its own rates.

### 3.2.2 Cost Ledger Record

A conforming platform MUST produce a **cost ledger** as the authoritative cost record, with one entry per construction task or effort increment. The ledger and its fields (cost-basis, budget, per-entry effort/cost, and totals) MUST conform to the **ISL r3 Enterprise Integration Records Schema** (§15). The totals MUST include planned cost, actual cost, cost variance, and cost performance index (CPI), and MAY include estimate-at-completion (EAC). These are the EVM inputs the organization consumes (§3.3); the platform does not make the funding decision.

Where the organization operates cost accounting separate from the platform, the platform MUST provide an exportable cost ledger (task, boundary, time, effort, cost) so the organization's financial system can reconcile it.

### 3.3 Cost Governance (Organization / PMO)

The organization MUST assign responsibility for budget ownership and financial sign-off, and MUST define the cost-overspend response (funding decision, scope reduction, or risk acceptance) consistent with the shared Governance Decision model. Cost thresholds MAY be enforced by the platform; the funding decision itself is Organizational.

---

## 4.0 Schedule and Time Management

### 4.1 Construction Schedule (Platform)

The task graph in ISL v1.5 defines *precedence*, not *duration*. A conforming platform MUST extend planning to produce a schedule:

* each construction task MUST carry an estimated duration (derived from expected effort and validated against historical telemetry);
* the scheduler MUST produce start/end dates against a calendar, respect task dependencies, and compute the critical path;
* the plan MUST include named milestones (e.g., specification-ready, plan-valid, `Machine-Valid`, `Autonomous-Ready`, build-complete, deployable) aligned to the readiness gates;
* scheduling MUST respect a project calendar (working days, holidays) supplied by the organization.

### 4.2 Schedule Baseline and Progress (Platform)

A conforming platform MUST establish a schedule baseline when construction is authorized at `Autonomous-Ready`, and MUST track:

* actual start/finish per task and per milestone against the baseline;
* schedule variance and a forward estimate (an Earned Schedule wrapper);
* automatic schedule re-baselining only through the governance change-control process (regression, reauthorization) — never silently;
* a schedule-progress export for PMO reporting.

### 4.2.1 Schedule Metrics (defined)

To make schedule progress meaningful, a conforming platform MUST compute and record the following defined metrics. The platform MUST NOT emit a schedule metric it cannot derive from the baseline and actual data.

| Metric | Definition (formula) | Meaning |
| --- | --- | --- |
| Planned Value (PV) | sum of estimated duration of tasks planned through the reporting date | The work scheduled to date, in effort units |
| Earned Value (EV) | sum of estimated duration of tasks actually completed through the reporting date | The work accomplished to date, in effort units |
| Schedule Variance (SV) | `EV − PV` (in effort units or days) | Negative = behind schedule |
| Schedule Performance Index (SPI) | `EV / PV` | < 1.0 = behind schedule |
| Forecast Completion | planned completion shifted by `1 / SPI` applied to remaining plan (Earned Schedule extension) | Projected finish against the baseline calendar |

### 4.2.2 Schedule Baseline Record

A conforming platform MUST produce a **schedule baseline** record containing the construction-plan reference, the project calendar, per-task estimated duration and planned start/end, milestones aligned to the readiness gates, critical path duration, and a progress block with the metrics in §4.2.1. This record MUST conform to the **ISL r3 Enterprise Integration Records Schema** (§15). The platform records and forecasts; it does not commit the schedule to stakeholders (§4.3).

### 4.3 Schedule Governance (Organization / PMO)

The organization MUST own the schedule commitment to external stakeholders relative to a release/launch date. The platform records and forecasts; the organization decides whether to accept slippage, add capacity, or rescope. Milestone dates are Governance-relevant and MUST be recorded in governance state when they change.

---

## 5.0 Resource Management

### 5.1 Human Gate-Staffing (Organization / PMO)

The platform does not need human engineers to build, but it does need humans to staff its gates. The organization MUST maintain a resource and staffing plan covering every governance role (Reviewer, Security Reviewer, Architecture Reviewer, Authorizing Official, Override Authority, Auditor, Operations Approver, Data Steward, Governance Administrator) with named, available, and trained personnel. This is the enterprise RACI for platform governance.

### 5.2 Capacity and Availability (Organization / PMO)

The organization MUST track the availability, capacity, and concurrency of the human gate staff, because a stalled approval stalls the platform. It SHOULD:

* map each named role to availability and a nominal review/approve throughput;
* level demand across concurrent ISL system projects so no single role becomes a bottleneck;
* record, for audit, who was available and assigned when a gate was pending, approved, or escalated.

### 5.3 Approval-Capacity Telemetry (Platform)

A conforming platform MUST record the time a gate spends pending and who acted on it, and MUST expose approval-queue load (number and age of pending gates by role). It SHOULD alert when a pending-gate age exceeds a threshold, so the organization can staff or reprioritize. This is the platform's contribution to bottleneck management — it does not staff the gate; it surfaces the queue. The queue data MUST be emitted as a validated `approval-queue-metrics` record in the Enterprise Integration Records Schema (§15).

---

## 6.0 Communications and Reporting Management

### 6.1 Project Communications Plan (Organization / PMO)

The organization MUST define a communications and reporting plan for each system project: who receives what information, at what cadence, in what form (status report, executive summary, risk/issue digest), and through which channel. This is an Organizational responsibility; the platform provides the underlying data.

### 6.2 Platform Reporting Surfaces (Platform)

A conforming platform MUST provide, in a form the organization can render into its reporting:

* a status report datum set: readiness level, risk tier, gate status, schedule progress, cost progress, open escalations, open waivers/overrides, and telemetry health;
* an exception report (escalations, blocked gates, overspend, schedule slippage, audit write failures);
* an executive summary of system-level state and top decisions requiring attention;
* export compatibility (e.g., JSON/CSV) with corporate reporting and business-intelligence tooling.

These are the raw signals for the plan in §6.1; the platform does not replace the organization's reporting process.

---

## 7.0 Benefits Realization and Value Management

### 7.1 Benefits Plan (Specification / System Owner)

Beyond the measurable Business Objective (`OBJ`), the system owner MUST declare, for each objective, a benefits realization plan: the expected benefit, the measure/KPI, the target, and the realization window after go-live. This turns an objective into an accountable value commitment.

### 7.2 Benefit Tracking (Platform / Organization)

A conforming platform MUST record, at deployment, the linkage between delivered value-bearing scope (which objectives were realized by which delivered artifacts) and the declared benefit plan. Post-deployment benefit measurement is an Organizational and operations activity; the platform MUST preserve the traceability that makes it possible and MUST NOT claim realization it cannot evidence.

### 7.3 Value Reporting (Organization / PMO)

The organization MUST schedule benefit-review checkpoints after go-live and record the outcome against each business objective. The platform's traceability export (§7.2) and the Reuse Model's savings (reused vs. regenerated effort) SHOULD feed value reporting.

---

## 8.0 Organizational Change Management and Adoption

### 8.1 Business Readiness (Organization / PMO)

"The specification is `Autonomous-Ready`" is a *technical* readiness to build. It is not a declaration that the business is ready to receive and adopt the delivered system. The organization MUST manage adoption readiness separately: impact on users and business processes, training, roll-out sequencing, and stakeholder acceptance. This is a distinct readiness from ISL v1.3's construction readiness.

### 8.2 Adoption Handoff (Platform / Organization)

A conforming platform MUST, at deployment, expose an adoption-relevant handoff package: the delivered scope, affected interfaces and workflows, operator roles, and any behavior changes, so the organization can build its training and change-impact plan. The organization owns the adoption plan and its execution.

### 8.3 Naming Avoidance (Clarity)

This document deliberately uses "business readiness" and "adoption" for §8.1 and §8.2 to avoid confusion with ISL v1.3's readiness levels. No readiness value from §8.1 may be written into the Project entity's `readiness-level` field; business adoption status MUST be recorded in a separate, explicitly-labeled record.

---

## 9.0 Service Transition and Operations Handoff

### 9.1 Handoff Package (Platform)

A conforming platform MUST, at the deployment gate, produce a service-transition handoff package that includes: the validated deployment artifacts, the operational runbook for the constructed system, configuration/identity handover, rollback guidance, and a record of assumptions made during construction that operations must honor. This aligns the deployment gate with IT service management (ITSM/ITIL) service-transition practice. The package MUST be emitted as a validated `service-transition-handoff` record in the Enterprise Integration Records Schema (§15).

### 9.2 Production Change and Operation (Organization / PMO)

Following deployment, the constructed system passes to operations ownership. The organization MUST:

* assign a service owner and an operational runbook owner;
* route production changes through the organization's production change-management process (distinct from and downstream of ISL construction change control);
* establish incident, problem, and service-level management per ITSM practice;
* feed operations-derived defects or change requests back through the ISL specification change-control process (§10 of the companion) when they affect the specification.

### 9.3 Operations Access and Evidence (Platform)

A conforming platform MUST expose the audit and evidence records for the deployed system to authorized operations and audit roles, and MUST NOT hand over secrets. The handoff package MUST reference evidence (validation, security, governance) but MUST NOT contain secrets.

---

## 10.0 Third-Party, Procurement, and Supply-Chain Management

### 10.1 Tool and Model Providers (Platform / Specification)

ISL already governs tool and model usage (ISL v2.1, v3.2). This document extends that into procurement discipline. The specification MUST declare, for each external tool, model provider, or dependency, the provider identity, the license/terms governing use, and the support/security posture, recorded as attributes on the relevant Tool or Model record.

### 10.2 Supplier and License Risk (Organization / PMO)

The organization MUST assess, for each external provider: licensing entitlements, data-handling and confidentiality (per the shared Sensitivity Classification), supply-chain and operational risk, and exit/replacement terms. The platform MUST record which providers were used per task so supplier risk and licensing can be audited per system.

### 10.3 Reusable-Asset Procurement (Organization / PMO)

The Reuse Decision (make-vs-buy) in ISL v1.5 is the technology build-vs-reuse decision. Where a reused asset is procured from a vendor rather than internally promoted, the organization MUST apply procurement governance (contracts, indemnity, maintenance) in addition to the platform's reuse fitness assessment.

---

## 11.0 Lessons Learned and Continuous Improvement

### 11.1 Learning Source (Platform)

The immutable audit and governance records, telemetry, escalating/waiver/override history, and schedule/cost variance ISL produces are a rich, evidence-based lessons-learned source. A conforming platform MUST be able to export a per-system and cross-system learning dataset (by system, task type, failure class, repair activity, waiver/override usage) for post-project review.

### 11.2 Review and Feedback Loop (Organization / PMO)

The organization MUST hold lessons-learned reviews at project close and regularly across multiple systems, and MUST feed findings back into: the specification templates, the reusable-asset library, governance configuration, and the organization's methodology. This closes the improvement loop — a PMBOK Closing and general PMO best practice — that ISL would otherwise leave implicit.

---

## 12.0 Approval Capacity and Human Bottleneck Management

### 12.1 Gate Queue Discipline (Platform)

A conforming platform MUST track each approval gate from open to decision and record the pending duration and deciding role. It MUST expose gate-queue load (by role and system project) and SHOULD alert on stale gates and repeated escalations, so the organization can staff accordingly (§5.2).

### 12.2 Authority Delegation and Backup (Organization / PMO)

The organization MUST define named backup/alternates for each critical governance role and a delegation rule consistent with Separation of Duties (ISL v1.3 §3.3), so a single individual's absence does not stall autonomous delivery. Delegation MUST be recorded and auditable, and MUST NOT break separation-of-duties constraints.

### 12.3 Approval SLA (Organization, platform-enforced where configured)

Where the organization defines an approval service-level expectation, the platform MUST be able to enforce it as a policy (e.g., overdue gate becomes `escalate`), consistent with the shared Governance Decision model.

---

## 13.0 Enterprise PM Coverage Matrix

The following matrix maps the mainstream enterprise-PM domains to this document (the authoritative control surface), to ISL r3, and to the responsible domain. It is the machine-readable summary of this integration model.

| Enterprise PM domain | Source standard family | Covered by | Responsible domain |
| --- | --- | --- | --- |
| Governance & stage gates | PRINCE2 Controls, PMBOK phases | ISL v1.3 + this §12 | Platform + Organization |
| Risk management | PMBOK Risk, ISO 21500 | ISL v0.1, v1.3 + this §3 | Platform + Specification |
| Integrated change control | PMBOK | ISL v1.3 + companion §10 | Platform |
| Quality / validation | PMBOK Quality | ISL v1.0, v3.x | Platform |
| Scope & requirements | PMBOK Scope | ISL v1.0, v1.1 | Specification |
| Traceability & audit | accountability practice | ISL v1.4 | Platform |
| Stakeholders / RACI | PMBOK Stakeholder | ISL v0.1, v1.3 + this §5 | Organization |
| **Portfolio / program** | PfMP/MSP, ISO 21504 | **this §2** | Organization |
| **Cost / financial / EVM** | PMBOK Cost | **this §3** | Organization + Platform |
| **Schedule / Earned Schedule** | PMBOK Schedule | **this §4** | Platform + Organization |
| **Resource / capacity** | PMBOK Resource | **this §5** | Organization + Platform |
| **Communications / reporting** | PMBOK Communications | **this §6** | Organization + Platform |
| **Benefits realization** | PMBOK/portfolio value | **this §7** | Organization + Specification |
| **Organizational change management** | OCM (e.g., ADKAR) | **this §8** | Organization |
| **Service transition / operations** | ITSM (ITIL) | **this §9** | Organization + Platform |
| **Procurement / suppliers** | PMBOK Procurement | **this §10** | Organization + Specification |
| **Lessons learned / CI** | PMBOK Closing, ISO 21500 | **this §11** | Organization + Platform |

Bold rows are the enterprise-PM domains newly covered by this document. They were the gaps a project manager would otherwise find when meeting ISL r3.

---

## 14.0 Conformance

### 14.1 Platform Conformance

A platform conforms to this document with respect to the Platform-owned requirements if it:

* supports a portfolio inventory and dependency export (§2.2);
* records task effort/cost against budget and exposes cost-progress and a cost ledger (§3.2);
* produces a schedule with durations, milestones, a calendar, a baseline, and progress tracking (§4.1, §4.2);
* records gate pending-duration, exposes approval-queue load, and supports approval-SLA policy (§5.3, §12.1, §12.3);
* provides status, exception, and executive reporting surfaces and export compatibility (§6.2);
* records benefits-linkage and preserves traceability for value reporting (§7.2);
* produces a service-transition handoff package and exposes operations evidence without secrets (§9.1, §9.3);
* records tool/model provider, license, and per-task usage for supplier audit (§10.1, §10.2);
* exports a lessons-learned dataset (§11.1); and
* keeps business/adoption readiness distinct from ISL construction readiness (§8.3); and
* produces the enterprise records it is required to produce (cost ledger, schedule baseline, approval-queue metrics, service-transition handoff, benefits linkage, portfolio inventory) as validated instances of the **ISL r3 Enterprise Integration Records Schema** (§15).

### 14.2 Specification Conformance

A specification conforms to this document if it declares a budget/portfolio linkage (§2.2, §3.1), a benefits realization plan (§7.1), declared external providers with licensing terms (§10.1), and a program/sponsor linkage when the system belongs to a program (§2.3).

### 14.3 Organizational Conformance

An organization conforms to this document with respect to the Organization/PMO-owned requirements if it operates portfolio prioritization and cross-project dependency management (§2.1, §2.3), a budget ownership and spend-decision process (§3.3), a schedule commitment and slippage/re-scope decision process (§4.3), a gate-staff resource and capacity plan (§5.1, §5.2), a communications and reporting plan (§6.1), benefit-review checkpoints (§7.3), an adoption/OCM plan (§8.1, §8.2), production change and operations ownership (§9.2), supplier and license risk management (§10.2), lessons-learned reviews feeding improvement (§11.2), and named role backups with auditable delegation (§12.2). It MUST evidence this conformance through an **Organizational Conformance Declaration** (§16).

### 14.4 Evidence

A conforming implementation MUST produce evidence that records, for each requirement, which responsibility domain satisfied it, referencing the applicable specification version, system project, and governance/audit records. Evidence must distinguish Platform, Specification/Owner, and Organization satisfaction, per §1.1. Platform-produced enterprise records are evidenced by their validated schema instances (§15); organization-satisfied requirements are evidenced by the conformance declaration (§16).

---

## 15.0 Enterprise Integration Records Schema

The platform-owned records this document introduces (and the structured organizational attestation) MUST be validated instances of the **`isl-r3-enterprise.schema.json`** document, which `$ref`s the common schema (`isl-r3-common.schema.json`) for shared primitives and enums. This provides the data contract that makes the cost, schedule, benefits, handoff, queue, and portfolio records machine-checkable rather than prose-only.

| Record (document-kind) | Validates | Produced by |
| --- | --- | --- |
| `portfolio-inventory` | §2.2 portfolio inventory and dependency export | Platform |
| `cost-ledger` | §3.2 cost basis, budget, per-entry effort/cost, EVM totals | Platform |
| `schedule-baseline` | §4.1/§4.2 calendar, task durations, milestones, critical path, progress metrics | Platform |
| `benefits-plan` | §7.1 benefit objectives, KPIs, targets, realization windows | Specification/owner; Platform records linkage |
| `benefits-realization` | §7.2/§7.3 measured vs. target value at review checkpoints | Organization review, recorded against the plan |
| `service-transition-handoff` | §9.1 deployment handoff package (artifacts, runbook, rollback), no secrets | Platform |
| `approval-queue-metrics` | §5.3/§12.1 gate queue load by role, stale/overdue gates | Platform |
| `coder-handoff` | §17.4/§17.5 blocked-coder handoff (task, AI coder, blocker, human action, effort, outcome) | Platform |
| `org-conformance-declaration` | §16 organizational attestation of domain-satisfied requirements | Organization |

A conforming Enterprise or Regulated implementation MUST ensure every such record validates against this schema before it is used for reporting or evidence. A record that fails validation MUST NOT be presented as authoritative evidence.

---

## 16.0 Organizational Conformance Declaration

Because Organization/PMO-owned practices (§8, §13) cannot be enforced by the platform, they are evidenced by a structured, attestable declaration rather than by platform telemetry. This makes organizational conformance auditable instead of merely asserted.

### 16.1 Declaration Record

A conforming organization MUST produce an **Organizational Conformance Declaration** (a validated `org-conformance-declaration` record in the Enterprise Integration Records Schema) that:

* identifies the attesting authority, their role, the organization, and the program/portfolio context;
* lists each applicable Organization-domain requirement from this document, with the attesting status of `implemented`, `declared`, `waived`, or `not-applicable`;
* cites evidence references for each attestation;
* carries a status (`active`, `superseded`, `revoked`) and, where applicable, an expiry;
* is versioned and immutable after attestation (corrections appended as a new version, per §1.3 of ISL v1.3).

### 16.2 Attestation Discipline

A declaration MUST be signed/attested by an authorized role and dated. A `waived` attestation MUST reference a compensating control consistent with the shared governance model. A declaration MUST be reissued when the organization or its practices change in a way that affects the attested items. The declaration does not exempt the organization from the underlying requirements; it is the auditable record that the organization accepts them and states how it satisfies each.

### 16.3 Evidence

The declaration is itself evidence under §14.4 for the Organization domain. It must be preservable and queryable like any governance/audit record. It does not replace platform evidence for Platform-owned requirements.

---

## 17.0 The Senior Developer and the AI Coder

This section defines the working-level human–AI pairing that agencies and enterprises require: a named, accountable human developer working in conjunction with one or more AI coders. It is distinct from the governance-level oversight in ISL v1.3. Here the human is not a gate approver; they are the accountable owner of a sprint and its deliverables, and the AI coder is the executor under that owner.

### 17.1 Roles

| Role | Responsibility | Accountability |
| --- | --- | --- |
| **Senior Developer / Accountable Coder** | Owns the sprint contract: scope, schedule, definition of done, and outcome. Plans, guides, questions, reviews, tests, validates, and recovers the AI coder's work. | Named accountable owner of each deliverable; answers for the code, schedule, and cost |
| **AI Coder** | Executes assigned tasks under the Senior Developer's direction: generates, refines, and self-checks artifacts within the sprint. | None — the Senior Developer is accountable for the AI coder's output |

A deliverable MUST have a named Senior Developer owner. The AI coder MUST NOT be recorded as the accountable owner of a deliverable.

### 17.2 Accountable Effort (measurable and costed)

The Senior Developer's oversight is a real, measured, budgeted cost — not free and not invisible. A conforming platform MUST:

* record the Senior Developer's effort (planning, guidance, review, questioning, testing, validation, recovery) against the task or sprint in the cost ledger (§3.2), costed at the human's rate-per-effort-unit;
* record the AI coder's cost (generation, compute/usage, tool/model invocation) as a separate cost stream in the same ledger;
* record, for each task, the **producer** (`human`, `ai-coder`, or `paired`) and the **estimated** and **actual** time-to-produce, feeding the schedule baseline (§4.2);
* expose both cost streams and the paired-task durations so the PM can judge whether the budgeted human + AI effort is sufficient for the job.

### 17.3 Sprint Planning Guidance

Sprint planning under the pairing is a defined process. A conforming platform SHOULD support, and the organization MUST operate, the following:

1. scope the sprint from a Construction Boundary / task graph (the sprint's work package);
2. break the work into tasks and assign each to `human`, `ai-coder`, or `paired` (AI generates, human reviews/validates);
3. estimate each task — human effort, AI effort, and duration — and record the estimates;
4. set the definition of done per task from the ISL validation entities;
5. plan oversight checkpoints where the Senior Developer reviews, questions, and tests the AI coder's output;
6. plan the recovery path per task (the blocked-coder handoff, §17.4);
7. track actuals against estimates and feed the next sprint.

### 17.4 Blocked-Coder Handoff (recovery)

When an AI coder cannot proceed — an unknown situation, a missing skill, an ambiguous requirement, or a repeated failure — the platform MUST NOT silently retry indefinitely. It MUST:

* flag the task as blocked and record the blocker (type, description, attempts);
* open a **blocked-coder handoff** to the Senior Developer;
* record the human's action: `unblocked` (guidance/clarification/re-scope, then the AI coder resumes) or `took-over` (the human completes the task directly);
* record the outcome and the human effort spent, in the cost ledger and schedule baseline.

The handoff is a real, expected cost — the human's recovery time is budgeted, not exceptional. This is the mechanism that makes the pairing accountable: when the AI coder cannot go on, a named human takes responsibility and the record shows who actually did the work.

### 17.5 Coder-Handoff Record

Each blocked-coder handoff MUST be recorded as a validated `coder-handoff` record in the Enterprise Integration Records Schema (§15). The record MUST include the task, the AI coder, the blocker, the human action (`unblocked`/`took-over`), the human effort, and the outcome. These records are the auditable evidence that every deliverable has a named human owner and that recovery was performed by a responsible engineer.

### 17.6 Conformance

A platform conforms to this section if it records producer, estimated/actual time-to-produce, both cost streams, and blocked-coder handoffs as validated records. An organization conforms if it assigns a named Senior Developer owner to every deliverable, operates the sprint-planning process, and budgets the human's recovery effort.
