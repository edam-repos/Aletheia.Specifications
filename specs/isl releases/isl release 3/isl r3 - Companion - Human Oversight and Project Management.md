# Human Oversight and Project Management Companion

**Companion to:** ISL Release 3 (r3)
**Audience:** Project managers, program and PMO leads, product owners, engineering leads, and the people who answer for the system
**Status:** Informative — a working guide that maps ISL r3 to mainstream project-management practice, not a normative specification

---

**About this document.** This companion was written by a senior data architect working with an AI teammate, as a joint message to project managers. It is a working guide, not a polished specification, and it will have rough edges — that is fine. Where it points to the normative ISL r3 documents, those are the authority; this is the plain-language map. If a claim here feels too strong, check it against the corpus before you repeat it. We tried not to overstate anything.

---

## Table of Contents

1. Why ISL Is a Sound, Durable Foundation
2. A Familiar Project, With a New Crew
3. Glossary of Terms
4. Your Project Lifecycle in ISL Terms
5. Part One — The Human-Side Responsibilities
   - 5.1 Owning the Specification
   - 5.2 Reviewing Before the Machine Takes Over
   - 5.3 Assigning the Risk Tier
   - 5.4 Authorizing Autonomous Construction
   - 5.5 Granting Waivers and Overrides
   - 5.6 Handling Escalations
   - 5.7 Approving Deployment
   - 5.8 Auditing and Staying Accountable
   - 5.9 Managing Stakeholders and Data
   - 5.10 Reauthorizing After Change
6. Part Two — The Canonical Entities
   - 6.1 How to Read This Table
7. Part Three — The PM Toolbox, Mapped
8. Part Four — Where ISL Does NOT Replace Your PM
   - 8.1 The Enterprise-PM Coverage Matrix
   - 8.2 The Boundaries — What the Platform Will Never Do Alone
   - 8.3 What the PMO Must Add Around ISL — Checklist
9. Part Five — The Working Relationship
10. Part Six — The Senior Developer and the AI Coder
    - 10.1 The pairing in one line
    - 10.2 Accountable effort — the human's time is a real cost
    - 10.3 Measurable time-to-produce — for both human and AI
    - 10.4 A valid, credible, sufficient cost model for both
    - 10.4.1 Baseline Costing Model (illustrative)
    - 10.4.2 The real price of AI coding — commercial services are not free
    - 10.4.3 Maintenance, updates, and the AI coder as a managed dependency
    - 10.5 Clear sprint planning guidance
    - 10.6 Communicating the value — the arguments that hold up
    - 10.7 Why this is credible
11. Governance Axioms
12. Closing
Appendix A — Baseline Costing Model: Full Worked Sample

---

## 1. Why ISL Is a Sound, Durable Foundation

ISL r3 is an enterprise normative resource — a governing specification, not a suggestion. It was built on recognized standards and established practice to guide the preparation of real solutions, applications, resources, and other artifacts that organizations actually deliver and operate. It is not a bet on an unproven paradigm. Underneath the automation sits the same project-management and software-engineering discipline organizations have trusted for decades: governance, risk, quality, scope, traceability, change control. The foundation is not new. Only the crew is.

That is why it is a reasonable base for real work. The practices ISL rests on have delivered systems for a long time, and there is no reason to think they will stop making sense. Adopting ISL does not ask you to abandon what you know. It asks you to keep the foundation you already trust and let the platform do the repetitive work on top of it.

A solution delivered on this base is meant to last. Because the underlying practices are the ones that have already stood the test of time, the systems you deliver today rest on a foundation that should still make sense years from now.

---

## 2. A Familiar Project, With a New Crew

If you manage projects for a living, the first thing to know about ISL r3 is that it is not asking you to abandon anything you already know. The platform described here runs a project the way you do — it just happens to be a project where much of the mechanical delivery work has been handed to a disciplined, automated crew. Everything else about running a project still applies. You are still Initiating, Planning, Executing, Monitoring & Controlling, and Closing (in PMI PMBOK® terms). You still run stage gates. You still hold a risk register. You still get a sign-off before anything reaches production. And you are still the person who answers for the outcome.

This companion is written so you can relate the ISL effort to the standards and good practice you already use — PMI's *PMBOK® Guide* and its process groups and knowledge areas, PRINCE2's themes and controls, ISO 21500's guidance on project and portfolio management, and the everyday PMO tooling you already rely on (project charters, work-breakdown structures, RACI matrices, RAID logs, change control, lessons-learned registers). ISL r3 does not replace your methodology. It plugs into it. The goal of this document is to show you the plug points.

One governing principle, which should feel like common sense to any senior PM: **autonomy is a privilege the organization grants, not a right the platform claims.** The platform does a great deal on its own, but it must pause at every point where the organization has decided a human judgment is required. When it pauses, it is not failing — it is honoring a stage gate. Your job is to be ready to answer.

**A worked example.** To see this companion applied to a concrete project, see the *SCEN-0001* scenario package in the `scenarios` folder — an RFP-style communicable-disease case-management web-app with human-only and AI-assisted delivery baselines, a risk register, a one-page comparison, and a deliverable/acceptance checklist. It is this companion's argument made concrete: the same discipline, the same budget, and the pairing's advantage showing up in completeness and quality rather than speed or cost.

---

## 3. Glossary of Terms

A short reference for the ISL r3 terms used throughout this companion, each defined in plain project-management language.

| Term | What it means |
| --- | --- |
| **ISL r3** | The ALETHEIA Specification Language, release 3 — the corpus of documents that define the platform and its practices. |
| **The platform** | The ISL r3 tooling and runtime that reads the specification and autonomously builds, validates, and prepares the system for deployment. The "crew." |
| **The specification** | The machine-readable project document (charter + scope + requirements) that defines what to build. |
| **Canonical entities** | The structured records the platform creates from the specification (requirements, services, data, policies, etc.) — the platform's project plan. |
| **Readiness level** | The maturity gate of a specification: Draft → Reviewable → Machine-Valid → Autonomous-Ready. |
| **Autonomous-Ready** | The readiness level at which the platform is authorized to build. |
| **Construction Boundary** | A scoped unit of work (a work package / sprint) with entry and exit criteria. |
| **Connection Context** | A controlled handoff of work between two Construction Boundaries. |
| **Construction Task** | A unit of work the platform executes — a task in the plan. |
| **Task graph** | The dependency-ordered plan of construction tasks. |
| **Reusable Asset** | An approved, validated component available for reuse. |
| **Reuse Decision** | The make-vs-buy decision: reuse, wrap, extend, or build new. |
| **Deterministic validation** | Machine-checked verification that generated artifacts are correct (tests, schema checks, security scans). |
| **Traceability** | The link from every output back to the requirement or entity that caused it. |
| **Governance** | The roles, policies, approvals, and controls that regulate the lifecycle. |
| **Approval gate** | A checkpoint where the platform pauses for a human approval. |
| **Risk tier** | The risk rating (`low` / `standard` / `high` / `critical`) that drives control strength. |
| **Waiver** | A controlled, time-limited exception to a policy or validation finding. |
| **Override** | A stronger, exceptional approval to proceed past a failed check. |
| **Escalation** | A controlled pause that hands a decision back to a human. |
| **Deployment authorization** | The human sign-off required before production deployment. |
| **Audit log** | The immutable record of every governance decision. |
| **Telemetry** | The operational data the platform emits (events, metrics, traces). |
| **Cost ledger** | The record of effort and cost (human + AI) per task. |
| **Schedule baseline** | The approved plan of task durations, milestones, and dates. |
| **Blocked-coder handoff** | The recorded handoff when an AI coder cannot proceed and a human takes over. |
| **Senior Developer / Accountable Coder** | The human who owns the sprint and its deliverables. |
| **AI Coder** | The AI executor that generates artifacts under the Senior Developer. |
| **Conformance profile** | The level of compliance an implementation claims (Core / Team / Enterprise / Regulated). |

