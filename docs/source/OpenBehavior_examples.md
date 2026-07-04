# Examples

This section presents complete OpenBehavior scenario examples. These examples demonstrate how different language components work together to build interactive, multi-agent driving scenarios.

Unlike the Language Reference, which explains individual constructs, this section focuses on **end-to-end scenario composition**.

---

## Example 1: Three-Lane Highway Interaction (Lane Change)

### Overview

This example demonstrates a lane-change interaction on a three-lane highway.

The ego vehicle starts in **Lane 1** and aims to merge into **Lane 2**, while surrounding vehicles execute heterogeneous driving behaviors.

- **Npc1** performs a forced lane change followed by a learning-based driving policy.
- **Npc2 and Npc3** execute adaptive merging behaviors with stochastic lane interactions.
- Behavior execution is controlled through **behavior model binding and orchestration mechanisms**.

---

### Scenario Illustration

![Highway Lane Change Interaction](images/example.png)

---

### Scenario Script

The following script defines a complete OpenBehavior scenario, including environment setup, agent definition, behavior binding, and execution logic.

```python
import openbehavior_basic.osc
import adaptive.osc

scenario top:
    path: Path
    path.set_map("Town04")
    path.path_min_driving_lanes(3)

    ego_vehicle: Model3
    npc1: Rubicon
    npc2: TT
    npc3: A2

    # Adaptive behavior binding
    adaptive_targets: list of string = [npc2, npc3]
    user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "openbehavior_s1")
    auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)

    do parallel:

        # Ego vehicle behavior
        ego_vehicle.drive(path) with:
            set_behavior_model(behavior_type: Learning, model: Apollo, hyperparameters: Default)
            set_behavior_logic(
                lane: "1, at:start",
                position: "80, at:start",
                lane: "2, at:end",
                position: "260, at:end"
            )

        # NPC1 multi-stage behavior
        serial:

            start: npc1.drive(path) with:
                set_behavior_model(behavior_type: Learning, model: NAG-RL_agent)
                set_behavior_logic(
                    lane: "2, at:start",
                    position: "0, at:start",
                    lane: "2, at:end",
                    position: "[100..120], at:end"
                )

            change_lane: parallel(duration: 5s):
                npc1.drive(path) with:
                    set_behavior_model(behavior_type: Rule, model: Directive, hyperparameters: "speed=10")
                    set_behavior_logic(
                        change_lane(lane_changes: 1, side: left)
                    )

            end: npc1.drive(path) with:
                set_behavior_model(behavior_type: Learning, model: NAG-RL_agent)
                set_behavior_logic(
                    lane: "1, at:start",
                    lane: "2, at:end",
                    position: "10, ahead_of: ego_vehicle, at:end"
                )
```

---

## Example 2: Adaptive Behavior Switching (Conceptual)

This example demonstrates how OpenBehavior supports dynamic behavior selection using scenario modes.

### Key Idea

The same NPC can exhibit different driving behaviors depending on the selected `scenario_mode`.

---

### Adaptive Behavior Library

```python
struct adapt_npc_bm:
    def adapt(scenario_mode: string) is

        if scenario_mode == "openbehavior_s1":
            choose:
                {
                    model_name: "normal_behavior_agent",
                    behavior_type: "Rule"
                },
                {
                    model_name: "NAG-RL_agent",
                    behavior_type: "Learning"
                }

        elif scenario_mode == "natural":
            model_name: "normal_behavior_agent"
            behavior_type: "Rule"

        elif scenario_mode == "adversarial":
            model_name: "aggressive_behavior_agent"
            behavior_type: "Rule"
```

---

### Key Insight

By decoupling:

- **Behavior Model (how to drive)**
- **Scenario Logic (what to achieve)**

OpenBehavior enables:

- reusable NPC policies
- stochastic behavior selection
- scenario-level diversity control

---

## Notes

- These examples are simplified for readability.
- Full implementation details (including physics libraries and actor definitions) are provided in the Language Reference section.
- Additional examples (intersection, cut-in, pedestrian interaction) will be added in future updates.