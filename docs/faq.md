# PCAM-24 FAQ

This FAQ addresses common questions, misunderstandings, and objections about PCAM-24.  
If you are skeptical, that is expected — this document exists to answer those concerns directly.

---

## Is PCAM-24 just frame data with a new name?

No.

Frame data is tied to:
- render rate
- animation playback speed
- platform performance

PCAM-24 phase:
- is engine-agnostic
- is independent of frame rate
- remains stable under latency
- is authoritative for simulation

Frames describe *presentation*.  
Phase describes *semantic stage*.

---

## Why not just use animation events or notifies?

Animation events:
- are clip-specific
- break under blending
- assume animation is authoritative
- do not generalize across systems

PCAM-24 treats animation as a **derived view**.
Gameplay logic never depends on animation timing.

If animation changes, gameplay does not break.

---

## Why 24 phases specifically?

24 provides:
- sufficient resolution for nuanced actions
- clean divisibility (2, 3, 4, 6, 8, 12)
- a full action cycle without excessive granularity
- cognitive tractability for designers

PCAM-24 is not about numerology.
The principle is discrete, bounded, semantic phase.

Other phase counts are possible, but 24 is a practical sweet spot.

---

## Does PCAM-24 require a fixed timestep?

No.

PCAM-24 requires **discrete phase advancement**, not a specific timestep.

Phase can advance:
- one step per tick
- multiple steps per tick
- via fixed-point accumulators

The only requirement is that phase advancement is deterministic and integer-based.

---

## Is this just a state machine?

PCAM-24 is not a traditional state machine.

State machines:
- represent discrete modes
- often hide timing inside transitions
- do not encode progression semantics explicitly

PCAM-24:
- encodes progression as phase
- makes stage explicit and inspectable
- allows continuous movement through semantic space

Actions are processes, not states.

---

## How does this work with physics?

PCAM-24 does not replace physics.

Physics continues to use:
- continuous time
- floating-point integration

PCAM-24 defines **authoritative interaction semantics**:
- when damage applies
- when invulnerability applies
- when cancels are allowed

Physics answers *where things are*.  
Phase answers *what stage they are in*.

---

## Is PCAM-24 only for melee combat?

No.

PCAM-24 applies to:
- ranged attacks
- spell casting
- ability cooldowns
- AI decision windows
- movement abilities
- interactive simulations beyond games

Any system where actions have meaningful stages can benefit.

---

## How does PCAM-24 help multiplayer?

PCAM-24 replicates:
- action identity
- phase
- start-captured parameters

It does not replicate:
- elapsed time
- animation state
- frame counts

This makes reconciliation:
- simpler
- explainable
- consistent across clients

Disagreements can be reasoned about in phase units.

---

## What about latency and fairness?

Latency still exists.

PCAM-24 improves fairness by:
- making adjudication deterministic
- allowing bounded forgiveness in phase units
- avoiding ambiguous timing inference

Instead of “lag ate my parry,” you get:
- “the server resolved phase 9, not phase 10”

That distinction matters.

---

## Does PCAM-24 eliminate skill timing?

No.

PCAM-24 makes skill timing **explicit**.

Skill expression comes from:
- window size
- window placement
- recovery length
- cancel availability

Not from hidden frame math.

---

## Is PCAM-24 overkill for simple games?

Possibly.

If your game:
- has no contested interactions
- is single-player only
- does not care about determinism or explainability

Then PCAM-24 may be unnecessary.

PCAM-24 shines as complexity grows.

---

## Can PCAM-24 coexist with existing systems?

Yes.

PCAM-24 defines **authority**, not presentation.

It can coexist with:
- existing animation systems
- physics engines
- rendering pipelines

The key requirement is respecting phase as the source of truth.

---

## Is PCAM-24 production-proven?

PCAM-24 is a formalized model, not a shipping engine.

However, many successful systems implicitly approximate phase-based thinking.
PCAM-24 makes those semantics explicit, unified, and inspectable.

---

## What is the relationship between PCAM-24 and AG-24?

PCAM-24 defines:
- the semantic rules
- the guarantees
- the philosophy

AG-24 is an execution substrate designed to enforce PCAM-24.

PCAM-24 comes first.

---

## Final clarification

PCAM-24 does not claim:
- to replace all timing systems
- to eliminate physics
- to be the only valid model

It claims that **for action semantics, stage matters more than time**.

