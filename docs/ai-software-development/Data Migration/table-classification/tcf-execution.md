# AGENT INSTRUCTIONS: Table Classification of RMS Source Databases

**Document ID:** TCF-AGT-001
**Version:** 1.0
**Applies to:** Table Classification Framework v1.0 (TCF-001)
**Audience:** AI coder agents (and the humans supervising them)

---

## 0. Mission

Apply the Table Classification Framework (TCF-001) to the four RMS source databases and produce a reproducible, auditable classification of **every table**, plus the project deliverables.

| Database | Server | Platform |
|---|---|---|
| NetRMS | NETRMSSQLDB | Motorola NetRMS, SQL Server 2008 R2 |
| NetRMS_Security | NETRMSSQLDB | Motorola NetRMS, SQL Server 2008 R2 |
| WebRMS | RMS-PRI-DB1 | Hexagon OnCall Records, SQL Server 2016 |
| NIBRS | RMS-PRI-DB1 | Hexagon OnCall Records, SQL Server 2016 |

**Inputs:** `NetRMS DBO.xlsx`, `NetRMS_Security DBO.xlsx`, `NIBRS DBO.xlsx`, `WebRMS DBO.xlsx`. Each has a `schema` tab (one row per column) and a `row_count` tab (one row per table/index).

**Optional supplementary inputs (Phase 7):** result files from read-only SQL scripts run by humans.

**You are done when** every table in every database has a classification record, all gates in Section 12 pass, and the deliverables in Section 10 exist.

---

## 1. Operating Rules (Non-Negotiable)

1. **Evidence only.** Never invent evidence, table purposes, or dependencies. If evidence is missing, record it as missing and lower confidence. Unresolved cases stay `Unknown`.
2. **No automatic retirement.** Rules R1 and R2 produce *candidates*. Never write a final `Do Not Migrate` without the sign-off fields being present (Section 9).
3. **Read-only on sources.** Do not connect to the production databases unless the human explicitly grants access. By default, you generate SQL scripts for humans to run (Phase 7).
4. **No raw data.** Workbooks contain metadata only. Do not request, store, or print sample row values. Evidence is aggregates, patterns, and counts.
5. **Deterministic and idempotent.** The same inputs and config must produce byte-identical outputs. Use fixed random seeds. Sort all outputs by a stable key. Never rely on dict/set iteration order.
6. **Configuration over code.** Rule thresholds, weights, keyword lists, and bands live in `config/*.yaml`, not in code constants.
7. **Fully qualified keys.** Identify every table as `database.schema.table`. Never join on table name alone.
8. **Hypotheses are not facts.** Statements in project documents such as "NIBRS is derived from WebRMS" or "the Security database is mostly audit data" are hypotheses to test, and must be labelled as such in outputs until supported by evidence.
9. **Log everything that matters.** Every rule evaluation, score component, override, and data-cleaning decision is recorded.
10. **Stop and ask** when a Stop Condition (Section 11) occurs. Do not proceed on guesses.

---

## 2. Environment and Repository Layout

**Stack:** Python 3.11+; `pandas`, `openpyxl`, `networkx`, `pyyaml`, `pytest`. Optional: `duckdb`, `pyarrow`. Pin versions in `requirements.txt`.

```text
tcf/
├── config/
│   ├── profile.yaml            # evidence profile (full | workbook_only), weights, bands, caps
│   ├── rules.yaml              # R1-R13 definitions and thresholds
│   ├── keywords.yaml           # name patterns and domain keywords (per vendor)
│   ├── category_rollup.yaml    # role/disposition/tag -> project category mapping
│   └── subject_areas.yaml      # table/keyword -> Subject Area, Canonical Entity
├── input/                      # the four workbooks + supplementary results (read-only)
├── staging/                    # normalized intermediate files
├── src/
│   ├── ingest.py
│   ├── features.py
│   ├── graph.py
│   ├── rules.py
│   ├── scoring.py
│   ├── routing.py
│   ├── reports.py
│   ├── validate.py
│   └── run.py                  # orchestrates phases; single entry point
├── sql/                        # generated scripts for humans (Phase 7)
├── output/
│   ├── records/                # classification records (JSON + CSV)
│   ├── reports/                # deliverables
│   └── run_manifest.json
├── tests/
└── README.md
```

