# PCAM-24: A Phase-Centric Action Model for Deterministic Interactive Simulation

## Abstract
This paper introduces **PCAM-24 (Phase-Centric Action Model)**, a simulation model in which discrete phase—rather than continuous time—is the authoritative coordinate for action progression, interaction adjudication, and cross-system synchronization. PCAM-24 defines actions as cyclic phase processes within a fixed modular phase space and expresses all gameplay-relevant semantics (e.g., hit windows, invulnerability, cancels, transitions) as phase membership.

By elevating phase to a first-class primitive, PCAM-24 eliminates timing inference between subsystems, simplifies deterministic networking, and provides a shared semantic language for animation, combat, AI, and tooling. The model is particularly suited to real-time interactive simulations where perceived fairness, explainability, and determinism are critical.

---

## 1. Introduction

Modern interactive simulations—especially games—coordinate multiple subsystems that each operate on their own notion of time. Animation systems advance clip time, combat systems evaluate frame-based windows, AI systems reason in ticks, and networking layers reconcile wall-clock deltas. While effective in practice, this fragmentation produces systemic problems:

* Desynchronization between animation and gameplay outcomes.
* Brittle timing logic expressed in floats or frame counts.
* Ambiguous interaction adjudication under latency.
* Difficulty explaining or debugging why a given outcome occurred.

PCAM-24 proposes a different foundation: replace time as the primary semantic coordinate with **discrete phase**. Rather than asking *"when did something happen?"*, PCAM-24 asks *"what stage of the action is occurring?"*

## 2. Core Concept

### 2.1 Phase Space

PCAM-24 defines a fixed, cyclic phase space:

$$\text{Phase} \in \mathbb{Z}_{24} = \{0, 1, \dots, 23\}$$

Phase advances discretely and wraps modulo 24. Phase progression is monotonic and integer-valued; partial phases and fractional authority are disallowed.

The choice of 24 phases reflects a balance between expressive resolution and cognitive tractability. It provides sufficient granularity to model nuanced action stages while remaining small enough to reason about as a complete cycle.

### 2.2 Actions

An **action** is the atomic unit of interactive intent (e.g., attack, dodge, parry, block). Each action owns:

* A current phase value.
* A phase progression rule.
* A set of named phase windows.
* Deterministic transition rules.

An action is not measured in seconds. It progresses through semantic stages defined by phase.

## 3. Phase Windows and Semantics

A **phase window** is a named subset of the phase space. Formally, each window is a set:

$$W \subseteq \mathbb{Z}_{24}$$

Examples include:

* **ACTIVE:** Phases during which an attack can deal damage.
* **INVULN:** Phases during which damage is negated.
* **PARRY_PERFECT:** Phases during which a parry fully succeeds.
* **RECOVERY:** Phases during which vulnerability or restrictions apply.

All authoritative gameplay decisions are reduced to window membership tests. Time, frame count, or animation normalized time are never authoritative inputs.

## 4. Phase Authority

PCAM-24 enforces several non-negotiable axioms:

* **Phase is authoritative:** Phase is the sole coordinate for ordering, gating, and adjudication of actions and interactions.
* **Discrete semantics:** All gameplay-relevant logic must be expressible as phase membership or phase-gated transitions.
* **Monotonic progression:** Phase advances monotonically on the cyclic ring. Rewinds are prohibited; corrections occur by snapping phase or restarting actions.
* **Subsystem subscription:** Subsystems (animation, combat, AI, VFX, audio, UI) observe phase and windows but do not advance or reinterpret them.
* **Interaction resolution by phase:** Cross-entity interactions are resolved exclusively through phase-defined rules and explicit precedence.

## 5. Action Lifecycles

PCAM-24 categorizes actions by lifecycle semantics:

* **Impulse actions** (e.g., melee attack): Progress once through the phase cycle and terminate.
* **Sustained actions** (e.g., block hold): Loop through phases while input is maintained.
* **Reactive actions** (e.g., parry): Feature narrow success windows and defined failure recovery.
* **Evasive actions** (e.g., dodge roll): Include invulnerability or mitigation windows and phase-driven movement.

Each action must explicitly define what phase wrap means (termination, loop, or clamp), preventing ambiguous lifecycle behavior.

## 6. Cancels, Transitions, and Buffering

Action transitions—such as cancels, combos, or state changes—are phase-gated:

1.  Cancels are allowed only within defined windows.
2.  Inputs may be buffered and consumed deterministically when entering valid windows.
3.  If multiple transitions are eligible simultaneously, a deterministic priority rule resolves the outcome.

This replaces ad-hoc frame windows and timing constants with inspectable, authorable phase logic.

## 7. Interaction Adjudication

PCAM-24 requires explicit precedence for contested interactions. A canonical ordering is:

1.  **Evasion** (e.g., invulnerability windows)
2.  **Perfect defense** (e.g., parry)
3.  **Mitigating defense** (e.g., block)
4.  **Hit** (full effect)

When an attack intersects a defender, the outcome is determined by the defender’s current action and phase windows—not by animation timing, render order, or wall-clock arrival.

This makes outcomes deterministic, explainable, and consistent under network conditions.

## 8. Networking and Determinism

PCAM-24 is inherently network-friendly because it replicates **phase**, not time.

* Network replication transmits action identifiers, phase values, and any start-captured parameters.
* Clients may predict locally by advancing phase using the same rules.
* Reconciliation is performed by snapping phase to authoritative values, optionally with bounded grace policies expressed in phase units.

No reconstruction of exact elapsed time is required.

## 9. Tooling and Explainability

A PCAM-24 system is only viable if its semantics are visible. Tooling must allow inspection of:

* Current action and phase.
* Active phase windows.
* Eligible transitions and their conditions.
* Recent interaction outcomes.

By making phase explicit, PCAM-24 transforms timing bugs from opaque artifacts into inspectable state.

## 10. Benefits and Implications

PCAM-24 offers several advantages over time-centric models:

* **Consistency:** Animation, combat, AI, and networking share a single semantic clock.
* **Determinism:** Discrete phase eliminates floating-point drift and ambiguous comparisons.
* **Explainability:** Outcomes can be justified directly by phase membership.
* **Authorability:** Designers reason in semantic stages rather than frames or milliseconds.
* **Extensibility:** New systems subscribe to phase without redefining timing logic.

## 11. Non-Goals

PCAM-24 does not attempt to:

* Replace continuous physics solvers.
* Reform civil or real-world timekeeping.
* Mandate a specific engine or update loop.
* Eliminate all uses of real time in rendering or presentation.

It defines only what is authoritative for **interaction semantics**.

## 12. Conclusion

PCAM-24 reframes interactive simulation timing from a question of *when* to a question of *what stage*. By elevating discrete phase to a first-class primitive, it provides a unifying semantic substrate across subsystems that are traditionally decoupled and inference-driven. The result is a model that is deterministic, inspectable, and aligned with how players perceive actions—not as durations, but as stages of intent.

PCAM-24 is not an optimization of existing timing systems. It is a replacement of their underlying assumption.