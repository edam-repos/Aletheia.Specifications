# Project Specifications

**Project:** [name]
**Status:** Draft · Under Review · Approved
**Last updated:** [date]
**Owner:** [Product Owner / PM name]

Produced during Phase 2 (Planning), from the approved `project-charter.md`. Approve this document before Execution begins (`AGENTS-README-FIRST.md` Step 7).

---

## 1. Vision

What this solution does, for whom, and why it matters. One to two paragraphs — the elevator-pitch version of the charter's problem and objectives.

---

## 2. Requirements

Reference or summarize here; keep the detailed, ID-tagged versions in `/docs/requirements/`.

### 2.1 Business Requirements
| ID | Requirement | Business Objective | Priority |
|---|---|---|---|
| REQ-001 | | | |

### 2.2 Functional Requirements
| ID | Requirement | Inputs | Outputs | Behavior / Rules |
|---|---|---|---|---|
| REQ-010 | | | | |

### 2.3 Non-Functional Requirements
| ID | Category | Requirement |
|---|---|---|
| REQ-020 | Security | |
| REQ-021 | Availability | |
| REQ-022 | Reliability | |
| REQ-023 | Scalability | |
| REQ-024 | Maintainability | |
| REQ-025 | Compliance | |
| REQ-026 | Accessibility | |
| REQ-027 | Performance | |
| REQ-028 | Observability | |

---

## 3. User Stories

Full stories live in `/docs/requirements/user-stories/`. List them here for traceability.

| Story ID | Summary | Priority | Linked Requirement(s) |
|---|---|---|---|
| US-001 | | | |

---

## 4. Use Cases

Full use cases live in `/docs/requirements/use-cases/`. List them here for traceability.

| Use Case ID | Name | Actor(s) | Linked Requirement(s) |
|---|---|---|---|
| UC-001 | | | |

---

## 5. Acceptance Criteria

Solution-level acceptance criteria — the conditions under which the project as a whole is considered to meet its objectives. Work-item-level acceptance criteria live on each backlog item.

- [ ]
- [ ]

---

## 6. Validation Requirements

What must be validated, and how, before work is marked DONE (`AGENTS.md` §11–§13).

| Area | Validation Approach | Owner |
|---|---|---|
| Requirement coverage | | |
| Data quality / integrity | | |
| Security | | |
| Performance | | |
| Architecture compliance | | |

---

## 7. Technical Specifications

**If any field below is genuinely undecided, write "Undecided" and log it as a risk in `risks-and-dependencies.md` rather than leaving it blank or guessing.** The agent confirms this section before Execution (`AGENTS.md` §7 Step 6, §10) and does not assume or invent a stack when it is missing.

### 7.1 Languages and Frameworks
| Layer | Language / Framework | Version |
|---|---|---|
| Frontend | | |
| Backend | | |
| Data / ETL | | |
| Infrastructure | | |

### 7.2 Package Manager(s)
-

### 7.3 Commands
| Purpose | Command |
|---|---|
| Install dependencies | |
| Build | |
| Run tests | |
| Lint | |
| Run locally | |

### 7.4 Repository Conventions
- **Branching model:**
- **Commit message style:**
- **Folder layout notes:**
- **Code review requirements:** (beyond `AGENTS.md` §9.6)

### 7.5 Environments and Deployment
| Environment | Platform / Hosting | Notes |
|---|---|---|
| Local / Dev | | |
| Staging | | |
| Production | | |

### 7.6 Dependencies and Integrations
| Dependency / Integration | Purpose | Version / Notes |
|---|---|---|
| | | |

### 7.7 CI/CD
- **Pipeline tool:**
- **Stages run on commit / PR / merge:**
- **Quality gates enforced:** (link to `AGENTS.md` §12)

---

## 8. Open Questions and Assumptions

Anything above that was assumed rather than confirmed. Move resolved items to `decisions-log.md` once answered.

| Item | Assumption Made | Needs Confirmation From | Status |
|---|---|---|---|
| | | | Open |