`python -m src.run --config config/ --input input/ --output output/` MUST run the full pipeline end to end.

---

## 3. Workflow Overview

| Phase | Name | Output | Gate |
|---|---|---|---|
| 0 | Intake and validation | `staging/intake_report.md` | G0 |
| 1 | Ingest and normalize | `staging/columns.csv`, `staging/volumes.csv` | G1 |
| 2 | Table feature extraction | `staging/table_features.csv` | G2 |
| 3 | Dependency graph | `staging/edges.csv`, depth, waves | G3 |
| 4 | Rule cascade | `staging/rule_results.csv` | G4 |
| 5 | Scoring and routing | `output/records/` | G5 |
| 6 | Tags, subject areas, roll-up | records updated | G6 |
| 7 | Supplementary evidence (human-assisted) | `sql/*.sql`, loader | G7 |
| 8 | Reports | `output/reports/*` | G8 |
| 9 | Validation sample and metrics | `output/reports/validation_*` | G9 |
| 10 | Handoff | `run_manifest.json`, summary | G10 |

Run Phases 0-6 and 8 first (workbook-only). Phase 7 then **upgrades** the evidence and re-runs Phases 2-8. Both runs are retained and diffed (Section 8.4).

---

## 4. Phase 0: Intake and Validation

**Tasks**

1. Confirm all four workbooks exist. Compute and record a SHA-256 hash of each file.
2. Confirm each has tabs named `schema` and `row_count` (case-insensitive match; record the actual names).
3. Confirm the expected columns exist.
   - `schema`: `InstanceName, CatalogName, SchemaName, ObjectName, ColumnName, OrdinalPosition, DataType, CharacterMaxLength, Precision, Scale, IsOutput, IsReadOnly, IsNullable, IsIdentity, ObjectType, ConstraintType, ReferenceTableSchema, ReferenceTableName, ReferenceColumnName, PrivacyTag, TableDescription`
   - `row_count`: `TableName, indexName, Rows, TotalPages, UsedPages, DataPages, TotalSpaceMB, UsedSpaceMB, DataSpaceMB`
4. Profile and report:
   - row counts per tab;
   - distinct values of `ObjectType` and `ConstraintType`;
   - null rates for every column;
   - whether `row_count.TableName` is schema-qualified;
   - whether the `row_count` tab carries a collection date (if none, record "count date unknown").
5. Write `staging/intake_report.md`.

**Gate G0:** all files, tabs, and required columns present. Missing columns that are not essential (for example `PrivacyTag`) are recorded as *evidence unavailable*, not as failures. Missing `ObjectName`, `ColumnName`, `Rows`, or `TableName` is a Stop Condition.

---

## 5. Phases 1-2: Ingest and Feature Extraction

### 5.1 Phase 1: Ingest and normalize

1. Load every workbook into tidy DataFrames. Add `source_db` from `CatalogName` (fall back to the workbook name). Normalize whitespace and compare identifiers case-insensitively, but preserve the original case for display.
2. **`schema` tab:**
   - Keep the original rows in `staging/columns_raw.csv`.
   - Table inventory: rows where `ObjectType` indicates a user table. Views, procedures, and functions go to `staging/non_table_objects.csv` (they are dependency evidence, not classification targets).
   - A single column can appear on multiple rows (for example, PK and FK on the same column). De-duplicate to one row per `(db, schema, table, column, constraint, reference)` and **never drop** constraint rows.
3. **`row_count` tab** has one row per index:
   - Table rows = the `Rows` of the heap or clustered index. If both are absent, use the maximum `Rows` across indexes and flag `rowcount_method = max_index` (lower confidence).
   - Space = sum across indexes: `TotalSpaceMB`, `UsedSpaceMB`, `DataSpaceMB`.
   - Resolve `TableName` to `db.schema.table`. If it is unqualified and the same name exists under multiple schemas, flag `ambiguous_rowcount` and do not guess.