---

## 4. Your Project Lifecycle in ISL Terms

The ISL lifecycle maps cleanly onto the project lifecycle you already manage. Think of the readiness levels as the phases on your project schedule and the gates between them as your stage-gate reviews:

| Standard Process Group (PMBOK) | Human Activity | ISL Readiness / Gate |
| --- | --- | --- |
| **Initiating** | Authorize the project; identify objectives and scope; secure a charter and a sponsor | Draft specification → **Reviewable** |
| **Planning** | Review for completeness and coherence; confirm scope and requirements | **Reviewable** → **Machine-Valid** (Reviewer sign-off) |
| **Planning / Stakeholder** | Security and architecture review; assign risk tier; prepare evidence | **Machine-Valid** (Security + Architecture Reviewer sign-offs) |
| **Authorizing / Governance** | Formal construction authorization; sponsor accepts risk | **Machine-Valid** → **Autonomous-Ready** |
| **Executing** | Let the crew build; monitor as it generates, validates, and repairs artifacts | Autonomous construction under gates and telemetry |
| **Monitoring & Controlling** | Handle escalations, waivers, approvals, and change; replan on regression | Continuous governance throughout execution |
| **Closing** | Final validation, deployment authorization, and handover | Deployment gate → hand off to operations |

Read the table top to bottom and you are effectively reading a familiar phase-by-phase project plan, with each phase ending in a human decision before the next begins. The genuinely new part — the autonomous platform — sits inside the Executing and Monitoring & Controlling groups, where it does the delivery work under your oversight.

---

## 5. Part One — The Human-Side Responsibilities

These are the decisions and duties that stay with people. In your terms: the stage-gate reviews, sign-offs, and risk decisions on your plan. They are the control points that make autonomous delivery safe enough to use. Each responsibility below is anchored to the standard practice a PMO would recognize.

### 5.1 Owning the Specification (Initiating and Planning)

Everything the platform builds traces back to the specification. Before any code exists, someone must own that document: define the business objectives, set the scope *and what is out of scope*, and make sure the requirements say what the business actually needs. This is your Project Charter and Scope Statement written in machine-readable form. The platform can help you draft and check it, but it cannot know what your organization is trying to achieve. Own it the way you own a business case — it is the foundation everything else rests on.

### 5.2 Reviewing Before the Machine Takes Over (Quality Control / Phase-Gate Review)

Before a specification moves toward autonomous construction, it must pass human review — your version of a phase-gate review before the build phase. Three review roles matter most:

- **The Reviewer** confirms the specification is coherent, the objectives are understandable, the requirements are reviewable, and there are no obvious contradictions — your design review.
- **The Security Reviewer** checks security policy coverage, data protection, and security risk — your security review gate.
- **The Architecture Reviewer** checks architecture, boundaries, interfaces, data model, and operational feasibility — your architecture review gate.

Reviews are not rubber stamps. A blocking finding must be resolved or formally waived before the specification advances. This is your chance to catch problems while they are still cheap to fix — before the platform has generated a single artifact.

### 5.3 Assigning the Risk Tier (Risk Management)

Early in the lifecycle, someone must decide how risky this system is. ISL uses four tiers — `low`, `standard`, `high`, `critical` — which you can read as your risk ratings in the Project Risk Register. The tier drives a lot downstream: how many reviewers are required, how much evidence must be kept, how strict waivers are, and whether deployment needs explicit approval. A system that handles personal data, operates in a regulated domain, or supports critical infrastructure should be rated accordingly. Under-rating risk is how autonomous delivery becomes an incident. Assign a risk owner for each tier decision, just as you would in any risk register.

### 5.4 Authorizing Autonomous Construction (Sponsorship / Initiating Authority)

A key human decision is the construction authorization. A specification does not become `Autonomous-Ready` on its own; an Authorizing Official (your project sponsor or governance board) must explicitly record that the organization accepts the risk of letting the platform build. Treat it with the same seriousness as approving a production deployment, because that is effectively what it is — an end-of-Initiating / project-baseline approval. The authorization applies to one specific version of the specification and nothing else.

### 5.5 Granting Waivers and Overrides (Risk Acceptance / Change Control)

No system is perfect, and sometimes a known issue is acceptable. ISL gives you two tools, and they are not the same thing — both familiar to anyone who runs a RAID log and a change control board (CCB):

- A **waiver** permits a known, documented policy violation or validation finding to proceed under explicit constraints, for a limited time and scope. In your terms: an accepted risk / documented exception with compensating controls and an expiry.
- An **override** is stronger and riskier. It permits progression despite a failed automated check, for exceptional circumstances. In your terms: an approved deviation from a control that the normal path cannot clear — the kind of decision that goes to an elevated review, not routine handling.

Both require an expiry, both must be traceable to the affected target, and both need stricter approval for high- and critical-risk systems. Use them sparingly. A project accumulating waivers and overrides is a project that has stopped trusting its own controls — a signal worth investigating, like a risk register filling with red items.

### 5.6 Handling Escalations (Issue Management)

When the platform cannot safely proceed on its own, it escalates — your issue/escalation process at work. Escalation is a controlled pause that hands a decision back to a human. It happens when repair limits are reached, a policy requires escalation, an approval gate is unsatisfied, a security finding needs human review, or deployment authorization is missing. When you see an escalation you are being asked to make a call: resume, repair, replan, waive, override, halt, or fail. The platform will not proceed until you do. Think of it as an elevated issue item with an owner and a required decision.

### 5.7 Approving Deployment (Closing / Release Approval)

Being ready to deploy is not the same as being authorized to deploy. Even after the platform has built and validated everything, production deployment requires explicit deployment authorization from an Authorizing Official (and, where required, an Operations Approver). The platform must verify that execution completed, validation passed or was waived, blocking policy violations are resolved, traceability checks passed, and risk-tier approvals are satisfied — and then a human still signs off. This is your release / go-live approval, the final gate so that no system reaches production without a person accepting responsibility.

### 5.8 Auditing and Staying Accountable (Monitoring & Controlling / Lessons Learned)

Every approval, waiver, override, escalation, and deployment decision is recorded in an immutable audit log. An Auditor role can review governance records, traceability, evidence, and compliance history — but cannot approve readiness transitions or deployments. This is your accountability and compliance layer: your evidence that the organization followed its own process, and — after the fact — the raw material for lessons-learned sessions. If something goes wrong, the trail tells you exactly who decided what, when, and why.

