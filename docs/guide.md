# PCAM-24: A Designer’s Guide
*To Phase-Based Actions*

!!! tip "One idea to remember"
    **Actions don’t happen over time. Actions move through stages.**

PCAM-24 gives you a clear, shared way to describe those stages—so animation, combat, AI, and player input all agree on what is happening right now.

## 1. What problem PCAM-24 solves
*In designer terms*

If you’ve ever dealt with:
* "The animation hit, but the damage didn’t"
* "The parry felt right but failed online"
* "This move is balanced at 60 FPS but broken at 120"
* "Which frame does the hitbox turn on again?"

PCAM-24 exists to remove those problems at the design level, not as a late technical fix.

## 2. Phase replaces time
*How to think differently*

In PCAM-24, every action moves through **24 phases**, numbered 0–23.



| Do NOT think in... | DO think in... |
| :--- | :--- |
| Seconds | Stages of the action |
| Frames | Wind-up / Commitment |
| Normalized Time | Impact / Recovery |

## 3. Phase windows
*Your main design tool*

A **Window** is just a named range of phases.



**Example: Light Attack**

| Window Name | Phases | Meaning |
| :--- | :--- | :--- |
| **Anticipation** | 0–5 | Player commits |
| **Commit** | 6–9 | Cannot cancel |
| **Active** | 10–13 | Attack can hit |
| **Recovery** | 14–21 | Vulnerable |
| **Reset** | 22–23 | Combo allowed |

**No timers. No frame math. No "magic numbers".**

## 4. What you author (and what you don’t)

**You Author:**
* Phase windows
* Which actions can cancel into others
* Where hits, invulnerability, parries, and combos live
* How risky or safe an action feels

**You Do NOT Author:**
* Animation frame numbers
* Millisecond windows
* Per-FPS tuning
* Separate logic for online vs offline

## 5. Animation becomes a follower
Animation does not decide gameplay. Instead, **Animation reads the current phase.**

* If the attack looks like it hits, it is in the  window.
* There is no "visual lie."

!!! quote " The Shift"
    You stop asking: **"Which animation frame is the hit?"**
    
    You start asking: **"Which phase is dangerous?"**

## 6. Dodge, Parry, and Block
*Why this feels fair*

### Dodge
Invulnerability is a phase window.
* *Example:* Phases 7–12 are .
* If a hit occurs outside that window, it always lands.

### Parry
Perfect parry is a very small phase window.
* *Example:* Phases 4–6 are .
* Late parry may block instead of parry.

### Block
Block strength can change by phase. Holding block too long can naturally weaken it as the phase loops.

## 7. Cancels and Combos
*Clean and readable*

Instead of *"combo window is frames 38–41"*, you define **Chain Windows**.

* *Example:* Combo allowed only during phases 22–23.
* Attack input buffered earlier will fire automatically there.

This makes combos **consistent, latency-safe, and easy to rebalance.**

## 8. Why this helps AI design
AI doesn’t guess anymore. It sees:
1. Opponent Action
2. Opponent Phase
3. Opponent Window

So it can dodge only during  windows or punish only during .

## 9. Multiplayer feels better
*Without you designing "for netcode"*

Because phase is authoritative:
* Online and offline behave the same.
* Hits are decided by phase, not packet timing.
* Disagreements are explainable (*"you were in phase 9, not 10"*).

## 10. How you tune difficulty
You adjust **Window Sizes** and **Window Positions**.

* **Make a move safer** $\to$ Shorten  window.
* **Make parry harder** $\to$ Shrink  window.
* **Make combat faster** $\to$ Shift  earlier.
* **Make stamina matter** $\to$ Reduce  windows.

## 11. Debugging becomes visual
A PCAM-24 game can show you exactly why a hit failed.
* Instead of *"It feels off sometimes..."*
* You get *"This window is too short"* or *"This cancel overlaps too much."*

## 12. Mental Model Summary

!!! success "Keep This"
    * **Phase** = Where the action is
    * **Windows** = What the action can do
    * **Time** = Irrelevant to gameplay authority
    * **Animation** = Presentation
    * **Phase** = Truth

## 13. What PCAM-24 is not
To avoid confusion, PCAM-24 is **not**:
* A new animation system
* A timing tweak
* A combat gimmick
* A fighting-game-only thing

It is a shared language for action stages.

## 14. Why this is a design upgrade
PCAM-24 lets designers reason about gameplay in human terms and avoid fragile timing logic.

**You stop fighting the engine. You start shaping actions.**
