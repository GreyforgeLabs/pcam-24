# PCAM-24: A Phase-Centric Action Model
*For Deterministic Interactive Simulation*

!!! quote "Abstract"
    This paper introduces **PCAM-24**, a simulation model in which discrete phase—rather than continuous time—is the authoritative coordinate for action progression. By elevating phase to a first-class primitive, PCAM-24 eliminates timing inference between subsystems, simplifies deterministic networking, and provides a shared semantic language for animation, combat, and AI.

## 1. Introduction
Modern interactive simulations—especially games—coordinate multiple subsystems that each operate on their own notion of time. Animation advances clip time, combat evaluates frame-based windows, and networking reconciles wall-clock deltas. This fragmentation produces:
* Desynchronization between animation and gameplay
* Brittle timing logic (floats/frames)
* Ambiguous interaction adjudication

PCAM-24 proposes a different foundation: replace time as the primary semantic coordinate with **discrete phase**. Rather than asking "when did something happen?", PCAM-24 asks "what stage of the action is occurring?"

## 2. Core Concept

### 2.1 Phase Space
PCAM-24 defines a fixed, cyclic phase space:

$$Phase \in \mathbb{Z}_{24} = \{0,1,\dots,23\}$$

Phase advances discretely and wraps modulo 24.

The choice of 24 phases reflects a balance between expressive resolution and cognitive tractability. It provides sufficient granularity to model nuanced action stages while remaining small enough to reason about as a complete cycle.

### 2.2 Actions
An action is the atomic unit of interactive intent. Each action owns:
* A current phase value
* A phase progression rule
* A set of named **phase windows**
* Deterministic transition rules

## 3. Phase Windows and Semantics
A phase window is a named subset of the phase space $W \subseteq \mathbb{Z}_{24}$.
* **ACTIVE:** Phases where an attack deals damage.
* **INVULN:** Phases where damage is negated.
* **PARRY_PERFECT:** Phases where a parry succeeds.

## 4. Phase Authority (The Axioms)
PCAM-24 enforces several non-negotiable axioms:

1.  **Phase is Authoritative:** It is the sole coordinate for ordering and adjudication.
2.  **Discrete Semantics:** All logic must be expressible as phase membership.
3.  **Monotonic Progression:** No rewinds. Corrections occur by snapping phase.
4.  **Subsystem Subscription:** Animation/Audio observe phase but do not drive it.
5.  **Interaction by Phase:** Cross-entity interactions are resolved by phase rules, not wall-clock time.

## 5. Action Lifecycles
* **Impulse:** Progress once and terminate (e.g., Melee Attack).
* **Sustained:** Loop while input is held (e.g., Block).
* **Reactive:** Narrow success window (e.g., Parry).
* **Evasive:** Invulnerability window + movement (e.g., Dodge).

## 6. Transitions & Buffering
* Cancels are allowed only within defined windows.
* Inputs are buffered and consumed deterministically.
* Priority rules resolve simultaneous transition eligibility.

## 7. Interaction Adjudication
PCAM-24 requires explicit precedence for contested interactions:
1.  Evasion (e.g., INVULN)
2.  Perfect Defense
3.  Mitigating Defense
4.  Hit

## 8. Networking and Determinism
PCAM-24 is inherently network-friendly.
* **Replication:** Transmits Action ID + Phase.
* **Prediction:** Clients predict by advancing phase.
* **Reconciliation:** Snap to authoritative phase (no complex time rewinding).

## 10. Benefits
!!! success "Why use PCAM-24?"
    * **Consistency:** All systems share a single semantic clock.
    * **Determinism:** Eliminates floating-point drift.
    * **Explainability:** Bugs are inspectable state, not vague timing artifacts.
    * **Authorability:** Designers reason in stages, not milliseconds.

## 12. Conclusion
PCAM-24 reframes interactive simulation timing from a question of *when* to a question of *what stage*. It is not an optimization of existing timing systems. It is a replacement of their underlying assumption.
