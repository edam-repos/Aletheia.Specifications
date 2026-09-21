# SCEN-0001 — Human-Only Delivery Baseline (End-to-End)

| | |
| --- | --- |
| **Scenario** | SCEN-0001 — Communicable-Disease Case-Management Web-App: Person Intake, Case Initiation, and Triage Reporting (Initial Prototype) |
| **Baseline type** | **Human-only** — full delivery by a single senior developer using standard generic IDE tooling. **No AI.** |
| **Status** | Baseline for cost/schedule comparison against the AI-assisted variant |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Purpose** | The control artifact. The AI-assisted run is measured, task-for-task, against this baseline. |

---

## 1. Purpose — what this baseline is for

This document describes the **mandatory-core, end-to-end, human-only delivery** of the SCEN-0001 prototype — every task, from project initiation through deployment and acceptance — as if developed by **one senior developer using standard generic IDE scaffolding, with no AI assistance.** (The scope boundary — what is and is not costed — is defined in §2.)

It is written the way a project manager budgets and schedules a delivery: a complete work-breakdown, a requirement-to-effort traceability, a definition of done, a schedule, and a cost. It is the **control** the AI-assisted variant will be measured against, task-for-task, so the comparison is fair and audit-able.

## 2. What "done" means — the end-state definition

A **fully working, documented, tested, and validated** deliverable of the **mandatory core**:

- A Blazor WebAssembly + ASP.NET Core Web API + SQL Server application that satisfies the **mandatory core** of the scenario — F1–F12 functional and NFR1–NFR7 non-functional requirements, plus the baseline evidence required for acceptance (role-based access and audit, deterministic validation, core automated tests, core documentation, deployment/CI-CD, SBOM + dependency scan, OWASP review, and the traceability matrix).
- Deployed and validated in a target environment; CI/CD in place; acceptance signed off against the requirement set.
- Documentation delivered: architecture, API, technical reference, and a user guide.

**Scope boundary — what this baseline costs (and what it does not).** This baseline prices the **mandatory core** defined in the RFP (§18). It does **not** include the RFP's **priced options**: full FHIR/exchange implementation (IR-5), an independent third-party penetration test (QA-3), full Section 508 remediation (AR-1), a full training program (TD-1/2), extended warranty, or **infrastructure/hosting** (prototype ~$100–$300/mo; FedRAMP-authorized production ~$500–$1,500/mo). Those are priced separately. This is why the baseline's nominal cost (234 hrs ≈ $25,740) is for the mandatory core only — and why a conforming bid must value-engineer to ≈173 hrs to land under the $19,000 ceiling (see §11).

## 3. Baseline assumptions (the premises)

| # | Assumption |
| --- | --- |
| A1 | **One senior developer, full-time**, on the project. |
| A2 | Uses **standard generic IDE scaffolding** (Visual Studio project templates, EF Core reverse-engineering, scaffolding wizards). **Not AI.** Not hand-written boilerplate. |
| A3 | The existing **Eci.DiseaseSurveillance schema is reused as-is** (data layer is mapped, not redesigned). |
| A4 | Fully-loaded human cost rate: **$110/hour** (companion Appendix A baseline). |
| A5 | Productive capacity: **~35 hours/week** after meetings/admin. |
| A6 | No external blockers: schema is stable, tools are available, requirements are clear. |

### 3.1 The scaffolding premise (what "generic, not tailored" means)

A human developer does not hand-write every line of boilerplate. They start from **generic IDE templates** — the project template, the EF Core reverse-engineering of the schema, the scaffolded CRUD controllers. These templates give a *generic* shape; the developer then does the real work of reworking that shape into the actual requirements (the triage questionnaire, reporting criteria, RBAC policy, audit rules).

This is the correct baseline, and it is the honest control. When the AI-assisted variant is measured later, its advantage is expected to be **scenario-tailored scaffolding** — generation aligned to this schema and workflow from the start — not merely "faster boilerplate."

## 4. End-to-end Work Breakdown (PM framing)

