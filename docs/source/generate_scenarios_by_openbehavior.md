## Describe a Scenario

In OpenBehavior, a scenario is organized into three core components:

- **Global Configuration**
- **Agent Definition**
- **Orchestration**

This separation cleanly decouples environment specification, agent modeling, and orchestration logic, enabling modular and reusable scenario construction.

---

### Global Configuration

Global configuration defines the static and environmental context of a scenario, including the map and environmental conditions.

#### Map

The map specifies the driving environment where the scenario takes place.

Related example:

```
path: Path
path.set_map("Town04")
```

#### Environment

The environment defines dynamic conditions such as weather.

Related example:

```
environment: Environment
environment.clouds(8)
environment.air(80)
environment.rain(20mmph)
environment.wind(40mps)
environment.fog(5m)
```


---

### Agent Definition

The agent definition describes all traffic participants in the scenario, including the ego vehicle and NPC vehicles.

#### Ego Vehicle

The ego vehicle is the autonomous system under test.

Related example:

```
ego_vehicle: Model3
```

#### NPC Vehicles

NPC vehicles represent surrounding traffic participants that interact with the ego vehicle.

Related example:

```
npc1: Rubicon
```


---

### Orchestration

Orchestration defines how agents are coordinated and controlled during scenario execution.


#### Behavior model binding

Related Examples:

```
npc3.drive(path) with:
    set_behavior_model(behavior_type: Rule, model:behavior_agent, hyperparameters: Default)
    set_behavior_logic(
        lane:"[1..4], at:start",
        position:"[-20..30], behind:ego_vehicle, at:start",
        lane:"[1..4], at:end",
        position:"[-20..30], ahead_of:ego_vehicle, at:end"
    )
```

####  Adaptive Behavior Profiles

Scenario mode specifies high-level behavioral configurations that guide adaptive behavior binding and scenario generation.

Related example:

```
adaptive_targets: list of string = [npc4]
user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "openbehavior_s1")
auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)
```


### Example 2: Lane Following on a Straight Road

#### Overview
This example demonstrates a standard multi-vehicle interaction on a highway-style straight road.
1. **The Ego Vehicle** follows a specific path.
2. **Standard NPCs** (npc1, npc2, npc3) use rule-based "normal" models with randomized spatial ranges to ensure scenario diversity.
3. **Adaptive NPC** (npc4) utilizes our **Behavioral Model Binding** mechanism, which dynamically switches its driving profile based on the `scenario_mode`.


<img src="/images/s3.png" alt="s3" width="400">

.. figure:: /images/s3.png
   :width: 70%
   :align: center

#### Scenario Script
The main script orchestrates the orchestration block. It defines the map, initializes actors, and executes a 40-second parallel simulation.

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
        auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)
    
        do parallel(duration: 40s):
            ego_vehicle.drive(path) with:
                set_behavior_model(behavior_type: Learing, model: Apollo, hyperparameters: Default)
                set_behavior_logic(
                    lane:"2, at:start",
                    position:"0, at:start",
                    lane:"2, at:end",
                    position:"150, at:end"
                )
            npc1.drive(path) with:
                set_behavior_model(behavior_type: Rule, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], ahead_of:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            npc2.drive(path) with:
                set_behavior_model(behavior_type: Rule, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], behind:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            npc3.drive(path) with:
                set_behavior_model(behavior_type: Rule, model:behavior_agent, hyperparameters: Default)
                set_behavior_logic(
                    lane:"[1..4], at:start",
                    position:"[-20..30], behind:ego_vehicle, at:start",
                    lane:"[1..4], at:end",
                    position:"[-20..30], ahead_of:ego_vehicle, at:end"
                )
            

### adaptive.osc
#### Understanding `adaptive.osc`: The Behavior Library

The `adaptive.osc` file defines a reusable **Behavior Profile Library** that decouples an agent's driving policy from the specific scenario context.

#### Core Concept: Behavioral Decoupling
In OpenBehavior, an agent's behavior is defined as a tuple $\mathcal{P} = \langle \mathcal{M}, \mathcal{L} \rangle$:
* **Model Configuration ($\mathcal{M}$):** Specifies the underlying decision-making policy (e.g., a rule-based `behavior_agent` script or a learning-based `NAG-RL_agent`) and hyperparameters.
* **Logic Configuration ($\mathcal{L}$):** Specifies the logic constraints (e.g., "start at Lane 3, end at Lane 1 ahead of the Ego vehicle").

---

#### Key Features in the Script

##### 1. Scenario Mode Mapping
The `adapt` function maps a scenario_mode string (e.g., "openbehavior_s1") to the corresponding internal configurations of all targeted agents.

##### 2. Behavior-Models Selection (`choose`)

In modes such as openbehavior_s1 and openbehavior_s2, we introduce the **`choose`** keyword to facilitate the exploration of diverse test scenarios.
* It allows the user to provide multiple valid "options" for a scenario.
* *Example:* In `openbehavior_s1`, the NPC might be controlled by a rule_based `behavior_agent` or a learning_based `NAG-RL_agent`.

##### 3. Model-Aware Modifiers
Notice the use of **General Modifiers** within the `logic_params`:
* `ahead_of: ego_vehicle`: Relational positioning that works regardless of the coordinate system.
* `at: start` / `at: end`: Defines the start and end state.

---

#### How to Use This Library
In your main scenario file, you can simply "Batch Bind" these behaviors to multiple NPCs using the `auto_orchestrates_behavior` primitive:

```python
# 1. target agents
adaptive_targets: list of string = [npc4]

# 2. Resolve the profile
user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "openbehavior_s1")

# 3. Apply to a group of agents
auto_orchestrates_behavior(user_adaptive_npc_bm, adaptive_targets)

```
##### Why Use Adaptive Behavior Binding?

The `adapt_npc_bm` library simplifies scenario creation by abstracting individual NPC configurations into high-level **Scenario Modes**. Instead of manually specifying parameters for each vehicle, the `auto_orchestrates_behavior` primitive enables batch assignment of coordinated, context-aware behaviors to multiple agents. This reduces repetitive configuration and allows the overall traffic dynamics of the environment to be controlled via a single parameter.

Users can switch between different behavior models and parameter sets simply by changing the `scenario_mode`, avoiding the need to manually configure each individual NPC.
```
struct adapt_npc_bm:
    def adapt(scenario_mode: string) is
     if scenario_mode == "openbehavior_s1":
        choose:
            {
                model = {
                    model_name: "normal_behavior_agent",
                    behavior_type: "Rule",
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
                    behavior_type: "Learning",
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
    elif scenario_mode == "openbehavior_s2":
        choose:
            {
                model = {
                    model_name: "normal_behavior_agent",
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



