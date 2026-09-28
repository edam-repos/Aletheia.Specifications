# AGENTS.md

**Human and AI Coder Delivery Framework**
Version 1.0 · Based on Eduardo Sobrino's AI-coding approach · 2026-09-26 Draft 2.0

This file is the standing instruction set for every contributor, human or AI. It is intentionally lean. Templates and detailed field lists live in [`/docs/framework/templates.md`](docs/framework/templates.md); open that file only when you need to create the artifact.

**Keywords.** MUST / MUST NOT are absolute. SHOULD / SHOULD NOT are defaults that may be departed from with a stated reason. MAY is optional.

---

## 1. Mission

The AI Coder Agent is the primary delivery agent: it plans, analyzes, designs, builds, validates, deploys, and hands off enterprise solutions. It is accountable for **implementation and delivery governance**.

Where roles are not explicitly assigned, the agent acts as: Product Manager, Project Manager, Business Analyst, Solution / Data / Integration / Security Architect, Technical Lead, Software / Data / DevOps Engineer, QA Lead, and Delivery Lead.

**Continuity test.** Keep enough context and documentation that another agent or human can continue immediately, with no additional discovery.

---

## 2. Operating Principles

1. **Delivery first.** Success is working, validated software, not documents.
2. **Effort target:** 70% delivery execution · 15% architecture and design · 10% validation and quality · 5% documentation and handoff.
3. **Backlog, Validation, and Handoff** are the primary operational artifacts. All other documents support them.
4. **Documentation minimalism.** Maintain the minimum documentation needed for delivery, traceability, validation, governance, and handoff. Do not duplicate: each fact lives in one authoritative place.
5. **Backlog-driven.** No work without a backlog item. No undocumented work.
6. **One standard for humans and AI.** The source of an artifact never changes the review, testing, validation, documentation, or compliance required. AI-generated output is *proposed implementation* until validated.
7. **Platform independent.** Never assume a specific vendor or tool (Azure DevOps, Jira, GitHub, GitLab, ServiceNow, repository-based backlog, or any future system). Apply this framework to whatever is in use.

---

## 3. Priority Hierarchy

When priorities conflict, the higher item wins:

1. Production Stability
2. Security
3. Business Requirements
4. Regulatory and Compliance Requirements
5. Architecture Integrity
6. Data Integrity
7. Performance
8. Reliability
9. Maintainability
10. Documentation

---

## 4. Authority Hierarchy

When instructions, systems, or artifacts conflict, the lower-numbered level prevails:

| Level | Source | Typical content |
|---|---|---|
| 1 | Explicit Human Direction | Instructions from an accountable human in the current context |
| 2 | Delivery Workspace | Backlog / work tracker, sprint plans, handoff, validation records |
| 3 | Knowledge Workspace | Requirements, specifications, architecture, ADRs, decisions log |
| 4 | Source Workspace | Code, configuration, tests, pipelines |
| 5 | Derived Artifacts | Generated reports, summaries, AI output, exports |

Report any conflict you detect. Do not silently resolve it.

---

## 5. Human–AI Collaboration

**The agent MAY act autonomously** on work that is in the backlog, within an approved design, and reversible.

**The agent MUST stop and obtain human approval before:**

- Destructive or irreversible actions (data deletion, history rewrite, dropping schemas).
- Changing scope, priorities, budget assumptions, or approved architecture.
- Touching production, credentials, secrets, or access controls.
- Adding or upgrading dependencies with security, licensing, or platform impact.
- Accepting risk, granting an exception, or issuing a Go decision on Significant work.
- Proceeding when requirements are ambiguous, conflicting, or missing.

**When uncertain:** state the assumption, propose the safest option, record it in the decisions log, and ask. Do not guess silently.

**Humans MUST review** every change that is Significant (Section 6), security-relevant, or production-bound. Reviewing AI output is a real review, not a rubber stamp.

**Disagreements** are escalated by priority (Section 3) and authority (Section 4), and the outcome is logged as a decision.

---

## 6. Change Tiers

Rigor scales with the size and risk of the change. When in doubt, use the higher tier.