The work is organized into the phases a PM recognizes. Each task is measurable, maps to requirements, and is the level at which the AI run will be compared.

### Phase 1 — Setup & Architecture (PM: Planning) — 16 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T1 — Solution scaffolding from generic VS templates (Web API + WASM + solution structure) | 8 | Build foundation |
| T2 — Architecture: layering, DI, DTO strategy, structured logging base (Serilog) | 8 | Build foundation |

### Phase 2 — Data Layer (PM: Planning/Executing) — 24 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T3 — EF Core reverse-engineering of Entity + Surveillance schema | 8 | Data |
| T4 — Repository + service layer (Person, Case_Report, Contact_Report, Assessment) | 12 | Data, NFR4 |
| T5 — Migrations, seed code/reference data, PHI/PII tagging baseline | 4 | NFR1 |

### Phase 3 — Back-end Web API (PM: Executing) — 40 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T6 — Person endpoints (register, search/dedupe) | 8 | F1, F2 |
| T7 — Assessment/questionnaire endpoints (retrieve instrument, submit answers) | 8 | F3, F4 |
| T8 — Case endpoints (initiate, severity/disposition, mask-separate flag) | 8 | F5, F6, F7 |
| T9 — Contact-report endpoint | 4 | F9 |
| T10 — Case-report + export endpoint | 8 | F8, F10 |
| T11 — Reference-data + reporting-criteria endpoints | 4 | F11 |

### Phase 4 — Validation & Configurability (PM: Executing) — 16 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T12 — FluentValidation rules (date ranges, required codes, dedupe rule) | 8 | NFR3 |
| T13 — Configurable triage instrument + reporting criteria | 8 | NFR6 |

### Phase 5 — Authentication / RBAC / Audit (PM: Executing) — 18 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T14 — JWT auth + role-based access (triage staff / surveillance officer / admin) | 10 | NFR2 |
| T15 — Audit trail (person create, case initiate, report submit) | 8 | F12, NFR4, NFR7 |

### Phase 6 — Front-end Blazor WebAssembly (PM: Executing) — 52 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T16 — App shell, layout, MudBlazor, auth-ware, responsive scaffold | 8 | NFR5 |
| T17 — Person registration + search/dedupe page | 10 | F1, F2 |
| T18 — Triage questionnaire + assessment capture UI | 12 | F3, F4 |
| T19 — Case page (initiate, severity, disposition, mask-separate) | 10 | F5, F6, F7 |
| T20 — Case-report review + export page | 8 | F8, F10 |
| T21 — Admin page (reference data, config) | 4 | F11, NFR6 |

### Phase 7 — Testing (PM: Monitoring & Controlling) — 26 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T22 — Unit tests (services, validation, dedupe logic) | 10 | NFR3, NFR4 |
| T23 — Integration tests (EF + API) | 8 | NFR3 |
| T24 — Blazor component tests (bUnit) | 8 | NFR3 |

### Phase 8 — Documentation (PM: Closing preparedness) — 24 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T25 — Architecture / design documentation | 8 | Deliverable |
| T26 — API documentation (OpenAPI/Swagger + endpoint reference) | 6 | Deliverable |
| T27 — Technical reference (data model, configuration) | 5 | Deliverable |
| T28 — User guide (triage staff workflow) | 5 | Deliverable |

### Phase 9 — Deployment & Acceptance (PM: Closing) — 18 hrs
| Task | Est. | Maps to |
| --- | --- | --- |
| T29 — CI/CD pipeline (GitHub Actions / Azure DevOps) + Docker | 8 | Deploy |
| T30 — Environment setup + deployment | 4 | Deploy |
| T31 — End-to-end acceptance against requirements + defect fixes + sign-off | 6 | All |

*Note — mandatory-core compliance evidence.* The SBOM + dependency scan (SEC-8), OWASP review (SEC-7), and the traceability matrix (§16) are part of the mandatory core (RFP §18) and are **folded into the testing (T22–T24) and acceptance (T31) tasks**, not priced as separate line items.

## 5. Requirement → Deliverable → Effort traceability