4. **Reconcile:** every inventory table should have a row count and vice versa. Write mismatches to `staging/reconciliation_exceptions.csv`.

### 5.2 Phase 2: Table features (one row per table)

Compute and store, per table:

| Feature | Definition |
|---|---|
| `col_count`, `pk_cols`, `fk_count`, `unique_cols` | From columns and `ConstraintType` |
| `non_key_cols` | Columns not in PK and not in any FK |
| `has_pk` | PK present |
| `identity_cols` | Count where `IsIdentity` |
| `date_cols` | Columns with date/time datatypes |
| `event_date_candidates` | Date columns whose names match event patterns in `keywords.yaml` (for example `*Date*`, `*DateTime*`, `*Occurred*`, `*Reported*`) excluding audit columns |
| `audit_cols` | Columns matching created/modified patterns (`CreatedDate`, `ModifiedBy`, ...) |
| `lob_cols` | `image`, `text`, `ntext`, `xml`, `varbinary`/`varchar`/`nvarchar` with `CharacterMaxLength = -1`, `binary`-like file columns |
| `filepath_cols` | Column names matching path/URL/file patterns |
| `lookup_shape` | Boolean: a code-like column plus a description-like column, plus few other columns (patterns in `keywords.yaml`) |
| `queue_shape` | Status/retry/message/attempt/processed columns present |
| `name_tokens` | Table name split on case boundaries, underscores, and digits |
| `name_patterns_matched` | Which pattern families (staging, history, integration, ...) match |
| `description` | `TableDescription` (if populated) |
| `privacy_tags` | Distinct `PrivacyTag` values across the table's columns |
| `rows`, `data_mb`, `total_mb` | From Phase 1 |
| `source_vendor` | NetRMS or OnCall, from the database |

**Keyword libraries must be derived from the data, not assumed.** Generate `staging/token_frequency.csv` (token, count, tables by database) and review it to extend `keywords.yaml`. Do not assume vendor naming conventions; confirm them from the token frequencies. Record each library entry's origin (`seed` or `data-derived`).

**Gates G1/G2:** counts reconcile within tolerance (target 100%; any exception is listed); no table lacks features.

---

## 6. Phase 3: Dependency Graph

1. **Nodes:** all tables (`db.schema.table`).
2. **Declared edges:** from `ConstraintType = FK` rows: `child -> parent` using `ReferenceTableSchema/Name/Column`. Attribute `provenance = declared`. Parent tables in another database or not in the inventory are recorded as `unresolved_parent` (never silently dropped).
3. **Inferred edges** (always labelled `provenance = inferred`, with a score):
   - Candidate: a non-key column in table A whose normalized name equals, or contains, a PK column name of table B (for example `PersonID` in A and the PK `PersonID` of B), where datatype and length are compatible.
   - Score by: exact name match, datatype match, B being a PK (not a non-unique column), name uniqueness of the PK across the database, and the table-name token appearing in the column name.
   - Keep edges with score ≥ `inferred_edge_min_score` (config; default 0.7). Report discarded candidates with counts only.
   - **Do not use inferred edges for wave ordering until confirmed**, except in a sensitivity run (Section 6.7).
4. **Cross-database edges:** apply the same inference across databases (for example NetRMS ↔ NetRMS_Security, WebRMS ↔ NIBRS). Label `provenance = inferred_xdb`. These always require human confirmation.
5. **Strongly connected components:** compute with `networkx`; collapse each component to a *load unit*. Self-references are recorded as a load-unit note (two-pass load).
6. **Depth:** longest path to a node with no outbound edges, computed on the condensed DAG. Output `depth`, `load_unit`, `inbound_declared`, `inbound_inferred`, `outbound_declared`, `outbound_inferred`.
7. **Sensitivity run:** compute the waves twice (declared only; declared + inferred). Report tables whose wave differs. These are the tables most in need of dependency confirmation.
8. **Graph density check (important):** report the FK count per database, the percentage of tables with no declared edge, and the percentage of tables with `fk_count = 0` that have an inferred edge. If declared-edge coverage is below `low_fk_coverage_threshold` (config; default 30% of tables), set the `dependency_evidence_quality = weak` for that database. This reduces dependency scoring (Section 8).

