# AGENTS

> Operating instructions for AI-Coder agents (and any contributor) working in **[PROJECT NAME]**.
> Read this file in full before starting any work. It is the initiation contract between the project and every agent.

---

## 1. Mission & Repository Context

**[PROJECT NAME]** is **[one-line description of what the project is and what it produces]**.

- **[Optional: the upstream/authoritative source this repository realizes, e.g. a spec corpus, a design doc, or a parent repo.]**
- **[Optional: what this repository implements — the modules, services, and capabilities it provides.]**

Every decision you make must serve a single goal: **[the project's core objective — what "done" means for this codebase]**. You are not free-authoring an app; you are executing the project's specification and design intent.

---

## 2. Authoritative Source of Truth

**[Describe the authoritative source(s) for this project — a spec corpus, a design document, a schema catalog, a parent repository — and where it lives relative to this repo's root.]**

### Version / release policy

- **[Which version/release is the live authority, and where it lives.]**
- **[Which older versions remain only for historical reference, and the rule for not introducing/citing them.]**
- **[The entry-point document to read first if you are new to the project.]**

### Reading order & key entry points

**[List the documents/sources to orient with before implementing, in order, and which are MUST-read before any other work.]**

### Terminology grounding

Use **current terminology** only. **[List the canonical terms and any retired/legacy terms that must not be reintroduced.]**

---

## 3. Deprecated Terminology — Do Not Use

**[List any retired/legacy naming from an earlier phase that must not be introduced, perpetuated, or restored anywhere in this repository — code, docs, comments, PR titles, or commit messages.]**

- When you encounter stale legacy references in existing files, treat them as defects. Remove or replace them with the correct current term for the concept.
- Frame all work in **[PROJECT NAME]** / **[canonical vocabulary]** terms.

---

## 4. Repository Guidance

- This repository follows the documentation order:
  `docs/00-Project-Charter.md` → `docs/01-Project-WorkPlan.md` → `docs/02-Current-Sprint.md`.
- **`docs/02-Current-Sprint.md`** is the active implementation authority. Work on any phase, module, or capability is authorized **only** when it is explicitly described in the current sprint file, or in a sprint file that the current sprint references.
- If this repository has no `docs/` tree yet, the first authorized task is to create it (`docs/00-Project-Charter.md`, `docs/01-Project-WorkPlan.md`, `docs/02-Current-Sprint.md`) so the sprint authority exists.
- Place your modules and services in the repository locations mandated by the project's architecture **[e.g. modules under `/src/`, adapters under `/adapters/<category>`, host services under `/services`]**.

---

## 5. Documentation Maintenance (Mandatory)

- Keep `AGENTS.md`, `docs/02-Current-Sprint.md`, and all history/handoff/log files under `docs/` up to date with **every** change that advances the project. This is a standing mandate, not an optional step.
- Whenever work is completed, partially completed, or parked: update the current sprint file, the relevant handoff notes, and the roadmap/log files that record progress. **Never leave documentation describing a stale state.**
- Backlog / proposed-sprint work lives under `docs/backlog/`. Keep `docs/backlog/*` current as items are promoted (move to a sprint file), dropped (delete/archive), or re-scoped (update). A backlog item is **not** authorized work until the current sprint promotes it.
- Fully implemented backlog files are moved to `docs/backlog/archive/` (indexed by `docs/backlog/archive/README.md`) so the active backlog reads empty. Keep historical file contents untouched except for the status header.

---

## 6. Build, Test & Verify

- Build: **[the build command for this project]**
- Test: **[the test command(s) for this project]**
- **[Any additional verification commands: lint, static analysis, schema validation, etc.]**

> **Verify before you claim done.** A change is not complete if it does not build and the relevant test suite does not pass.

---

## 7. CI/CD

- **[The CI/CD workflow location and what it enforces, e.g. `.github/workflows/ci.yml` (restore / build / test with coverage collection).]**
- CI is the enforcement point for build & test success. Treat a failing CI run as a blocking defect, not a suggestion.

---

# Agent AI-Coder Instructions

## 8.1 Working Model — you are part of the development loop

You operate as a component of the project's development loop **[e.g. `spec → normalize → plan → execute → validate → repair → promote → trace → report`]**. Honor these non-negotiable principles:

- **Specification-Driven Operation** — Begin from a versioned specification or a governed change to one. Never begin from informal instructions, hidden context, or untracked changes.
- **Validation Before Acceptance** — Generated or modified artifacts must be validated (build + tests + deterministic checks) before promotion to stable. Your own assertion is **not** a substitute for deterministic validation.
- **Reuse Before Generation** — Prefer approved, validated, traceable reusable assets over fresh generation. Do not reinvent what already exists in the codebase.
- **Governed Autonomy** — You may act autonomously only within governance and sprint constraints. Sprint authority defines *what* is authorized; governance rules define *how* it may proceed.
- **Bounded Repair** — Repair until the defect is fixed or the configured limit is reached; on repeated failure, escalate rather than loop infinitely.
- **Targeted Re-Entry** — When a specification changes, use traceability / impact analysis to identify affected tasks and artifacts; avoid full regeneration when targeted construction suffices.
- **State-Controlled Continuation** — Use durable project state (sprint file, traceability records), not logs or memory, as the source of truth for whether work may proceed.

## 8.2 Operating Rules

- **Current Sprint is authoritative.** The active sprint determines what work is authorized, including any phase, module, service, UI, infrastructure, documentation, tests, or handoff updates it names.
- Completed phases are considered **closed** unless explicitly reopened by the current sprint or by a sprint file the current sprint references. Do not rework closed scope.
- **If `AGENTS.md` and `Current-Sprint.md` disagree, `Current-Sprint.md` takes precedence.** Do not request clarification when the current sprint clearly identifies the authorized work.
- **Before coding anything:** read the current sprint file, then the relevant authoritative documents (§2) and schema/contract files. Ground your vocabulary and contracts in the spec, not your own guesses.
- When a task, module name, or term is ambiguous, resolve it from the authoritative source; never silently invent meaning.

## 8.3 SDLC & Quality Standards (Enterprise Level)

- Always apply widely accepted best practices to achieve **Enterprise Quality** code and solutions.
- Build **extensible and replaceable** components, each purpose-designed for one responsibility, with rigorous **separation of concerns**.
- Manage **enterprise-grade dependency injection** to the highest standard: register by interface, respect lifetimes, prefer constructor injection, avoid service locator / anti-patterns.
- **Reuse instead of reinventing.** Before writing a component, check the existing codebase and any reusable-asset guidance.
- Write clean, readable, testable code: honor SOLID, `IAsyncDisposable`/`CancellationToken`/async-await conventions (where applicable), avoid static mutable state, and keep interfaces narrow.
- **Explicit Semantics** — entity types and identity must be explicit and machine-checkable, never inferred from prose.

## 8.4 Conventions to Honor

- Use **normative language** precisely: MUST/SHALL (mandatory), MUST NOT (prohibition), SHOULD (recommended, justify deviation), MAY (permitted).
- Use the project's **identifier conventions** and **shared enums**; do not redefine them.
- Dates/times in ISO 8601; versions in SemVer.
- Enforce the project's **cross-cutting invariants** **[e.g. no construction without readiness validation; no accepted artifact without deterministic validation; everything traceable; no secrets in logs/telemetry/specs; a tool timeout is never success]**.

## 8.5 Definition of Done

A change is **done** only when **all** of the following hold:

1. **Authorized** — explicitly described in the current sprint file.
2. **Implemented** — follows the project's conventions and repository layout (§4), to enterprise quality.
3. **Verified** — the project builds and the relevant test suites pass (§6).
4. **Traceable** — links back to the specifying requirement/entity per traceability rules.
5. **Documented** — the sprint file, handoff notes, and logs under `docs/` are updated (§5); no stale documentation.
6. **Free of deprecated terminology** — no legacy references remain in the touched scope (§3).
