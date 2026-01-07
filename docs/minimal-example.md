# Minimal Example: One Action, One Interaction

This document demonstrates PCAM-24 using the **smallest possible complete example**:
- one attack
- one dodge
- one interaction outcome

No engine assumptions. No animation details. Just the core logic.

---

## The Scenario

- An attacker performs a light melee attack.
- A defender attempts a dodge.
- We determine whether the hit lands or is avoided.

Everything is decided using **phase**, not time.

---

## Step 1: Define the Phase Space

PCAM-24 uses a fixed phase cycle:


cat << 'EOF' > docs/minimal-example.md
# Minimal Example: One Action, One Interaction

This document demonstrates PCAM-24 using the smallest complete example possible:
- one attack
- one dodge
- one interaction outcome

No engine assumptions. No animation details. Just the core logic.

---

## The Scenario

- An attacker performs a light melee attack.
- A defender attempts a dodge.
- We determine whether the hit lands or is avoided.

Everything is decided using phase, not time.

---

## Step 1: Define the Phase Space

PCAM-24 uses a fixed phase cycle:

Phase ∈ {0, 1, 2, ..., 23}

Phase advances discretely and wraps modulo 24.

---

## Step 2: Define the Attack Action

Action: MELEE_LIGHT

Window definitions:

ANTICIPATION: phases 0–5  
COMMIT: phases 6–9  
ACTIVE: phases 10–13  
RECOVERY: phases 14–21  
RESET: phases 22–23  

The only phases where damage is possible are phases 10–13.

---

## Step 3: Define the Dodge Action

Action: DODGE_ROLL

Window definitions:

STARTUP: phases 0–3  
INVULN: phases 7–12  
RECOVERY: phases 13–23  

The only phases where damage is negated are phases 7–12.

---

## Step 4: Advance the Simulation

Each simulation tick:

action.phase = (action.phase + 1) mod 24

There is no notion of:
- seconds
- frames
- animation time

Only phase progression.

---

## Step 5: Interaction Resolution

When the attacker is in range, the outcome is resolved as follows:

if defender.action == DODGE and defender.phase ∈ INVULN:
    result = DODGED
else if attacker.phase ∈ ACTIVE:
    result = HIT
else:
    result = NO_HIT

That is the complete adjudication logic.

---

## Step 6: Example Outcomes

Case A — Dodge succeeds  
Attacker phase = 11 (ACTIVE)  
Defender phase = 9 (INVULN)  

Result: DODGED

Case B — Dodge fails  
Attacker phase = 11 (ACTIVE)  
Defender phase = 14 (RECOVERY)  

Result: HIT

Case C — Attack cannot hit  
Attacker phase = 8 (COMMIT)  

Result: NO_HIT

---

## Step 7: What Is Not Used

This example deliberately avoids:
- animation frames
- normalized clip time
- floating-point comparisons
- timers or cooldowns
- frame rate assumptions
- network arrival order

Everything is decided by phase window membership.

---

## Step 8: Derived Views

Animation, VFX, and audio are derived from phase:

pose = animation.sample(phase)

They never affect gameplay outcomes.

This guarantees:
- visual correctness
- no “lying” animations
- consistent offline and online behavior

---

## Key Takeaway

PCAM-24 reduces action logic to:
- discrete phase
- explicit semantic windows
- deterministic rules

This minimal example scales directly to:
- parries
- blocks
- combos
- AI reactions
- multiplayer reconciliation

Without changing the core idea.

If you understand this example, you understand PCAM-24.