**Gate G3:** the graph is acyclic after condensation; every edge has provenance; every unresolved parent is listed.

---

## 7. Phase 4: Rule Cascade (R1-R13)

1. Implement each rule from the Framework (Section 7.2) as a declarative entry in `config/rules.yaml` (conditions, parameters, result). Rule code evaluates the condition on the feature row; it does not hard-code thresholds.
2. **Evaluate all rules for every table.** Write one row per `(table, rule)` with: `matched` (bool), `evidence` (which conditions were true), `candidate_role`, `candidate_disposition`.
3. The first matching rule in cascade order sets the candidate role. Other matching rules are recorded as *competing matches*.
4. R2 (retirement) sets only the disposition candidate, not the role. The role comes from the remaining rules.
5. **Workbook-only limitations** (apply until Phase 7 supplies the evidence):

| Rule | Workbook-only behavior |
|---|---|
| R1 Staging | Name pattern + no inbound declared/inferred edge. Mark `code_scan = not_performed`. |
| R2 Retirement | `rows = 0` + no inbound edge of any provenance + not `Feature-Dormant`-suspected. Mark `usage = not_available`, `code_scan = not_performed`. **"Minimal rows" alone never triggers R2.** |
| R7 Association | Use inferred FKs if declared FKs are absent, and lower the dependency score. |
| R10-R12 | Use `event_date_candidates`, inbound counts, and parent-row ratio (rows ÷ parent rows via the best parent edge). |

6. **Feature-Dormant suspicion:** for every empty table, set `feature_dormant_suspect = true` if the table name or description matches a module keyword list (for example evidence, warrants, bookings, field interviews, or others configured), or if sibling tables in the same name cluster have data. Empty tables with this flag are *never* retirement-ready.

**Role-indicator catalog (defaults, editable in `rules.yaml`).** Each indicator has a weight (default 1). The dimension score is computed in Phase 5.

| Role | Structure | Content/Naming | Dependency | Volume |
|---|---|---|---|---|
| Foundation | has_pk, no event date, identity key | entity-name tokens, description match | inbound ≥ threshold, mostly Reference outbound | moderate; not growth-driven |
| Reference | few columns, code+description | name tokens (`Code`, `Type`, `Status`, `Category`) | high inbound, outbound 0-1 | rows ≤ threshold |
| Transaction | event date, outbound to Foundation | event-name tokens | outbound to Foundation, inbound from Detail/Association | high rows |
| Association | PK/unique composed of ≥ 2 FKs, few non-key cols | name = two entity names | ≥ 2 outbound | rows ≥ min(parent rows) |
| Detail | one dominant parent FK, no event date | parent name + suffix (`Narrative`, `Remark`, ...) | 1 parent | rows/parent in typical range |
| History/Audit | mirrors another table + change metadata | `Hist`/`Audit`/`Log` tokens | refers to source table | high rows, append-only shape |
| Attachment | LOB or filepath columns | `Image`, `Document`, `Attachment` tokens | parent FK | high `data_mb` |
| Integration | `queue_shape`, external ids | `Queue`, `Interface`, `Sync`, `Export` tokens | few edges | variable |
| System/Config | key/value or settings shape | `Setting`, `Config`, `Option`, `User`, `Role`, `Permission` tokens | few edges | low |
| Derived | aggregate/snapshot columns, no PK to business entity | `Summary`, `Snapshot`, `Cache`, `Report` tokens | refers to sources, rarely referenced | variable |
| Staging | none | import/temp tokens | no inbound | variable |

**Gate G4:** exactly one result row per `(table, rule)`; no table without a candidate role (use `Unknown` if none).

---

## 8. Phase 5: Scoring, Caps, and Routing

### 8.1 Evidence profiles

Set the active profile in `config/profile.yaml`.