Every scenario requirement maps to delivered work. Nothing is orphaned; nothing is double-counted.

| Req | Delivered by | Est. hrs |
| --- | --- | --- |
| F1 Register person | T6, T17 | 18 |
| F2 Search / dedupe | T6, T17 | 18 |
| F3 Triage questionnaire | T7, T18 | 20 |
| F4 Assessment answers | T7, T18 | 20 |
| F5 Initiate case | T8, T19 | 18 |
| F6 Mask-and-separate flag | T8, T19 | 18 |
| F7 Severity / disposition | T8, T19 | 18 |
| F8 Record case report | T10, T20 | 16 |
| F9 Contact report | T9 | 4 |
| F10 Review / export report | T10, T20 | 16 |
| F11 Reference-data validation | T11, T21 | 8 |
| F12 Audit the reporting action | T15 | 8 |
| NFR1 Privacy / security tagging | T5 | 4 |
| NFR2 Role-based access | T14 | 10 |
| NFR3 Deterministic validation | T12, T22–T24 | 34 |
| NFR4 Traceability | T4, T15 | 20 |
| NFR5 Responsive UI | T16 | 8 |
| NFR6 Configurable instrument | T13, T21 | 12 |
| NFR7 Audit trail | T15 | 8 |

*(Requirements share tasks; the estimates above attribute the shared task across the requirements it satisfies.)*

## 6. Definition of done — acceptance criteria

Each requirement is **done** when it passes the following controls, and only then:

- **Works:** the feature operates as specified in §7, confirmed by the relevant automated test (unit/integration/component) and by a manual end-to-end walkthrough.
- **Validated:** input passes the deterministic validation rules; no invalid or incomplete records are persisted (NFR3).
- **Documented:** the feature is reflected in the architecture/API/user docs where applicable.
- **Auditable:** creation, case-initiation, and report-submission actions are recorded (NFR7).
- **Traceable:** the output links back to the person and the responsible user (NFR4).
- **Accepted:** the acceptance walkthrough against §7 is signed off (T31).

## 7. Effort roll-up and schedule

| Phase | hrs | Cumulative |
| --- | --- | --- |
| 1 — Setup & Architecture | 16 | 16 |
| 2 — Data Layer | 24 | 40 |
| 3 — Back-end Web API | 40 | 80 |
| 4 — Validation & Configurability | 16 | 96 |
| 5 — Auth / RBAC / Audit | 18 | 114 |
| 6 — Front-end Blazor WASM | 52 | 166 |
| 7 — Testing | 26 | 192 |
| 8 — Documentation | 24 | 216 |
| 9 — Deployment & Acceptance | 18 | 234 |
| **Total** | **234 hrs** | |

**Schedule (one senior developer):** 234 hrs ÷ ~35 productive hrs/week ≈ **6.7 weeks (~7 weeks)**, full-time, no external blockers. The critical path runs through the front-end phase (52 hrs) and the back-end phase (40 hrs), which together are the bulk of the effort.

### 7.1 Schedule with dates

Assumed start: **Monday 2025-06-02** (shift all dates if the start moves). Milestones align to the RFP §17 payment gates:

| Milestone | Gate | Target date |
| --- | --- | --- |
| M1 — Design / architecture complete | 15% | 2025-06-06 (end wk 1) |
| M2 — Working vertical slice | 25% | 2025-06-20 (end wk 3) |
| M3 — All requirements | 30% | 2025-07-04 (end wk 5) |
| M4 — Testing & security | 20% | 2025-07-11 (end wk 6) |
| M5 — Acceptance & handover | 10% | 2025-07-18 (end wk 7) |

Critical path: the front-end (52 hrs) and back-end (40 hrs) phases dominate; the schedule is single-resource and serial.

## 8. Baseline cost (human labour only)

**234 hrs × $110/hr = $25,740.**

This is the **fully-loaded human labour cost** for delivering the complete, documented, tested, validated prototype end-to-end. It does **not** include the AI coder (the AI-assisted variant adds a small AI cost stream) nor infrastructure. It is the control number: the human-only cost the AI-assisted run is measured against.

