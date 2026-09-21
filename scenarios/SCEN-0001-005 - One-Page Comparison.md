# SCEN-0001-005 — One-Page Comparison (for Finance & Leadership)

| | |
| --- | --- |
| **Scenario** | SCEN-0001 — Communicable-Disease Case-Management Web-App (Initial Prototype) |
| **Type** | Executive summary of the two delivery models, for finance and leadership |
| **Status** | Illustrative — baselines to be validated, not quotes |
| **Author** | Senior Data Architect (with AI teammate), framed for a Project Manager |
| **Sources** | *SCEN-0001-001* (RFP), *-002* (human-only), *-003* (AI-assisted), *-004* (risk register), *-006* (deliverable register & acceptance) |

---

## The decision in one line
Both delivery models fit under the **$19,000 fixed-price ceiling** for the **mandatory core**. The AI-assisted pairing is not meaningfully cheaper or faster at the headline — its real, defensible advantage is **completeness and quality**: it delivers more of the requirement and compliance surface, deeper test and security evidence, and a fuller traceability matrix, for the same budget.

## Head-to-head (mandatory core)

| Metric | Human-only | AI-assisted | Δ |
| --- | --- | --- | --- |
| Human effort | 234 hrs | 126 hrs | **−46%** |
| Calendar (1 Senior Dev) | ~6.7 weeks | ~3.6 weeks | **−46%** |
| Total cost | $25,740 | ~$13,950 | **−46%** |
| Budgeted total (incl. maintenance & security reserve) | — | ~$14,200 | — |
| AI cost share | — | ~$93 (~0.7%) | added, negligible |
| Fits $19,000 ceiling? | only after value-engineering to ≈173 hrs | yes, ≈25% headroom | — |
| **Completeness/quality (CQ-1…6)** | **Full core / Partial surface** | **Full / Substantial / High** | **the differentiator** |
| Definition of done | same | same | none |
| Accountable owner | Senior Dev | Senior Dev | none |

## The differentiator is completeness and quality, not speed or cost
- **Human-only** conforms to $19k only by thinning the discretionary compliance and evidence surface (CQ-2…CQ-6 **partial**).
- **AI-assisted** reinvests its ≈25% headroom into **full/substantial/high** coverage across every CQ target — because generation is cheap and the scarce human hours go to verification breadth.
- This is **Axiom #1**: *generation is not delivery.* The human remains ~99% of the cost and the accountable owner in both models.
- The pairing does not speed up the same job — it changes it: the developer shifts from producing to verifying/directing, and the PM's job gains new responsibilities (managing the AI dependency, tighter acceptance, new cost/risk accounting). See the PM Companion Summary §4.

## Budget reconciliation
- **$19,000** covers the **mandatory core** (F1–F12, NFR1–NFR7 + baseline evidence: RBAC/audit, validation, core tests, core docs, deployment/CI-CD, SBOM + dependency scan, OWASP review, traceability matrix).
- **Priced options** outside the ceiling: full FHIR/exchange, independent penetration test, full Section 508, full training, extended warranty, and **infrastructure/hosting** (~$100–$300/mo prototype; ~$500–$1,500/mo production).

## The AI coder is not free (and must be managed)
- Seats + compute range from ~$10–$40/mo (assistant) to ~$200–$600+/mo (agentic), plus metered compute.
- Hold a **maintenance & security reserve** (~25–50% of the AI stream + 1–2 human re-validation hrs per tool/model update) for version drift, newly-identified CVEs, provider/terms changes, and behavior drift (§10.4.3). **This is now priced into the AI-assisted baseline** (~$250 → budgeted total ~$14,200, still ≈25% under the $19k ceiling).
- **If AI quality degrades, the saving erodes and can exceed the $19k ceiling** — see the sensitivity in *-003* §9.2 (moderate rework → ~$17,670; poor → ~$21,130).

## Top risks (see -004)
1. **AI quality / completeness shortfall** — the pairing's value rests on verification (R-AI-4, R-P-8).
2. **Budget** — the $19k ceiling is the binding constraint (R-P-1, R-H-2).
3. **AI coder as a managed dependency** — version/security drift, priced via the reserve (R-AI-1, R-AI-2).
4. **Compliance shortfall** — acceptance is gated on the traceability matrix and CQ evidence (R-P-5).

## Recommendation
Adopt the **Senior Developer + AI Coder pairing** for the mandatory core: it meets the same definition of done, fits the same $19,000 budget, and returns a measurably more complete, higher-quality, better-evidenced deliverable. The human remains the accountable owner; the AI is the accelerator. This is the honest, defensible position for a skeptical stakeholder.

The **deliverable register and acceptance checklist** is in *SCEN-0001-006*.

---
*Illustrative, not a quote. Validate rates, AI pricing, and infra against current market before committing. See the source documents for full detail.*