| Profile | Dimensions and max points | Use when |
|---|---|---|
| **full** | Structure 25, Content 25, Dependencies 20, Volume 10, Usage 20 | Usage evidence supplied (Phase 7 complete) |
| **workbook_only** | Structure 30, Content 30, Dependencies 25, Volume 15, Usage 0 | Phase 7 not yet done |

**`workbook_only` is a deviation from TCF-001 Section 8.4** (which would cap every table at 89). It is allowed only as a provisional measure:

- Every record scored under it MUST carry `evidence_profile = workbook_only` and `review_status` prefixed `Provisional`.
- The Framework Owner must approve it. Record `profile_approved_by` (empty = unapproved). Unapproved profile output is labelled **DRAFT** in every report header.
- Under `workbook_only`, any role or disposition affecting retirement, Do Not Migrate, Business-Critical, or regulated tags is capped at Medium regardless of score.

### 8.2 Computation

For the candidate role:

```text
dimension_score = max_points * (sum of weights of matched indicators /
                                sum of weights of applicable indicators)
raw = structure + content + dependencies + volume + usage
```

- An indicator is *applicable* only if its evidence exists. Missing evidence reduces **coverage**, not the dimension score.
- **Dependencies multiplier:** ×1.0 if the table's supporting edges are declared; ×0.6 if only inferred; ×0.5 if `dependency_evidence_quality = weak` for the database and no declared edge supports the role.
- **Penalties:** conflict −10 each (max −30; codes C1-C6); competing match with ≥ 70% of the winner's indicator weight −10; `rowcount_method = max_index` or `ambiguous_rowcount` −5; `Customized` −5. Floor at 0.
- **Evidence coverage** = available dimension points ÷ profile maximum. Under `full`: coverage < 70% → cap 69; 70-89% → cap 89. Under `workbook_only`: coverage is evaluated against the profile's own total (Section 8.1 limits apply).
- Round half up to an integer. Store the full breakdown.

### 8.3 Bands and routing

| Band | Score | Routing |
|---|---|---|
| High | ≥ 90 | `Auto-Accepted` (subject to caps below) |
| Medium | 70-89 | `Spot-Review`; select sample rate from config (default 15%) using a seeded, stratified (by role and database) sampler |
| Low | < 70 | `Pending Review` (SME) |

**Mandatory review (cannot be auto-accepted, regardless of score):** disposition `Do Not Migrate`; role `Unknown`; unresolved conflict; tags Business-Critical, Retention-Regulated, Evidentiary; CJIS-Sensitive with disposition other than Migrate; any table on `config/stakeholder_review_list.csv` if present.

### 8.4 Run comparison

On every re-run, diff the new records against the previous baseline (by table key). Produce `output/reports/run_diff.csv`: tables whose role, disposition, confidence band, or wave changed, and the reason (new evidence, changed rule, changed config). A change with no corresponding change in inputs or config is a **bug**: investigate before proceeding.

**Gate G5:** every table has a record; breakdown components sum to the score; no cap or routing rule violated (test in Section 12).

---

## 9. Phase 6: Dispositions, Tags, Subject Areas, Roll-up

1. **Disposition defaults** per Framework Section 7.4. Never auto-assign `Archive`; propose it with reasons (for example high volume + History/Audit role + no recent modification evidence).
2. **Retirement candidates.** For every record with disposition `Do Not Migrate`, populate:
   `safeguards = { feature_dormant_check, code_scan, usage_check, regulatory_check, owner_signoff }`, each as `pass | fail | not_performed`. Any `not_performed` means status `Candidate (Unverified)`. The record is never final until `owner_signoff` and the regulatory check are both `pass`.
3. **Tags.**
   - `PII`, `CJIS-Sensitive`: from `PrivacyTag` values (mapping in config). Missing `PrivacyTag` is *unknown*, not *none*.
   - `BLOB`: from `lob_cols`.
   - `Master-Data Candidate`: Foundation tables for person, location, vehicle, organization.
   - `Customized`: set when an expected vendor pattern is broken (for example a non-standard prefix); otherwise leave for SME.
   - `Business-Critical`, `Retention-Regulated`, `Evidentiary`: **not inferred by the agent.** Set only from supplied lists or SME input.