## 9. Scope of comparison (so the later run is fair)

When the AI-assisted variant is prepared, it will be described **mirror-image to this baseline** — the same 234-hour WBS, same requirements, same definition of done — with only two things different:

1. **Producer per task** — each task tagged `human`, `ai-coder`, or `paired` (AI generates, human verifies/integrates).
2. **Scaffolding** — *tailored* (AI-generated to this schema/workflow) instead of *generic* (IDE template).

The AI-assisted run **will not** eliminate the human: verification, integration, and accountability remain the dominant, irreducible cost. A fair comparison is therefore *human-only full delivery* vs. *AI generation + human verification/integration*, not "human vs. AI."

## 10. Risks and caveats

A full risk register for the delivery is in *SCEN-0001-004*.

- **This is a baseline to be validated, not a guarantee.** Real effort moves with schema drift, tooling, and requirement interpretation.
- A1–A6 assumptions must hold; if the schema changes or the developer is interrupted, the estimate moves.
- The 234-hour figure includes the generic-scaffolding premise of §3.1. If the baseline were to assume hand-written boilerplate it would over-state (and be unfair to) the human team; if it assumed AI scaffolding it would no longer be human-only. The premise is deliberately fixed here.
- The estimate is **single-resource**; parallelizing (e.g., a second developer on front-end and back-end) compresses *calendar* time but not necessarily *total* hours, and adds coordination overhead.

### 10.1 Validation plan — how to confirm this baseline

- **Validate the rate.** Confirm the $110/hr fully-loaded rate against your market (US $100–135, W.Europe $80–120, offshore $30–60) before quoting.
- **Run a pilot.** Deliver one Construction Boundary / sprint and compare actual hours to the estimate; the cost ledger (v0.3 §3) records actuals.
- **Re-estimate after change.** If the schema, tooling, or requirements drift, re-run the WBS before committing.
- **Track actuals vs. baseline.** Feed actuals back each sprint so the baseline becomes a forecast, not a guess.
- **Independent review.** Have a second senior engineer sanity-check the WBS and estimates.
- **Hold contingency.** The estimate is single-resource and serial; hold contingency for interruptions and rework.

## 11. Completeness / quality coverage under the RFP fixed-price ceiling

The RFP (§18) caps the deliverable at **$19,000** — below the nominal full-delivery cost of this baseline (**234 hrs ≈ $25,740**). To submit a conforming bid, the human-only team must value-engineer the plan down to **≈173 hrs (a ≈26% cut)**. Because verification and documentation effort is roughly linear in human hours, **the cuts land on the discretionary completeness/quality surface**, not the mandatory core:

| CQ target (§18.1) | Level | What the ≈173-hr human-only response delivers |
| --- | --- | --- |
| **CQ-1** Requirement coverage | **Full (core)** | All F1–F12, NFR1–NFR7 built and tested; coverage statements concise |
| **CQ-2** Compliance-surface coverage | **Partial** | Mandatory §8–15 minimum addressed; heavy items (pentest, full FHIR, extended support) moved to priced options |
| **CQ-3** Test completeness | **Partial** | Core unit/integration/component tests; thinner edge-case and regression coverage; lighter UAT script |
| **CQ-4** Documentation completeness | **Partial** | Required docs (architecture, OpenAPI, technical, user guide) present but concise |
| **CQ-5** Security evidence depth | **Partial** | SBOM, dependency scan, OWASP review; independent penetration test deferred to priced option |
| **CQ-6** Verifiability | **Partial** | Auditable on the mandatory core; thinner evidence on the deferred compliance surface |

**Headline:** the human-only model can conform to $19,000, but only by thinning the wider compliance and evidence surface. Completeness outside the mandatory core is the cost of staying in budget.

---
*This baseline is the human-only control. It is meant to be compared, task-for-task, with the AI-assisted delivery baseline once it is prepared from the same requirement set (§7) and the same definition of done (§6).*
