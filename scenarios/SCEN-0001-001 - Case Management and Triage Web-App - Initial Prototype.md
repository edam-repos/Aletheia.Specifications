# SCEN-0001-001 — Communicable-Disease Case-Management Web-App: Person Intake, Case Initiation, and Triage Reporting (RFP-Style Scenario)

| | |
| --- | --- |
| **Scenario ID** | SCEN-0001-001 |
| **Type** | RFP-style **scenario**, prepared as a Statement of Work a CDC-participant public-health agency would issue |
| **Status** | Draft RFP / scenario baseline for the initial triage prototype |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Governing reference** | CDC *Standard Operating Procedure for Triage to Prevent Transmission* — cdc.gov/covid/hcp/non-us-settings/sop-triage-prevent-transmission.html |
| **Underlying data model** | Eci.DiseaseSurveillance repository (`Entity` and `Surveillance` schema) |
| **Focus** | The **person → case initiation and reporting** track of the triage workflow |
| **Companion baseline docs** | *SCEN-0001-002* (human-only control) and *SCEN-0001-003* (AI-assisted variant) |

---

## 1. Purpose and Scope

The **Agency** invites a compliant, fully-working solution for the **initial prototype** of a communicable-disease **case-management web application**. The first vertical slice is the front end of the triage workflow: **receiving a person, screening them for symptoms and risk, initiating a case when the reporting criteria are met, and producing the case report for the surveillance authority.**

This scenario is written to be genuinely assessable as a real engagement: it states functional and non-functional requirements, then details the regulatory, security, interoperability, accessibility, privacy, reliability, and procurement obligations a public-health agency carries. A reviewer should be able to look at this and recognize a real, defensible scope — not an invented exercise.

The prototype deliberately restricts itself to the **person → case initiation and reporting** track. Full case investigation, contact tracing at scale, laboratory integration, outbreak clustering, and jurisdiction-based reporting are out of scope (see §23).

## 2. Governing Standard — the CDC Triage SOP

The application is designed around the CDC triage-to-prevent-transmission operating procedure:

1. **Screen on arrival** — screen for symptoms (fever, cough, respiratory distress) and epidemiological risk (travel, exposure to a confirmed/suspected case).
2. **Mask and separate** — any person with symptoms/risk is masked and moved to a separate area to prevent transmission.
3. **Assess severity** — identify warning signs of severe illness; severe cases are prioritized for stabilization and emergency referral.
4. **Classify and manage** — non-severe persons are classified for management (self-isolation with monitoring, referral, or other disposition).
5. **Report the case** — when reporting criteria are met, register the person, initiate a case, and report to the surveillance authority.

The prototype implements steps 1, 2, and 5 in full; steps 3–4 are captured as assessment/classification data on the case record. *(Validate the exact step wording and the official screening instrument against the live CDC page before implementation.)*

## 3. Actors and Roles

| Actor | Role |
| --- | --- |
| **Triage staff / healthcare worker** | Screens, records the assessment, registers the person, initiates and reports the case |
| **Person / subject** | The individual presenting; identified and screened |
| **Surveillance officer / authority** | Receives the reported case and routes it for further action |
| **Public-health administrator** | Manages user roles, reference data, and configuration |
| **Privacy / security officer** | Reviews PII/PHI handling, access, and audit; a required governance role |

## 4. The Triage Workflow (person → case initiation and reporting)

1. **Present** — a person arrives (walk-in, self-report, referral) or is identified as a contact.
2. **Screen** — triage questionnaire: symptom presence, onset date, severity signs, travel and exposure history.
3. **Register the person** — create the person record (identity, demographics, contact).
4. **Assess** — record the triage assessment answers against the questionnaire.
5. **Evaluate criteria** — apply reporting criteria to decide whether a case is initiated.
6. **Initiate the case** — create the case report (report date, illness onset, severity/disposition, report source).
7. **Report** — produce the reportable record for the surveillance authority; capture who reported, when, and from what source.
8. **Route** — hand the reported case to the surveillance authority.

## 5. Statement of Work and Project Description

The **Agency** requires a responsive web application that lets triage staff:

- Register a person and capture identifying/demographic data against the `Entity.Person` model.
- Run a screening/triage questionnaire and capture answers.
- Initiate a case (`Surveillance.Case_Report`) when reporting criteria are met.
- Record the report source and reporting date.
- Record a contact report (`Surveillance.Contact_Report`) when the person is a contact.
- Present and export the case report for the surveillance authority.

The application is a thin, well-structured client over the existing surveillance model — **not** a rewrite of the model.

### 5.1 Technology Stack

The solution is a full-stack .NET application: ASP.NET Core Web API, Blazor WebAssembly, and SQL Server. Components are mainstream and supportable.

**Back end — ASP.NET Core Web API**
- .NET 8/9, ASP.NET Core Web API (REST/JSON)
- Entity Framework Core 8/9 mapped to the existing `Entity` / `Surveillance` schema
- Repository + service layering over the EF `DbContext`
- FluentValidation for deterministic request validation
- JWT bearer auth + role-based authorization (ASP.NET Core Identity or lightweight role/JWT model)
- AutoMapper for entity ↔ DTO mapping; Serilog for structured logging; Swagger/OpenAPI
- **Security libraries:** ASP.NET Core Data Protection; FIPS-compliant TLS 1.2/1.3 in transit; AES-256 for PHI fields at rest (column-level) — see §8

**Front end — Blazor WebAssembly**
- .NET 8/9 Blazor WebAssembly single-page application
- MudBlazor (or Radzen) component library
- `HttpClient` + JWT authorization header
- Client-side validation aligned to server rules; PWA enhancement for constrained/field settings
- **Section 508 / WCAG 2.1 AA** accessible components — see §10

**Data — SQL Server**
- SQL Server (MSSQL), schema from the repo's SSDT project (`Eci.DiseaseSurveillance.sqlproj`)
- EF Core migrations (or sqlproj) as the single source of schema truth
- Reference data seeded from code tables; PII/PHI fields tagged per HIPAA / GDPR / PII / NIST-PII conventions
- Backups, point-in-time restore, and encryption at rest — see §11 and §13

**Interoperability — health-standards ready** (see §9)
- HL7 **FHIR® R4** readiness (FHIR .NET library or a compatible client)
- Vocabulary mapping to **LOINC**, **SNOMED CT**, **ICD-10-CM** (report/condition codes)

**Testing & quality**
- xUnit + FluentAssertions (unit); Moq; bUnit (Blazor components); integration tests (EF InMemory / local SQL Server)
- Security testing: OWASP Top 10 review, dependency scanning, penetration-testing findings — see §14

**Delivery / DevOps**
- CI via GitHub Actions or Azure DevOps; Docker; containerization option
- OpenAPI contract shared between front and back

### 5.1.1 AI-Coding Tooling Stack (options)

The application stack above is delivered by the Senior Developer + AI Coder pairing. The AI-coding tooling is a separate, first-class part of the stack, and for a sensitive public-health domain it is a **compliance decision as much as a cost decision**. The stack is a choice along two **independent** dimensions — **model** (commercial vs open-source) and **deployment** (on-cloud vs self-hosted). Two on-cloud options are presented for cost purposes on equal footing; the choice is the Agency's to make with its security/compliance team.

**Option A — Commercial on-cloud coding model (e.g., Anthropic Claude).**
- A hosted, frontier-quality coding model (e.g., Claude Opus/Sonnet) used through an agentic coding interface.
- **Quality:** frontier — strongest on the hardest, most complex coding tasks; most reliable for novel or ambiguous work.
- **Cost:** per-seat subscription + metered compute (vendor hosting bundled); higher than a low-cost assistant.
- **Compliance posture:** code and data leave the environment to the vendor's cloud. Requires enterprise/zero-data-retention terms and, for PHI, a business-associate agreement; may be restricted for the most sensitive modules.