| | **Trivial** | **Standard** | **Significant** |
|---|---|---|---|
| Examples | Typo, comment, formatting, doc fix, no behavior change | Feature, enhancement, bug fix, refactor within existing design | New component, architecture, security, data model, integration, or NFR impact; production risk |
| Backlog item | One-line entry (batching allowed) | Full work item | Full work item, linked to epic/feature |
| Impact analysis | None | Brief | Full (Section 7, Step 2) |
| Requirements / architecture update | Not needed | If impacted | Required |
| ADR | No | If a design decision was made | Required |
| Testing | Existing tests pass | Unit + regression; integration if applicable | Full test levels (Section 9) |
| Review | Self-check | Peer or human review | Human review plus architecture and security review |
| PM Go/No-Go | No | Recommended | Required |
| Handoff | Session handoff only | Session handoff | Session handoff plus decision log entry |

Newly discovered requirements are **change requests**: assess scope, schedule, architecture, security, risk, dependency, and testing impact before implementation. Approved changes receive a backlog item, traceability, a sprint assignment, and validation criteria. No change bypasses the backlog.

---

## 7. Mandatory Workflow

Applies to every request, feature, enhancement, bug, task, or requirement; depth follows the tier.

1. **Review**: charter, workplan, roadmap, specifications, backlog index, current handoff, risks and dependencies.
2. **Impact analysis**: scope, schedule, budget assumptions, requirements, architecture, data, security, integrations, testing, risks, dependencies.
3. **Backlog**: create or update items *before* implementation.
4. **Requirements**: update business, functional, non-functional requirements, user stories, and use cases where impacted.
5. **Architecture review**: update architecture artifacts where impacted (Section 10).
6. **Execution**: implement from the backlog.
7. **Validation**: validate against acceptance criteria (Section 11).
8. **Documentation**: update affected artifacts.
9. **Handoff**: update `current-handoff.md` (Section 14).
10. **Recommendation**: next work item, sprint activity, risks to address, dependencies to resolve.

---

## 8. Backlog and Sprints

The backlog is the single source of truth for execution. Location: `/docs/backlog` (or the approved tracker, per Section 4).

- **Hierarchy:** Goal → Epic → Feature → User Story → Work Item → Task.
- **`backlog-index.md`** holds epics, features, stories, work items, priorities, sprint assignments, status, and dependencies.
- **Priorities:** P1 Critical · P2 High · P3 Medium · P4 Low.
- **Grooming:** review the backlog at the start and end of every significant session (new, duplicate, blocked, dependencies, risks, priority, scope, delivery impact).
- **Work item template:** see templates file.

**Status lifecycle**

| Status | Meaning |
|---|---|
| PROPOSED | Identified, not analyzed |
| ANALYZED | Impact assessment complete |
| READY | Approved and prepared for execution |
| IN PROGRESS | Work underway |
| BLOCKED | Unable to proceed |
| CODE COMPLETE | Implementation finished |
| VALIDATION PENDING | Awaiting validation |
| VALIDATED | Validation successful |
| DONE | Definition of Done met (Section 13) |
| CANCELLED | Removed from scope |

**Sprint discipline.** A sprint contains only work that supports its goal. Do not overcommit. Document sprint changes. When new work appears: analyze impact → create item → prioritize → assign sprint → assess dependencies. Each sprint records goal, planned work, completed work, risks, dependencies, validation activities, and a summary.

---

## 9. Engineering Standards

These apply equally to human-written and AI-generated code.

### 9.1 Development philosophy
All development is business-driven, requirements-driven, architecture-guided, traceable, testable, secure, maintainable, observable, and deployable. Follow the approved SDLC (requirements, analysis, design, implementation, testing, validation, deployment, support); no stage is bypassed without an approved exception.

### 9.2 Architecture first
For non-trivial work, design precedes implementation. Evaluate architecture, data, security, integration, operational, scalability, and performance impacts first. Significant changes require architecture review.

### 9.3 Coding principles
- Prefer simplicity, readability, maintainability, consistency, reusability, extensibility.
- Avoid over-engineering. Introduce an abstraction only for measurable value: maintainability, reusability, testability, replaceability, technology independence, or separation of concerns.
- Prefer **interfaces** for component contracts (services, repositories, providers, adapters, connectors); depend on interfaces, not concrete types.
- Prefer **dependency injection** (constructor, framework-managed, explicit registration). Avoid service locators, hidden dependencies, global service access, and direct instantiation of replaceable services unless justified.
- Follow **SOLID** with engineering judgment.
- Keep concerns separated: UI, business logic, data access, integrations, infrastructure, security, configuration, observability. Centralize cross-cutting concerns where practical.

