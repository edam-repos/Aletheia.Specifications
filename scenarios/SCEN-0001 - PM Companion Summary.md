# SCEN-0001 — PM Companion Summary

**The exercise and its findings, in plain terms.**

| | |
| --- | --- |
| **Prepared for** | Project Managers and stakeholders |
| **Prepared by** | A senior data architect working with an AI teammate |
| **Status** | Illustrative — baselines to be validated, not quotes |
| **Source package** | *SCEN-0001-001* through *-006* (see Document Map, §10) |

---

## 1. What this is

This is a worked exercise that answers a question every PM is now being asked: **is the human + AI-coder pairing real, and is it worth it?** Rather than argue in the abstract, we built a realistic procurement — a CDC-participant agency RFP for a communicable-disease case-management web-app (*-001*) — and priced it two ways:

- **Human-only** (the control): a senior developer delivers the whole thing (*-002*).
- **AI-assisted**: the same senior developer, paired with an AI coder (*-003*).

Both use the **same work breakdown, the same requirements, the same definition of done, and the same $19,000 fixed-price ceiling.** The only difference is who produces each task and how much scaffolding is generic vs. tailored. That makes the comparison fair and audit-able.

## 2. The axioms — the foundation for understanding the human + AI coding team

Six ground rules underpin everything in this package. They hold regardless of tooling, budget, or schedule, and they are the lens through which to read the rest of this summary.

1. **Generation is not delivery.** The value of the pairing is the completeness and quality of what is actually delivered and verified — not speed or cost. AI output is a draft until a human verifies it.
2. **The PM's process is unchanged; the PM's job content is not.** The stages, gates, and discipline stay; the PM gains new responsibilities (managing the AI dependency, tighter acceptance, new cost/risk accounting).
3. **The AI coder is not free.** Accountable AI costs are real, belong in the ledger, and can be high.
4. **More generation means more surface for the hardest problems; verification is where the cost and risk hide.** The more the AI produces, the more the human's verification is the irreducible cost.
5. **The AI coder is a managed third-party dependency, not a static asset.** Versions change, security risks surface, terms shift — price the maintenance and security reserve in.
6. **The human role shifts from producing to verifying and directing.** The developer reviews and directs; the PM's value moves to acceptance rigor and dependency management.

## 3. The honest numbers

| Metric | Human-only | AI-assisted | Δ |
| --- | --- | --- | --- |
| Human effort | 234 hrs | 126 hrs | **−46%** |
| Calendar (1 senior dev) | ~6.7 weeks | ~3.6 weeks | **−46%** |
| Total cost | $25,740 | ~$13,950 | **−46%** |
| Budgeted total (incl. maintenance & security reserve) | — | ~$14,200 | — |
| Fits the $19,000 ceiling? | only after value-engineering to ≈173 hrs | yes, ≈25% headroom | — |

The pairing is roughly **46% faster and cheaper on the baseline.** But that is not the point of the exercise.

## 4. The real finding: the advantage is completeness and quality, not speed or cost

Because generation is cheap, the constrained human verification time stretches further. The AI-assisted model can reinvest its headroom into **more complete, better-tested, better-evidenced delivery** — measured against six completeness/quality targets (CQ-1…CQ-6): requirement coverage, compliance-surface coverage, test completeness, documentation completeness, security evidence depth, and verifiability.

The headline: **generation is not delivery.** The value of the pairing shows up in what is actually completed, tested, documented, and evidenced — not in how fast the code appears. This is the argument in the companion's Axiom #1.

## 5. How the work changes — for the PM and the developer

The pairing does not speed up the same job; it changes the job. This is the part a PM may not see at first, and it is worth being explicit about.

- **The PM's process is unchanged; the PM's job content is not.** The stages, gates, risk register, and sign-off all stay. But the PM gains new responsibilities: managing the AI coder as a dependency, tightening acceptance and verification (because generation is cheap, the volume to verify is higher), and accounting for new cost and risk lines (AI compute/seats, a maintenance & security reserve, and AI-quality risk).
- **The developer's role shifts from producing to verifying and directing.** Less writing of boilerplate, more reviewing AI output at volume, decomposing work for the AI, and catching the hard-to-find bugs that generation surfaces.

