# PCAM-24 Formal Specification v1.0

**Status:** Stable  
**Scope:** Normative  
**Audience:** Engine programmers, technical designers, system architects

This document defines the **Phase-Centric Action Model (PCAM-24)** in precise, implementation-independent terms.  
All conforming systems MUST adhere to the requirements stated herein.

---

## 1. Definitions

### 1.1 Phase

A **phase** is a discrete integer representing an action’s position within its lifecycle.

- Phase ∈ ℤ₍₂₄₎ = {0, 1, …, 23}
- Phase arithmetic MUST be performed modulo 24
- Phase values MUST be integer
- Fractional or continuous phase values are forbidden

---

### 1.2 Action

An **action** is a stateful process representing a unit of interactive intent (e.g., attack, dodge, block).

Each action MUST define:
- a current phase value
- a phase progression rule
- one or more phase windows
- explicit transition semantics

Only actions are permitted to own or advance phase.

---

### 1.3 Phase Window

A **phase window** is a named subset of the phase space.

Formally:

Window ⊆ ℤ₍₂₄₎


Phase windows encode gameplay semantics including but not limited to:
- damage authority
- invulnerability
- cancel eligibility
- vulnerability or recovery
- transition availability

All gameplay-relevant semantics MUST be expressible through phase windows.

---

## 2. Phase Authority

### 2.1 Authoritative Coordinate

Phase is the **sole authoritative coordinate** for:
- action progression
- interaction adjudication
- cancel and transition eligibility
- deterministic resolution across subsystems

Time MUST NOT be used as an authoritative input for gameplay semantics.

---

### 2.2 Derived Time

Elapsed time MAY be used for:
- rendering
- animation sampling
- interpolation
- audio playback

Derived time MUST NOT influence:
- phase progression
- phase window membership
- interaction outcomes

---

## 3. Phase Progression

### 3.1 Discrete Advancement

Phase MUST advance discretely in integer steps.

Valid advancement:

phase_next = (phase_current + k) mod 24

where `k ≥ 0` is an integer.

Fractional advancement is forbidden.

---

### 3.2 Determinism

Given identical inputs, phase progression MUST be deterministic.

Phase advancement rules MUST:
- be integer-based
- avoid floating-point dependence
- be invariant across platforms

---

## 4. Action Lifecycle Semantics

### 4.1 Lifecycle Completion

Each action MUST define its behavior upon phase wrap:

- **TERMINATE** — action ends and yields control
- **LOOP** — action repeats its phase cycle
- **CLAMP** — phase is held at a terminal value

Undefined wrap behavior is forbidden.

---

### 4.2 Transitions

Actions MAY define transitions to other actions.

Transitions MUST be:
- phase-gated
- explicitly defined
- deterministic

Implicit or time-based transitions are forbidden.

---

## 5. Interaction Adjudication

### 5.1 Window-Based Resolution

All interactions between entities MUST be resolved by evaluating:
- the interacting actions
- their current phases
- their active phase windows

No interaction outcome may depend on:
- animation state
- frame order
- wall-clock time
- network arrival order

---

### 5.2 Precedence

When multiple phase windows apply, a deterministic precedence rule MUST be defined.

Typical precedence (example):
1. Invulnerability
2. Perfect defense
3. Mitigating defense
4. Hit

The precedence ordering MUST be explicit and stable.

---

## 6. Cancellation Semantics

### 6.1 Cancel Eligibility

Cancels MUST be defined as phase windows.

An action MAY only cancel into another action if:
- the current phase lies within a defined cancel window
- the target action is explicitly permitted

---

### 6.2 Buffered Inputs

Input buffering MAY be supported.

Buffered inputs MUST:
- be consumed only when entering a valid phase window
- resolve deterministically
- not bypass phase restrictions

---

## 7. Subsystem Interaction

### 7.1 Subscription Model

Subsystems (animation, AI, VFX, audio, networking) MUST subscribe to phase.

Subsystems MUST NOT:
- infer action stage from time
- advance phase independently
- override phase authority

---

### 7.2 Presentation

Presentation systems MAY derive visuals from phase.

Presentation MUST NOT influence gameplay outcomes.

---

## 8. Networking and Replication

### 8.1 Replicated State

A networked PCAM-24 system MUST replicate:
- action identity
- phase value
- start-captured parameters

Elapsed time MUST NOT be replicated as authoritative state.

---

### 8.2 Reconciliation

Phase reconciliation MUST be:
- discrete
- deterministic
- bounded in phase units

Phase snapping is permitted.
Phase interpolation for authority is forbidden.

---

## 9. Error Handling

### 9.1 Invalid States

The following are invalid:
- fractional phase values
- undefined phase windows
- time-based adjudication
- implicit transitions

Implementations MUST fail fast or correct deterministically.

---

## 10. Conformance

An implementation conforms to PCAM-24 if and only if:

- phase is the sole authoritative coordinate
- all gameplay semantics are phase-window-based
- phase progression is discrete and deterministic
- subsystems subscribe without inference
- time is strictly derived

---

## 11. Versioning

This document defines **PCAM-24 v1.0**.

Future revisions MUST:
- preserve phase authority
- remain backward-compatible unless explicitly stated

---

## 12. Final Statement

PCAM-24 defines a model in which **stage, not time, is the source of truth**.

This specification is intentionally minimal.
It defines what MUST be true, not how it must be implemented.
EOF