### 5.9 Managing Stakeholders and Data (Stakeholder Engagement / Data Governance)

Two quieter but important responsibilities round out the human side. Stakeholders with approval authority must be mapped to governance roles — your stakeholder register and RACI — so the right people approve the right things. And a Data Steward reviews data classification, retention, privacy, and data-handling rules — your data-governance and information-security lens. The platform handles data; someone must decide how sensitive it is and how long it may be kept.

### 5.10 Reauthorizing After Change (Integrated Change Control)

Software changes. When a specification changes after it has been authorized, the change may invalidate the readiness level, and the specification must regress and be reauthorized. This is the integrated change control the platform enforces: you cannot quietly slip a change past the gates. A change to a must-have requirement, a security policy, a data classification, an interface contract, or a risk tier triggers governance review and, where required, a fresh construction authorization — your change request / change control board in action. Plan for this in your change-management process, and treat specification changes the way you treat scope changes to a signed baseline: formally, not silently.

---

## 6. Part Two — The Canonical Entities

Where the human side is about decisions, the canonical entities are the machine's managed artifacts — the structured records the platform creates, tracks, and reasons about as it builds. You can think of them as the project plan and its working documents, written in a form the machine can act on. Each has a stable identifier with a type prefix, so you can trace any output back to the entity that caused it — your traceability is built in from the start, not reconstructed at the end.

| Canonical Entity | Prefix | What It Is | Familiar PM Artifact |
| --- | --- | --- | --- |
| **Project** | `PRJ` | Top-level container; records system identity, version, readiness level | Project folder / charter record |
| **Context** | `CTX` | External environment and assumptions the system operates within | Assumptions & constraints register |
| **Objective** | `OBJ` | A measurable business objective | Business case / measurable goals |
| **Scope** | `SCP` | What is in and out of scope | Scope statement (incl. exclusions) |
| **Requirement** | `REQ` | A functional requirement | Requirements document / backlog |
| **Non-Functional Requirement** | `NFR` | A quality requirement (performance, security, availability) | NFR / SLOs in the requirements baseline |
| **Actor** | `ACT` | A person or system that interacts with the system | User / system actor catalog |
| **Stakeholder** | `STK` | Someone with an interest or approval authority | Stakeholder register |
| **Data Entity** | `DAT` | A data structure and its attributes | Data model / data dictionary |
| **Service** | `SVC` | A single-responsibility service boundary | Component / service breakdown |
| **Interface** | `INT` | A contract between services or with the outside | Interface control document (ICD) |
| **Workflow** | `WFL` | A sequence of steps and exception paths | Process flow / use-case walkthrough |
| **Policy** | `POL` | A verifiable rule the system must enforce | Policy / compliance control |
| **Validation** | `VAL` | A criterion the system must satisfy | Acceptance criteria / test plan |
| **Reusable Asset** | `RSA` | An approved, validated asset available for reuse | Reusable component library |
| **Reuse Decision** | `RSD` | A decision to reuse, wrap, extend, or reject an asset | Make-vs-buy decision record |
| **Reuse Fitness Assessment** | `RFA` | An assessment of whether an asset fits | Fit-gap / feasibility assessment |
| **Construction Boundary** | `BND` | A scoped unit of construction with entry/exit criteria | Work package / WBS element |
| **Connection Context** | `CNX` | A controlled handoff between boundaries | Handoff / integration interface |
| **Boundary Continuation Record** | `BCR` | State carried across a boundary | Handoff notes / context transfer |
| **Artifact** | `ART` | A generated output (source, config, schema, test, package) | Project deliverable |

### 6.1 How to Read This Table

The left side is the machine's world — structured records it manages. The right column shows the familiar project artifact each entity corresponds to, so you can relate them to documents you already produce. When you look at a traceability report or an audit log, you are looking at these entities and the links between them. When you approve a gate or grant a waiver, you are making a decision the platform records against these entities — the same way you record a decision in a risk or change register.

Some of the mapping is intuitive:

- Risk tier on the Project entity = the risk rating in your Risk Register.
- Construction authorization at `Autonomous-Ready` = your sponsor's approval at the end of the initiating phase.
- Waivers / overrides linked to Policy or Validation entities = accepted risks and approved deviations in your RAID log.
- Construction Boundaries = work packages; Connection Contexts = handoffs between them; your WBS and integration plan.
- Reuse Decisions (build vs. reuse) = the make-vs-buy analysis your PMO already performs.

---

## 7. Part Three — The PM Toolbox, Mapped

If you want to run your ISL project with tools you already know, here is the mapping of standard PMO practices and PM-BOK/PRINCE2/ISO 21500 concepts onto ISL r3. This is your "plug-in" point — where an existing methodology can govern an autonomous delivery.

| Standard PM Practice | Standard (PMBOK / PRINCE2 / ISO 21500) | How ISL r3 Realizes It |
| --- | --- | --- |
| Project charter & business case | PMBOK Initiating; PRINCE2 Starting Up / Directing a Project | Specification + Project entity carries identity, objectives, scope |
| Work breakdown structure | PMBOK Planning (Scope) | Construction Boundaries as work packages; task graph as the plan |
| Stage-gate / phase-gate reviews | PRINCE2 Controls; PMBOK phase reviews | Readiness gates (Reviewable → Machine-Valid → Autonomous-Ready) |
| Risk register & risk owner | PMBOK Risk Management; ISO 21500 | Risk tier on the Project entity; policy violations recorded |
| Issues & escalations | PMBOK Issue Management; PRINCE2 Issues | Escalations with required decision and required role |
| Change control board | PMBOK Integrated Change Control | Waivers, overrides, and post-change reauthorization |
| Acceptance criteria & QA gates | PMBOK Quality Management | Validation entities and deterministic artifact validation |
| Stakeholder & RACI | PMBOK Stakeholder Management | Stakeholder mapping to governance roles; separation of duties |
| Make-vs-buy decision | PMBOK Procurement Management | Reuse Decision and Reuse Fitness Assessment |
| Audit trail & compliance evidence | PMBOK Monitoring & Controlling | Immutable audit log; evidence packages per readiness level |
| Lessons learned | PMBOK Closing | Governance/audit history feeding post-project review |
| Go-live / release approval | PMBOK Closing; ISO 21500 | Deployment authorization gate |

The three standards named here are the ones a PM is most likely to meet — PMI's *PMBOK® Guide* (process groups and knowledge areas), PRINCE2 (principles, themes, and controls), and ISO 21500 (project, program, and portfolio guidance). A conforming ISL platform is deliberately methodology-agnostic with respect to these: it enforces the gates and records the evidence, and it leaves the choice of which methodology to run on top of those controls to you and your organization.

---

## 8. Part Four — Where ISL Does NOT Replace Your PM

It would be unfair to you, and to the platform, to pretend ISL r3 covers every corner of enterprise PM. It does not — and it should not. The platform is strong at governance, risk, quality, scope, traceability, and change control, because those are the disciplines of control. It is deliberately thin on the disciplines of management — the parts that require business judgment, human capacity, and external accountability. Those are yours.

