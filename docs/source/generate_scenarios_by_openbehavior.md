# Generate Scenarios by OpenBehavior

This section describes how to construct OpenBehavior scenarios using core language components, including agents, environment settings, behavior binding, and scenario configuration.

A complete scenario is composed of the following elements:

- Ego Vehicle  
- NPC Vehicles  
- Map  
- Scenario Mode  
- Environment  
- Behavior Model  

---

## Ego Vehicle

The ego vehicle is the autonomous vehicle under test.

**Example:**
```python
ego_vehicle: Model3
```

---

## NPC Vehicles

NPC vehicles represent surrounding traffic participants in the scenario.

**Example:**
```python
npc1: Rubicon
npc2: TT
npc3: A2
```

---

## Map

The map defines the driving environment and road topology.

**Example:**
```python
path: Path
path.set_map("Town04")
```

---

## Scenario Mode

Scenario Mode controls how behavior models are selected and assigned to NPCs.

It is typically used with the adaptive behavior library.

**Example:**
```python
adaptive_targets: list of string = [npc4]
user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "openbehavior_s1")
```

---

## Environment

The environment defines weather and physical conditions of the simulation.

**Example:**
```python
environment: Environment
environment.clouds(8)
environment.air(80)
environment.rain(20mmph)
environment.wind(40mps)
environment.fog(5m)
```

---

## Behavior Model

Behavior models define how vehicles make decisions and execute actions.

Each behavior is composed of:

- **Behavior Model** (decision policy)
- **Behavior Logic** (motion and constraints)

**Example:**
```python
npc3.drive(path) with:
    set_behavior_model(behavior_type: Rule, model: normal, hyperparameters: Default)
    set_behavior_logic(
        lane: "[1..4], at:start",
        position: "[-20..30], behind:ego_vehicle, at:start",
        lane: "[1..4], at:end",
        position: "[-20..30], ahead_of:ego_vehicle, at:end"
    )
```

---

## Key Design Concept

OpenBehavior decouples scenario definition into two independent components:

- **Structure (static configuration)**  
  → vehicles, map, environment

- **Behavior (dynamic execution)**  
  → behavior models + logic

This separation enables flexible and reusable scenario design.

---

## Note on Full Scenarios

Full scenario scripts combining all components (including adaptive orchestration, multi-agent coordination, and scenario execution) are provided in the **Examples** section.

This section focuses only on the fundamental building blocks used to construct those scenarios.
