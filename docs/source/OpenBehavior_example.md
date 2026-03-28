# OpenBehavior example
In this section, we will provide a few complete examples of OpenBehavior.

## S3-Lane Following on a Straight Road

### Overview
This example demonstrates a standard multi-vehicle interaction on a highway-style straight road. The key highlight here is the **heterogeneous behavior control**:
1. **The Ego Vehicle** follows a specific path with a "cautious" driving style.
2. **Standard NPCs** (npc1, npc2, npc3) use rule-based "normal" models with randomized spatial ranges to ensure scenario diversity.
3. **Adaptive NPC** (npc4) utilizes our **Behavioral Model Binding** mechanism, which dynamically switches its driving profile based on the `scenario_mode`.

### Scenario Script
The main script orchestrates the orchestration block. It defines the map, initializes actors, and executes a 40-second parallel simulation where each agent fulfills its bound Behavior Profile.

    import openbehavior_basic.osc
    import adaptive.osc
    
    scenario top:
        path: Path
        path.set_map("Town04")
        path.path_min_driving_lanes(2)
    
        ego_vehicle: Model3
        npc1: Rubicon
        npc2: TT
        npc3: A2
        npc4: TT
    
        adaptive_targets: list of string = [npc4]
        user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "openbehavior_s1")
    
        do parallel(duration: 40s):
            ego_vehicle.drive(path) with:
                set_behavior_model(behavior_type: Learing_based, model: Apollo, hyperparameters: Default)
                set_behavior_logic(
                    lane:"2, at:start",
                    position:"0, at:start",
                    lane:"2, at:end",
                    position:"150, at:end"
                )
            npc1.drive(path) with:
                set_behavior_model(behavior_type: Script, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], ahead_of:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            npc2.drive(path) with:
                set_behavior_model(behavior_type: Script, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], behind:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            npc3.drive(path) with:
                set_behavior_model(behavior_type: Script, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], behind:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)

### adaptive.osc

```
struct adapt_npc_bm:
    def adapt(scenario_mode: string) is
     if scenario_mode == "openbehavior_s1":
        choose:
            {
                model = {
                    model_name: "normal",
                    behavior_type: "behavior_agent",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane:"[3..5], at:start",
                        position:"[90..100], ahead_of:ego_vehicle, at:start",
                        lane:"3, at:end",
                        position:"[50..80], ahead_of:ego_vehicle, at:end"
                    }
                }
            },
            {
                model = {
                    model_name: "NAG-RL_agent",
                    behavior_type: "AI",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane:"[3..5], at:start",
                        position:"[90..100], ahead_of:ego_vehicle, at:start",
                        lane:"3, at:end",
                        position:"[50..80], ahead_of:ego_vehicle, at:end"
                    }
                }
            }
    elif scenario_mode == "natural":
        model = {
            model_name: "normal",
            behavior_type: "Script",
            hyperparameters: {
                max_acc: 5
            }
        }
        logic={
            logic_params: {
                lane: "3, at:start",
                position: "[1..20], behind: ego_vehicle, at:start",
                lane: "1, side_of: ego_vehicle, side: right, at:end",
                position: "[50..70], ahead_of: ego_vehicle, at:end"
            }
        }
    elif scenario_mode == "diverse":
        choose:
            {
                model = {
                    model_name: "normal",
                    behavior_type: "Script",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane: "3, at:start",
                        position: "[1..100], behind: ego_vehicle, at:start",
                        lane: "1, at:end",
                        position: "[30..70], ahead_of: ego_vehicle, at:end"
                    }
                }
            },
            {
                model = {
                    model_name: "cautious",
                    behavior_type: "Script",
                    hyperparameters: {
                        max_acc: 5,
                        max_speed: 20
                    }
                }
                logic = {
                    logic_params: {
                        lane: "3, at:start",
                        position: "[1..100], ahead_of: ego_vehicle, at:start",
                        lane: "1, at:end",
                        position: "[30..70], ahead_of: ego_vehicle, at:end"
                    }
                }
            }
    elif scenario_mode == "adversarial":
        model= {
            model_name: "aggressive",
            behavior_type: "Script",
            hyperparameters: "default"
        }
        logic= {
            logic_params: {
                lane: "3, at:start",
                position: "[60..80m], behind: ego_vehicle, at:start",
                lane: "1, at:end",
                position: "[50..80m], ahead_of: ego_vehicle, at:end"
            }
        }

    elif scenario_mode == "openbehavior_s4":
        choose:
            {
                model = {
                    model_name: "normal",
                    behavior_type: "Script",
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
                    behavior_type: "AI",
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


### Scenario Illustrations

![s3](/images/s3.png)


### openbehavior_basic.osc
The detail openbehavior_basic.osc is as follows:

```
# tag::library-physical-time[]
type time is SI(s: 1)
unit s           of time is SI(s: 1, factor: 1)
unit hour        of time is SI(s: 1, factor: 3600)
unit h           of time is SI(s: 1, factor: 3600)
unit minute      of time is SI(s: 1, factor: 60)
unit min         of time is SI(s: 1, factor: 60)
# end::library-physical-time[]

# tag::library-physical-length[]
type length is SI(m: 1)
unit m          of length is SI(m: 1, factor: 1)
unit meter      of length is SI(m: 1, factor: 1)
unit km         of length is SI(m: 1, factor: 1000)
# end::library-physical-length[]

# tag::library-physical-velocity[]
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
# end::library-physical-velocity[]

# tag::library-physical-angle[]
type angle is SI(rad: 1)
unit degree of angle is SI(rad: 1, factor: 0.01745329252)
unit deg    of angle is SI(rad: 1, factor: 0.01745329252)
unit radian of angle is SI(rad: 1, factor: 1)
unit rad    of angle is SI(rad: 1, factor: 1)
# end::library-physical-angle[]

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