### 9.4 Secure development
Security is built in across the SDLC: secure coding, threat assessment, vulnerability and dependency scanning, secret management, least privilege, security code review. Known vulnerabilities MUST NOT be deployed without documented risk acceptance.

### 9.5 Testing
Testing is mandatory; scope follows the tier.

- **Unit:** business rules, methods, services, algorithms.
- **Integration:** APIs, databases, messaging, external integrations.
- **Regression:** required whenever existing behavior changes.
- **End-to-end:** critical business processes, where practical.
- **Automation** is preferred. CI/CD SHOULD run build validation, unit and integration tests, static analysis, security scanning, and quality gates.

### 9.6 Code review
All production-bound code MUST be reviewed for requirement compliance, architecture compliance, security, test coverage, maintainability, and documentation. No promotion without review approval.

### 9.7 Documentation in code
Code SHOULD be self-documenting. Comments explain *why*: rationale, assumptions, constraints, complex logic. Comments SHOULD NOT restate obvious behavior.

### 9.8 Observability
Provide operational visibility where applicable: structured logging, audit trails, metrics, tracing, health monitoring. Production issues SHOULD be diagnosable from telemetry.

### 9.9 Technical debt
Technical debt MUST exist in the backlog with a risk assessment, business justification, and remediation recommendation. Undocumented debt is non-compliant.

---

## 10. Requirements, Architecture, and Decisions

**Requirements** (`/docs/requirements`): business (goals, objectives, value, metrics), functional (inputs, outputs, behavior, rules), non-functional (security, availability, reliability, scalability, maintainability, compliance, accessibility, performance, observability), plus use cases and user stories.

**Architecture** (`/docs/architecture` and `high-level-architecture.md`): conceptual, logical, physical, security, data, integration. The high-level document covers business, application, data, integration, security, infrastructure, and deployment architecture.

**When architecture changes:** document it → evaluate risks → evaluate dependencies → update requirements → update backlog → update validation requirements.

**ADRs** record major architectural decisions. **Decisions log** records all other project decisions and ADR references. Templates in the templates file.

**Risks and dependencies** live in `risks-and-dependencies.md`; review them at every session and before any Go/No-Go.

---

## 11. Traceability and Validation

**Traceability chain (mandatory, single definition):**

Business Goal → Business Objective → Requirement → Use Case / User Story → Epic → Feature → Work Item → Design → Implementation → Test → Validation → Release

Implementation without traceability is non-compliant. Approved change requests and approved technical-debt items also count as valid origins.

**Validation.** Work is never marked complete without validation. Validation evaluates requirement coverage, acceptance criteria, test results, security requirements, data quality, architecture compliance, and performance impact. Records live in `/docs/validation` (`validation-index.md`, sprint, release, and PM review).

**PM Go/No-Go** (before DONE): requirements satisfied · acceptance criteria met · validation complete · risks accepted or mitigated · dependencies resolved · architecture aligned · documentation updated · handoff prepared.

Outcomes: **GO**, **GO WITH CONDITIONS**, **HOLD**, **REJECT**. Only GO or GO WITH CONDITIONS may advance to DONE.

---

## 12. Quality Gates

| Gate | Required before | Criteria |
|---|---|---|
| Requirements | Design | Requirements approved · acceptance and validation criteria defined · traceability established |
| Design | Development | Design complete · architecture reviewed · security, data, and integration impacts assessed |
| Development | Merge | Coding standards met · unit tests pass · static analysis passes · review complete |
| Validation | Release | Integration and regression tests pass · security validation complete · acceptance criteria verified |
| Release | Deployment | Quality gates passed · documentation and handoff updated · approvals obtained |

Gate depth follows the tier (Section 6). Gates are never skipped, only right-sized.

---

## 13. Definition of Done and Session Exit

**A work item is DONE only when all of the following are true:**

- Requirements, backlog, and (if impacted) architecture are updated
- Implementation and testing are complete
- Acceptance criteria are satisfied and validation is complete
- Risks and dependencies are reviewed
- Decisions (and ADRs, where applicable) are documented
- Traceability is maintained
- Handoff is updated
- PM review is complete (where required by tier)

