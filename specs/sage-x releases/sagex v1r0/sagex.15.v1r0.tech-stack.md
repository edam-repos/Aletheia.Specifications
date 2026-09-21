# SAGEX Technology Stack Specification

Normative Rules for Implementation Discipline, Abstraction Enforcement, and Platform Integrity

## 1 Scope

This specification defines the **technology stack selection and usage constraints** for implementing SAGE‑X.  
It establishes:

  - allowed architectural usage patterns
  - abstraction enforcement rules
  - constraints to avoid leakage, implicit behavior, and over‑coupling  
    The objective is to ensure that **any chosen stack conforms to SAGE‑X architectural guarantees**.

## 2 Conformance

An implementation conforms if it:

  - enforces all abstraction boundaries
  - prevents implicit execution behavior
  - ensures all logic flows through defined models (plan, state machine, APIs)
  - avoids direct coupling between core and external components

## 3 Core Principles

**3.1 Architecture First**

Technology shall implement the architecture, not influence it.

**3.2 Controlled Abstraction**

All external capabilities shall be accessed only through defined abstraction layers.

**3.3 Deterministic Execution**

System behavior shall be driven exclusively by:

  - Execution Plan
  - State Machine

**3.4 No Implicit Logic**

No framework, library, or tool shall introduce hidden execution behavior.

## 4 Approved Stack (Reference Implementation)

**4.1 Core Runtime**

  - Language: C\# (.NET)
  - Requirement: strongly typed models aligned with schemas

**4.2 Orchestration Layer**

  - Framework: Semantic Kernel (restricted role)
  - Usage: Capability Adapter ONLY

**4.3 Data Layer**

  - Primary datastore: Microsoft SQL Server
  - Usage:
      - execution state
      - plans
      - governance
      - trace

**4.4 API Layer**

  - Framework: ASP.NET (or equivalent)
  - Requirement: strict schema enforcement

**4.5 Capability Layer**

  - Implemented through Capability Registry
  - SK, LLMs, and tools integrated via adapters only

## 5 Mandatory Abstraction Rules

**5.1 Execution Core Ownership**

  - Execution flow shall be controlled ONLY by SAGE‑X Execution Core
  - No external framework shall orchestrate execution

**5.2 Capability Isolation**

  - Capabilities shall not:
      - access internal state directly
      - control execution flow
  - All interactions must go through Capability Registry

**5.3 No Direct Framework Coupling**

The system shall not:

  - embed framework logic into execution plan
  - depend on framework-specific behavior

**5.4 Adapter Pattern Enforcement**

All external integrations shall follow:

Execution Core → Capability Registry → Adapter → External Tool

## 6 Semantic Kernel Constraints

**6.1 Allowed Usage**

Semantic Kernel may be used for:

  - prompt execution
  - capability abstraction

**6.2 Prohibited Usage**

Semantic Kernel shall NOT:

  - control execution flow
  - replace planning logic
  - act as orchestrator

**6.3 Integration Rule**

All SK functionality shall be wrapped in capability adapters.

## 7 Data Layer Rules

**7.1 State Integrity**

  - Execution state shall be authoritative in database
  - No in-memory-only critical state allowed

**7.2 Consistency**

  - Transactions shall ensure atomic state updates

**7.3 Schema Alignment**

  - Database models shall match canonical schemas

## 8 Control Layer Rules

**8.1 API Enforcement**

  - All system interaction shall go through API layer
  - No direct internal manipulation permitted

**8.2 Contract Enforcement**

  - API inputs/outputs shall conform to defined schemas

## 9 Observability and Trace Rules

**9.1 Mandatory Trace**

  - Every operation shall generate trace events

**9.2 No Silent Execution**

  - No operation may execute without trace logging

## 10 Anti-Patterns (Strictly Prohibited)

The system shall NOT:

  - allow Semantic Kernel planners to drive logic
  - bypass Execution Plan
  - bypass State Machine
  - embed hidden workflows
  - invoke tools directly from business logic
  - mutate execution state outside Execution Core

## 11 Enforcement Mechanisms

The implementation shall enforce:

  - module isolation (separate layers)
  - schema validation at boundaries
  - adapter-only integration
  - execution path validation

## 12 Architectural Guarantees

This specification ensures:

**12.1 Control**

Execution remains fully governed by SAGE‑X

**12.2 Predictability**

No hidden or implicit behaviors affect outcomes

**12.3 Portability**

Stack can be replaced without changing architecture

**12.4 Integrity**

System behavior cannot drift due to tooling

## 13 Conclusion

This specification defines how technologies are used within SAGE‑X while preserving architectural integrity.  
It ensures that:

  - abstraction is enforced
  - execution remains deterministic
  - no framework compromises system design  
    This transforms the stack into:  
    **an implementation detail, not a system dependency**
