# Why Phase, Not Time?

This document explains **why PCAM-24 replaces time as the authoritative coordinate** for interactive action semantics, rather than attempting to refine or optimize time-based systems.

The goal is not to claim that time-based models are “wrong,” but to show **where they break down** and why phase offers a cleaner, more reliable alternative.

---

## The Core Problem with Time-Based Authority

Most interactive systems treat **time** as the primary ordering mechanism:

- seconds elapsed
- frames passed
- normalized animation time
- cooldown timers
- wall-clock deltas (network)

However, **players do not perceive actions in time**.

They perceive:
- wind-up
- commitment
- impact
- recovery
- vulnerability

These are **stages**, not durations.

Time-based systems must *infer* stage from time, which introduces ambiguity.

---

## Common Time-Based Approaches (and Their Limits)

### 1. Frame-Based Combat Logic

**Approach**
- Hits, parries, and cancels are defined in animation frames
- Example: “Active on frames 12–16”

**Problems**
- Frame counts depend on playback speed
- FPS changes alter behavior
- Online corrections create disagreement about which frame occurred
- Designers reason in frames, players do not

Frame data works, but it is fragile and difficult to generalize across systems.

---

### 2. Normalized Time (0.0–1.0)

**Approach**
- Gameplay logic tied to normalized animation progress
- Example: “Hit at 0.45–0.55 of the clip”

**Problems**
- Assumes animation is the authority
- Blending and retargeting distort meaning
- Different clips encode different semantics
- Normalized time answers *how far*, not *what stage*

Normalized time hides semantics behind floating-point comparisons.

---

### 3. Timer / Cooldown Systems

**Approach**
- Explicit durations in seconds
- Example: “Invulnerable for 0.3s”

**Problems**
- Sensitive to tick rate and jitter
- Requires tuning per platform
- Difficult to align with animation and AI
- Hard to explain under latency

Timers answer *when something ends*, not *what is happening now*.

---

### 4. Fixed Timestep Simulation

**Approach**
- Fixed delta time for gameplay
- Rendering interpolated separately

**Problems**
- Still requires time-based windows
- Does not solve semantic alignment
- Subsystems still infer stage independently

Fixed timestep improves stability but does not address semantic fragmentation.

---

## The Fundamental Issue: Inference

Time-based systems require every subsystem to infer **action stage** independently:

- Animation infers from clip time
- Combat infers from timers
- AI infers from state flags
- Networking infers from arrival order

These inferences drift.

PCAM-24 removes inference entirely.

---

## Phase as a Semantic Coordinate

PCAM-24 introduces **phase** as a shared, discrete coordinate representing **where an action is in its lifecycle**.

Key properties:

- Phase is discrete, not continuous
- Phase is cyclic and bounded
- Phase directly encodes semantics
- Phase is inspectable and explainable

Instead of asking:
> “How much time has passed?”

PCAM-24 asks:
> “Which phase window is active?”

---

## Why Discrete Phase Works Better

### 1. Phase Matches Human Reasoning

Designers and players think in stages:
- “early”
- “active”
- “late”
- “recovery”

Phase encodes these stages directly.

---

### 2. Phase Eliminates Floating-Point Ambiguity

- No epsilon comparisons
- No drift
- No frame-rate dependence

Membership in a phase window is unambiguous.

---

### 3. Phase Unifies Subsystems

Animation, combat, AI, VFX, audio, and networking all reference **the same phase value**.

No subsystem needs its own clock.

---

### 4. Phase Makes Networking Explainable

Instead of:
> “Latency caused the hit to register late”

You get:
> “The defender was in phase 9, not phase 10”

This is debuggable and fair.

---

## Why 24 Phases?

PCAM-24 uses a fixed 24-phase cycle because it offers:

- Enough resolution for nuanced actions
- Clean subdivision (2, 3, 4, 6, 8, 12)
- A full action cycle without overfitting
- Cognitive tractability for designers

The specific number is less important than the principle:
**phase must be discrete, bounded, and semantic**.

---

## Why Not “Just Frames”?

Frames are:
- hardware- and framerate-dependent
- presentation-driven
- unsuitable as a shared semantic language

Phase is:
- engine-agnostic
- simulation-authoritative
- presentation-independent

---

## Phase Does Not Eliminate Time

PCAM-24 does **not** eliminate time entirely.

Time still exists for:
- rendering
- interpolation
- animation sampling
- audio playback

However, time becomes a **derived view**, not the authority.

Phase decides *what is happening*; time decides *how it is shown*.

---

## Summary

Time-based systems answer:
> “When did something occur?”

PCAM-24 answers:
> “What stage is the action in?”

For interactive simulations—where fairness, feel, and explainability matter more than absolute timestamps—**stage is the more meaningful truth**.

PCAM-24 replaces timing inference with explicit semantics.

---

*Phase is not an optimization of time.  
It is a replacement for time where meaning matters.*

