# SCEN-0001 — AI-Assisted Delivery Baseline (End-to-End)

| | |
| --- | --- |
| **Scenario** | SCEN-0001 — Communicable-Disease Case-Management Web-App: Person Intake, Case Initiation, and Triage Reporting (Initial Prototype) |
| **Baseline type** | **AI-assisted** — the Senior Developer + AI Coder pairing (v0.3 §17). |
| **Mirror of** | *SCEN-0001-002 - Human-Only Delivery Baseline* (same WBS, same requirements, same definition of done) |
| **Status** | Baseline for cost/schedule comparison against the human-only control |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Purpose** | The measured variant. It is compared, task-for-task, against the human-only control. |

---

## 1. Purpose — how this baseline is read

This document is the **mirror-image** of the human-only baseline. It describes the same end-to-end delivery of the SCEN-0001 prototype — the same 31 tasks (T1–T31), the same requirement set (F1–F12, NFR1–NFR7), the same definition of done — but produced by the **Senior Developer + AI Coder pairing** described in ISL v0.3 §17.

Two things differ from the control, and nothing else:

1. **Producer per task** — each task is tagged `human`, `ai-coder`, or `paired` (the AI coder generates, the Senior Developer verifies and integrates).
2. **Scaffolding** — **tailored** (generated to this schema and workflow) instead of the generic IDE template the human baseline uses.

The fair reading is *human-only full delivery* vs. *AI generation + human verification/integration* — **not** "human vs. AI." The human remains on the critical path.

## 2. What "done" means — identical to the control

Same as the human-only baseline (§6): **works, validated, documented, auditable, traceable, accepted.** The pairing does not lower the bar; it meets the same bar with a different division of labour.

**Scope boundary — identical to the control.** This baseline prices the same **mandatory core** as the human-only control (F1–F12, NFR1–NFR7, plus the baseline evidence in the RFP §18). It does **not** include the RFP's **priced options** — full FHIR/exchange implementation (IR-5), an independent third-party penetration test (QA-3), full Section 508 remediation (AR-1), a full training program (TD-1/2), extended warranty, or **infrastructure/hosting** (prototype ~$100–$300/mo; FedRAMP-authorized production ~$500–$1,500/mo). The comparison in §9 is therefore a like-for-like comparison of the mandatory core, and the ~$14,200 budgeted total (incl. the maintenance & security reserve) fits the $19,000 ceiling with ≈25% headroom.

## 3. Assumptions

| # | Assumption |
| --- | --- |
| A1 | **One Senior Developer (accountable owner) + one AI Coder (executor)** under that owner (v0.3 §17). |
| A2 | Same fully-loaded human rate: **$110/hour**. |
| A3 | AI cost recorded in two small streams: **compute per task (~$1–$3)** plus a **seat (~$19/month)**, per companion Appendix A. |
| A4 | **Tailored scaffolding** — the AI coder generates scaffolding aligned to the existing `Eci.DiseaseSurveillance` schema and the triage workflow, replacing generic IDE templates. |
| A5 | Productive capacity: **~35 human hours/week**; AI generation is fast and parallel to human work. |
| A6 | Same requirement set and schema; the human is accountable for every deliverable (v0.3 §17.1). |

## 4. End-to-end Work Breakdown (mirror of the control, with producers)

Each task shows the **human** verification/integration/accountability hours (the real, billable cost) and the **AI** compute cost. `paired` = AI generates, human verifies/integrates; `human` = human performs directly.

### Phase 1 — Setup & Architecture — 8 human hrs / $4 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T1 — **Tailored** solution scaffolding (Web API + WASM + structure) | paired | 2 | $2 |
| T2 — Architecture: layering, DI, DTO strategy, logging base | human | 6 | $2 |

### Phase 2 — Data Layer — 10 human hrs / $6 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T3 — Reverse-engineer Entity + Surveillance schema | paired | 2 | $2 |
| T4 — Repository + service layer (Person, Case, Contact, Assessment) | paired | 6 | $3 |
| T5 — Migrations, seed code/reference data, PHI/PII tags | paired | 2 | $1 |

