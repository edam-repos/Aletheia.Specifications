# READ ME FIRST: How to Start a Project

**For humans and AI Coder Agents.** This file gets a project from zero to a first working sprint. It is the entry point; the rules themselves live in `AGENTS.md`.

| File | Purpose |
|---|---|
| `AGENTS-README-FIRST.md` (this file) | How to start and how to run a session |
| `AGENTS.md` | Standing rules: priorities, workflow, standards, Definition of Done |
| `docs/framework/templates.md` | Templates; open only when creating an artifact |

**Rule of thumb:** if you are an agent and you have not read `AGENTS.md` in this session, stop and read it before doing anything else.

---

## Part A: Starting a New Project

### Step 1. Install the framework (human, 5 minutes)

Place the three files in the repository root as shown above, then create the folder skeleton below it:

```text
docs/
├── framework/
├── requirements/
│   ├── use-cases/
│   └── user-stories/
├── architecture/
│   ├── conceptual/
│   ├── logical/
│   ├── physical/
│   ├── security/
│   ├── data/
│   └── integration/
├── backlog/
│   └── archive/
├── validation/
│   ├── sprint-validation/
│   ├── release-validation/
│   └── pm-review/
├── handoff/
├── execution/
│   ├── status-reports/
│   ├── implementation-notes/
│   ├── release-notes/
│   └── lessons-learned/
├── backlog/backlog-index.md
├── validation/validation-index.md
├── risks-and-dependencies.md
└── handoff/
    ├── current-handoff.md
    ├── handoff-history.md
    └── decisions-log.md
```

**No terminal needed:** create the folders and empty files above by hand in File Explorer, Finder, or your IDE's file tree. That's all Step 1 requires.

**Prefer a terminal?** Pick the one that matches your system.

*Windows — PowerShell:*
```powershell
$dirs = "framework","requirements\use-cases","requirements\user-stories",
        "architecture\conceptual","architecture\logical","architecture\physical",
        "architecture\security","architecture\data","architecture\integration",
        "backlog\archive","validation\sprint-validation","validation\release-validation",
        "validation\pm-review","handoff","execution\status-reports",
        "execution\implementation-notes","execution\release-notes","execution\lessons-learned"
foreach ($d in $dirs) { New-Item -ItemType Directory -Force -Path "docs\$d" | Out-Null }

$files = "handoff\current-handoff.md","handoff\handoff-history.md","handoff\decisions-log.md",
         "backlog\backlog-index.md","validation\validation-index.md","risks-and-dependencies.md"
foreach ($f in $files) { New-Item -ItemType File -Force -Path "docs\$f" | Out-Null }
```

*macOS / Linux / Git Bash — bash:*
```bash
mkdir -p docs/{framework,requirements/{use-cases,user-stories},backlog/archive,validation/{sprint-validation,release-validation,pm-review},handoff,execution/{status-reports,implementation-notes,release-notes,lessons-learned}}
mkdir -p docs/architecture/{conceptual,logical,physical,security,data,integration}
touch docs/handoff/{current-handoff.md,handoff-history.md,decisions-log.md}
touch docs/backlog/backlog-index.md docs/validation/validation-index.md docs/risks-and-dependencies.md
```

Small project? You may merge the charter, workplan, and roadmap into one file, as long as every required section is kept (see `AGENTS.md` §17).

### Step 2. Choose where the backlog lives (human decision)

This is a one-time decision per project. It decides which system the agent treats as the single source of truth for work items (`AGENTS.md` §4, §8) — never run both at once.

**Option (a): In the repo, as markdown files under `docs/backlog/`**
Choose this if:
- You have no project tracker yet, or don't want to pay for/administer one.
- The team is small, or is mostly the AI agent plus one or two people.
- You want backlog history to live in git, versioned alongside the code.

How it works: the agent reads and writes `docs/backlog/backlog-index.md` and the work-item files directly, using the templates in `templates.md`. Nothing else to set up.

**Option (b): An external tracker (Azure DevOps, Jira, GitHub Issues, Linear, etc.)**
Choose this if:
- The team already uses one of these tools for other projects.
- Non-technical stakeholders need to view or comment on backlog items without opening the repo.
- You need reporting, dashboards, or integrations the tracker already provides.

How it works: you tell the agent which tool and give it access (e.g., a connected app or exported/synced items it can read). The agent still follows the same statuses, priorities, and item fields from `AGENTS.md` §8, just recorded in that tool instead of markdown files. `docs/backlog/backlog-index.md` is not used; if the agent cannot reach the tracker directly, it works from an export or summary you provide.

**How to decide, in practice:** if you're not sure, start with (a). It requires no setup and can be exported into a tracker later if the project grows. Switching from (a) to (b) mid-project is possible but is itself a decision that should be logged (`decisions-log.md`).

**How to tell the agent:** include your choice in the kickoff prompt in Step 4 — the line `Backlog location: [...]` is where this goes.

### Step 3. Gather the kickoff inputs (human)

The agent cannot invent these. Provide what you know; the agent will list the gaps and ask.