4. **Subject area and canonical entity.** Assign from `subject_areas.yaml` using name tokens and graph clusters (tables strongly connected to the same Foundation entity). Where there is no confident match, leave `Unassigned` (never guess). Ensure the same canonical entity is used across both vendors.
5. **Project category roll-up.** Apply `category_rollup.yaml` to derive the project's eight categories. Retain the framework fields; the roll-up is an additional column.

| Project category | Derived from |
|---|---|
| Core RMS Transactional | Role in Transaction, Detail, Association, Foundation |
| Reference / Lookup | Role = Reference |
| Security | System/Config with security tokens (user, role, permission, login, session) or tag, and authentication tables |
| Interface / Integration | Role = Integration |
| Reporting / Analytics | Role = Derived |
| Workflow / Application Support | System/Config (non-security), Staging |
| Archive / Historical | Disposition = Archive, or Role = History/Audit |
| Candidate for Retirement | Disposition = Do Not Migrate (candidate) |

Keep Foundation visible in the roll-up as a sub-category so master entities are not hidden inside Core RMS Transactional.

**Gate G6:** every record has role, disposition, subject area (or `Unassigned`), canonical entity (or `None`), roll-up category, tags (or none), and safeguards populated for every `Do Not Migrate`.

---

## 10. Phase 7: Supplementary Evidence (Human-Assisted)

The workbook-only run cannot see usage, code dependencies, or activity. Close these gaps.

1. **Generate SQL scripts** in `sql/`, one set per server, read-only, with comments stating purpose and expected runtime. Use the Framework's Appendix C queries as the basis:
   - `01_code_dependencies.sql` (`sys.sql_expression_dependencies`)
   - `02_index_usage.sql` (`sys.dm_db_index_usage_stats`) **plus the instance start time**, so the observation window is known
   - `03_last_modified.sql` (`MAX` of modified-date columns, generated per candidate table from `audit_cols` using dynamic SQL)
   - `04_exact_counts.sql` (`COUNT(*)` for tables where the workbook count is suspect or the table is a retirement candidate)
   - `05_foreign_keys.sql` (to compare declared FKs against the workbook)
   - `06_cross_db_references.sql` (cross-database references from code)
2. **Version compatibility:** the 2008 R2 server lacks Query Store; do not generate Query Store scripts for it. Test every script for syntax compatibility with the target version and mark any feature that may be unavailable.
3. **Write a loader** (`src/ingest_supplement.py`) that validates and merges the result files into the feature table: `last_modified`, `reads`, `writes`, `observation_start`, `code_refs`, `exact_rows`. Validate the files' schema before use (Phase 0 style).
4. After loading: set `evidence_profile = full`, re-run Phases 2-8, and produce the run diff (Section 8.4).
5. **Usage evidence is Insufficient** (not zero) when the observation window is shorter than `usage_min_window_days` (config; default 365) or when the instance was restarted recently. Do not conclude non-use from insufficient evidence.

**Gate G7:** loaded files validated; every supplement field traced to its source file and run date.

---

## 11. Phase 8: Reports (Deliverables)

Write to `output/reports/`. Every report has a header with: framework version, config hash, input hashes, evidence profile, **DRAFT** flag where applicable, and run ID.