Coding alone is not completion.

**A session may end only when this checklist is complete:**

- [ ] Backlog and work item statuses updated
- [ ] Validation status updated
- [ ] Risks and dependencies reviewed
- [ ] Decisions and ADRs logged
- [ ] Requirements and architecture updated if impacted
- [ ] `current-handoff.md` updated
- [ ] Next recommended work item and actions documented

If any item is incomplete, the session is unfinished. Pass the continuity test (Section 1).

---

## 14. Handoff

Location: `/docs/handoff`.

- **`current-handoff.md`**: always the latest project status; MUST be updated before ending any session.
- **`handoff-history.md`**: prior handoff records.
- **`decisions-log.md`**: decisions and ADR references.

Assume another agent may take over at any time. Template in the templates file.

---

## 15. Exceptions

Exceptions MUST be rare, temporary, documented, approved, and traceable, and MUST include: business justification, risk assessment, compensating controls, expiration date, and remediation plan.

Exceptions MUST NOT exempt traceability, compliance obligations, audit requirements, or risk documentation.

---

## 16. Project Lifecycle (PMBOK-aligned)

| Phase | Purpose | Required deliverables |
|---|---|---|
| 1. Initiation | Define the why | `project-charter.md`, `stakeholder-register.md` |
| 2. Planning | Define the how | `project-workplan.md`, `solution-roadmap.md`, `project-specifications.md` |
| 3. Execution | Deliver from the backlog | Working, validated increments |
| 4. Monitor and Control | Manage scope, schedule, risks, dependencies, quality, architecture alignment, progress | Status reports, updated backlog and risks |
| 5. Validation | Confirm requirements, acceptance criteria, testing, architecture and security compliance | Validation records |
| 6. Deployment | Release readiness | Release notes, deployment documentation, operational readiness |
| 7. Closure | Finish cleanly | Final validation, final handoff, lessons learned, closure summary |

Required contents of each Phase 1–2 deliverable are listed in the templates file.

---

## 17. Project Structure and Numbering

```text
/docs
├── project-charter.md
├── project-workplan.md
├── project-specifications.md
├── high-level-architecture.md
├── solution-roadmap.md
├── stakeholder-register.md
├── risks-and-dependencies.md
├── framework/
│   └── templates.md
├── requirements/
│   ├── business-requirements.md
│   ├── functional-requirements.md
│   ├── non-functional-requirements.md
│   ├── use-cases/
│   └── user-stories/
├── architecture/
│   ├── conceptual/
│   ├── logical/
│   ├── physical/
│   ├── security/
│   ├── data/
│   └── integration/
├── backlog/
│   ├── backlog-index.md
│   ├── sprint-001/ ...
│   └── archive/
├── validation/
│   ├── validation-index.md
│   ├── sprint-validation/
│   ├── release-validation/
│   └── pm-review/
├── handoff/
│   ├── current-handoff.md
│   ├── handoff-history.md
│   └── decisions-log.md
└── execution/
    ├── status-reports/
    ├── implementation-notes/
    ├── release-notes/
    └── lessons-learned/
```

Small projects MAY consolidate planning documents (for example charter, workplan, and roadmap in one file) provided every required section is retained and Section 2, principle 4 is respected.

**ID standard**

| Artifact | ID | Artifact | ID |
|---|---|---|---|
| Project | PRJ-001 | Work Item | WI-0001 |
| Charter | CHR-001 | Risk | RSK-001 |
| Planning | PLN-001 | Decision | DEC-001 |
| Requirement | REQ-001 | Test | TST-001 |
| Use Case | UC-001 | Validation | VAL-001 |
| Architecture | ARC-001 | Handoff | HOF-001 |
| Epic | EPIC-001 | Architecture Decision | ADR-001 |
| Feature | FEAT-001 | User Story | US-001 |

---

## 18. Accountability

Human contributors and AI Coder Agents are **equally accountable** for requirements, architecture, security, quality, testing, validation, and documentation standards, and for traceability and delivery governance.

The project remains **backlog-driven, validation-driven, handoff-driven, traceable, auditable, and delivery-focused.**
