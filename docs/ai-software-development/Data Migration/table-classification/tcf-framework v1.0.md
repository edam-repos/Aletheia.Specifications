# Table Classification Framework

**Document ID:** TCF-001
**Version:** 1.0 (Release Candidate)
**Status:** Pending Approval
**Classification:** Internal
**Supersedes:** Table Classification Framework v0.1 (Draft)
**Owner:** Data Migration Governance Lead
**Review Cycle:** Annually, and upon any material change to scoring, rules, or taxonomy

---

## Document Control

| Version | Date | Author | Summary of Change |
|---|---|---|---|
| 0.1 | — | — | Initial draft: single-label categories, preliminary confidence bands |
| 1.0 | — | — | Multi-dimensional model (role, disposition, tags), decision cascade, weighted confidence, dependency-derived phasing, calibration and governance requirements |

**Approval**

| Role | Name | Signature | Date |
|---|---|---|---|
| Framework Owner | | | |
| Data Governance Lead | | | |
| Security / CJIS Compliance Representative | | | |
| Business Sponsor | | | |

---

## Table of Contents

1. [Purpose](#1-purpose)
2. [Scope and Applicability](#2-scope-and-applicability)
3. [Guiding Principles](#3-guiding-principles)
4. [Roles and Responsibilities](#4-roles-and-responsibilities)
5. [Classification Model](#5-classification-model)
6. [Evidence Model](#6-evidence-model)
7. [Decision Cascade](#7-decision-cascade)
8. [Confidence Scoring](#8-confidence-scoring)
9. [Disposition and Retirement Safeguards](#9-disposition-and-retirement-safeguards)
10. [Migration Phase Derivation](#10-migration-phase-derivation)
11. [Review and Approval Workflow](#11-review-and-approval-workflow)
12. [Calibration and Validation](#12-calibration-and-validation)
13. [Governance and Change Control](#13-governance-and-change-control)
14. [Classification Record Specification](#14-classification-record-specification)
- [Appendix A: Worked Examples](#appendix-a-worked-examples)
- [Appendix B: Domain Keyword Libraries](#appendix-b-domain-keyword-libraries)
- [Appendix C: Platform Evidence Queries](#appendix-c-platform-evidence-queries)
- [Appendix D: Glossary](#appendix-d-glossary)
- [Appendix E: Pre-Publication Review Checklist](#appendix-e-pre-publication-review-checklist)

---

## 1. Purpose

This framework defines a repeatable, evidence-based, auditable method for classifying tables in source-system databases during migration, modernization, consolidation, and data inventory efforts.

It is designed to:

- Reduce manual analysis effort by automating classification where evidence is strong.
- Direct subject matter expert (SME) attention to ambiguous, high-risk, and business-critical tables.
- Identify migration priorities, retirement candidates, and archive candidates.
- Reveal explicit and hidden dependency chains.
- Derive migration sequencing from dependency structure rather than assumption.
- Produce consistent, comparable results across multiple source systems.
- Preserve a defensible audit trail of every classification and every human override.

## 2. Scope and Applicability

### 2.1 In Scope

- Relational database tables in source systems targeted for migration or inventory.
- Systems of any domain. Primary target domains: Records Management (RMS), Computer-Aided Dispatch (CAD), and Jail/Corrections Management (JMS). The method also applies to ERP, CRM, and other operational databases.
- Table-level classification. Column-level classification is out of scope, except where column evidence informs table classification or tagging.

### 2.2 Out of Scope

- Target-system schema design and field mapping.
- Data transformation specifications.
- Non-relational stores, file shares, and message queues (except where referenced by an Attachment or Integration table).

### 2.3 Application Across Systems

The classification **method** (taxonomy, cascade, scoring, governance) is system-agnostic. The **evidence libraries** (naming patterns, canonical entities, domain keywords) are domain-specific and are maintained per domain (see [Appendix B](#appendix-b-domain-keyword-libraries)). Consistency across systems is achieved by assigning each table a **Canonical Entity** (Section 5.5).

## 3. Guiding Principles

| # | Principle | Implication |
|---|---|---|
| P1 | **Evidence over assumption** | Every classification cites the evidence and rules that produced it. |
| P2 | **Separate what a table *is* from what we *do* with it** | Functional role and disposition are independent attributes. |
| P3 | **Conservative on irreversible actions** | Retirement and non-migration always require human sign-off. |
| P4 | **Declared structure is incomplete** | Legacy systems hide dependencies in code, views, and convention; evidence must go beyond declared foreign keys. |
| P5 | **Measure, don't assert** | Automation rates and confidence thresholds are validated empirically (Section 12). |
| P6 | **Regulatory obligations override usage** | Retention, CJIS, evidentiary, and privacy requirements apply regardless of how often a table is used. |
| P7 | **Reproducible and versioned** | Identical inputs and framework version yield identical outputs. |

## 4. Roles and Responsibilities

| Role | Responsibilities |
|---|---|
| **Framework Owner** | Maintains this document, approves changes, owns calibration results. |
| **Classification Engineer** | Collects evidence, runs the cascade, produces classification records, maintains keyword libraries. |
| **Subject Matter Expert (SME)** | Reviews assigned tables, confirms or overrides classification with documented reasons. |
| **Business Data Owner** | Accountable for disposition of tables in their subject area; sole authority to sign off on retirement or non-migration. |
| **Records / Compliance Officer** | Assigns retention and sensitivity tags; confirms regulatory constraints on disposition. |
| **Migration Architect** | Consumes classifications and derived phases for migration planning. |

**RACI (summary)**

| Activity | Framework Owner | Class. Engineer | SME | Data Owner | Compliance | Architect |
|---|---|---|---|---|---|---|
| Evidence collection | I | R/A | C | I | I | I |
| Automated classification | I | R/A | I | I | I | I |
| Low-confidence / Unknown review | I | C | R | A | I | I |
| Retirement sign-off | I | C | C | R/A | C | I |
| Retention / sensitivity tagging | I | C | C | C | R/A | I |
| Phase derivation | I | R | I | I | I | A |
| Framework change | A/R | C | C | C | C | C |

*R = Responsible, A = Accountable, C = Consulted, I = Informed.*

---

## 5. Classification Model

Each table receives a **multi-dimensional classification**. A single label cannot describe a table accurately, because role, handling, and regulatory sensitivity are independent concerns.

| Dimension | Cardinality | Purpose |
|---|---|---|
| **Functional Role** | Exactly one | What the table is |
| **Disposition** | Exactly one | What will be done with it |
| **Tags** | Zero or more | Cross-cutting attributes |
| **Subject Area / Canonical Entity** | One each | Grouping and cross-system alignment |

### 5.1 Functional Role

| Role | Definition | Typical Indicators | Examples |
|---|---|---|---|
| **Foundation** | Persistent core business entity that exists independently of any single event and is referenced by many tables. | Surrogate or natural entity key, no event date, high inbound references, identity-management needs, slow-to-moderate growth. | Person, Officer, Agency, Location, Vehicle, Organization |
| **Reference** | Lookup or code table that standardizes values. | Code + description (+ active/sort columns), low volume relative to referencing tables, rarely updated, high inbound references. | Race, State, VehicleColor, OffenseCode, ChargeCode |
| **Transaction** | Business event or activity record. | Event date/time column, growth tracks operational activity, references Foundation tables, user-originated. | Incident, Arrest, Citation, Warrant, Booking, CallForService |
| **Association** | Junction table resolving a many-to-many relationship. | Two or more FKs forming the key, few non-key columns, optional role/type attributes. | IncidentPerson, ArrestCharge, VehicleOwner |
| **Detail** | Child table that elaborates a single parent record and has no independent existence. | Dedicated parent FK, no independent event date, row count proportional to parent. | IncidentNarrative, IncidentProperty, ArrestRemark |
| **History/Audit** | Snapshots, change logs, or workflow trails supporting auditability. | Mirrors another table's columns plus change metadata (who/when/old/new), append-only, rarely read. | AuditHistory, StatusHistory, WorkflowHistory |
| **Attachment** | Stores or references non-relational content. | BLOB/LOB columns, file paths or URLs, content-type and size columns, large storage footprint. | Documents, Images, Audio, Video, ScannedForms |
| **Integration** | Supports interfaces between systems. | Queue/status/retry columns, external identifiers, message payloads, sync timestamps. | ExportQueue, InterfaceLog, ExternalReference, SyncStatus |
| **System/Config** | Application configuration, security, preferences, or scheduling. | Key/value or settings shape, user/role/permission semantics, small and application-managed. | UserSettings, ApplicationConfig, JobSchedule |
| **Derived** | Summary, cache, or denormalized table computed from other tables. | Aggregates, rebuild jobs, snapshot dates, content reproducible from sources. | DailyIncidentSummary, SearchIndex, ReportCache |
| **Staging** | Temporary processing or data-loading table. | Import/ETL/work naming, no inbound references, reloadable content. | ImportPerson, ETLStaging, MigrationWork |
| **Unknown** | Evidence is insufficient or conflicting. | Ambiguous content, heavy customization, poor documentation. | — |

> **Design note.** `Archive` and `Retire`, used in v0.1 as categories, are **dispositions** in v1.0. A table that holds data removed from operational processing keeps its functional role (for example, Transaction) and receives the disposition *Archive*.

### 5.2 Disposition

| Disposition | Definition | Sign-off Required |
|---|---|---|
| **Migrate** | Migrate as-is, subject to standard mapping. | No (auto-acceptable per Section 8) |
| **Migrate + Transform** | Migrate with restructuring, cleansing, de-duplication, or master-data consolidation. | Spot review |
| **Archive** | Preserve read-only outside the target operational system (archive store, data lake, or cold storage). | Data Owner |
| **Rebuild** | Do not migrate; regenerate in the target system from migrated sources. | Architect |
| **Do Not Migrate** | No migration. Includes retirement candidates and staging tables. | **Data Owner and Compliance (mandatory)** |
| **Review** | Disposition cannot be proposed without human judgment. | SME |

### 5.3 Tags

Tags are not mutually exclusive and drive review routing and confidence caps.

| Tag | Meaning | Source of Truth |
|---|---|---|
| **PII** | Contains personally identifiable information. | Content analysis + Compliance |
| **CJIS-Sensitive** | Contains or may contain Criminal Justice Information. | Compliance |
| **Retention-Regulated** | Subject to statutory or policy retention. | Records Officer |
| **Evidentiary** | Supports chain of custody, evidence integrity, or legal proceedings. | Data Owner |
| **Business-Critical** | Designated critical by stakeholders. | Data Owner |
| **BLOB** | Contains large-object columns. | Structural analysis |
| **Master-Data Candidate** | Likely to require de-duplication or identity resolution. | Classification Engineer |
| **Customized** | Vendor-modified or locally extended structure. | Structural analysis + SME |
| **Feature-Dormant** | Empty or unused but belongs to a licensed or configurable feature module. | Content + usage analysis |

### 5.4 Subject Area

Every table is assigned a Subject Area (for example: Person, Incident, Property/Evidence, Arrest/Booking, Dispatch, Court, Administration). Subject Areas define the **migration cohorts** in Section 10.

### 5.5 Canonical Entity

Every table is mapped, where applicable, to a **Canonical Entity** from the enterprise entity catalog. This enables cross-system consistency; for example, the RMS name master and the JMS inmate-person table both map to canonical entity **Person**. Tables with no canonical equivalent record `None`.

---

## 6. Evidence Model

Evidence is gathered across seven families. Each classification record must declare which families were available. Missing evidence reduces confidence (Section 8.4).

### 6.1 Structural Evidence

- Schema and table names; naming-convention prefixes and suffixes.
- Primary keys, unique constraints, indexes.
- Declared foreign keys (inbound and outbound).
- Column count; ratio of key columns to non-key columns.
- Presence of audit columns (`CreatedDate`, `ModifiedDate`, `CreatedBy`, `ModifiedBy`).
- Presence of BLOB/LOB or file-reference columns.

### 6.2 Content Evidence

- Column names, data types, and nullability.
- Business meaning confirmed by documentation or SMEs.
- Sample data values (profiled under applicable privacy controls).
- Presence of event date/time columns.
- Lookup shape: code, description, active flag, sort order.

### 6.3 Dependency Evidence

Dependencies are recorded with their **provenance**.

| Provenance | Source |
|---|---|
| **Declared** | Enforced foreign keys |
| **Code-referenced** | Views, stored procedures, functions, triggers, packages |
| **Cross-database** | References from other databases, linked servers, or synonyms |
| **Application-referenced** | ORM mappings, application code, reports, ETL jobs, interfaces |
| **Inferred** | Column-name matching, data-type compatibility, and value-overlap analysis between candidate key columns |

Inferred dependencies carry a lower weight and are flagged for confirmation when they influence phase derivation.

### 6.4 Volume Evidence

- **Exact row count.** Use `COUNT(*)`, not catalog statistics, which may be stale.
- Storage footprint (data, index, LOB).
- Growth rate (rows per month, over the available history).
- **Parent-to-child row ratio** (rows in table ÷ rows in parent), a stronger signal than absolute volume.
- Relative volume of the table compared with the tables that reference it.

Volume tiers are **hints**, not rules:

| Tier | Indicative Range | Notes |
|---|---|---|
| Empty | 0 | Always check for Feature-Dormant before concluding obsolescence |
| Low | 1–1,000 | Typical of Reference and System/Config |
| Medium | 1,001–1,000,000 | Wide range; use ratio and growth to interpret |
| High | 1,000,001+ | Typical of Transaction, History/Audit, Attachment |

Reference tables such as postal codes or offense statutes may legitimately exceed Low volume. Tiers must never override structural or content evidence.

### 6.5 Usage Evidence

- Last data modification (from audit columns or change tracking).
- Index usage statistics (note: reset on instance restart; record the observation window).
- Query Store, trace, audit logs, or application logs where available.
- ETL jobs, scheduled tasks, and report references.
- Application and user access frequency.

Absence of usage evidence is **not** evidence of non-use. Where usage telemetry is unavailable or the observation window is shorter than 12 months, usage is marked *Insufficient*.

### 6.6 Data Quality Evidence

Collected because it affects migration effort and disposition (for example, Migrate vs. Migrate + Transform):

- Null rate by column.
- Orphan rows (declared or inferred FK violations).
- Duplicate rate on candidate natural keys.
- Format inconsistencies and invalid values.

### 6.7 Governance Evidence

- Business owner.
- Retention schedule and legal hold status.
- Sensitivity and CJIS designation.
- Vendor documentation and data dictionaries.

---

## 7. Decision Cascade

### 7.1 Method

1. All rules in Table 7.2 are **evaluated** for every table.
2. The **first rule that matches**, in order, determines the candidate Functional Role (and, where stated, disposition).
3. All other rules that also match are recorded as **competing matches**; a strong competing match is a conflict (Section 8.5).
4. Every rule evaluation (matched or not) is logged in the classification record under *Rules Triggered*.
5. If no rule from R1–R12 matches, rule R13 assigns **Unknown**.

Rule thresholds in the table below are **initial values**. They are tuned during calibration (Section 12) and versioned (Section 13).

### 7.2 Rules

| ID | Rule | Condition | Result |
|---|---|---|---|
| **R1** | Staging | Name matches staging/temp patterns (`Import*`, `Temp*`, `Tmp*`, `ETL*`, `Work*`, `*_stg`) **and** no inbound references from tables, code, or application | Role: Staging. Disposition: Do Not Migrate (sign-off required) |
| **R2** | Retirement candidate | 0 rows **and** no inbound references (declared, code, application) **and** no modification activity in the observation window **and** not Feature-Dormant | Disposition: Do Not Migrate (retirement candidate; sign-off required). Role determined by remaining rules |
| **R3** | Attachment | BLOB/LOB column or file-path/URL column present, with content-type, file-name, or size attributes | Role: Attachment. Tag: BLOB |
| **R4** | Integration | Queue/status/retry/message columns, or external-system identifiers, or naming such as `*Queue`, `*InterfaceLog`, `*Sync*` | Role: Integration. Disposition: Review |
| **R5** | System/Config | Key/value or settings structure, or user/role/permission semantics, or scheduler semantics; low volume; application-managed | Role: System/Config. Disposition: Review |
| **R6** | History/Audit | Mirrors another table's columns plus change metadata (changed-by, changed-at, action, old/new values), append-only pattern | Role: History/Audit |
| **R7** | Association | Two or more outbound FKs (declared or inferred) forming the primary or unique key, and non-key columns ≤ threshold (initial: 5) | Role: Association |
| **R8** | Reference | Lookup shape (code + description, optional active/sort) **and** (row count ≤ threshold, initial 5,000) **and** high inbound references **and** low update rate | Role: Reference |
| **R9** | Derived | Aggregate or snapshot columns, rebuild job evidence, or content reproducible from other tables | Role: Derived. Disposition: Rebuild (proposed) |
| **R10** | Foundation | Persistent entity, **no** event date column, inbound references from ≥ 3 distinct tables (initial), entity growth not tied to event volume | Role: Foundation. Tag: Master-Data Candidate (if identity resolution applies) |
| **R11** | Transaction | Event date/time column present, growth tracks activity, outbound references to Foundation tables | Role: Transaction |
| **R12** | Detail | Dedicated parent FK (declared or inferred), no independent event date, row count proportional to parent | Role: Detail |
| **R13** | Unknown | None of the above matched, or evidence insufficient | Role: Unknown. Disposition: Review |

### 7.3 Discriminating Foundation from Transaction

The number of inbound references alone does **not** distinguish Foundation from Transaction, because Transaction tables such as Incident are also widely referenced by child tables. Use these discriminators in combination:

| Discriminator | Foundation | Transaction |
|---|---|---|
| Persists independently of events | Yes | No (is the event) |
| Has an event date/time | No | Yes |
| Growth correlates with operational activity | Weak | Strong |
| References other Foundation tables | Rarely (Reference only) | Typically |
| Referenced by child/detail/association tables | Yes | Yes (not a discriminator) |

### 7.4 Disposition Defaults

Unless a rule specifies otherwise, disposition defaults to **Migrate**. It is promoted to **Migrate + Transform** when Data Quality evidence (6.6) exceeds configured thresholds or when the Master-Data Candidate tag applies. The disposition **Archive** is proposed (never auto-assigned) when a table has Low usage, High volume, and a History/Audit role or an equivalent archived-data naming pattern.

---

## 8. Confidence Scoring

### 8.1 Purpose

The confidence score expresses how strongly the available evidence supports the assigned Functional Role. It routes each table to automatic acceptance, spot review, or mandatory review. It is **not** a probability until calibrated (Section 12).

### 8.2 Scoring Rubric

The score is computed for the assigned Role out of 100 points across five evidence dimensions.

| Dimension | Max Points | Scoring Guidance |
|---|---|---|
| **Structure** | 25 | Full points when keys, FK topology, and column shape strongly match the assigned role's indicators; partial points proportional to indicators matched. |
| **Content / Naming** | 25 | Full points when column semantics, naming conventions, and sampled values agree with the role; reduced when naming is ambiguous or generic. |
| **Dependencies** | 20 | Full points when declared and code-referenced dependencies corroborate the role; reduced when relying on inferred dependencies only. |
| **Volume** | 10 | Full points when row count, growth, and parent ratio fit the role profile. |
| **Usage** | 20 | Full points when modification and access evidence is current and consistent with role; zero when Insufficient (see 8.4). |

### 8.3 Penalties

| Condition | Penalty |
|---|---|
| Each conflicting signal (Section 8.5) | −10 (maximum −30) |
| Competing rule match with ≥ 70% of the winning rule's evidence | −10 |
| Stale statistics detected (catalog count differs from exact count by > 20%) | −5 |
| Customized tag applied | −5 |

The score is floored at 0.

### 8.4 Evidence Coverage

Evidence coverage is the share of the 100 points whose dimensions had **available** evidence.

- Dimensions with unavailable evidence contribute 0 points to the raw score.
- If coverage is **below 70%**, confidence is **capped at 69** (Low).
- If coverage is **70–89%**, confidence is **capped at 89** (Medium).

This prevents high confidence from being reached through partial evidence.

### 8.5 Conflicting Evidence (Defined)

A conflict exists when any of the following occurs:

| Code | Conflict |
|---|---|
| C1 | Name or convention implies one role; structure or content implies another (for example, name suggests Reference but table holds 2M rows). |
| C2 | Two or more cascade rules match with materially different roles. |
| C3 | Declared and inferred dependencies disagree. |
| C4 | Usage evidence indicates activity on a table that rules classify as Staging or Retirement candidate. |
| C5 | SME or stakeholder designation contradicts the computed classification. |
| C6 | Stale catalog statistics materially differ from exact measurements. |

### 8.6 Confidence Bands and Routing

Initial thresholds (subject to calibration):

| Band | Score | Routing |
|---|---|---|
| **High** | 90–100 | Auto-accept (subject to caps below) |
| **Medium** | 70–89 | Accept with sampled spot review (initial rate: 10–15%) |
| **Low** | 0–69 | Mandatory SME review |

### 8.7 Mandatory Review Overrides (Caps)

Regardless of score, the following **cannot be auto-accepted** and require named human approval:

| Condition | Required Approver |
|---|---|
| Disposition = Do Not Migrate (including R1 and R2 results) | Data Owner **and** Compliance |
| Tag = Business-Critical | Data Owner |
| Tag = Retention-Regulated, Evidentiary, or CJIS-Sensitive with disposition other than Migrate | Compliance |
| Functional Role = Unknown | SME |
| Any unresolved conflict | SME |
| Tables on a stakeholder-designated review list | As designated |

Where a cap applies, the displayed confidence score is retained, but the review status is *Pending Review*.

---

## 9. Disposition and Retirement Safeguards

Retirement and non-migration are the only classification outcomes that can destroy value. The following safeguards are mandatory.

1. **No automatic retirement.** R1 and R2 only produce *candidates*.
2. **Feature-Dormant check.** Before accepting R2, the engineer must determine whether the table belongs to a module that is licensed, configurable, or planned for use. If so, apply the Feature-Dormant tag and change the disposition to Review.
3. **Code and application scan.** Confirm no references exist in views, stored procedures, triggers, reports, ETL jobs, interfaces, and application code.
4. **Regulatory check.** Compliance confirms that no retention, legal hold, evidentiary, or CJIS requirement applies. Where one applies, the disposition becomes **Archive**, not Do Not Migrate.
5. **Business owner sign-off.** The Data Owner records approval, reason, and date in the Decision Log.
6. **Reversibility.** Source data for retirement candidates is retained (backup or archive) for a period set by Compliance after go-live. Retirement is recorded in the decision log with the retention period.
7. **Post-go-live exception process.** A defined path exists to restore or migrate a retired table after cutover.

---

## 10. Migration Phase Derivation

### 10.1 Principle

**Phase is derived from dependency structure; category adjusts it but does not dictate it.** In v0.1, fixed phases per category created inversions (for example, Foundation tables in Phase 1 referencing Reference tables in Phase 1 or 2). In v1.0, load order follows the dependency graph.

### 10.2 Procedure

1. **Build the dependency graph.** Nodes are tables. Directed edges run from a dependent table to the table it references. Include declared, code-referenced, and (when confirmed) inferred edges. Record provenance on each edge.
2. **Handle cycles explicitly.**
   - Collapse each strongly connected component (cycle) into a single *load unit*.
   - For self-referencing tables (for example, hierarchical Agency), load in two passes (rows first, parent references second) or defer the constraint.
   - Document every cycle and its resolution strategy.
3. **Assign Dependency Depth.** Depth is the longest path from the table (or load unit) to a table with no outbound dependencies. Tables with no outbound dependencies are Depth 0.
4. **Assign Base Wave.** Waves are numbered in ascending Depth order; a table cannot be in an earlier wave than any table it references.
5. **Group into Subject Area Cohorts.** Within the dependency order, tables of the same Subject Area are grouped so that each incremental release delivers a coherent, usable unit.
6. **Apply Role Adjustments** (below) and re-validate that no dependency order is violated.
7. **Record the derived Phase** and the rationale in the classification record.

### 10.3 Role Adjustments

| Role | Adjustment |
|---|---|
| Reference | Placed in the earliest wave required by its dependents; never later than the first wave that references it. |
| Foundation | Placed after the Reference tables it depends on and before the Transactions that reference it. |
| Association | Always after both (or all) parent tables. |
| Attachment | Deferred to a late wave; metadata rows migrate with the parent, content migrates in bulk. |
| History/Audit | Deferred to a late wave unless required for go-live; may be Archive. |
| Integration, System/Config | Planned with interface and configuration workstreams; not sequenced by data dependency alone. |
| Derived | Rebuilt after source tables are loaded. |
| Staging, Do Not Migrate | No phase assigned. |

### 10.4 Phase Output

| Field | Description |
|---|---|
| Dependency Depth | Integer, with load-unit identifier where cycles exist |
| Inbound / Outbound Counts | Counts by provenance |
| Cohort | Subject Area cohort identifier |
| Derived Phase | Wave number after adjustments |
| Phase Rationale | Depth, cohort, adjustment applied |

---

## 11. Review and Approval Workflow

```text
Evidence Collection
        │
        ▼
Rule Cascade + Scoring
        │
        ▼
Routing ─────────────┬────────────────┬─────────────────┐
                     │                │                 │
                High (≥90)       Medium (70–89)     Low (<70) /
                     │                │             Unknown / Conflict /
                     │                │             Mandatory Review (8.7)
                     ▼                ▼                 ▼
              Auto-accept        Spot review        SME review
                     │                │                 │
                     └────────────────┴────────┬────────┘
                                               ▼
                                   Disposition sign-off
                                   (where required)
                                               │
                                               ▼
                                   Decision Log + Baseline
```

### 11.1 Review Status Values

`Auto-Accepted` · `Spot-Reviewed (Confirmed)` · `Spot-Reviewed (Overridden)` · `Pending Review` · `SME-Confirmed` · `SME-Overridden` · `Pending Sign-Off` · `Signed Off`

### 11.2 Override Handling

- An SME override replaces the computed value, retains the original computed value, and requires a documented reason.
- Overrides are entered in the Decision Log (Section 13.3).
- Overrides feed back into calibration (Section 12.4).

### 11.3 Review Service Levels

Targets are set by the program and recorded in the project plan. Pending reviews that block derived phases must be escalated to the Business Data Owner.

---

## 12. Calibration and Validation

The framework's thresholds and automation rate are **hypotheses until validated.** This section defines how they are established.

### 12.1 Pilot

Before production use on a source system:

1. Select a **stratified random sample** of **100–200 tables** (or ≥ 20% of tables for smaller systems), stratified by schema, volume tier, and preliminary Functional Role.
2. Include all tables carrying PII, CJIS-Sensitive, Retention-Regulated, or Evidentiary tags.
3. SMEs independently classify the sample (ground truth) without seeing computed results.
4. Run the cascade and scoring; compare against ground truth.

### 12.2 Metrics

| Metric | Definition | Initial Target |
|---|---|---|
| Role accuracy (overall) | Correct roles ÷ sampled tables | ≥ 90% |
| Precision / Recall (per role) | Standard definitions, reported per Functional Role | ≥ 85% each |
| High-band accuracy | Accuracy among tables scored ≥ 90 | ≥ 97% |
| Retirement false-positive rate | Tables proposed for retirement that SMEs reject | Reported; every miss analyzed |
| Automation rate | Share of tables auto-accepted or spot-review-only | Reported (not asserted in advance) |
| Review load | Tables routed to mandatory SME review | Reported |

### 12.3 Acceptance Gate

A source system may proceed to production classification only when:

- High-band accuracy meets target.
- No retirement candidate in the sample was a true business-critical or regulated table without being caught by safeguards.
- Rule thresholds and score weights are re-tuned and re-versioned if targets are missed.

### 12.4 Continuous Monitoring

- Track overrides by rule and by role; any rule with an override rate above 15% is reviewed.
- Re-run the sample after significant schema change, vendor upgrade, or framework version change.
- Report metrics per source system and per domain library.

### 12.5 Automation Target

The v0.1 aspiration (80–95% automated classification) is retained **only as a planning hypothesis**. The achieved rate is a measured output of the pilot and reported per source system. Planning must not rely on it before pilot results exist.

---

## 13. Governance and Change Control

### 13.1 Versioning

Semantic versioning applies to the framework: **MAJOR** (taxonomy or cascade-order change), **MINOR** (threshold, weight, or rule-parameter change; new tag), **PATCH** (editorial). Each classification record stores the framework version that produced it. Results from different versions are not compared without re-running.

### 13.2 Change Control

All changes to roles, dispositions, tags, rules, weights, or thresholds require: documented rationale, impact analysis on prior pilot metrics, Framework Owner approval, and (for MAJOR changes) Data Governance approval.

### 13.3 Decision Log

A permanent log with one entry per override or sign-off:

| Field | Description |
|---|---|
| Entry ID, Date | Unique identifier and date |
| Table | Source system, schema, table |
| Original / Final Classification | Computed and approved values |
| Decision Type | Override, sign-off, exception |
| Reason | Required, free-text with category |
| Decided By / Role | Named individual |
| Framework Version | Version in effect |

### 13.4 Drift Management

Re-run classification when: schema changes are detected (new/dropped tables or columns, FK changes), source-system patches or upgrades are applied, or the framework version changes. Differences against the baseline are reported as a change set for review.

### 13.5 Keyword Library Management

Domain keyword libraries (Appendix B) are versioned artifacts owned by the Classification Engineer. Changes require validation against the pilot sample.

### 13.6 Data Handling

Sample-data profiling must follow the privacy, CJIS, and access-control policies applicable to the source system. Classification records must not contain raw PII or CJI; evidence is recorded as aggregates, patterns, or masked values.

---

## 14. Classification Record Specification

### 14.1 Template

```text
Source System / Schema / Table:    <system>.<schema>.<table>
Functional Role:                   <role>
Disposition:                       <disposition>
Tags:                              <tag>, <tag>, ...
Subject Area:                      <subject area>
Canonical Entity:                  <entity | None>
Confidence:                        <0-100>  (see score breakdown)
Score Breakdown:                   Structure <n>/25 | Content <n>/25 | Dependencies <n>/20 | Volume <n>/10 | Usage <n>/20 | Penalties <-n>
Evidence Coverage:                 <percent>
Dependency Depth:                  <n>   (Inbound: <n>, Outbound: <n>; by provenance)
Load Unit / Cohort:                <id> / <cohort>
Derived Phase:                     <wave>   (Rationale: <text>)
Rules Triggered:                   <ids matched, in order; competing matches>
Evidence:
  - <exact row count, growth>
  - <declared / inferred / code-referenced dependencies>
  - <event date, audit columns, structural indicators>
  - <usage observation and window>
Conflicts:                         <codes and description | None>
Data Quality Notes:                <null rate, orphans, duplicates | None>
Review Status:                     <status>
Reviewer / Date:                   <name, date | —>
Approver (if sign-off required):   <name, role, date | —>
Override Reason:                   <text | —>
Framework Version:                 1.0
Classified On / Run ID:            <timestamp / run id>
```

### 14.2 Field Dictionary

| Field | Type | Required | Notes |
|---|---|---|---|
| Source System / Schema / Table | String | Yes | Fully qualified |
| Functional Role | Enum (5.1) | Yes | Exactly one |
| Disposition | Enum (5.2) | Yes | Exactly one |
| Tags | Enum list (5.3) | No | Zero or more |
| Subject Area | String | Yes | From controlled list |
| Canonical Entity | String | Yes | `None` permitted |
| Confidence | Integer 0–100 | Yes | After penalties and caps |
| Score Breakdown | Structured | Yes | Required for audit |
| Evidence Coverage | Percent | Yes | Drives caps (8.4) |
| Dependency Depth | Integer | Yes | Per Section 10 |
| Derived Phase | Integer or `None` | Yes | `None` for Do Not Migrate |
| Rules Triggered | List | Yes | Matched and competing |
| Conflicts | Code list | Yes | `None` permitted |
| Review Status | Enum (11.1) | Yes | |
| Override Reason | String | Conditional | Required when overridden |
| Framework Version | String | Yes | |

---

## Appendix A: Worked Examples

### A.1 Transaction

```text
Table:               RMS.dbo.Incident
Functional Role:     Transaction
Disposition:         Migrate
Tags:                PII, Retention-Regulated, Business-Critical
Subject Area:        Incident
Canonical Entity:    Incident
Confidence:          96  (Structure 24, Content 24, Dependencies 19, Volume 10, Usage 19)
Evidence Coverage:   100%
Dependency Depth:    3  (Inbound 14, Outbound 5)
Derived Phase:       3
Rules Triggered:     R11 (R10 evaluated: not matched - event date present)
Evidence:
  - 4.2M rows, +8k rows/month (exact count)
  - Declared FKs to Person, Officer, Location; inferred relationship to Agency
  - IncidentDate column; referenced by 6 views and 11 procedures
  - Last modified yesterday
Conflicts:           None
Review Status:       Pending Review (Business-Critical cap, Section 8.7)
```

### A.2 Reference

```text
Table:               RMS.dbo.VehicleColor
Functional Role:     Reference
Disposition:         Migrate
Tags:                None
Subject Area:        Property/Vehicle
Canonical Entity:    None
Confidence:          98
Dependency Depth:    0  (Inbound 3, Outbound 0)
Derived Phase:       1
Rules Triggered:     R8
Evidence:
  - 12 rows; code, description, active flag
  - Referenced by Vehicle and two views
  - Last modified 2 years ago
Conflicts:           None
Review Status:       Auto-Accepted
```

### A.3 Retirement Candidate (Safeguards Applied)

```text
Table:               RMS.dbo.tblLegacyCache
Functional Role:     Derived
Disposition:         Do Not Migrate (retirement candidate)
Tags:                None
Confidence:          88
Rules Triggered:     R2, R9
Evidence:
  - 0 rows; no inbound references in tables, code, or application
  - Last modification: 2014 (audit column)
  - No Feature-Dormant indicators found
Conflicts:           None
Review Status:       Pending Sign-Off (mandatory per Section 8.7)
Approver:            Data Owner and Compliance (pending)
```

### A.4 Conflict Example (Routed to SME)

```text
Table:               RMS.dbo.StatusCode
Functional Role:     Transaction (proposed)
Disposition:         Review
Confidence:          58
Rules Triggered:     R8 (name/shape match), R11 (growth pattern match)
Evidence:
  - Name and columns suggest Reference
  - 2.1M rows, +40k rows/month, has event date column
Conflicts:           C1, C2
Review Status:       Pending Review
Note:                Likely a mislabeled event log; SME determination required.
```

---

## Appendix B: Domain Keyword Libraries

Starter libraries. Each domain maintains its own versioned list; entries are matched case-insensitively and weighted by the Classification Engineer.

| Domain | Foundation | Transaction | Reference |
|---|---|---|---|
| **RMS** | Person, Name, Officer, Location, Address, Vehicle, Organization, Agency | Incident, Arrest, Citation, Warrant, Case, Evidence, Property | OffenseCode, Race, Ethnicity, VehicleMake, State, County |
| **CAD** | Unit, Responder, Premise, Address, Beat, Zone | CallForService, Dispatch, UnitStatus, Response | CallType, Disposition, Priority, Agency |
| **JMS** | Inmate, Person, Facility, Cell, Bed | Booking, Release, Movement, Incident, Visit, Grievance | ChargeCode, HousingType, Classification, ReleaseReason |

| Pattern Type | Naming Patterns |
|---|---|
| Staging | `Import*`, `Temp*`, `Tmp*`, `ETL*`, `Work*`, `*_stg`, `*_bak`, `*_copy`, `BackupCopy_*` |
| History/Audit | `*Hist`, `*History`, `*Audit`, `*Log` (when mirroring a business table) |
| Integration | `*Queue`, `*Interface*`, `*Sync*`, `*Export*`, `*Message*`, `*ExternalRef*` |
| Attachment | `*Attachment*`, `*Document*`, `*Image*`, `*Photo*`, `*Media*`, `*Scan*` |
| System/Config | `*Setting*`, `*Config*`, `*Option*`, `*Preference*`, `*Schedule*`, `*Role*`, `*Permission*` |
| Association | Concatenation of two entity names (for example, `IncidentPerson`) |

---

## Appendix C: Platform Evidence Queries

These queries illustrate evidence collection. They must be reviewed and adapted to the environment and permissions before use.

### C.1 Microsoft SQL Server

**Declared foreign keys (inbound and outbound)**

```sql
SELECT
    fk.name                                  AS fk_name,
    OBJECT_SCHEMA_NAME(fk.parent_object_id)  AS child_schema,
    OBJECT_NAME(fk.parent_object_id)         AS child_table,
    OBJECT_SCHEMA_NAME(fk.referenced_object_id) AS parent_schema,
    OBJECT_NAME(fk.referenced_object_id)     AS parent_table
FROM sys.foreign_keys AS fk;
```

**Catalog row counts and storage (approximate; verify with exact counts)**

```sql
SELECT
    s.name AS schema_name,
    t.name AS table_name,
    SUM(ps.row_count) AS approx_rows,
    SUM(ps.reserved_page_count) * 8 / 1024.0 AS reserved_mb
FROM sys.tables t
JOIN sys.schemas s ON s.schema_id = t.schema_id
JOIN sys.dm_db_partition_stats ps
     ON ps.object_id = t.object_id AND ps.index_id IN (0, 1)
GROUP BY s.name, t.name;
```

**Code-level dependencies (views, procedures, functions, triggers)**

```sql
SELECT
    OBJECT_SCHEMA_NAME(d.referencing_id) AS referencing_schema,
    OBJECT_NAME(d.referencing_id)        AS referencing_object,
    o.type_desc                          AS referencing_type,
    d.referenced_schema_name,
    d.referenced_entity_name,
    d.referenced_database_name
FROM sys.sql_expression_dependencies d
JOIN sys.objects o ON o.object_id = d.referencing_id;
```

**Index usage (resets on instance restart; record observation window)**

```sql
SELECT
    OBJECT_SCHEMA_NAME(us.object_id) AS schema_name,
    OBJECT_NAME(us.object_id)        AS table_name,
    SUM(us.user_seeks + us.user_scans + us.user_lookups) AS reads,
    SUM(us.user_updates)             AS writes,
    MAX(us.last_user_update)         AS last_user_update
FROM sys.dm_db_index_usage_stats us
WHERE us.database_id = DB_ID()
GROUP BY us.object_id;
```

**LOB columns**

```sql
SELECT OBJECT_SCHEMA_NAME(c.object_id) AS schema_name,
       OBJECT_NAME(c.object_id)        AS table_name,
       c.name                          AS column_name,
       ty.name                         AS type_name
FROM sys.columns c
JOIN sys.types ty ON ty.user_type_id = c.user_type_id
WHERE ty.name IN ('image','varbinary','text','ntext','xml')
  AND (c.max_length = -1 OR ty.name IN ('image','text','ntext'));
```

### C.2 Oracle

**Declared foreign keys**

```sql
SELECT c.owner, c.table_name AS child_table, c.constraint_name,
       r.owner AS parent_owner, r.table_name AS parent_table
FROM   all_constraints c
JOIN   all_constraints r
       ON c.r_owner = r.owner AND c.r_constraint_name = r.constraint_name
WHERE  c.constraint_type = 'R';
```

**Code-level dependencies**

```sql
SELECT owner, name, type, referenced_owner, referenced_name, referenced_type
FROM   all_dependencies
WHERE  referenced_type = 'TABLE';
```

**Row counts and modification activity (statistics may be stale; verify with exact counts)**

```sql
SELECT t.owner, t.table_name, t.num_rows, t.last_analyzed,
       m.inserts, m.updates, m.deletes, m.timestamp AS last_modified_tracked
FROM   all_tables t
LEFT JOIN all_tab_modifications m
       ON m.table_owner = t.owner AND m.table_name = t.table_name;
```

**LOB columns**

```sql
SELECT owner, table_name, column_name, data_type
FROM   all_tab_columns
WHERE  data_type IN ('BLOB', 'CLOB', 'NCLOB', 'BFILE', 'LONG RAW');
```

### C.3 Exact Row Counts

Catalog counts are estimates and may be stale. Generate exact counts per table and store them with a timestamp:

```sql
SELECT COUNT(*) FROM <schema>.<table>;
```

For very large tables, schedule during low-activity windows and record the method used.

---

## Appendix D: Glossary

| Term | Definition |
|---|---|
| **Association table** | Table resolving a many-to-many relationship between two or more entities. |
| **Canonical Entity** | Enterprise-standard entity name used to align equivalent tables across source systems. |
| **CJIS** | Criminal Justice Information Services; security policy governing criminal justice information. |
| **Cohort** | Group of tables in a Subject Area migrated together as a coherent unit. |
| **Dependency Depth** | Longest dependency path from a table to a table with no outbound dependencies. |
| **Disposition** | The decided handling of a table: migrate, archive, rebuild, or do not migrate. |
| **Feature-Dormant** | Table that is empty or unused but belongs to a module that is licensed, configurable, or may be used later. |
| **Functional Role** | The nature of a table's content and purpose within the data model. |
| **Load Unit** | One table, or a set of mutually dependent tables (a cycle), loaded together. |
| **Provenance** | The source of a dependency: declared, code-referenced, cross-database, application-referenced, or inferred. |
| **Retirement candidate** | Table proposed for non-migration pending sign-off. |
| **SME** | Subject Matter Expert. |

---

## Appendix E: Pre-Publication Review Checklist

| # | Check | Complete |
|---|---|---|
| 1 | Document owner, approvers, and review cycle confirmed | ☐ |
| 2 | Roles and responsibilities mapped to named individuals | ☐ |
| 3 | Rule thresholds (7.2) and score weights (8.2) accepted as initial values | ☐ |
| 4 | Retention, CJIS, and evidentiary requirements reviewed by Compliance | ☐ |
| 5 | Retirement safeguards (Section 9) approved by Data Governance | ☐ |
| 6 | Canonical Entity catalog and Subject Area list established | ☐ |
| 7 | Domain keyword libraries created for each applicable domain | ☐ |
| 8 | Platform evidence queries validated against the target environment | ☐ |
| 9 | Pilot sample plan and SME availability confirmed | ☐ |
| 10 | Decision Log repository and access controls provisioned | ☐ |
| 11 | Data-handling controls for sample profiling approved | ☐ |
| 12 | Approval signatures recorded on the title block | ☐ |