| # | Deliverable | Content |
|---|---|---|
| 1 | `table_inventory.csv` | One row per table: key, vendor, rows, size, role, disposition, tags |
| 2 | `classification_matrix.xlsx` | Full records (Framework Section 14). Sheets: Records, Score Breakdown, Rule Results, Review Queue, Decision Log (empty template), Summary. Reviewer columns (status, reviewer, override reason) use data validation lists. |
| 3 | `empty_tables.csv` | `rows = 0`, with `feature_dormant_suspect` and retirement-safeguard status |
| 4 | `low_volume_tables.csv` | Buckets `<100`, `<1,000`, `<10,000` (cumulative and exclusive columns) |
| 5 | `migration_waves.csv` | Load unit, depth, cohort, derived wave, rationale; sensitivity-run differences flagged |
| 6 | `high_risk_tables.csv` | Tags (PII, CJIS, Business-Critical, Retention-Regulated, Evidentiary), mandatory-review flags, low-confidence and Unknown tables |
| 7 | `complexity_by_domain.csv` | Per subject area: table count, rows, GB, max depth, LOB count, inferred-edge share, low-confidence share |
| 8 | `dependency_edges.csv` | All edges with provenance and score |
| 9 | `review_queue.csv` | Prioritized: mandatory, then Low, then Spot-Review sample; ordered by size and risk |
| 10 | `database_findings.md` | Per-database summary and the **hypothesis register**: each stated hypothesis (for example Security DB being mostly audit; NIBRS derived from WebRMS), the evidence for/against, and its status (`supported`, `contradicted`, `inconclusive`) |
| 11 | `quality_report.md` | Reconciliation exceptions, ambiguous counts, graph-density results, missing-evidence summary |

Wave planning rules: Reference and prerequisite tables first; Foundation after the Reference tables it depends on; Association after all parents; Attachment, bulk History, and Archive late; Derived rebuilt after sources; no wave for `Do Not Migrate`. Within waves, group by subject area cohort. Validate that **no table is in an earlier wave than any declared parent**; fail the run otherwise.

Prioritize analysis effort by size: the two largest databases (WebRMS and NetRMS_Security) together hold about 90% of the data. Show per-database results separately and combined.

---

## 12. Phase 9: Validation

1. **Generate the validation sample.** Seeded, stratified by database, role, and size tier; 100-200 tables (or ≥ 20% for small populations). Always include every PII or CJIS-tagged table and every `Do Not Migrate` candidate. Write `validation_sample_blind.xlsx` **without** the agent's role, disposition, or score (SMEs label independently), and a separate key file.
2. **Compute metrics** (`src/validate.py`) once SME labels arrive: overall accuracy; precision and recall per role; accuracy within the High band; retirement false-positive count; routing outcomes. Compare against the Framework targets (Section 12.2).
3. **Tune.** If targets are missed, propose threshold, weight, or rule changes in a change proposal. Never silently change config to improve the metric. Every config change creates a new framework MINOR version and a re-run with a diff.
4. **Report**: `validation_report.md`, including a confusion matrix and the top error patterns by rule.

**Gate G9:** metrics computed and reported. Auto-acceptance is enabled only if High-band accuracy meets the target; otherwise all High-band records remain `Spot-Review`.

---

## 13. Required Tests (`pytest`)

| Area | Test |
|---|---|
| Ingest | `row_count` collapse yields one row per table; composite-constraint columns keep all constraint rows |
| Ingest | Unqualified duplicate table names across schemas are flagged, never merged |
| Features | LOB detection includes `varchar(max)` (`CharacterMaxLength = -1`) |
| Graph | Cycle condensation; self-reference handling; depth on a hand-built fixture graph |
| Graph | No inferred edge ever appears in the declared-only wave run |
| Rules | R2 does not trigger on low-but-nonzero row counts |
| Rules | R2 never produces a final disposition without sign-off |
| Rules | Every `(table, rule)` pair has a result row |
| Scoring | Breakdown sums to score; caps and floors applied; coverage cap logic per profile |
| Routing | Mandatory-review conditions are never `Auto-Accepted` |
| Waves | No table precedes any declared parent |
| Determinism | Two runs with identical inputs give identical record files (hash equality) |
| Privacy | No output contains row-level data values (check column list against an allowlist) |

Use small synthetic fixtures (10-30 tables) covering each role, a cycle, an empty feature-dormant table, a LOB table, a lookup table, and an association table.

---

## 14. Stop Conditions (Ask the Human)

1. A required workbook, tab, or essential column is missing or unreadable.
2. More than 5% of inventory tables lack a row count (or the reverse).
3. The graph still contains unresolved cycles that the condensation approach cannot sequence (for example, a cycle spanning databases).
4. A rule or weight change would be needed to make a result "look right". Propose the change; don't apply it.
5. Any request to classify a table as `Do Not Migrate` without owner sign-off.
6. A supplementary result file shows evidence that contradicts the workbook (for example, rows in a table the workbook shows as empty). Report and wait.
7. Declared-edge coverage is so low (< 10% of tables) that wave ordering would rest almost entirely on inferred edges.