The human is not doing less — they are doing different, higher-leverage work. This is the companion's Axiom #2 and #6.

## 6. AI is not free, and it must be managed

The AI coder is not a static asset. It is a **managed third-party dependency** with accountable costs in the ledger and a **maintenance & security reserve** (~$250) for version drift, newly-identified CVEs, provider/terms changes, and behavior drift. The human remains the accountable owner (~99% of the cost); the AI is the accelerator.

The AI-coding tooling itself is a choice with cost and compliance implications: a hosted commercial model (e.g., Claude) is frontier quality but sends data to a vendor cloud (hosting bundled in the price); an open-source model (e.g., DeepSeek, Qwen) is competitive on common tasks and can run on your cloud GPU or self-hosted — with a separate hosting cost, and data to your chosen provider (or fully in-house if self-hosted). Both are costed as options in *-003* §9.3; the choice is the Agency's to make with its security/compliance team.

## 7. Tech-stack notes for the PM

A few things to keep in mind about the AI-coding stack (both options):

- **The AI-coding stack is a separate decision from the application stack.** The app is Blazor WASM + ASP.NET Core + SQL Server; the AI-coding tooling is a distinct choice with its own cost and compliance implications.
- **It is a compliance decision as much as a cost decision.** For a sensitive public-health domain, the choice of model (commercial vs open-source) and deployment (on-cloud vs self-hosted) is the Agency's to make with its security/compliance team — not the PM's alone.
- **The choice moves two cost lines.** It changes the AI tooling + hosting cost *and* the human verification hours (via AI quality). Both options fit the $19k ceiling, but with different headroom.
- **Option A (commercial on-cloud, e.g., Claude):** frontier quality, less human verification on the hardest tasks; data goes to the vendor's cloud — needs enterprise/zero-data-retention terms and possibly a BAA; may be restricted for PHI.
- **Option B (open-source on-cloud, e.g., DeepSeek, Qwen):** competitive on common tasks; data goes to your chosen cloud provider (you control residency); cost is cloud GPU hosting (the model itself is free). Self-hosting is a separate deployment variant for the data-never-leaves case.
- **Either way, the AI tooling is a managed dependency** — version drift, security CVEs, and terms changes — covered by the maintenance & security reserve.

## 8. The key risk: AI quality

The −46% saving assumes the AI's output is accurate enough that verification (not heavy repair) dominates. If AI quality degrades, the advantage erodes — and can exceed the budget:

| AI quality | Budgeted total | vs $19,000 |
| --- | --- | --- |
| Good (baseline) | ~$14,200 | fits (≈25% headroom) |
| Moderate (some rework) | ~$17,670 | fits (tight) |
| Poor (heavy rework) | ~$21,130 | **exceeds** |
| Very poor (≈ human-only) | ~$25,740 | **exceeds** |

This is why verification, not generation, is the deliverable — and why the pairing must be run with the same discipline as any other delivery.

## 9. Recommendation

Adopt the **Senior Developer + AI Coder pairing** for the mandatory core: it meets the same definition of done, fits the same $19,000 budget, and returns a measurably more complete, higher-quality, better-evidenced deliverable. The human remains the accountable owner; the AI is the accelerator. This is the honest, defensible position for a skeptical stakeholder.

## 10. Document map

| Doc | What it is | Use it to |
| --- | --- | --- |
| *-001* | The RFP scenario | See the real procurement: requirements, security, budget, evaluation |
| *-002* | Human-only baseline | See the control: WBS, effort, cost, schedule |
| *-003* | AI-assisted baseline | See the pairing: WBS with producers, cost, reserve, sensitivity |
| *-004* | Risk register | See the concerns and mitigations |
| *-005* | One-page comparison | Get the executive summary for finance/leadership |
| *-006* | Deliverable register & acceptance checklist | See how acceptance is gated on evidence |

---
*Illustrative, not a quote. Validate rates, AI pricing, and infrastructure against current market before committing. See the source documents for full detail.*