- [ ] **Problem:** what is broken or missing, and for whom?
- [ ] **Objectives and success metrics:** how will we know it worked?
- [ ] **Scope and out-of-scope:** what is explicitly not included?
- [ ] **Stakeholders:** who decides, who approves, who is affected?
- [ ] **Constraints:** deadline, budget assumptions, compliance, security, existing systems
- [ ] **Technology:** language, frameworks, hosting, and the commands to build, test, and lint (or "undecided")
- [ ] **Autonomy limits:** anything beyond `AGENTS.md` §5 that needs your approval

### Step 4. Give the agent the kickoff prompt

Paste this into the agent, filling in the brackets:

```text
Read AGENTS-README-FIRST.md and AGENTS.md completely. This is a new project.

Project: [name]
Problem: [...]
Objectives: [...]
Scope / out of scope: [...]
Stakeholders: [...]
Constraints: [...]
Technology: [...]
Backlog location: [repo /docs/backlog | tracker name]

Execute Phase 1 (Initiation) only: create the project charter and stakeholder
register using docs/framework/templates.md. List any missing information as
questions and do not assume answers. Log assumptions in docs/handoff/decisions-log.md.
Create the first current-handoff.md before you stop.
```

### Step 5. Gate 1: Approve Initiation (human)

Review `project-charter.md` and `stakeholder-register.md`. Check that the problem, objectives, scope, and success metrics are correct and measurable. Tell the agent **approved** or give corrections. Do not proceed to planning on an unapproved charter.

### Step 6. Run Planning (agent, then human review)

Prompt: *"Charter approved. Execute Phase 2 (Planning)."*

The agent produces, in this order:

1. `project-specifications.md`: vision, requirements, acceptance criteria, and a **Technical Specifications** section with the build/test/lint commands and repo conventions
2. `high-level-architecture.md` and the initial requirements documents
3. `risks-and-dependencies.md` with the initial risks and dependencies
4. `project-workplan.md` and `solution-roadmap.md`
5. The initial backlog: Goal → Epics → Features → Stories, with priorities (P1–P4)
6. Sprint 001 plan, with a sprint goal and only the work that supports it

### Step 7. Gate 2: Approve Planning (human)

Check: requirements match your intent · architecture is acceptable (Significant items get real review, per `AGENTS.md` §6) · risks are honest · Sprint 001 is small enough to finish. Approve, or send it back.

**Recommended Sprint 001:** a thin end-to-end "walking skeleton" (repository, build, test harness, CI, one trivial feature deployed). It proves the pipeline and the framework before real complexity arrives. This is a suggestion, not a rule.

### Step 8. Execute

Prompt: *"Planning approved. Begin Sprint 001 following AGENTS.md §7."* From here, every session follows Part B.

---

## Part B: Every Session

### Session start (agent, mandatory)

Read, in this order:

1. `AGENTS.md` (skim if already known; always re-check §5 and §6)
2. `docs/handoff/current-handoff.md`
3. `docs/backlog/backlog-index.md` (or the tracker)
4. `docs/risks-and-dependencies.md`
5. Relevant items in requirements, architecture, and `decisions-log.md`

Then **confirm to the human**: current sprint, in-progress work, blockers, and the plan for this session. Conflicting or missing information gets reported, not silently resolved (`AGENTS.md` §4).

### During the session

- New request or newly discovered requirement? Classify the tier (`AGENTS.md` §6), then follow the workflow (§7). Backlog first, then code.
- Unsure, or facing an action listed in `AGENTS.md` §5? **Stop and ask.**
- Keep changes small and traceable to a backlog item.

### Session end (agent, mandatory)

Complete the session-exit checklist in `AGENTS.md` §13, then finish with a short human-readable summary: what was done, what is validated, what is open, and the recommended next work item. A session is not finished until `current-handoff.md` is updated.

### Human responsibilities each session

- Review Significant, security-relevant, and production-bound changes. Review AI output the same as any other.
- Approve or reject Go/No-Go recommendations.
- Answer the agent's open questions; unanswered questions become blockers.

---

## Part C: Adopting the Framework on an Existing Project

1. Install the files and folder skeleton (Step 1).
2. Ask the agent to **discover, not change**: *"Read the repository and produce a current-state summary: architecture, components, build/test commands, tests present, known issues. Do not modify code."*
3. Have it draft a *lightweight* charter, requirements, and architecture from what exists, marked **Draft, needs human confirmation**. Correct them.
4. Load existing open work into the backlog. Record known technical debt as backlog items (`AGENTS.md` §9.9).
5. Create the first `current-handoff.md`, then continue with Part B. No retroactive paperwork beyond what delivery needs.

---

## Quick Reference

| I want to... | Do this |
|---|---|
| Start a project | Part A, Steps 1–8 |
| Resume work | Part B, session start |
| Fix a typo | Trivial tier: one-line backlog entry, tests pass |
| Add a feature | Standard tier: full workflow |
| Change architecture, security, or a data model | Significant tier: human review plus PM Go/No-Go |
| Understand a rule | `AGENTS.md` (section numbers are cited above) |
| Create a document | `docs/framework/templates.md` |
| Skip a rule | Formal exception, `AGENTS.md` §15 |

**Remember:** the project is backlog-driven, validation-driven, and handoff-driven. If it is not in the backlog, validated, and handed off, it is not done.