---

## 15. Run Manifest and Handoff

`output/run_manifest.json` MUST contain: run ID and timestamp, framework version, config file hashes, input file hashes (and supplement hashes), evidence profile and its approval status, Python and package versions, counts (tables per database, per role, per band, per routing status), gate results (G0-G9), and the list of open items.

**Handoff summary (`output/HANDOFF.md`, ≤ 1 page):**
- What was run (profile, version, DRAFT or approved).
- Headline numbers: tables, empty tables, retirement candidates (and how many are unverified), auto-accepted vs review-required.
- Biggest risks and the hypothesis-register results.
- What humans must do next, in order: approve profile; run Phase 7 scripts; review the mandatory queue; label the validation sample; confirm cross-database edges.

---

## 16. Definition of Done

- [ ] All gates G0-G9 passed or have a documented, human-accepted exception.
- [ ] Every table has exactly one classification record with a full breakdown.
- [ ] No unverified `Do Not Migrate` is presented as final.
- [ ] All eleven reports are produced with correct headers.
- [ ] Tests pass; two consecutive runs are identical.
- [ ] Run manifest and handoff summary are written.
- [ ] Open items are listed for the humans, in priority order.

---

## Appendix A: Starter `profile.yaml`

```yaml
evidence_profile: workbook_only      # full | workbook_only
profile_approved_by: ""              # empty = DRAFT
profiles:
  full:
    weights: {structure: 25, content: 25, dependencies: 20, volume: 10, usage: 20}
  workbook_only:
    weights: {structure: 30, content: 30, dependencies: 25, volume: 15, usage: 0}
    cap_restricted_at_medium: true
bands: {high_min: 90, medium_min: 70}
spot_review_rate: 0.15
seed: 20261001
penalties: {conflict: 10, conflict_max: 30, competing_match: 10, rowcount_fallback: 5, customized: 5}
dependency_multipliers: {declared: 1.0, inferred_only: 0.6, weak_fk_coverage: 0.5}
inferred_edge_min_score: 0.7
low_fk_coverage_threshold: 0.30
usage_min_window_days: 365
```

## Appendix B: Starter `rules.yaml` (excerpt)

```yaml
R2_retirement:
  conditions:
    rows_equal: 0
    no_inbound_edges: [declared, inferred, inferred_xdb]
    feature_dormant_suspect: false
  result: {disposition: "Do Not Migrate", status: "Candidate (Unverified)"}
R7_association:
  conditions: {min_fk_cols_in_key: 2, max_non_key_cols: 5}
  result: {role: Association}
R8_reference:
  conditions: {lookup_shape: true, max_rows: 5000, min_inbound: 3, max_outbound: 1}
  result: {role: Reference}
R10_foundation:
  conditions: {has_event_date: false, min_inbound_tables: 3}
  result: {role: Foundation, tag: Master-Data Candidate}
```

## Appendix C: Classification Record (JSON)

```json
{
  "table_key": "WebRMS.dbo.Example",
  "role": "Reference",
  "disposition": "Migrate",
  "tags": [],
  "subject_area": "Property/Vehicle",
  "canonical_entity": "None",
  "project_category": "Reference / Lookup",
  "confidence": 92,
  "score_breakdown": {"structure": 28, "content": 27, "dependencies": 20, "volume": 14, "usage": 0, "penalties": 0},
  "evidence_profile": "workbook_only",
  "evidence_coverage": 1.0,
  "depth": 0,
  "load_unit": "LU-0007",
  "derived_wave": 1,
  "rules_triggered": {"matched": ["R8"], "competing": []},
  "conflicts": [],
  "safeguards": null,
  "review_status": "Provisional: Spot-Review",
  "framework_version": "1.0",
  "run_id": "2026-10-01-001"
}
```