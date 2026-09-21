# SAGEX Execution State Machine Specification 

Deterministic Execution Control, State Management, and Recovery Model

## 1\. Scope

This specification defines the **Execution State Machine** for the SAGE‑X platform.

It establishes:

  - execution state definitions
  - valid state transitions
  - failure handling behavior
  - checkpoint and recovery semantics
  - human and autonomous interaction boundaries

This specification is **technology-agnostic** and defines **behavioral requirements only**.

## 2\. Conformance

An implementation conforms if it:

  - implements all required states and transitions
  - enforces all constraints defined with **“shall”**
  - preserves traceability and recoverability guarantees

## 3\. Core Concepts

### 3.1 Execution Instance

A single runtime realization of an execution plan.

### 3.2 Execution State

The current condition of an execution instance.

### 3.3 State Transition

A change from one valid state to another, governed by defined rules.

### 3.4 Checkpoint

A validated state eligible for rollback and resumption.

### 3.5 Failure Origin

The first state where execution becomes invalid.

## 4\. State Definitions

An execution instance **shall** operate within the following states:

### 4.1 INITIALIZED

  - Execution instance created
  - Plan loaded and validated

### 4.2 READY

  - Execution is ready to start
  - All prerequisites satisfied

### 4.3 RUNNING

  - Active execution of steps
  - May include sub-states per step

### 4.4 WAITING\_FOR\_INPUT

  - Awaiting human or external input
  - Execution paused without failure

### 4.5 WAITING\_FOR\_APPROVAL

  - Awaiting governance or human approval
  - Execution blocked at controlled checkpoint

### 4.6 VALIDATING

  - Execution step results under evaluation
  - No forward progression allowed

### 4.7 COMPLETED\_STEP

  - A step completed successfully
  - Eligible for checkpoint creation

### 4.8 CHECKPOINTED

  - Execution state recorded and validated
  - Eligible for rollback target

### 4.9 BRANCHED

  - Alternate execution path created
  - Original path preserved

### 4.10 FAILED

  - Validation failure detected
  - Execution cannot proceed forward

### 4.11 RECOVERING

  - System performing rollback or correction
  - Transitional state only

### 4.12 RESUMED

  - Execution restarted from a checkpoint
  - Continues with corrected state

### 4.13 COMPLETED

  - Entire plan executed successfully

## 5\. State Transition Requirements

### 5.1 Initialization Flow

INITIALIZED → READY → RUNNING

The system **shall not** enter RUNNING unless:

  - plan validation is complete
  - initial conditions are satisfied

### 5.2 Step Execution Flow

RUNNING → VALIDATING → COMPLETED\_STEP → CHECKPOINTED → RUNNING

The system:

  - **shall** validate every step
  - **shall not** proceed if validation fails

### 5.3 Human Interaction Flow

RUNNING → WAITING\_FOR\_INPUT → RUNNING

RUNNING → WAITING\_FOR\_APPROVAL → RUNNING

The system:

  - **shall** preserve execution state during waiting
  - **shall** record all human actions

### 5.4 Failure Flow

RUNNING → VALIDATING → FAILED

Upon failure:

  - system **shall** stop forward execution
  - system **shall** identify failure origin

### 5.5 Recovery Flow

FAILED → RECOVERING → CHECKPOINTED → RESUMED → RUNNING

The system:

  - **shall** rollback to last valid checkpoint
  - **shall** discard invalid state
  - **shall** resume from validated state only

### 5.6 Branching Flow

CHECKPOINTED → BRANCHED → RUNNING

The system:

  - **may** create alternative execution paths
  - **shall** preserve original path integrity

### 5.7 Completion Flow

RUNNING → COMPLETED

The system **shall only enter COMPLETED if**:

  - all steps executed
  - all validations passed

## 6\. State Integrity Rules

### 6.1 Valid State Guarantee

At any time:

  - exactly one primary state **shall** be active
  - all transitions **shall** be valid

### 6.2 No Invalid State Propagation

The system **shall not**:

  - allow execution beyond FAILED
  - allow resumption from non-checkpoint state

### 6.3 Determinism

Given identical:

  - execution plan
  - inputs
  - specification version

The state transitions **shall be identical**

## 7\. Checkpoint Requirements

### 7.1 Checkpoint Creation

A checkpoint **shall** be created when:

  - a step completes
  - validation passes
  - or explicitly defined in plan

### 7.2 Checkpoint Properties

Each checkpoint **shall** include:

  - state snapshot
  - step reference
  - validation status
  - recoverability flag

### 7.3 Checkpoint Usage

  - rollback **shall** target the nearest valid checkpoint
  - checkpoints **shall not** include invalid states

## 8\. Failure Localization Requirements

### 8.1 Failure Identification

The system **shall**:

  - identify the first failing step
  - identify rule or dependency violation

### 8.2 Failure Classification

Failures **should** be classified as:

  - validation failure
  - dependency failure
  - input inconsistency
  - override-induced inconsistency

## 9\. Recovery Requirements

### 9.1 Rollback Scope

The system **shall**:

  - revert state to last valid checkpoint
  - remove all invalid derived data

### 9.2 Resume Behavior

The system **shall**:

  - resume only from checkpointed state
  - reapply execution logic deterministically

## 10\. Human Interaction Requirements

### 10.1 Interaction Safety

Human intervention:

  - **shall not** bypass validation
  - **shall** be traceable
  - **shall** produce a new execution state

### 10.2 Override Handling

Overrides:

  - **shall** trigger validation
  - **may** create new execution branch

### 10.3 Scoped Mode Interaction

Execution mode may be controlled at plan, segment, or step scope.

Mode changes do not create a separate execution state. Instead, they influence whether execution may continue automatically or must transition through:

  - WAITING_FOR_INPUT
  - WAITING_FOR_APPROVAL

The system **shall** record the active segment, effective execution mode, and any governed mode change in traceable execution state or trace records.

The system **shall not** use a mode change to bypass validation, approval, checkpoint, recovery, or failure handling requirements.

## 11\. Observability Requirements

The system **shall** expose:

  - current state
  - state history
  - transition log
  - failure history

## 12\. Architectural Guarantees

The execution state machine ensures:

  - **traceability** → every state transition recorded
  - **recoverability** → no permanent execution failure
  - **determinism** → predictable state evolution
  - **control** → human and machine parity

## 13\. Conclusion

This specification defines the formal execution behavior of SAGE‑X as a **state-driven system**. It ensures that all execution is:

  - governed by explicit states
  - constrained by valid transitions
  - resilient to failure
  - fully recoverable

The Execution State Machine, together with the Execution Plan schema, forms the **core runtime contract** of the platform.
