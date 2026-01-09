# YAML Data Standard v1.0

!!! tip "Quick Download"
    **[Download pcam_standard_v1.yaml](https://raw.githubusercontent.com/GreyforgeLabs/pcam-24/main/pcam_standard_v1.yaml)**

This page presents the **canonical YAML schema** for defining PCAM-24 combat actions. This file is the universal definition format that game engines read to understand how a move works.

## Purpose

The YAML Data Standard provides:

- **Strictly-typed action definitions** — No ambiguity in how windows are specified
- **Integer-only timing** — All phases use discrete integers (0-23), never floats
- **Engine-agnostic format** — Works with Unity, Unreal, Godot, or custom engines
- **Built-in validation rules** — Schema includes constraints to catch authoring errors

---

## Schema Overview

```yaml
global:
  phase_cycle:
    total_segments: 24    # The Phase Wheel has exactly 24 phases (0-23)
  frame_lock:
    simulation_rate_hz: 60
    phases_per_second: 24

action_schema:
  metadata:      # ID, name, description
  phase_map:     # Startup, Active, Recovery windows (phase integers only)
  attributes:    # Damage, stun, pushback
  tags:          # Weight class, height, type

interaction_matrix:
  priority_order:
    - Phase State Resolution    # ACTIVE beats STARTUP
    - Weight Class Resolution   # HEAVY beats MEDIUM
    - Phase Precedence          # Who entered first
```

---

## Full Specification

??? abstract "Expand to view complete `pcam_standard_v1.yaml`"

    ```yaml
    # ==============================================================================
    # PCAM-24: Phase-Centric Action Model - Data Standard v1.0
    # ==============================================================================
    # Greyforge Labs - Universal Combat Definition Schema
    #
    # PURPOSE:
    # This file defines the canonical data format for describing combat actions
    # in a deterministic, frame-rate-independent manner. By replacing continuous
    # time with discrete phase segments, we guarantee reproducible outcomes across
    # all platforms and network conditions.
    # ==============================================================================


    # ==============================================================================
    # SECTION 1: GLOBAL CONSTANTS
    # ==============================================================================
    global:
      phase_cycle:
        total_segments: 24          # The wheel has exactly 24 phases (0-23)
        min_phase: 0                # First valid phase index
        max_phase: 23               # Last valid phase index (inclusive)
        
      frame_lock:
        simulation_rate_hz: 60      # Physics/logic updates per second
        phases_per_second: 24       # One complete action cycle = 1 second


    # ==============================================================================
    # SECTION 2: ACTION DEFINITION SCHEMA
    # ==============================================================================
    # ╔═══════════════════════════════════════════════════════════════════════════╗
    # ║  CRITICAL: The "active" window CANNOT overlap with "recovery".            ║
    # ║  All phases 0-23 must be accounted for exactly once across all windows.  ║
    # ╚═══════════════════════════════════════════════════════════════════════════╝

    action_schema:
      metadata:
        id: string                  # Unique identifier (e.g., "ATK_HEAVY_PUNCH")
        name: string                # Display name
        description: string         # Tooltip text
        version: string             # Schema version

      phase_map:
        anticipation:
          start_phase: integer      # Wind-up start (typically 0)
          end_phase: integer        # Wind-up end
          cancelable: boolean
          
        commit:
          start_phase: integer
          end_phase: integer
          armor_active: boolean
          
        active:
          start_phase: integer      # Hitbox live start
          end_phase: integer        # Hitbox live end
          
        recovery:
          start_phase: integer      # Must start AFTER active.end_phase
          end_phase: integer
          
        reset:
          start_phase: integer
          end_phase: integer        # Typically 23
          combo_window: boolean

      attributes:
        damage:
          base_value: integer
          chip_damage: integer
        stun:
          hit_stun: integer         # Phases opponent is in hit reaction
          block_stun: integer
        pushback:
          on_hit: integer
          on_block: integer
        meter:
          gain_on_hit: integer
          gain_on_whiff: integer
          cost: integer

      tags:
        weight_class:
          value: enum               # LIGHT | MEDIUM | HEAVY | SUPER
        height:
          value: enum               # HIGH | MID | LOW | OVERHEAD | UNBLOCKABLE
        type:
          value: enum               # STRIKE | THROW | PROJECTILE | SPECIAL | SUPER
        properties:
          is_invincible: boolean
          is_armored: boolean
          is_grab: boolean
          is_reversal: boolean


    # ==============================================================================
    # SECTION 3: INTERACTION MATRIX
    # ==============================================================================
    interaction_matrix:
      priority_order:
        - priority: 1
          name: "Phase State Resolution"
          resolution_table:
            ACTIVE_vs_ANTICIPATION: "ACTIVE_WINS"
            ACTIVE_vs_COMMIT: "ACTIVE_WINS"
            ACTIVE_vs_ACTIVE: "CHECK_NEXT_PRIORITY"
            
        - priority: 2
          name: "Weight Class Resolution"
          resolution_table:
            SUPER_vs_HEAVY: "SUPER_WINS"
            HEAVY_vs_MEDIUM: "HEAVY_WINS"
            MEDIUM_vs_LIGHT: "MEDIUM_WINS"
            SAME_WEIGHT: "CHECK_NEXT_PRIORITY"
            
        - priority: 3
          name: "Phase Precedence Resolution"
          resolution_logic:
            compare: "phase_entry_timestamp"
            lower_wins: true
            tie_result: "TRADE"

      special_cases:
        - name: "Invincibility Override"
          condition: "defender.properties.is_invincible == true"
          result: "ATTACKER_WHIFFS"
        - name: "Throw vs Strike"
          condition: "attacker.type == THROW && defender.type == STRIKE"
          result: "STRIKE_WINS"


    # ==============================================================================
    # SECTION 4: EXAMPLE ACTION
    # ==============================================================================
    example_actions:
      - metadata:
          id: "ATK_HEAVY_PUNCH"
          name: "Heavy Punch"
          description: "A powerful straight punch with significant wind-up."
          version: "1.0"
          
        phase_map:
          anticipation:
            start_phase: 0
            end_phase: 5
            cancelable: true
          commit:
            start_phase: 6
            end_phase: 9
            armor_active: false
          active:
            start_phase: 10
            end_phase: 13
          recovery:
            start_phase: 14
            end_phase: 21
          reset:
            start_phase: 22
            end_phase: 23
            combo_window: true
            
        attributes:
          damage:
            base_value: 80
            chip_damage: 8
          stun:
            hit_stun: 12
            block_stun: 8
          pushback:
            on_hit: 3
            on_block: 2
          meter:
            gain_on_hit: 15
            gain_on_whiff: 5
            cost: 0
            
        tags:
          weight_class:
            value: HEAVY
          height:
            value: MID
          type:
            value: STRIKE
          properties:
            is_invincible: false
            is_armored: false
            is_grab: false
            is_reversal: false
    ```

---

## Validation Rules

When parsing action definitions, engines **must** verify:

| Rule | Description |
|------|-------------|
| **Phase Coverage** | All windows must sum to exactly phases 0-23 with no gaps or overlaps |
| **Phase Bounds** | All phase values must be in range `[0, 23]` |
| **No Active/Recovery Overlap** | `active.end_phase + 1 == recovery.start_phase` |
| **Integer Only** | All phase and attribute values must be integers, never floats |
| **Valid Enums** | Tags must use only predefined enum values |

---

## Usage Example

```python
import yaml

with open('pcam_standard_v1.yaml') as f:
    standard = yaml.safe_load(f)

# Access global constants
phases = standard['global']['phase_cycle']['total_segments']  # 24

# Parse an action
heavy_punch = standard['example_actions'][0]
active_start = heavy_punch['phase_map']['active']['start_phase']  # 10
active_end = heavy_punch['phase_map']['active']['end_phase']      # 13
```