**Option B — Open-source on-cloud coding model (e.g., DeepSeek, Qwen2.5-Coder).**
- An open-source coding model run on your cloud GPU or a managed inference service.
- **Quality:** competitive — open-source coding models have closed the gap and can match commercial on common and standard tasks; the frontier commercial model's edge is concentrated on the hardest tasks.
- **Cost:** no per-seat license and the model itself is free; the cost is running it — cloud GPU hosting (or a managed inference service).
- **Compliance posture:** data goes to your chosen cloud provider (not the model vendor's proprietary cloud); you control residency.

**Self-hosted variant (a separate deployment choice).** Self-hosting is not a property of open-source — it is a deployment decision. For the compliance-maximal case (data never leaves your environment), an open-source model can be self-hosted on-prem or in a FedRAMP-approved cloud you control.

Both on-cloud options are costed in *SCEN-0001-003* §9.3. The choice is not made here.

### 5.2 Baseline timeline (reference)

A complete human-only delivery is estimated at **~234 hours ≈ 6–8 weeks ≈ $25,740** (see *SCEN-0001-002*). An AI-assisted variant at **126 human hrs ≈ 4 weeks ≈ ~$13,950** (see *SCEN-0001-003*). The Agency's requirement here is a **documented, tested, validated** deliverable; see §14 for the acceptance bar.

## 6. Initial Analysis — Domain Model Mapping

The prototype surfaces these existing schema objects and validates entry against them.

| Domain concern | Repo table | Key fields |
| --- | --- | --- |
| Person identity & demographics | `Entity.Person` | `Person_ID`, `Given_Name`, `Middle_Name`, `Family_Name`, `Name_Full`, `Birth_Date`, `Sex_Code_ID`, `Status_Code_ID` |
| Person contact | `Entity.Communication` | phone / email / address reference |
| Screening assessment | `Surveillance.Assessment`, `Assessment_Questionnaire`, `Assessment_Question`, `Assessment_Answer`, `Answer_Type` | questionnaire → question → answer |
| Case creation | `Surveillance.Case_Report` | `Report_Date`, `Reported_Date`, `Type_ID`, `Illness_Onset_Date`, `Hospitalized_Flag_ID`, `Exposure_Setting_Code_ID`, `Subject_ID`, `Status_Code_ID` |
| Contact reporting | `Surveillance.Contact_Report` | `Contacted_Date`, `Contact_Type_ID`, `Contact_Flag_ID`, `Case_Linked_Flag_ID`, `Subject_ID` |
| Report source & type | `Surveillance.Reporting_Source_Type`, `Report_Type` | source/type for routing |
| Reporting criteria | `Surveillance.Reporting_Criteria_Code` | whether a case is reportable |
| Reference data | `Entity.*_Code`, `Surveillance.*_Code` | validated pick-lists |

**Data-flow note.** `Case_Report.Subject_ID` and `Contact_Report.Subject_ID` reference `Entity.Person`; the prototype must maintain that identity link (person → case, person → contact).

## 7. Requirements

### 7.1 Functional requirements

| ID | Requirement | Priority |
| --- | --- | --- |
| **F1** | Register a person (identity, sex, birth date, one communication channel; unique `Person_ID`) | Must |
| **F2** | Search / de-duplicate a person (name + birth date) before creating a duplicate | Must |
| **F3** | Run a configurable triage screening questionnaire (symptoms, onset, severity, travel/exposure) | Must |
| **F4** | Record assessment answers against `Assessment_Question` / `Assessment_Answer`, linked to the case | Must |
| **F5** | Initiate a case from an assessment when reporting criteria are met | Must |
| **F6** | Record the mask-and-separate flag (SOP step 2) as a workflow marker | Must |
| **F7** | Capture severity classification and disposition (SOP steps 3–4) | Must |
| **F8** | Record the case report (`Reported_Date`, source, type) | Must |
| **F9** | Record a contact report when the person is a known contact | Should |
| **F10** | Review and export the case report for the surveillance authority | Must |
| **F11** | Validate all coded fields against repo reference-data tables | Must |
| **F12** | Audit the reporting action (who, when, from what source) | Must |

### 7.2 Non-functional requirements

| ID | Requirement | Priority |
| --- | --- | --- |
| **NFR1** | PII/PHI fields carry the repo's HIPAA / GDPR / PII / NIST-PII tags | Must |
| **NFR2** | Role-based access with separation of duties (staff / officer / admin) | Must |
| **NFR3** | Deterministic validation (date ranges, required codes, no duplicate persons) | Must |
| **NFR4** | Traceability: every answer/action links to the person and responsible user | Must |
| **NFR5** | Responsive UI on desktop and tablet during in-person triage | Should |
| **NFR6** | Configurable instrument and reporting criteria without code changes | Should |
| **NFR7** | Immutable audit trail for person creation, case initiation, report submission | Must |

## 8. Governance, Compliance, and Security

A public-health agency handling PII/PHI carries binding obligations. The solution **must** address these; compliance is part of the deliverable, not an add-on.

### 8.1 Regulatory and compliance posture

| Ref | Obligation | Requirement |
| --- | --- | --- |
| **SR-1** | U.S. federal context | FISMA-aligned controls; align security to **NIST SP 800-53** |
| **SR-2** | Privacy/health data | Handle data consistent with **HIPAA** Privacy and Security Rules and **HITECH** (PHI) |
| **SR-3** | International context | Support **GDPR** principles (lawful basis, data-subject rights) where the Agency operates under it |
| **SR-4** | Classification | Every PII/PHI field is classified and tagged in the data dictionary (per repo conventions) |
| **SR-5** | Authorization | The solution is prepared for a **security assessment & authorization (ATO)** evidence package |

### 8.2 Security controls

| Ref | Requirement |
| --- | --- |
| **SEC-1** | **Authentication:** MFA-capable; strong identity per NIST SP 800-63 |
| **SEC-2** | **Authorization:** role-based least privilege; separation of duties |
| **SEC-3** | **Encryption in transit:** TLS 1.2/1.3; strong cipher suites |
| **SEC-4** | **Encryption at rest:** AES-256 for PHI fields at rest (column/database level) |
| **SEC-5** | **Cryptography:** FIPS 140-2/140-3 validated primitives |
| **SEC-6** | **Audit:** tamper-evident, immutable audit log of every sensitive action |
| **SEC-7** | **Application security:** OWASP Top 10 applied; input validation, output encoding, safe auth/session handling |
| **SEC-8** | **Dependency hygiene:** dependency scanning; no known critical CVEs; **SBOM** delivered |
| **SEC-9** | **Penetration testing:** findings remediated before acceptance (see §14) |
| **SEC-10** | **Session management:** secure cookies, idle/session timeout, log-out |
| **SEC-11** | **AI-tooling supply chain:** disclose any AI coding tools/models used; extend the **SBOM**, dependency scan, and an **AI threat-intel watch** to that tooling; pin the tool/model version with change control and **re-validation after any update** (see §10.4.3 of the companion) |

### 8.3 Security requirements (explicit, in RFP terms)

The Offeror **must** deliver a security compliance matrix mapping each control above to its implementation and evidence, and a vulnerability report showing no open critical/high findings at acceptance.

## 9. Interoperability and Health-Data Standards

A case-management app that feeds surveillance must speak health-data standards.

| Ref | Requirement |
| --- | --- |
| **IR-1** | **HL7 FHIR R4** readiness: expose persons, cases, and assessment data in a FHIR R4 representation (or a documented mapping) |
| **IR-2** | **LOINC** for assessment/screening questions and results wherever the clinical item has a LOINC code |
| **IR-3** | **SNOMED CT** for conditions, findings, and dispositions where applicable |
| **IR-4** | **ICD-10-CM** for reportable condition and diagnosis codes on the case report |
| **IR-5** | **Public-health reporting alignment:** structure the case report for **NNDSS / PHIN** electronic exchange readiness and **eCR (electronic case reporting)** pathways |
| **IR-6** | **Referential integrity:** preserve person → case → report identity across standard representations |

## 10. Accessibility and Localization

| Ref | Requirement |
| --- | --- |
| **AR-1** | **WCAG 2.1 AA** / **Section 508**: keyboard navigation, screen-reader support, sufficient contrast, accessible forms |
| **AR-2** | **Multilingual UI** readiness for the CDC non-US-setting context (UI text externalized, RTL support where needed) |
| **AR-3** | Accessible data-entry for high-stress, fast triage (clear labels, minimized cognitive load) |

## 11. Reliability, Resilience, and Performance

| Ref | Requirement |
| --- | --- |
| **RR-1** | **Availability:** documented availability target (e.g., 99.5%) and a support plan to meet it |
| **RR-2** | **Backup & recovery:** automated backups; defined **RPO/RTO** (e.g., RPO ≤ 24h, RTO ≤ 4h) |
| **RR-3** | **Disaster recovery / business continuity:** documented recovery runbook |
| **RR-4** | **Performance:** triage throughput and response-time targets (e.g., a triage assessment screen renders/commits within a set limit; supports concurrent triage staff) |
| **RR-5** | **Observability:** structured logs and the audit trail survive restarts; no silent data loss |

## 12. Privacy and Data Management

| Ref | Requirement |
| --- | --- |
| **PR-1** | **Privacy Impact Assessment (PIA)** inputs: document what PII/PHI is collected, why, and the lawful basis |
| **PR-2** | **Data minimization:** collect only the fields the CDC SOP triage and reporting need |
| **PR-3** | **Consent / notice** where applicable; record it with the person |
| **PR-4** | **Retention and disposition:** configurable data-retention periods aligned to jurisdictional rules; secure deletion |
| **PR-5** | **Data-subject rights** (where GDPR applies): access, correction, erasure workflows surfaced to authorized users |
| **PR-6** | **Role-limited access:** PII/PHI only visible to authorized roles; no broad read-all |

## 13. Deployment, Hosting, and Security Posture

| Ref | Requirement |
| --- | --- |
| **DP-1** | **Hosting:** deployable to an authorized environment (e.g., FedRAMP-authorized cloud, or an Agency-approved on-prem data center per jurisdiction) |
| **DP-2** | **Environment segregation:** clear separation of development, test, and production; protected credentials/secrets |
| **DP-3** | **Deployment:** reproducible CI/CD; immutable/versioned releases; rollback path |
| **DP-4** | **Operational security:** hardened OS/container images, least-privilege service accounts, network isolation |

## 14. Quality Assurance, Testing, and Acceptance

The Agency will not accept an untested deliverable. Acceptance is defined by evidence.

| Ref | Requirement |
| --- | --- |
| **QA-1** | **Automated tests:** unit, integration, and Blazor component tests that run in CI |
| **QA-2** | **Functional acceptance:** every requirement (F1–F12, NFR1–NFR7) demonstrated in UAT against §7 |
| **QA-3** | **Security testing:** OWASP-aligned review, dependency scan, and penetration-test findings remediated |
| **QA-4** | **Usability:** Section 508 check and a short triage-staff usability walkthrough |
| **QA-5** | **Acceptance evidence pack:** test reports, security compliance matrix, SBOM, deployment runbook, and audit-log demonstration delivered for sign-off, tracked against the **deliverable register and acceptance checklist** (*SCEN-0001-006*) |
| **QA-6** | **Pilot / phased rollout** option before full production cut-over |

## 15. Training, Documentation, and Support

| Ref | Requirement |
| --- | --- |
| **TD-1** | **User training:** a triage-staff training session and quick-reference guide |
| **TD-2** | **Administrator training:** configuration, reference data, and user-role management |
| **TD-3** | **Documentation:** architecture, API (OpenAPI), technical reference, configuration, and deployment runbook |
| **TD-4** | **Support & warranty:** a defined post-delivery support/warranty period and a support contact |

## 16. Compliance and Requirements Traceability Matrix

The Offeror **must** deliver a traceability matrix showing, for every functional and non-functional requirement and every §8–15 control/obligation:

1. **Requirement / control** — the ID (§7, §8–15).
2. **Where implemented** — the module, table, or service.
3. **Evidence of compliance** — the test, artifact, or inspection that proves it.
4. **Completion status** — `delivered-and-tested` / `partial` / `not-delivered`.
5. **Completeness/quality (CQ) note** — a concise statement of the coverage level delivered for that line, mapped to the **CQ-1…CQ-6** targets (§18.1).

Nothing is accepted on verbal assurance; the matrix is the acceptance ledger. Because both delivery models fit under the fixed price (§18.1), the **completion-status column is the objective completeness score** the Agency uses to compare human-only vs. AI-assisted responses on an equal ledger (§22).

The matrix must also carry a **CQ-coverage block** reporting each of CQ-1…CQ-6 with a level (`full` / `substantial` / `partial` / `thin`) and the evidence behind it.

## 17. Procurement and RFP Framing

So the scenario reads as a real agency RFP, the following procurement framing is included:

- **Engagement type** — fixed-scope prototype delivery with defined deliverables, acceptance, warranty, and support.
- **Deliverables** — the application, tests, documentation, acceptance evidence pack, deployment runbook, and source code to the Agency.
- **Ownership** — the Agency owns the delivered source and resulting data; the Offeror licenses nothing back beyond agreed terms.
- **Milestones / payment** — tied to demonstrated deliverables, not to calendar drift: (1) design/architecture, (2) working vertical slice, (3) full requirements, (4) testing/security, (5) acceptance and handover.
- **Evaluation criteria** — technical completeness (requirements + compliance matrix), security posture, past performance on public-health systems, and realistic cost (costs assessed against the *SCEN-0001-002 / -003* baselines).
- **Response format** — a compliance matrix, technical approach, delivery plan, a **risk register** (*SCEN-0001-004*), a **deliverable register + acceptance checklist** (*SCEN-0001-006*), and an itemized cost.
- **Terms** — warranty period, support SLA, source-code escrow option, confidentiality of PII/PHI, and acceptance sign-off gate.

## 18. Budget and Fixed-Price Model

The Agency requires a **firm fixed-price** delivery under a tight budget ceiling. The Offeror bears cost-overrun risk; the price is firm for the defined scope. This is deliberately challenging — it forces real value engineering.

- **Contract type:** fixed-price (no time-and-materials). The price is inclusive of all labour, overhead, tooling, and the §14 acceptance evidence pack.
- **Budget ceiling:** **$19,000 (firm).** This is the maximum price the Agency will pay for the mandatory scope.
- **Why it is tight:** the human-only baseline estimate is **~$25,740** (234 hrs × $110) and the AI-assisted baseline **~$13,950** (126 hrs + ~$93 AI). At **$19,000**, the human-only model must value-engineer hard — from ~234 hrs to ~173 hrs (≈26% reduction) — to fit under the cap; the AI-assisted model fits with margin. **Both models are intended to be deliverable under the ceiling.** The cap is deliberately set so the competition is decided by the **completeness and quality of the delivered, verified product** (see §18.1), not by who can price a thin shell cheapest.
- **Payment schedule (milestone-gated, no payment for unaccepted work):** 15% design/architecture → 25% working vertical slice → 30% all requirements → 20% testing & security → 10% acceptance & handover.
- **Liquidated damages:** a defined daily rate for late acceptance beyond the agreed delivery date, capped at a percentage of the contract price.
- **Change control:** scope changes beyond the baseline are priced separately and require written Agency approval; unbudgeted scope is not absorbed into the fixed price.
- **Mandatory core vs. priced options.** The $19,000 fixed price covers the **mandatory core** — the F1–F12 / NFR1–NFR7 build plus the baseline evidence required for acceptance: role-based access and audit (NFR2/NFR7), deterministic validation (NFR3), core automated tests (QA-1), core documentation (TD-3), deployment and CI/CD (DP), an SBOM and dependency scan (SEC-8), an OWASP review (SEC-7), and the traceability matrix (§16). The delivery baselines (*SCEN-0001-002 / -003*) cost exactly this mandatory core. **Priced options** outside the ceiling: full FHIR/exchange implementation (IR-5), an independent third-party penetration test (QA-3), full Section 508 remediation (AR-1), a full training program (TD-1/2), extended warranty, and **infrastructure/hosting** (prototype ~$100–$300/mo; FedRAMP-authorized production ~$500–$1,500/mo). Options are priced separately so the mandatory price stays credible and within budget.

### 18.1 Value proposition — completeness and quality is the differentiator

The Agency intends this engagement to be **achievable under the fixed-price ceiling in both delivery models** — a disciplined human-only delivery and the Senior Developer + AI Coder pairing. The procurement is deliberately structured so the outcome is **not decided by speed or headline price**, but by the **completeness and quality of what is actually delivered, tested, documented, and evidenced** within the same $19,000.

The reasoning is a deliberate target of this RFP:

- Within a tight fixed price, a human-only team must cut its delivery back and makes harder trade-offs — thinning test depth, documentation breadth, or non-functional coverage to fit.
- An AI-assisted pair spends its constrained human verification time on the same surface but, because generation is cheap, can cover **more** of the requirement and compliance surface and produce **deeper** evidence for the same price.
- The measurable advantage is therefore the **completeness of the deliverable**, not how fast it shipped or the cents on the invoice.

**Completeness / quality targets (explicit):**

| Ref | Target |
| --- | --- |
| **CQ-1** | **Requirement coverage:** the delivered build demonstrably satisfies as much of §7 as the budget allows, with a per-requirement coverage statement |
| **CQ-2** | **Compliance-surface coverage:** the §8–16 obligations are addressed proportionally to budget; the more complete the compliance matrix, the better |
| **CQ-3** | **Test completeness:** measured automated-test coverage, with evidence the tests genuinely exercise behavior (not stubs) |
| **CQ-4** | **Documentation completeness:** architecture, API, technical, and user docs present and matching the delivered system |
| **CQ-5** | **Security evidence depth:** the SBOM, dependency scan, OWASP review, and audit-trail demonstration are complete and verifiable |
| **CQ-6** | **Verifiability:** an independent reviewer can confirm the deliverable is not merely "shipped" but genuinely complete and usable |

**Both-models feasibility:** a viable submission in either the human-only or the AI-assisted model is expected under the cap. The winning response is the one that maximizes **CQ-1…CQ-6 within the fixed price** — not the one that ships fastest or cheapest.

## 19. Vendor (Offeror) Qualifications

The Offeror must demonstrate it is a credible, responsible supplier of public-health technology. The following are **mandatory, pass/fail**; failure to meet any is grounds for rejection without scoring.

| Ref | Mandatory qualification |
| --- | --- |
| **VQ-1** | At least **2 completed public-health, disease-surveillance, or health-information-system projects** of comparable scope within the last 3 years |
| **VQ-2** | Prior implementation handling **PII/PHI** under **HIPAA** and/or **GDPR**, with a documented compliance approach |
| **VQ-3** | A security program consistent with **NIST SP 800-53**; ability to produce an **SBOM** and remediate findings; readiness for penetration testing |
| **VQ-4** | **Financial stability** — auditable financials supporting fixed-price completion; appropriate **cybersecurity and liability insurance** |
| **VQ-5** | **No debarment/suspension** — not on any federal/state debarment list (responsibility check) |
| **VQ-6** | **Past performance** — at least **2 references** from public-health or equivalent engagements |
| **VQ-7** | Deployment capability on a **FedRAMP-authorized cloud** or an authorized environment (documented path) |
| **VQ-8** | An **AI-supply-chain posture:** disclose AI coding tooling and model versions; commit to an **SBOM + threat-intel watch** on that tooling, and a **continuity/fallback plan** if a provider deprecates a model or changes price or terms |

Each qualification must be evidenced (deliverables, references, certificates, financials). Declarations are verified before award.

## 20. Key Personnel and Team Qualifications

The Offeror will staff the project with **named, available key personnel** whose qualifications are **part of the contract**; substitution requires prior Agency approval.

| Role | Required qualifications |
| --- | --- |
| **Project Manager** | PMP (or equivalent); prior public-health / health-IT delivery; fixed-price delivery track record |
| **Solution / Data Architect** | .NET and health-data-model experience; PII/PHI systems; surveillance/reporting domain familiarity |
| **Security Lead** | CISSP (or equivalent); HIPAA Security Rule experience; runs OWASP review and penetration-finding remediation |
| **.NET / Blazor Developer** | ASP.NET Core + Blazor WebAssembly experience; entity/data modeling |
| **QA / Test Engineer** | Automated test design (xUnit/bUnit); Section 508 and security-testing exposure |
| **All personnel** | Completed **HIPAA privacy/security training**; data-handling awareness |

The **staffing plan and team size must be consistent with the $19,000 fixed-price ceiling.** The Offeror must explain how the team is staffed to complete a tested, documented, compliant deliverable under the cap — the PM must justify head-count, hours, and approach against the budget.

## 21. Contractual and Performance Terms

- **Fixed-price terms** — price firm; Offeror bears overrun; change control required for scope change.
- **Liquidated damages / penalties** — late-delivery and missed-milestone consequences, capped.
- **Warranty** — a defined defect-correction period (e.g., 90 days) after acceptance at no additional cost.
- **Support SLA** — documented response/availability targets during the warranty/support period.
- **AI-tooling governance** — pin the AI tool/model version; any update is change control requiring re-validation; the Offeror keeps an **SBOM / threat-intel watch** on that tooling and a **continuity/fallback** if the provider deprecates a model or changes price or terms (see §10.4.3 of the companion).
- **Breach & incident** — duty to notify the Agency of a PII/PHI breach per applicable regulation; incident-response plan.
- **Confidentiality & data ownership** — non-disclosure; the Agency owns all data and the delivered source/IP.
- **Termination** — convenience and for-cause termination with defined compensation.
- **Acceptance gate** — formal sign-off driven by the §14 evidence pack before final payment.

## 22. Bid Evaluation and Scoring

Responses failing a mandatory qualification (§19, §20) or exceeding the $19,000 ceiling are rejected without scoring. Qualified responses are scored as follows.

**Because both delivery models are expected to fit under the fixed price, evaluation weight is placed on completeness and quality (CQ-1…CQ-6, §18.1) rather than on speed or headline cost.** Technical completeness is the largest single factor, and within it the degree to which the deliverable is actually completed, tested, documented, and evidenced drives the score.

A one-page head-to-head summary of the two models is in *SCEN-0001-005*.

| Criterion | Weight | Basis |
| --- | --- | --- |
| Technical completeness & quality | 45% | Compliance matrix, §7 coverage, §8–16 obligations, and the CQ-1…CQ-6 completeness/quality targets (§18.1) |
| Cost realism | 25% | Against the $19,000 ceiling; realistic, not a lowball; the more delivered for the fixed price, the better |
| Security posture | 15% | Controls, certifications, SBOM / penetration readiness |
| Past performance | 10% | References, public-health experience, quality |
| Staffing & key personnel | 5% | Qualifications, availability, no-substitution plan |

## 23. Prototype Scope

**In scope:** person registration + de-duplication, triage screening questionnaire, assessment capture, case initiation, severity/disposition, case-report creation and export, contact report (basic), reference-data validation, role-based access, audit trail, and the §8–15 compliance obligations.

**Out of scope (later tracks):** full case investigation, contact tracing at scale, laboratory/viral report integration, outbreak clustering, multi-jurisdiction/national reporting automation, EHR integration, and clinical decision support beyond the SOP triage classification.

## 24. Complexity Assessment

This is a **moderate-to-substantial and commercially challenging** first scenario. It is real and defensible because it is **not just a CRUD app**: it carries a regulatory and interoperability surface (HIPAA/PII, FHIR/NNDSS/eCR readiness, WCAG, NIST-aligned security), a configurable clinical instrument, deterministic validation, a role-based audit trail, an accountable human reporting decision, **and a tight fixed-price budget, mandatory vendor/key-person qualifications, and liquidated damages** — the exact combination that makes a PM treat it as a genuine, high-stakes procurement. The $19,000 ceiling is set so **both** the human-only and the AI-assisted models are deliverable; the competition is deliberately decided by **completeness and quality of the delivered, verified product** (§18.1), so the real advantage of the pairing is expected to show up in how much is delivered and evidenced, not in speed or cents.

Scaling options, if an even heavier RFP is wanted: add **full case investigation**, **contact tracing at scale**, **eCR/lab integration**, or **multi-jurisdiction report routing** — each further strains the fixed-price ceiling.

---
*This RFP-style scenario is provided for scenario planning and costing. It summarizes the CDC triage framework; validate the exact screening steps against current CDC guidance before implementation. All standards mentioned (HIPAA, FHIR, LOINC, SNOMED CT, ICD-10-CM, NNDSS, NIST 800-53, WCAG 2.1, Section 508, FIPS 140) are real and binding in the relevant jurisdictions — applicability must be confirmed for the Agency's specific operating authority.*