ISL r3 addresses them in a companion specification, `isl r3 - v0.3 Enterprise Project, Program, and Portfolio Integration`, which closes each gap and assigns explicit ownership to one of three domains: the Platform (what the tooling records/enforces), the Specification / System Owner (what the spec must declare), and the Organization / PMO (what your organization must operate around the platform). The normative detail is in v0.3; what follows is the project-manager-facing summary.

### 8.1 The Enterprise-PM Coverage Matrix

This is the honest scorecard. Bold rows are the domains ISL r3 now covers explicitly — they were the gaps.

| PM domain | Source standard | Who owns it | Where defined |
| --- | --- | --- | --- |
| Governance & stage gates | PRINCE2 Controls, PMBOK phases | Platform + Organization | ISL v1.3 |
| Risk management | PMBOK Risk, ISO 21500 | Platform + Specification | ISL v0.1, v1.3 |
| Integrated change control | PMBOK | Platform | ISL v1.3 |
| Quality / validation | PMBOK Quality | Platform | ISL v1.0, v3.x |
| Scope & requirements | PMBOK Scope | Specification | ISL v1.0, v1.1 |
| Traceability & audit | accountability practice | Platform | ISL v1.4 |
| Stakeholders / RACI | PMBOK Stakeholder | Organization | ISL v0.1, v1.3 + v0.3 §5 |
| **Portfolio / program** | PfMP/MSP, ISO 21504 | Organization | v0.3 §2 |
| **Cost / financial / EVM** | PMBOK Cost | Organization + Platform | v0.3 §3 |
| **Schedule / earned schedule** | PMBOK Schedule | Platform + Organization | v0.3 §4 |
| **Resource / capacity** | PMBOK Resource | Organization + Platform | v0.3 §5 |
| **Communications / reporting** | PMBOK Comms | Organization + Platform | v0.3 §6 |
| **Benefits realization** | PMBOK/portfolio value | Organization + Specification | v0.3 §7 |
| **Organizational change mgmt** | OCM (e.g., ADKAR) | Organization | v0.3 §8 |
| **Service transition / operations** | ITSM (ITIL) | Organization + Platform | v0.3 §9 |
| **Procurement / suppliers** | PMBOK Procurement | Organization + Specification | v0.3 §10 |
| **Lessons learned / continuous improvement** | PMBOK Closing, ISO 21500 | Organization + Platform | v0.3 §11 |

### 8.2 The Boundaries — What the Platform Will Never Do Alone

Be clear with your stakeholders about these. If the platform "can't do X," it is usually because X is genuinely a human/PMO responsibility, not a platform shortcoming:

- **The platform does not decide the budget or approve overspend.** It records cost and alerts; your funding decision does the rest. *(v0.3 §3)*
- **The platform does not commit a launch date to executives.** It forecasts schedule against a baseline; you own the schedule commitment and the slippage/re-scope call. *(v0.3 §4)*
- **The platform does not staff its own gates.** It needs named, available humans to approve; your resource plan supplies them. *(v0.3 §5)*
- **The platform does not write your reports.** It provides status, exception, and executive data surfaces; your communications plan turns them into reporting. *(v0.3 §6)*
- **The platform does not measure realized value.** It preserves the traceability that makes value reporting possible; your benefit-review checkpoints do the measuring. *(v0.3 §7)*
- **The platform does not manage adoption.** `Autonomous-Ready` is technical readiness to build, not business readiness to receive; your organizational change plan handles training, impact, and acceptance. *(v0.3 §8)*
- **The platform does not run production.** It hands over a service-transition package; your operations team owns change, incidents, and service levels from there. *(v0.3 §9)*
- **The platform does not sign vendor contracts.** It records tool/model usage and providers for audit; your procurement function manages licenses and supplier risk. *(v0.3 §10)*
- **The platform does not hold the retrospective.** It can export rich lessons-learned data; you hold the review and feed the findings back. *(v0.3 §11)*

### 8.3 What the PMO Must Add Around ISL — Checklist

To actually run an ISL system project as an enterprise project, put these on your PMO plan:

1. **Portfolio intake** — register each system project, sponsor, objectives, and risk tier; prioritize across the portfolio. *(v0.3 §2)*
2. **Cost model** — declare a budget per system; link the cost ledger to your financial system; define the overspend decision. *(v0.3 §3)*
3. **Schedule wrapper** — accept the platform's forecast; commit milestones (gates) to stakeholders; track slippage. *(v0.3 §4)*
4. **Gate-staffing RACI** — name who fills every governance role, with availability and backups; level demand across projects. *(v0.3 §5, §12)*
5. **Communications plan** — who gets status/exception/executive data, how often, and through what channel. *(v0.3 §6)*
6. **Benefits plan** — KPIs and realization windows per objective; schedule post-go-live benefit reviews. *(v0.3 §7)*
7. **Adoption / OCM plan** — training, change impact, roll-out sequencing, stakeholder acceptance. *(v0.3 §8)*
8. **Operations handoff** — assign service and runbook owners; route production changes through ITSM. *(v0.3 §9)*
9. **Third-party register** — track each tool/model provider, license, and supplier risk. *(v0.3 §10)*
10. **Lessons-learned loop** — hold reviews at close and across systems; feed findings into templates, reusable assets, and governance config. *(v0.3 §11)*

---

## 9. Part Five — The Working Relationship

The relationship between the human side and the canonical entities is a division of labor any PM will recognize:

**The humans decide.** They own the specification, set the objectives and scope, review the work, assign risk, authorize construction, grant exceptions, handle escalations, approve deployment, and audit the result. Their decisions are recorded as governance events and become part of the immutable record — your decisions, your accountability.

**The platform executes.** It normalizes the specification into canonical entities, plans the construction, discovers and reuses proven assets, generates artifacts, validates them deterministically, repairs them within limits, traces every output back to its source, and prepares deployment — all under the governance controls the humans configured.

**The gates are the contract.** Every point where the platform pauses for a human is a gate — your stage gate. The platform enforces the gates; the humans staff them. If a gate is not satisfied, the platform does not proceed — not because it cannot, but because the organization decided it should not.

A few habits will make the relationship work well — and you already practice most of them:

- **Review early and often.** The cheapest problems to fix are the ones caught at review, before the platform has generated anything. Your design and requirements reviews, done early.
- **Rate risk honestly.** The risk tier drives the strength of every control. Under-rating risk is the fastest way to turn autonomy into an incident. Treat it like your risk register — no inflated optimism.
- **Treat waivers and overrides as exceptions, not a workflow.** A steady stream of them means your controls or your specification need attention — like an excess of approved deviations on a change log.
- **Answer escalations promptly.** The platform is waiting on you. A stalled escalation is a stalled project — the same as an unanswered issue on your RAID log.
- **Keep the audit trail clean.** Every decision you make is recorded. Make decisions you are willing to be accountable for — the standard you already hold yourself to at phase-gate reviews.

---

## 10. Part Six — The Senior Developer and the AI Coder: An Accountable, Measurable, Costed Pairing