### Phase 3 — Back-end Web API — 20 human hrs / $10 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T6 — Person endpoints (register, search/dedupe) | paired | 4 | $2 |
| T7 — Assessment/questionnaire endpoints | paired | 4 | $2 |
| T8 — Case endpoints (initiate, severity/disposition, mask-separate) | paired | 4 | $2 |
| T9 — Contact-report endpoint | paired | 2 | $1 |
| T10 — Case-report + export endpoint | paired | 4 | $2 |
| T11 — Reference-data + reporting-criteria endpoints | paired | 2 | $1 |

### Phase 4 — Validation & Configurability — 10 human hrs / $4 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T12 — FluentValidation rules (dates, required codes, dedupe) | paired | 5 | $2 |
| T13 — Configurable triage instrument + reporting criteria | paired | 5 | $2 |

### Phase 5 — Authentication / RBAC / Audit — 14 human hrs / $4 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T14 — JWT auth + role-based access (security-critical) | human | 8 | $2 |
| T15 — Audit trail (accountability-critical) | human | 6 | $2 |

### Phase 6 — Front-end Blazor WebAssembly — 26 human hrs / $12 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T16 — App shell, layout, MudBlazor, auth-ware, responsive scaffold | paired | 3 | $2 |
| T17 — Person registration + search/dedupe page | paired | 5 | $2 |
| T18 — Triage questionnaire + assessment capture UI | paired | 7 | $3 |
| T19 — Case page (initiate, severity, disposition, mask-separate) | paired | 5 | $2 |
| T20 — Case-report review + export page | paired | 4 | $2 |
| T21 — Admin page (reference data, config) | paired | 2 | $1 |

### Phase 7 — Testing — 13 human hrs / $6 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T22 — Unit tests (services, validation, dedupe logic) | paired | 5 | $2 |
| T23 — Integration tests (EF + API) | paired | 4 | $2 |
| T24 — Blazor component tests (bUnit) | paired | 4 | $2 |

### Phase 8 — Documentation — 11 human hrs / $6 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T25 — Architecture / design documentation | paired | 4 | $2 |
| T26 — API documentation (OpenAPI + endpoint reference) | paired | 2 | $2 |
| T27 — Technical reference (data model, configuration) | paired | 2 | $1 |
| T28 — User guide (triage staff workflow) | paired | 3 | $1 |

### Phase 9 — Deployment & Acceptance — 14 human hrs / $3 AI
| Task | Producer | Human hrs | AI cost |
| --- | --- | --- | --- |
| T29 — CI/CD pipeline (GitHub Actions / Azure DevOps) + Docker | paired | 5 | $2 |
| T30 — Environment setup + deployment | paired | 3 | $1 |
| T31 — End-to-end acceptance + sign-off | human | 6 | $0 |

*Note — mandatory-core compliance evidence.* The SBOM + dependency scan (SEC-8), OWASP review (SEC-7), and the traceability matrix (§16) are part of the mandatory core (RFP §18) and are **folded into the testing and acceptance tasks**, not priced as separate line items.

## 5. Requirement → Producer → Effort traceability

Same requirement set, same delivery, with the producer noted.

| Req | Delivered by | Producer | Human hrs |
| --- | --- | --- | --- |
| F1 Register person | T6, T17 | paired | 9 |
| F2 Search / dedupe | T6, T17 | paired | 9 |
| F3 Triage questionnaire | T7, T18 | paired | 11 |
| F4 Assessment answers | T7, T18 | paired | 11 |
| F5 Initiate case | T8, T19 | paired | 9 |
| F6 Mask-and-separate flag | T8, T19 | paired | 9 |
| F7 Severity / disposition | T8, T19 | paired | 9 |
| F8 Record case report | T10, T20 | paired | 8 |
| F9 Contact report | T9 | paired | 2 |
| F10 Review / export report | T10, T20 | paired | 8 |
| F11 Reference-data validation | T11, T21 | paired | 4 |
| F12 Audit the reporting action | T15 | human | 6 |
| NFR1 Privacy / security tagging | T5 | paired | 2 |
| NFR2 Role-based access | T14 | human | 8 |
| NFR3 Deterministic validation | T12, T22–T24 | paired | 18 |
| NFR4 Traceability | T4, T15 | paired + human | 12 |
| NFR5 Responsive UI | T16 | paired | 3 |
| NFR6 Configurable instrument | T13, T21 | paired | 7 |
| NFR7 Audit trail | T15 | human | 6 |

