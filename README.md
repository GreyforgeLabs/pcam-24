# PCAM-24  
## Phase-Centric Action Model for Deterministic Interactive Simulation

PCAM-24 is a **phase-first simulation model** for interactive systems (games, training simulations, real-time agents) in which **discrete phase—not time—is the authoritative coordinate** for action progression, interaction adjudication, and cross-system synchronization.

Rather than asking *“how much time has passed?”*, PCAM-24 asks:

> **“What stage of the action is happening right now?”**

This shift eliminates timing inference between subsystems and replaces fragile, time-based logic with explicit, inspectable semantics.

---

## Why PCAM-24 Exists

Modern engines coordinate multiple subsystems—animation, combat, AI, input, networking—each operating on its own notion of time. This fragmentation leads to common problems:

- animations that look correct but resolve incorrectly  
- frame- or FPS-dependent gameplay behavior  
- brittle timing logic expressed in floats or magic numbers  
- inconsistent outcomes under network latency  
- difficulty explaining *why* a hit, parry, or dodge succeeded or failed  

PCAM-24 addresses these issues by introducing **phase as a shared semantic substrate** across all subsystems.

---

## Core Idea

In PCAM-24:

- Every **action** progresses through a fixed, cyclic set of **24 discrete phases** (0–23).
- All gameplay-relevant semantics—hits, invulnerability, parries, cancels, combos—are expressed as **phase windows**.
- Subsystems **subscribe to phase**; they do not infer timing or drive progression.
- Cross-entity interactions are resolved deterministically using **phase-defined precedence**, not frame order or wall-clock time.

Phase is authoritative.  
Time is a derived view.

---

## What PCAM-24 Is (and Is Not)

### PCAM-24 *is*:
- A semantic model for action progression
- Deterministic by construction
- Network-friendly (phase-first replication)
- Designer-authorable via explicit windows
- Engine-agnostic

### PCAM-24 is *not*:
- A new animation system
- A timing tweak or optimization
- A fighting-game-only mechanic
- A physics replacement
- A civil or real-world time reform

PCAM-24 defines **what is authoritative**, not how things are rendered.

---

## Who This Is For

PCAM-24 is relevant to:

- **Engine programmers** designing core gameplay substrates
- **Combat and gameplay designers** tired of frame- and time-based tuning
- **Networked game developers** seeking fairness and explainability
- **Simulation developers** who need deterministic, inspectable behavior
- **Technical designers** bridging systems and authoring

---

## Repository Contents

This repository contains the following core documents:

- **Whitepaper**  
  Conceptual motivation, problem framing, and high-level implications

- **Formal Specification**  
  Normative definition of PCAM-24 semantics and conformance rules

- **Designer Guide**  
  Practical explanation of PCAM-24 for gameplay and combat designers

Additional supporting documents expand on comparisons, examples, and usage patterns.

---

## Recommended Reading Order

If you are new to PCAM-24:

1. **Designer Guide** — understand the model intuitively  
2. **Whitepaper** — understand the motivation and system-level impact  
3. **Formal Specification** — understand the exact rules and guarantees  

---

## Status

PCAM-24 is currently:
- **Conceptually complete**
- **Formally specified**
- **Engine-agnostic by design**

Reference implementations, tooling, and execution substrates (e.g., ActionGraph-24) are intended as follow-up work and are deliberately separated from the core model.

---

## Design Philosophy (Short Version)

- Phase is truth  
- Systems subscribe, never infer  
- Determinism beats cleverness  
- Authoring clarity over timing hacks  
- Explainability is a feature  

---

## License and Usage

The PCAM-24 model and documentation are published to encourage:
- exploration
- discussion
- implementation
- critique

Please see the license file for usage terms.

---

## Getting Involved

Discussion, critique, and experimentation are welcome.  
When engaging with the material, please respect the core PCAM axioms and avoid reintroducing time-based authority into the model.

---

*PCAM-24 is a proposal to rethink how interactive actions are represented, reasoned about, and synchronized—not by time, but by stage.*