Agencies and enterprises keep asking for human developers even while accepting AI coding. The reason is not nostalgia — it is accountability. They want a named, responsible engineer who can be held to account for the code, the schedule, and the cost. This section is about how you, as the PM, shape that pairing so it is credible: the human's effort is real and measured, the time to produce artifacts is measurable, the cost of maintaining both the human and the AI coder is valid and sufficient, and sprint planning is clear.

### 10.1 The pairing in one line

**The Senior Developer is the accountable owner of the sprint and its deliverables; the AI Coder is the executor working under that owner.** The human does not merely approve at the end — they plan, guide, question, review, test, validate, and take over when the AI coder is blocked. And every hour of that human effort, and every unit of AI cost, is recorded and budgeted like any other project cost.

This pairing does not speed up the same job — it changes the job. The developer's role shifts from producing to verifying and directing: less writing of boilerplate, more reviewing AI output at volume, decomposing work for the AI, and catching the hard-to-find bugs that generation surfaces (Axiom #4, #6). The PM's job content changes too: the stages and gates are unchanged, but the PM gains new responsibilities — managing the AI coder as a dependency, tightening acceptance and verification, and accounting for new cost and risk lines (Axiom #2). The human is not doing less; they are doing different, higher-leverage work.

### 10.2 Accountable effort — the human's time is a real cost

A key thing to communicate is that the Senior Developer's oversight is not free and not invisible. It is a tracked, budgeted cost stream, exactly like any engineer's time:

- The Senior Developer's review, guidance, questioning, testing, validation, and recovery time is recorded as effort against the task or sprint.
- That effort is costed at the human's rate-per-effort-unit in the cost ledger (ISL v0.3 §3), the same way you cost any developer.
- The human is the named accountable owner of each deliverable — the person who answers for it. That accountability is the thing the enterprise is buying, and it is auditable.

If you do not budget the human's effort, you will under-resource the pairing and the accountability will quietly disappear. Budget it explicitly.

### 10.3 Measurable time-to-produce — for both human and AI

Every artifact or task has a measured duration, whether produced by the human, the AI coder, or the two together. These actuals feed the schedule baseline (ISL v0.3 §4):

- Each task records estimated and actual time-to-produce, and whether the producer was the human, the AI coder, or a paired effort.
- You can therefore compare AI vs. human time-to-produce, validate estimates against actuals, and build a credible forecast from historical telemetry.
- A task that the AI coder produces in minutes but the human must review for hours is a paired task — and its true duration is the sum, which is what you schedule and measure.

Do not schedule the AI coder's generation time as if it were the whole task. The human's review and validation time is part of the task's duration.

### 10.4 A valid, credible, sufficient cost model for both

The pairing has two cost streams, and both belong in the cost ledger (ISL v0.3 §3):

| Cost stream | What it covers | How it is measured |
| --- | --- | --- |
| **Human (Senior Developer)** | Planning, guidance, review, questioning, testing, validation, recovery | Effort × human rate-per-effort-unit |
| **AI Coder** | Generation, compute/usage, tool/model invocation, seat | Usage/compute cost per task or per unit of work |

To judge whether the budgeted effort is sufficient for the job, use the same discipline you use for any estimate:

- Estimate human + AI effort per task at sprint planning.
- Track actuals against estimates in the cost ledger and schedule baseline.
- Compare against historical telemetry for similar tasks (the platform's lessons-learned data, ISL v0.3 §11).
- Hold contingency for the human's recovery time — the blocked-coder handoff is a real, expected cost, not an exception.

A cost model is credible when it names both streams, and sufficient when the budgeted human + AI effort covers the estimated work plus recovery contingency. If the budget only covers the AI coder, the pairing is under-funded and the accountability will fail.

### 10.4.1 Baseline Costing Model (illustrative)

To make the pairing concrete, here is a baseline costing model. The numbers are illustrative — they vary with your region, your cloud or edge setup, and the model you use — but the shape of the model holds. Validate the rates against your own market before you quote them.

**The senior human coder.** Cost a senior developer the way you cost any engineer: base salary × a fully-loaded multiplier (benefits, taxes, overhead), divided by billable hours. A US senior developer typically lands around $100–$135/hour fully loaded; we use $110/hour as the baseline. Western Europe runs $80–120; offshore $30–60 — swap in your local rate.

| Input | Value |
| --- | --- |
| Base salary (US senior SWE) | $140k–$180k/yr |
| Fully-loaded multiplier | 1.3–1.5× |
| Fully-loaded annual | ~$195k–$270k |
| Billable hours/yr | ~1,880 |
| **Fully-loaded hourly** | **$100–$135/hr (baseline $110)** |

**The AI coder.** Two cost streams: a seat/subscription (the "hire") and compute/API usage (the "usage"). At the entry level, a typical coding task consumes roughly 200k input + 50k output tokens, which lands around $1–$3 per task on current frontier models, plus a $10–$20/month seat. **This is the cheapest, mostly-supervised way to use an AI coder** — premium and agentic services price substantially higher (seats up to $200–$600+/mo and metered compute that can reach tens of dollars per task). The full commercial range is in §10.4.2; the point here is that the AI is *not free*, and both streams belong in the ledger (Axiom #3).

| Cost stream | Baseline |
| --- | --- |
| Seat / subscription | $10–$20/month |
| Compute / API per task | ~$1–$3 |

**The combined sprint.** Here is the number that matters. A 10-task, two-week sprint:

| Cost stream | Per task | Per sprint (10 tasks) | Share |
| --- | --- | --- | --- |
| **Human** (review + validate + integrate + recover) | 2 hrs × $110 = $220 | $2,200 | ~98.7% |
| **AI** (generate + compute + seat) | ~$2 + ~$1 seat | ~$30 | ~1.3% |
| **Total** | | ~$2,230 | 100% |

The human is ~99% of the cost — not because the AI is expensive, but because the human does the real work. Verification, integration, and accountability are the deliverable; generation is the cheap part. When someone says "the AI did it in minutes, why pay for a human?", they are looking at the 1.3% and ignoring the 98.7%.

**The maintenance & security reserve.** Add the cost of owning the AI coder over time (§10.4.3): a reserve of ~25–50% on the AI stream (~$7–15 per sprint here) plus 1–2 human re-validation hours per tool/model update, to cover new releases, newly-identified CVEs, provider/terms changes, and behavior drift. It is budgeted contingency held separately from the nominal figures above — so the ~98.7% / ~1.3% split stays the clean comparison while the price still accounts for keeping the tool safe and current.

**The counterfactual.** The model is incomplete without the cost of skipping the human. Defects found in production cost roughly 100× those found in requirements, and rework typically runs 20–40% of project cost. Unverified AI output ships defects, integration failures, and security gaps — which you pay for again, at the expensive end of the lifecycle. The pairing is not "human cost + AI cost"; it is "human cost now" versus "human cost now + rework and incident cost later."

**Caveat.** These are illustrative baselines, not quotes. Rates vary with region, cloud vs. edge, model tier, and task complexity. Validate salary, multiplier, and token rates against your own market before you publish them.

### 10.4.2 The real price of AI coding — commercial services are not free

The baseline above uses a modest assistant seat ($10–$20/mo) and light per-task compute ($1–$3) — the *cheapest* way to use an AI coder. A PM should know the full commercial landscape, because real services range widely and the word "free" is never the actual price.

**Commercially available AI-coding services (indicative price bands, per user per month):**

| Service class | Indicative band | What to expect |
| --- | --- | --- |
| Assistant seat (in-editor assisted coding) | ~$10–$40 | GitHub Copilot Pro/Pro+/Business, Cursor Pro, Windsurf Pro — completion, chat, small autocompletions |
| Premium / heavy assistant | ~$60–$200 | Cursor Ultra-class, Claude Max, larger context and agentic features, heavier usage |
| Agentic / autonomous coding platform | ~$200–$600+ | Devin-class, team/enterprise tiers — autonomous multi-step coding "engineers," priced like a junior-to-mid hire |
| Metered LLM compute (agentic / API) | variable; can reach tens of $ per task | Token consumption for autonomous loops far exceeds light assistant use; heavy agentic coding can burn hundreds of $/month per coder |

**What this means for you:**

- **Introducing one or a few AI coders is not free.** A seat is a recurring cost and compute is metered; together they range from a rounding error to a meaningful budget line, depending on the tool and how much the coder is trusted to do autonomously.
- **Both streams go in the cost ledger** (ISL v0.3 §3, §10.4) exactly like the human's hours — the seat is the "hire," the compute is the "usage." If a cost is not in the ledger, it is unbudgeted risk (Axiom #3).
- **Higher capability usually means higher cost.** The tool that needs the least human supervision is the one that costs the most — which reverses the naive "AI is cheap, so we save money" assumption, and is exactly the trade a PM should name out loud.
- **Prices move and vary** by plan, region, and model. Treat these as indicative ranges and validate against the vendor's current price before quoting. This entire model is illustrative, not a guarantee.

**The open-source alternative.** The commercial landscape above is the hosted, paid path. A second viable path is an **open-source coding model** (e.g., DeepSeek, Qwen2.5-Coder, CodeLlama, StarCoder2), which can be run **on-cloud** (your cloud GPU or a managed inference service) or **self-hosted** (on-prem or a FedRAMP-approved cloud you control) — deployment is a separate choice from the model. There is no per-seat license and the model itself is free — but running it is not: the cost is cloud GPU hosting (or on-prem hardware + ops), which must be shown to compare apples-to-apples with the commercial path, where hosting is bundled into the vendor's seat/compute price. On-cloud, data goes to your chosen cloud provider (not the model vendor's proprietary cloud); self-hosted, data stays fully in-house, the defensible posture for PHI. The tradeoff is quality, and it is narrower than it once was: open-source coding models have closed the gap and can match commercial on common and standard tasks, so the human-verification premium is concentrated on the hardest, most complex tasks — not spread across the whole build. For a sensitive domain, the tooling choice is a **compliance decision as much as a cost decision**, and both paths should be costed and presented as options, not decided by the PM alone.

### 10.4.3 Maintenance, updates, and the AI coder as a managed dependency

The AI coder is a **third-party dependency your contract cannot reach.** It is not a static asset: the tool, the model, and the API change under you, and they carry their own security and commercial risk. Treat it like any other managed supplier — change control, re-validation, security monitoring, continuity, and a reserve in the price.

**The risks that matter:**

| Risk | What a PM must watch | Example |
| --- | --- | --- |
| **Tool / model updates** | A new release or retrained model can change output behavior, break prior assumptions, and force re-validation of earlier work | A model-version bump changes how the coder formats or names generated code; existing builds need regression |
| **Newly-identified security risks** | CVEs and LLM-specific threats emerge after go-live — prompt injection, data exfiltration, supply-chain compromise in the tool or its dependencies | A vulnerability is disclosed in the coding assistant; the vendor's own SBOM now shows a critical CVE in a transitive dependency |
| **Provider commercial / terms change** | Price, licensing, or model deprecation mid-contract; the seat you priced no longer exists | The provider raises the seat price or retires the model tier you pinned |
| **Data-handling policy change** | Terms change on how the tool handles your PII/PHI — a compliance shift for regulated data | The vendor updates processing terms; you must re-confirm PHI handling is still compliant |
| **Behavior drift** | Output quality degrades after an update — fewer, not more, reliable generations | A retrained model becomes less reliable at a specific task than the version you validated |

**Contractual handling (what goes in the agreement):**

- **Pin the version.** Define the tool + model + API version as a contract baseline; treat any change as change control.
- **Require re-validation after updates.** A regression gate: after any tool/model update, the human re-runs the affected validation before the change is absorbed. This is real human hours, priced in.
- **Extend security diligence to the AI tooling.** The offering must keep the AI tool's SBOM, dependency scan, and threat-intel watch current, exactly like the solution's own dependencies (§8). "The model did it" is not an acceptable answer for a vulnerable dependency.
- **Plan continuity / fallback.** Define what happens if the provider deprecates the model, raises price, or fails — the accountable human and an alternative tool are the fallback.
- **The prime stays accountable.** Even though the model is third-party, the offering carries the warranty, SLA, and accountability. A third-party model failure is the offering's problem to resolve, not the agency's.

**Pricing it in (this is not optional):**

- **Reserve on the AI cost stream** — on the order of **25–50%** of the AI stream (in this companion's baseline, roughly **$7–15 per sprint** on a ~$30 AI stream) for tool/model updates, metadata, and re-scoping.
- **Human re-validation hours** — add a small allowance, e.g., **1–2 hours per tool/model update**, to re-run regression on affected work. This is the larger cost, because it is human time.
- **Show it in the price.** State the reserve as a line in the estimate or an explicit contingency in the contract, so the PM can show finance that maintenance and security are budgeted up front rather than discovered mid-delivery.

This is why the AI coder is never "free" (Axiom #3) and why it must be managed like any dependency (Axiom #5): the cost of ownership includes keeping it safe and current — which is human work.

### 10.5 Clear sprint planning guidance

Sprint planning under this pairing is a defined, repeatable process. As the PM, you and the Senior Developer run it like this:

1. Scope the sprint from the Construction Boundary / task graph (the work package for the sprint).
2. Break the work into tasks and assign each to the human, the AI coder, or a paired effort (AI generates, human reviews/validates).
3. Estimate each task — human effort, AI effort, and duration — and record the estimates.
4. Set the definition of done for each task: the validation criteria it must pass (ISL validation entities).
5. Plan the oversight points — where the Senior Developer reviews, questions, and tests the AI coder's output, not just at the end but at defined checkpoints.
6. Plan the recovery path — for each task, who takes over if the AI coder is blocked (unknown situation, missing skill, ambiguous requirement). This is the blocked-coder handoff (ISL v0.3 §17).
7. Track actuals against estimates in the cost ledger and schedule baseline, and adjust the next sprint from what you learn.

The sprint is the Senior Developer's contract. They own its scope, schedule, definition of done, and outcome — and the AI coder executes within it. That is the pairing the enterprise can defend.

### 10.6 Communicating the value — the arguments that hold up

You will be challenged. Customers, developer-organization administrators, and finance will see the AI coder finish in minutes and ask why they are paying for a human and waiting days. The reason is that they are measuring the wrong thing. This subsection gives you the reframe and the arguments that hold up under questioning.

**The reframe: generation is not delivery.** The AI coder's minutes are the drafting phase. The deliverable — a verified, integrated, documented, accountable feature — is the sum of two phases:

| Phase | Who | Time | What it produces |
| --- | --- | --- | --- |
| **Generation** | AI Coder | minutes | A draft: code, tests, and docs that look complete |
| **Verification + Integration + Accountability** | Senior Developer | days | A correct, integrated, defensible feature |

The "days + hours" estimate was never the AI's generation time. It was the estimate for the whole deliverable — and it is accurate. The AI simply does its part fast. The human's part is the necessary cost that turns a draft into something shippable.

**The arguments that hold up:**

1. **Fast output is unverified output — and unverified output is not a deliverable, it is a liability.** The AI produces code, tests, and docs in minutes, but those are claims, not proof. The human's time is the verification that makes them trustworthy: does it actually work, integrate with the rest of the system, pass security, and handle the edge cases the AI did not consider? Without that, the fast output is a defect waiting to happen — and defects cost more later than the human's verification costs now.

2. **The estimate is a commitment, not a speed prediction.** The "days + hours" is the number the team commits to stakeholders for a verifiable result. It is the honest number. The AI's speed is a bonus that shrinks the generation slice, but the verification and integration slice is irreducible. If you report "the AI finished in minutes," you are reporting the wrong number. Report the deliverable time.

3. **The human is not "secondary" — they are the quality and accountability engine.** The AI is the drafting engine; the human is the verification and accountability engine. They are two different primary jobs, not a primary and a backup. Calling the human secondary is like calling the architect or the QA lead secondary to a fast typist. No one questions the architect's cost because the builder builds quickly.

4. **Cost is about the deliverable, not the generation.** The cost of the pairing is the cost to produce a verified, integrated, accountable feature. If you pay only for generation, you get unverified, unintegrated, unaccountable output — and you pay for it again in rework, integration failures, security incidents, and "who is responsible?" disputes. Over the lifecycle, the pairing is cheaper, because it front-loads the verification cost instead of paying it later with interest.

5. **The human is the answer to the enterprise's own demand.** Agencies and enterprises keep asking for human developers while accepting AI coding. The human is not a cost to be minimized — the human is the thing the customer is actually buying: a named, responsible engineer who answers for the code. The AI is the accelerator; the human is the accountability. You cannot have the accountability without the human.

**The analogy that lands:** the AI coder is a very fast typist; the Senior Developer is the editor. A fast typist can produce a full manuscript in minutes, but the manuscript is not a book until an editor verifies it, checks the facts, fixes the logic, ensures it fits the series, and signs their name to it. No publisher pays the typist and skips the editor — because the editor is where the quality and accountability live. The typist's speed is a bonus; the editor's work is the product.

**What to report:** never report "time-to-generation." Report "time-to-verified-deliverable" and show the two-phase breakdown, so the human's phase is visible and measured (the cost ledger and producer tracking in ISL v0.3 §17 make this auditable). When the AI generates in 20 minutes and the human verifies and integrates in two days, show both — the two days is the real cost of the deliverable, and it is the number that matters.

### 10.7 Why this is credible

The pairing produces auditable evidence, not promises: who did what, how long it took, at what cost, and who is accountable. Every deliverable has a named human owner; every task has measured time-to-produce; every cost stream is in the ledger; and every blocked-coder handoff is recorded. That is what turns "AI coding is acceptable" into "AI coding is acceptable under a responsible engineer" — the position agencies and enterprises can actually defend.

---

## 11. Governance Axioms

A short set of self-evident ground rules the rest of this companion works from — principles you can defend to a stakeholder without qualification, because they hold regardless of tooling, budget, or schedule. Together they answer the assumptions a stakeholder is most likely to get wrong: that the pairing changes the project, that the AI is free, that speed shrinks accountability, that the AI coder is a static, maintenance-free asset you do not need to manage or price, and that the human's work is unchanged — that the PM's job and the developer's role stay the same.

**Axiom #1 — Generation is not delivery.** The value of the human–AI pairing is measured by the **completeness and quality of what is actually delivered and verified**, not by speed or cost. A credible project must keep both delivery models feasible — the human-only full delivery and the Senior Developer + AI Coder pairing — and, within the same budget and schedule, the pairing's defensible advantage is a fuller requirement and compliance surface, deeper test and security evidence, and a more complete traceability matrix, not a faster ship date or a cheaper invoice. AI-generated artifacts are **drafts until a human verifies them**; the human remains the accountable owner on the critical path throughout.

This is the principle driving the costing and value sections of this companion (§10 and Appendix A): the model that brings the human's effort into the open and measures the *deliverable* rather than the *generation* is the one that survives a skeptical stakeholder. It is also why a tight fixed-price budget is a feature rather than a flaw — it forces the pairing to show its real advantage where it counts, in the completeness and quality of what is finally handed over.

**Axiom #2 — The PM's process is unchanged; the PM's job content is not.** Introducing the pairing does not change the project lifecycle, the stage gates, or the discipline the organization already trusts — planning, risk, quality, change control, and sign-off all remain. But the PM's job gains real, new content: managing the AI coder as a dependency, tightening acceptance and verification (because generation is cheap, the volume to verify is higher), and accounting for new cost and risk lines (AI compute and seats, a maintenance and security reserve, and AI-quality risk). A proposal that claims a human governance step can simply be removed is a warning sign, not an innovation. ISL relocates the human to the judgment work; it does not delete the step — and it adds new judgment work the PM must be ready to staff. The team that knows how to run a project will find this easy to adopt; the one expecting to run less of it will be disappointed — and that disappointment is the point.

**Axiom #3 — The AI coder is not free; accountable AI costs are real, belong in the ledger, and can be high.** Introducing even one or a few AI coders carries subscriptions and compute that must be budgeted and tracked like any other resource. The commercial landscape ranges from low-cost assistant seats to premium and agentic coding platforms priced well above a seat, and heavier autonomous use can burn substantial compute (see §10.4.2). "Free" is never the actual price: an AI cost that is not in the cost ledger is either unbudgeted risk or unfunded work, and both eventually land on the same accountable owner — the human. When a stakeholder asks "what does the AI coder cost?", the answer is a real line item, not a rounding error.

**Axiom #4 — More generation means more surface for the hardest problems; verification is where the cost and risk hide.** Faster, larger generation changes the defect profile: more code, more integration surface, and more subtle failures — concurrency, security, non-deterministic behavior — that are expensive and genuinely hard to find. The more the AI produces, the more the human's verification, integration, and recovery are the irreducible cost (Axiom #1; the §10.4.1 counterfactual). Speed does not remove the quality function; it increases what the quality function must cover. An estimate that budgets the generation but not the hunting for hidden bugs is not a saving — it is a deferred defect.

**Axiom #5 — The AI coder is a managed third-party dependency, not a static asset.** The tool and the model are a supplier your contract cannot reach directly: versions change, security risks surface after go-live, and pricing and terms can shift mid-project. Treat the AI coder under the same change control, re-validation, security diligence, and continuity planning as any critical third-party dependency — and price its maintenance and security reserve in, not around (see §10.4.3). "The model did it" is never an answer an accountable human can give.

**Axiom #6 — The human role shifts from producing to verifying and directing.** The pairing does not speed up the same job; it changes the job. The developer moves from writing boilerplate to reviewing AI output at volume, decomposing work for the AI, and catching the hard-to-find bugs that generation surfaces (Axiom #4). The PM's value moves to acceptance rigor and dependency management (Axiom #2). The human is not doing less — they are doing different, higher-leverage work. A team that expects to run less of the project will be disappointed; a team that expects to run it differently will be ready.

---

## 12. Closing

ISL r3 does not remove the human from software delivery. It relocates the human to the work that matters most: deciding what to build, judging whether it is good enough, accepting risk, and answering for the result — the very work a project manager is trained and paid to do. The platform is a powerful, disciplined crew. Your job is to run the project — and to run it differently: staff the gates, hold the risk register, manage the changes, own the outcome, and manage the AI coder as the dependency it is (Axiom #2, #5, #6).

Read this companion as the plain-language bridge between your methodology and the ISL corpus. The normative requirements live in the r3 documents, especially ISL v1.3 (Readiness and Governance Model) for the human roles and gates, ISL v0.1 (Common Conventions) for the shared vocabulary, ISL v1.1 (The Canonical Semantic Model) for the canonical entities, and ISL v0.3 (Enterprise Project, Program, and Portfolio Integration) for how the enterprise-PM domains — portfolio/program, cost, schedule, resources, communications, benefits, adoption, service transition, procurement, and lessons learned — are governed and who owns each. Use this document as the map; use the corpus as the authoritative detail.

---

## Appendix A — Baseline Costing Model: Full Worked Sample

This appendix is a complete, reproducible baseline costing model for the Senior Developer + AI Coder pairing. It is illustrative, not a quote. Every number is stated as a named assumption with its basis, so you can audit the model, change any input, and recompute. The goal is a model a PM can defend to finance.

### A.1 The model in one formula

**Total pairing cost per sprint = (human hours × human fully-loaded rate) + (AI tasks × AI cost per task) + (AI seat amortized per sprint)**

Everything below is that formula expanded and worked through.

### A.2 Assumptions — every input, with its basis

| # | Input | Value | Basis / note |
| --- | --- | --- | --- |
| A1 | Senior dev base salary (US) | $150,000/yr | 2024–25 US market midpoint |
| A2 | Fully-loaded multiplier | 1.4× | benefits + taxes + overhead + tools |
| A3 | Fully-loaded annual | $210,000 | A1 × A2 |
| A4 | Billable hours/yr | 1,880 | 2,080 − 200 (PTO / holidays / admin) |
| A5 | Fully-loaded hourly | $111.70 | A3 ÷ A4 |
| A6 | AI seat (e.g., Copilot Business) | $19/mo | vendor list price |
| A7 | AI compute per task | $2.00 | ~200k input + 50k output tokens @ frontier rates |
| A8 | Tasks per sprint | 10 | 2-week sprint |
| A9 | Human hours per task | 2.0 | review + validate + integrate + recover |
| A10 | Sprints per month | 2 | 2-week cadence |

### A.3 Senior human coder — cost build-up

- Fully-loaded annual = $150,000 × 1.4 = **$210,000**
- Fully-loaded hourly = $210,000 ÷ 1,880 = **$111.70**
- Human cost per sprint = 10 tasks × 2.0 hrs × $111.70 = **$2,234**

### A.4 AI coder — cost build-up

- Compute per sprint = 10 tasks × $2.00 = **$20.00**
- Seat per sprint = $19 ÷ 2 = **$9.50**
- AI cost per sprint = $20.00 + $9.50 = **$29.50**

These use the **entry-level, supervised** rates (A6–A7, §10.4.1) — the cheapest way to run an AI coder. Premium and agentic commercial services can price far higher (seats up to $200–$600+/mo and metered compute reaching tens of dollars per task; see §10.4.2). The cost ledger must reflect the tool you actually choose, not the cheapest one, and the "free" label on any AI tool is never the full price.

### A.5 Combined sprint model

| Line | Amount |
| --- | --- |
| Human (Senior Developer) | $2,234.00 |
| AI (compute + seat) | $29.50 |
| **Total per sprint** | **$2,263.50** |
| Human share | 98.7% |
| AI share | 1.3% |

**Ownership reserve (not optional).** On top of these nominal figures, hold an AI maintenance & security reserve for the cost of owning the AI coder over time (§10.4.3): ~25–50% of the AI stream (≈$7–15 per sprint on the $29.50 stream above) plus 1–2 human re-validation hours per tool/model update. It is budgeted contingency, kept separate from the nominal split, so finance sees maintenance and security priced in up front.

### A.6 Monthly and annual projection

| Period | Amount | Basis |
| --- | --- | --- |
| Per sprint | $2,263.50 | A.5 |
| Per month | $4,527.00 | 2 sprints |
| Per year | $54,324.00 | 24 sprints |

This is the pairing cost for **one** Senior Developer + AI Coder on **one** delivery stream. Scale by the number of streams you run.

### A.7 Sensitivity — how the model moves

| Variable | Baseline | Low | High | Effect on total |
| --- | --- | --- | --- | --- |
| Region (hourly rate) | $111.70 (US) | ~$45 (offshore) | ~$135 (US high) | Human line dominates |
| Cloud vs. edge (compute/task) | $2.00 | ~$1.00 | ~$3.00 | Small — AI line is ~1% |
| Model tier | frontier | smaller | frontier+ | Small — AI line is ~1% |
| Task complexity (human hrs/task) | 2.0 | 1.0 | 4.0 | Human line moves most |

The model is robust to the AI-side assumptions because the AI line is ~1% of the total. The number that actually moves the total is the human hourly rate and the human hours per task — which is exactly the point: the human is the real cost, and the real cost is the real work.

### A.8 Auditability — how to reproduce and validate

- **Every input is a named assumption with a basis** (A.2). Nothing is hidden.
- **Change any input and recompute** — the model is transparent and reproducible from the formula in A.1.
- **Validate against your own data:** A1/A2 from payroll and benefits; A6 from your vendor invoice; A7 from your actual token usage; A9 from your historical task telemetry.
- **Compare actuals to baseline:** the platform's cost ledger (ISL v0.3 §3) records real human effort and AI cost per task. Feed those actuals back into this model to refine it sprint over sprint — the baseline becomes a forecast, not a guess.

### A.9 Caveats

- Illustrative, not a quote. Rates vary by region, cloud vs. edge, model tier, and task complexity.
- Does not include: infrastructure, licensing beyond the AI seat, training, or the cost of defects (see §3.1 for the counterfactual).
- The human hours per task (A9) is the average across a sprint; a hard task takes more, a trivial one less.

*PMBOK® is a trademark of the Project Management Institute. PRINCE2® is a registered trademark of AXELOS Limited. This companion references these practices for orientation only; it is not an official PMI, AXELOS, or ISO publication.*