## 6. Definition of done — identical to the control

The **same** acceptance bar as the human-only baseline (§6): works, validated, documented, auditable, traceable, accepted. The pairing meets it with a different division of labour; it does not water it down.

## 7. Effort roll-up and schedule

| Phase | Human hrs | AI cost |
| --- | --- | --- |
| 1 — Setup & Architecture | 8 | $4 |
| 2 — Data Layer | 10 | $6 |
| 3 — Back-end Web API | 20 | $10 |
| 4 — Validation & Configurability | 10 | $4 |
| 5 — Auth / RBAC / Audit | 14 | $4 |
| 6 — Front-end Blazor WASM | 26 | $12 |
| 7 — Testing | 13 | $6 |
| 8 — Documentation | 11 | $6 |
| 9 — Deployment & Acceptance | 14 | $3 |
| **Total** | **126 human hrs** | **$55 AI compute** |

**Schedule (one Senior Developer):** 126 human hrs ÷ ~35 hrs/week ≈ **3.6 weeks (~4 weeks)**. AI generation is fast and runs ahead of the human; the human verification/integration path is the critical path, exactly as in the control but shorter.

## 8. Baseline cost

| Stream | Amount |
| --- | --- |
| Human (Senior Developer) | 126 hrs × $110 = **$13,860** |
| AI compute | $55 |
| AI seat (~$19/mo × ~2 months) | ~$38 |
| **AI-assisted total (nominal)** | **~$13,950** |
| Maintenance & security reserve (§10.4.3) | ~$250 (AI stream ~$30 + human re-validation ~$220) |
| **Budgeted total (incl. reserve)** | **~$14,200** |

