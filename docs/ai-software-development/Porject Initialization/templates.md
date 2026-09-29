# Framework Templates

Companion to [`AGENTS.md`](../../AGENTS.md). Open this file only when creating or updating the artifact concerned. Fields marked *(Trivial: skip)* may be omitted for Trivial changes; see AGENTS.md Section 6.

---

## 1. Phase 1–2 Deliverables: Required Contents

**project-charter.md**: Project Name · Business Problem · Business Opportunity · Business Objectives · Business Value · Strategic Alignment · Scope · Out of Scope · Assumptions · Constraints · Risks · Success Metrics · Approval Criteria

**stakeholder-register.md**: Stakeholder · Role · Responsibilities · Authority · Communication Requirements · Approval Responsibilities

**project-workplan.md**: Scope · WBS · Timeline · Milestones · Sprint Plan · Resource Plan · Risk Plan · Dependency Plan · Communication Plan

**solution-roadmap.md**: Current State · Future State · Releases · Major Milestones · Dependencies · Release Strategy

**project-specifications.md**: Vision · Requirements · User Stories · Use Cases · Acceptance Criteria · Validation Requirements · Technical Specifications

---

## 2. Backlog

### Sprint record
Sprint Goal · Planned Work · Completed Work · Risks · Dependencies · Validation Activities · Sprint Summary

### Work Item

```text
ID:
Title:
Type:
Priority:            (P1 Critical / P2 High / P3 Medium / P4 Low)
Status:              (see AGENTS.md Section 8)
Sprint:
Business Objective:
Requirement References:
User Story References:
Dependencies:
Description:
Acceptance Criteria:
Validation Criteria:
Definition Of Done:
Implementation Notes:
Testing Notes:
Produced Artifacts:
Risks:
Next Actions:
```

Trivial changes: ID · Title · Status · one-line Description is sufficient.

---

## 3. Requirements

### Use Case

```text
Use Case ID:
Name:
Objective:
Business Value:
Actors:
Preconditions:
Trigger:
Main Flow:
Alternate Flow:
Exception Flow:
Business Rules:
Data Requirements:
Validation Criteria:
Acceptance Criteria:
Definition Of Done:
```

### User Story

```text
Story ID:
As a <Role>
I want <Capability>
So that <Business Value>
Priority:
Story Points:
Dependencies:
Acceptance Criteria:
Validation Criteria:
Definition Of Done:
```

---

## 4. Architecture and Decisions

### Architecture Decision Record (ADR)

```text
ADR-ID:
Date:
Status:
Context:
Problem Statement:
Decision:
Alternatives Considered:
Consequences:
Impacted Components:
Related Requirements:
Related Work Items:
```

### Decision (decisions-log.md)

```text
Decision ID:
Date:
Category:
Decision:
Reason:
Alternatives Considered:
Impact:
Owner:
Status:
```

---

## 5. Risks and Dependencies (`risks-and-dependencies.md`)

### Risk

```text
Risk ID:
Description:
Probability:
Impact:
Severity:
Mitigation Plan:
Contingency Plan:
Owner:
Status:
```

### Dependency

```text
Dependency ID:
Description:
Required By:
Owner:
Impact:
Status:
```

---

## 6. Validation

### Work Item Validation

```text
Validation ID:
Work Item:
Summary:
Requirements Covered:
Acceptance Criteria Results:
Testing Performed:
Issues Found:
Technical Debt:
Risks:
Recommendation:
```

### Sprint Validation

```text
Sprint:
Sprint Goal:
Planned Work:
Completed Work:
Deferred Work:
Defects:
Risks:
Lessons Learned:
Quality Assessment:
PM Recommendation:
```

### PM Go / No-Go Review

```text
Work Item / Release:
Requirements Satisfied:           [ ]
Acceptance Criteria Met:          [ ]
Validation Completed:             [ ]
Risks Accepted or Mitigated:      [ ]
Dependencies Resolved:            [ ]
Architecture Aligned:             [ ]
Documentation Updated:            [ ]
Handoff Prepared:                 [ ]
Outcome:                          GO / GO WITH CONDITIONS / HOLD / REJECT
Conditions (if any):
```

---

## 7. Handoff (`current-handoff.md`)

```text
Handoff ID:
Date:
Agent:
Project Status:
Current Sprint:
Completed Work:
In Progress Work:
Open Work:
Open Risks:
Open Dependencies:
Decisions Made:
ADRs Created:
Artifacts Produced:
Files Updated:
Validation Status:
Recommended Next Actions:
Recommended Next Work Item:
Additional Notes:
```

---

## 8. Exception Request

```text
Exception ID:
Rule Being Excepted:
Business Justification:
Risk Assessment:
Compensating Controls:
Expiration Date:
Remediation Plan:
Approver:
```

Exceptions never exempt traceability, compliance obligations, audit requirements, or risk documentation.
