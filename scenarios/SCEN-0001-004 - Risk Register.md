# SCEN-0001-004 — Risk Register (RAID)

| | |
| --- | --- |
| **Scenario** | SCEN-0001 — Communicable-Disease Case-Management Web-App (Initial Prototype) |
| **Type** | Risk register for the delivery, covering both the human-only and the AI-assisted paths |
| **Status** | Working register — reviewed at each milestone; not a static list |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Companion docs** | *SCEN-0001-001* (RFP), *-002* (human-only baseline), *-003* (AI-assisted baseline), *-005* (one-page comparison), *-006* (deliverable register & acceptance) |

---

## 1. How to read this register

- **Likelihood** and **Impact** are rated `Low` / `Med` / `High`.
- **Exposure** = likelihood × impact, collapsed to `Low` / `Med` / `High`. High-exposure risks get a named owner and a mitigation that is *priced or scheduled*, not just noted.
- **Owner** is the accountable human. The AI coder is never a risk owner.
- This register is deliberately **fair to both paths**: it lists the AI-assisted path's risks *and* the human-only path's risks, so the comparison in *SCEN-0001-003* §9 is not one-sided.

## 2. AI-assisted path risks

| ID | Risk | Likelihood | Impact | Exposure | Mitigation | Owner |
| --- | --- | --- | --- | --- | --- | --- |
| **R-AI-1** | Tool/model deprecation or version change mid-project; output behavior drifts and earlier work needs re-validation | Med | High | **High** | Pin the tool/model version; change control on any update; a re-validation/regression gate after each update (§10.4.3) | Senior Dev |
| **R-AI-2** | Newly-identified CVE or LLM-specific threat in the AI tooling (prompt injection, data exfiltration, supply-chain) | Med | High | **High** | SBOM + dependency scan + threat-intel watch on the AI tooling (SEC-11); incident-response plan | Security Lead |
| **R-AI-3** | Provider raises seat/compute price or changes terms mid-contract | Med | Med | Med | Price the AI stream with a maintenance/security reserve (§10.4.3); continuity/fallback tool | PM |
| **R-AI-4** | AI output quality is poor → rework; human hours rise toward (or past) the human-only baseline | Med | High | **High** | Blocked-coder handoff (v0.3 §17.4); per-task verification; the reserve absorbs rework | Senior Dev |
| **R-AI-5** | Provider changes data-handling policy; PHI handling may no longer be compliant | Low | High | Med | Re-affirm PHI-handling terms; keep a compliant fallback; legal review at contract | PM / Security Lead |
| **R-AI-6** | AI generates subtle, hard-to-find defects (concurrency, security, non-deterministic) | Med | High | **High** | Axiom #4: budget verification and bug-hunting, not just generation; deeper test coverage | Senior Dev / QA |
| **R-AI-7** | Over-reliance on AI → verification/accountability gaps | Med | High | **High** | Axiom #1: generation is not delivery; named human owner per deliverable; audit trail | PM / Senior Dev |
| **R-AI-8** | AI tooling outage or unavailability blocks generation | Low | Med | Low | Human fallback; the human can always proceed manually (the pairing is additive) | Senior Dev |

## 3. Project risks (both paths)

| ID | Risk | Likelihood | Impact | Exposure | Mitigation | Owner |
| --- | --- | --- | --- | --- | --- | --- |
| **R-P-1** | Budget overrun against the $19,000 fixed-price ceiling | Med | High | **High** | Fixed-price discipline; milestone-gated payment; value-engineering to the mandatory core (§18); reserve | PM |
| **R-P-2** | Scope creep / unbudgeted change | Med | Med | Med | Change control; priced options kept outside the ceiling; written approval for scope change | PM |
| **R-P-3** | Schedule slip (single accountable resource) | Med | Med | Med | Dated schedule (§7 of the baselines); critical-path focus; liquidated-damages awareness | PM |
| **R-P-4** | Data breach / PII-PHI exposure | Low | High | Med | Encryption at rest/in transit, RBAC, audit, breach-notification duty (§8, §21) | Security Lead |
| **R-P-5** | Compliance shortfall (HIPAA / FHIR / Section 508) → acceptance failure | Med | High | **High** | Traceability matrix (§16); CQ targets (§18.1); compliance evidence pack (§14) | PM / Security Lead |
| **R-P-6** | Schema drift / requirement misinterpretation | Med | Med | Med | Deterministic validation; requirement-to-effort traceability (§5 of -002) | Senior Dev |
| **R-P-7** | Key-personnel availability (single senior developer) | Med | Med | Med | Named key personnel with no-substitution clause (§20); backups | PM |
| **R-P-8** | Quality/completeness shortfall (CQ-1…CQ-6 not met) | Med | High | **High** | CQ targets measured via the §16 completion-status column; acceptance gate | PM / Senior Dev |
| **R-P-9** | Infrastructure cost overrun (priced option) | Med | Med | Med | Fixed infra range (~$100–$300/mo prototype; ~$500–$1,500/mo production); right-size environment | PM |
| **R-P-10** | Third-party dependency continuity (AI tool provider) | Med | Med | Med | Continuity/fallback plan; the human is the fallback (Axiom #5) | PM |

## 4. Human-only path risks (for a fair comparison)

| ID | Risk | Likelihood | Impact | Exposure | Mitigation | Owner |
| --- | --- | --- | --- | --- | --- | --- |
| **R-H-1** | Slower delivery → schedule risk (6.7 weeks vs. 3.6) | Med | Med | Med | Parallelize front/back-end if calendar matters; accept the longer critical path | PM |
| **R-H-2** | Higher cost → budget risk ($25,740 vs. $19,000 ceiling) | High | High | **High** | Value-engineer to ≈173 hrs; cut discretionary compliance surface (CQ partial, §11 of -002) | PM |
| **R-H-3** | Manual-error defects from hand-written work | Med | Med | Med | Automated tests; review; the same acceptance gate | Senior Dev / QA |
| **R-H-4** | Less compliance-surface coverage (CQ-2 partial) | Med | Med | Med | Explicitly scope the mandatory core; price the rest as options (§18) | PM |

## 5. Top risks to watch (the short list)

1. **R-AI-4 / R-P-8** — AI quality and completeness shortfall: the pairing's whole value claim rests on verification, so a quality miss is the highest-impact risk.
2. **R-P-1 / R-H-2** — budget: the $19,000 ceiling is the binding constraint; both paths must stay inside it.
3. **R-AI-1 / R-AI-2** — the AI coder as a managed dependency: version and security drift are real, recurring, and priced via the reserve (§10.4.3).
4. **R-P-5** — compliance shortfall: acceptance is gated on the traceability matrix and CQ evidence.

---
*This register is a working PM artifact, not a guarantee. Likelihood/impact are judgment calls to be re-scored at each milestone as actuals come in. It is meant to be read alongside the baselines (-002, -003) and the RFP (-001).*
