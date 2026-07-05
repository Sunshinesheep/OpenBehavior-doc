# OpenBehavior Examples

This section presents an OpenBehavior example that demonstrates how to describe interactive traffic scenarios, bind diverse behavior models, and organize scenario logic using the OpenBehavior language.

## Example: Three-Lane Highway Interaction (Lane Change)

### Overview

This example demonstrates a lane-change interaction on a three-lane highway.

The ego vehicle starts in **Lane 1** and intends to merge into **Lane 2**, while surrounding vehicles perform different maneuvers driven by diverse behavior models.

* **Npc1**: Starts in **Lane 2**, executes a forced lane change to **Lane 1**, then continues with a learning-based driving model.
* **Npc2 & Npc3**: Originate from **Lane 3** and attempt to merge into **Lane 2** ahead of the ego vehicle. Their behavior is controlled by the `Scenario Mode` configuration.

#### Scenario Illustration

![Highway Lane Change Interaction](images/example.png)

*Figure: Lane-change interaction scenario used in this example.*

---

### Main Scenario Script

The following script defines the overall scenario and coordinates all traffic participants. It uses `parallel` to execute multiple agents simultaneously and `serial` to organize Npc1's staged driving behavior.

```python
import openbehavior_basic.osc
import adaptive.osc

scenario top:
    path: Path
    path.set_map("Town06")
    path.path_min_driving_lanes(3)

    ego_vehicle: Model3
    npc1: Rubicon
    npc2: TT
    npc3: A2
    
    # Batch Bind behavioral configurations to Npc2 and Npc3
    adaptive_targets: list of string = [npc2, npc3]
    user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "mode_1")
    auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)

    do parallel: 
        # Ego Vehicle: Lane 1 -> Lane 2
        ego_vehicle.drive(path) with:
            set_behavior_model(behavior_type: Learning, model: Apollo, hyperparameters: default)
            set_behavior_logic(
                lane: "1, at:start", position: "80, at:start",
                lane: "2, at:end", position: "260, at:end"
            )

        # Npc1: Initial Drive -> Lane Change -> Interaction
        serial:
            start: npc1.drive(path) with:
                set_behavior_model(behavior_type: Learning, model: NAG-RL_agent)
                set_behavior_logic(lane: "2, at:start", position: "0, at:start", lane: "2, at:end", position: "[100..120], at:end")

            change_lane: parallel(duration: 5s):
                npc1.drive(path) with:
                    set_behavior_model(behavior_type: Rule, model: Directive, hyperparameters: "speed = 10")
                    set_behavior_logic(change_lane(lane_changes: 1, side: left))

            end: npc1.drive(path) with:
                set_behavior_model(behavior_type: Learning, model: NAG-RL_agent)
                set_behavior_logic(lane: "1, at:start", lane: "2, at:end", position: "10, ahead_of: ego_vehicle, at:end")
```

---

### Adaptive Behavior Library (`adaptive.osc`)

The `adaptive.osc` file defines a reusable behavior library.

Through the `adapt` function and `choose` mechanism, it dynamically selects different behavior configurations based on the scenario mode, enabling diverse traffic behaviors.

```
struct adapt_npc_bm:
    def adapt(scenario_mode: string) is
        if scenario_mode == "mode_1":
            choose:
                {
                    model = {
                        model_name: "NAG-RL_agent",
                        behavior_type: "Learning",
                        hyperparameters: default
                    }
                    logic = {
                        logic_params: {
                            lane: "3, at:start",
                            position: "[10..30], ahead_of: ego_vehicle, at:start",
                            lane: "2, at:end",
                            position: "[30..50], ahead_of: ego_vehicle, at:end"
                        }
                    }
                },
                {
                    model = {
                        model_name: "behavior_agent",
                        behavior_type: "Rule",
                        hyperparameters: default
                    }
                    logic = {
                        logic_params: {
                            lane: "3, at:start",
                            position: "[10..40], ahead_of: ego_vehicle, at:start",
                            lane: "2, at:end",
                            position: "[30..40], ahead_of: ego_vehicle, at:end"
                        }
                    }
                }

    elif scenario_mode == "mode_2":
        choose:
            {
                model = {
                    model_name: "cautious_behavior_agent",
                    behavior_type: "Rule",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane:"[2..4], at:start",
                        position:"[5..20], ahead_of:ego_vehicle, at:start",
                        lane:"2, at:end",
                        position:"[-20..30], ahead_of:ego_vehicle, at:end"
                    }
                }
            },
            {
                model = {
                    model_name: "NAG-RL_agent",
                    behavior_type: "Learning",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane:"[2..4], at:start",
                        position:"[5..20], ahead_of:ego_vehicle, at:start",
                        lane:"2, at:end",
                        position:"[-20..30], ahead_of:ego_vehicle, at:end"
                    }
                }
            }
```
<!--
---

### OpenBehavior Basic Library

The `openbehavior_basic.osc` file defines the core built-in language constructs, including physical types, units, actors, and modifiers used across scenarios.

It provides:
- Actor definitions and basic scenario components

`openbehavior-basic-osc`：
```
# tag::library-physical-time[]
type time is SI(s: 1)
unit s           of time is SI(s: 1, factor: 1)
unit hour        of time is SI(s: 1, factor: 3600)
unit h           of time is SI(s: 1, factor: 3600)
unit minute      of time is SI(s: 1, factor: 60)
unit min         of time is SI(s: 1, factor: 60)
#end::library-physical-time[]

#tag::library-physical-length[]
type length is SI(m: 1)
unit m          of length is SI(m: 1, factor: 1)
unit meter      of length is SI(m: 1, factor: 1)
unit km         of length is SI(m: 1, factor: 1000)
#end::library-physical-length[]

#tag::library-physical-velocity[]
type velocity is SI(m: 1, s: -1)
unit meter_per_second    of velocity is SI(m: 1, s: -1, factor: 1)
unit mps                 of velocity is SI(m: 1, s: -1, factor: 1)
unit kilometer_per_hour  of velocity is SI(m: 1, s: -1, factor: 0.277777778)
unit kmph                of velocity is SI(m: 1, s: -1, factor: 0.277777778)
unit kph                 of velocity is SI(m: 1, s: -1, factor: 0.277777778)
unit mile_per_hour       of velocity is SI(m: 1, s: -1, factor: 0.447038889)
unit mph                 of velocity is SI(m: 1, s: -1, factor: 0.447038889)
unit miph                of velocity is SI(m: 1, s: -1, factor: 0.447038889)
unit mmph                of velocity is SI(m: 1, s: -1, factor: 0.000000278)
unit millimeter_per_hour of velocity is SI(m: 1, s: -1, factor: 0.000000278)
#end::library-physical-velocity[]

#tag::library-physical-angle[]
type angle is SI(rad: 1)
unit degree of angle is SI(rad: 1, factor: 0.01745329252)
unit deg    of angle is SI(rad: 1, factor: 0.01745329252)
unit radian of angle is SI(rad: 1, factor: 1)
unit rad    of angle is SI(rad: 1, factor: 1)
#end::library-physical-angle[]

actor dut
actor Path
modifier Path.set_map
modifier Path.path_min_driving_lanes
actor Model3
actor Rubicon
actor Walker02
actor TT
actor A2
actor Carlacola

```
-->
---

## Summary

In this example, you learned how to:

- Describe an interactive highway scenario
- Bind diverse behavior models to traffic participants
- Construct multi-stage behaviors using `parallel` and `serial`
- Use `adaptive.osc` to enable behavior diversity
- Reuse built-in language components

Continue with the **Language Reference** to learn the detailed syntax of OpenBehavior constructs.
