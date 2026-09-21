# SCEN-0001-006 — Deliverable Register and Acceptance Checklist

| | |
| --- | --- |
| **Scenario** | SCEN-0001 — Communicable-Disease Case-Management Web-App (Initial Prototype) |
| **Type** | Deliverable register + acceptance/UAT checklist for the mandatory core |
| **Status** | Working artifact — the acceptance ledger for sign-off |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Companion docs** | *SCEN-0001-001* (RFP), *-002/-003* (baselines), *-004* (risk register) |

---

## 1. Deliverable register (mandatory core)

Each deliverable is accepted only when its evidence is produced and verified. Nothing is accepted on verbal assurance (RFP §16).

| ID | Deliverable | Acceptance criteria | Evidence | Owner |
| --- | --- | --- | --- | --- |
| **D1** | Application — Blazor WASM + ASP.NET Core Web API + SQL Server | Satisfies F1–F12, NFR1–NFR7; deployed and validated in a target environment | Working build; UAT walkthrough; deployment record | Senior Dev |
| **D2** | Automated tests | Unit, integration, and Blazor component tests run in CI and pass | CI test report; coverage summary | QA |
| **D3** | Documentation | Architecture, API (OpenAPI), technical reference, and user guide present and matching the delivered system | Docs reviewed against the build | Senior Dev |
| **D4** | SBOM + dependency scan | SBOM delivered; no open critical CVEs in the solution's dependencies | SBOM file; scan report | Security Lead |
| **D5** | Security compliance matrix | Each SEC-1…SEC-11 control mapped to implementation and evidence | Compliance matrix (§8.3) | Security Lead |
| **D6** | Traceability matrix | Every requirement and §8–16 obligation traced with a completion status | Matrix with completion-status column (§16) | PM |
| **D7** | Deployment runbook + CI/CD | Reproducible build/deploy; rollback path documented | Runbook; CI/CD config | Senior Dev |
| **D8** | Acceptance evidence pack | Test reports, security matrix, SBOM, runbook, audit-log demonstration | Pack assembled and reviewed | PM |
| **D9** | Source code | Delivered to the Agency; Agency owns it | Repository handover | PM |

*Priced options (not in the mandatory core): full FHIR/exchange implementation, independent penetration test, full Section 508 remediation, full training program, extended warranty, infrastructure/hosting.*

## 2. Acceptance / UAT checklist

Sign-off is granted only when every box below is checked and evidenced.

### 2.1 Functional (F1–F12)
- [ ] F1 Register a person (unique `Person_ID`)
- [ ] F2 Search / de-duplicate before creating a duplicate
- [ ] F3 Run a configurable triage screening questionnaire
- [ ] F4 Record assessment answers linked to the case
- [ ] F5 Initiate a case when reporting criteria are met
- [ ] F6 Record the mask-and-separate flag
- [ ] F7 Capture severity classification and disposition
- [ ] F8 Record the case report (date, source, type)
- [ ] F9 Record a contact report (basic)
- [ ] F10 Review and export the case report
- [ ] F11 Validate coded fields against reference data
- [ ] F12 Audit the reporting action

### 2.2 Non-functional (NFR1–NFR7)
- [ ] NFR1 PII/PHI fields tagged (HIPAA/GDPR/PII/NIST-PII)
- [ ] NFR2 Role-based access with separation of duties
- [ ] NFR3 Deterministic validation (no invalid/incomplete records)
- [ ] NFR4 Traceability (output → person → responsible user)
- [ ] NFR5 Responsive UI on desktop and tablet
- [ ] NFR6 Configurable instrument and reporting criteria
- [ ] NFR7 Immutable audit trail

### 2.3 Security & compliance (SEC-1…SEC-11, §8)
- [ ] SEC-1 MFA-capable authentication
- [ ] SEC-2 Role-based least privilege
- [ ] SEC-3 TLS 1.2/1.3 in transit
- [ ] SEC-4 AES-256 at rest for PHI
- [ ] SEC-5 FIPS 140-2/3 primitives
- [ ] SEC-6 Tamper-evident audit log
- [ ] SEC-7 OWASP Top 10 applied
- [ ] SEC-8 SBOM + dependency scan, no critical CVEs
- [ ] SEC-9 Penetration findings remediated (or optioned)
- [ ] SEC-10 Session management
- [ ] SEC-11 AI-tooling supply chain (SBOM + threat-intel on AI tooling)

### 2.4 Completeness / quality (CQ-1…CQ-6, §18.1)
- [ ] CQ-1 Requirement coverage statement per requirement
- [ ] CQ-2 Compliance-surface coverage
- [ ] CQ-3 Test completeness (tests exercise behavior, not stubs)
- [ ] CQ-4 Documentation completeness
- [ ] CQ-5 Security evidence depth
- [ ] CQ-6 Verifiability (independent reviewer can confirm)

### 2.5 UAT & sign-off
- [ ] Triage-staff usability walkthrough completed
- [ ] Acceptance walkthrough against §7 signed off
- [ ] Acceptance evidence pack (D8) reviewed
- [ ] Final payment gate released (RFP §18 milestone M5)

---
*This register is the acceptance ledger. It is meant to be completed with evidence at each milestone and signed off at acceptance (RFP §14, §16).*