Human share of the AI-assisted total: **~99.3%.** The AI is a small accelerator; the human is the real cost because verification, integration, and accountability cannot be automated away. The **maintenance & security reserve** (~$250) is the priced cost of owning the AI coder over the project — a ~25–50% reserve on the AI stream plus 1–2 human re-validation hours per tool/model update — so the "AI is not free" reality (Axiom #3, §10.4.3) is in the budget, not discovered later.

## 9. Head-to-head comparison (human-only vs. AI-assisted)

| Metric | Human-only (control) | AI-assisted | Δ |
| --- | --- | --- | --- |
| Human effort | 234 hrs | 126 hrs | **−46%** |
| Calendar (1 Senior Dev) | ~6.7 weeks | ~3.6 weeks | **−46%** |
| Total cost | $25,740 | ~$13,950 | **−46%** |
| Budgeted total (incl. maintenance & security reserve) | — | ~$14,200 | — |
| AI cost share | — | ~$93 (~0.7%) | added, negligible |
| Definition of done | same | same | none |
| Accountable owner | Senior Dev | Senior Dev | none |

The reduction comes from the AI taking the **generation/drafting** slice. What it does **not** remove is the **verification/integration/accountability** slice — which is why the human remains ~99% of the cost and the quality bar is unchanged.

### 9.1 Schedule with dates

Assumed start: **Monday 2025-06-02** (shift all dates if the start moves). Milestones align to the RFP §17 payment gates:

| Milestone | Gate | Target date |
| --- | --- | --- |
| M1 — Design / architecture complete | 15% | 2025-06-06 (end wk 1) |
| M2 — Working vertical slice | 25% | 2025-06-13 (end wk 2) |
| M3 — All requirements | 30% | 2025-06-20 (end wk 3) |
| M4 — Testing & security | 20% | ~2025-06-24 (end wk 3.5) |
| M5 — Acceptance & handover | 10% | 2025-06-27 (end wk 4) |

The AI compresses the generation slice, but the human verification/integration remains serial on the critical path — the schedule is still single-accountable-owner.

### 9.2 Sensitivity — AI quality (the downside case)

The −46% saving assumes the AI coder's output is accurate enough that verification (not heavy repair) dominates. If AI quality degrades, the human must repair more and the advantage erodes — and can exceed the $19,000 ceiling:

| AI quality | Human hrs | Human cost | AI + reserve | Budgeted total | vs $19,000 |
| --- | --- | --- | --- | --- | --- |
| Good (baseline) | 126 | $13,860 | ~$340 | ~$14,200 | **fits (≈25% headroom)** |
| Moderate (some rework) | ~158 (+25%) | ~$17,325 | ~$340 | ~$17,670 | fits (tight) |
| Poor (heavy rework) | ~189 (+50%) | ~$20,790 | ~$340 | ~$21,130 | **exceeds** |
| Very poor (≈ human-only) | 234 | $25,740 | — | ~$25,740 | **exceeds** |

The blocked-coder handoff (v0.3 §17.4) and the maintenance/security reserve are the controls that catch degradation early. If the AI is not accurate enough, the pairing's value claim collapses toward the human-only baseline — which is exactly why verification, not generation, is the deliverable (Axiom #1).

### 9.3 AI-coding stack options (costed)

The AI-assisted baseline above assumes a low-cost commercial tool at "good" AI quality. For a sensitive public-health domain, the AI-coding stack is a choice along two **independent** dimensions — **model** (commercial vs open-source) and **deployment** (on-cloud vs self-hosted). To compare on equal footing, the two primary options below are both **on-cloud**; self-hosting is a separate deployment variant, not a property of open-source, and is noted after the table.

| | **Option A — Commercial on-cloud (e.g., Claude)** | **Option B — Open-source on-cloud (e.g., DeepSeek, Qwen)** |
| --- | --- | --- |
| Model | commercial, frontier quality | open-source, competitive quality |
| Deployment | vendor cloud (hosting bundled in seat/compute) | your cloud GPU or a managed inference service |
| Human effort | 126 hrs | ~126–158 hrs (competitive on common tasks; more on the hardest) |
| Human cost (× $110) | $13,860 | ~$13,860–$17,325 |
| AI tooling + hosting cost | ~$300 (seat + compute, incl. vendor hosting) | ~$500–$1,000 (cloud GPU hosting) |
| Maintenance & security reserve | ~$250 | ~$250 |
| **Budgeted total** | **~$14,400** | **~$14,600–$18,600** |
| Fits $19,000 ceiling? | yes, ≈24% headroom | yes, ≈2–23% headroom |
| Compliance posture | data to vendor cloud; needs enterprise/BAA terms; may be restricted for PHI | data to your chosen cloud provider (not the model vendor's proprietary cloud); you control residency |

**Self-hosted variant (a separate deployment choice).** Self-hosting is not a property of open-source — it is a deployment decision. For the compliance-maximal case (data never leaves your environment), an open-source model can be self-hosted on-prem or in a FedRAMP-approved cloud you control; the cost is hardware + ops (or the cloud GPU cost) and the data stays fully in-house. It is a deployment choice, not a model choice.

Both on-cloud options fit the $19,000 ceiling. The comparison is apples-to-apples on deployment (both on-cloud): Option A's hosting is bundled into the vendor's seat/compute price, while Option B's hosting is a separate cloud GPU cost. On quality, open-source coding models have closed the gap and can challenge commercial on common and standard tasks; the frontier commercial model's edge is concentrated on the hardest, most complex tasks. For a standard case-management app, Option B is a credible challenger, with the human-verification premium concentrated on the hardest edge cases rather than spread across the build. The tradeoff is the Agency's to weigh; this document does not recommend one.

## 10. Risks and caveats

A full risk register for the delivery is in *SCEN-0001-004*.

- **Same honest caveats as the control:** this is a baseline to be validated, not a guarantee; the estimate moves with schema drift, tooling, and requirement interpretation.
- **The comparison only holds because both baselines share the same WBS and definition of done.** If the control had assumed hand-written boilerplate (no generic scaffolding) or if this baseline had assumed no human verification, the comparison would be false and unfair.
- **The human hours are the bottleneck.** Even though the AI generates fast, one Senior Developer must verify/integrate serially. This baseline assumes the same single accountable owner on the critical path.
- **AI quality is not uniform.** The estimates assume the AI coder's generated artifacts are accurate enough that verification (not heavy repair) dominates. If the AI produces defective output requiring rework, the human hours rise toward the human-only baseline and the saving shrinks — which is precisely the risk the blocked-coder handoff (v0.3 §17.4) is designed to record and control.

### 10.1 Validation plan — how to confirm this baseline

- **Validate the rates.** Confirm the $110/hr human rate and the AI seat/compute against the vendor's current price (§10.4.2) before quoting.
- **Run a pilot.** Deliver one Construction Boundary / sprint and compare actual human + AI hours to the estimate; the cost ledger (v0.3 §3) records actuals.
- **Re-estimate after change.** If the schema, tooling, model, or requirements drift, re-run the WBS before committing.
- **Track actuals vs. baseline.** Feed actuals back each sprint so the baseline becomes a forecast, not a guess.
- **Independent review.** Have a second senior engineer sanity-check the WBS, estimates, and the AI-quality assumption.
- **Hold the reserve.** The maintenance & security reserve (~$250) covers version/security drift; validate the reserve % against observed rework.

## 11. Completeness / quality coverage under the RFP fixed-price ceiling

The RFP (§18) caps the deliverable at **$19,000**. This AI-assisted baseline costs **~126 human hrs + ~$93 AI + ~$250 maintenance & security reserve ≈ ~$14,200**, fitting under the ceiling with **≈$4,800 (≈25%) headroom**. Because generation is cheap, the constrained human verification time stretches further and the headroom is **reinvested in completeness and quality rather than taken as profit**:

| CQ target (§18.1) | Level | What the ~126-hr pairing delivers within $19,000 |
| --- | --- | --- |
| **CQ-1** Requirement coverage | **Full** | All F1–F12, NFR1–NFR7 built, tested, and traced with per-requirement coverage statements (AI drafts, human verifies) |
| **CQ-2** Compliance-surface coverage | **Substantial** | A broader §8–15 compliance matrix; can absorb some priced-option scope (independent pentest, wider FHIR mapping) at little marginal cost |
| **CQ-3** Test completeness | **Substantial** | A broader automated test suite generated and human-verified; edge-case and regression coverage included |
| **CQ-4** Documentation completeness | **Substantial** | Richer architecture/API/technical/user docs produced as a low-cost generation byproduct, then human-verified |
| **CQ-5** Security evidence depth | **Substantial** | SBOM, dependency scan, OWASP review, and penetration-finding remediation, all verifiable |
| **CQ-6** Verifiability | **High** | Fuller evidence pack; an independent reviewer can confirm breadth, not just that it shipped |

**Headline:** within the same $19,000, the pairing returns a more complete compliance matrix and deeper evidence (see the §16 completion-status column), because generation costs little and the scarce human hours go to verification breadth. This is the intended, measurable advantage — completeness and quality, not speed or cents. Compared against the human-only control (§11 of *SCEN-0001-002*, which thins to **partial** outside the core), the pair holds **full/substantial/high** across every CQ target within the same budget.

---
*This baseline is the measured variant. It is intentionally mirror-imaged to the human-only control (same T1–T31, same requirements, same definition of done) so the comparison in §9 is valid and audit-able.*
