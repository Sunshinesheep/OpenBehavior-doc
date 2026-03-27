# Generate Scenarios by OpenBehavior

## Describe a Scenario

In our desgin, a scenario is composed of six elements: **EgoVehicle** **NPCVehicles** **map** **scenario mode**

 **environment ** **behavior model** .We are going to explore it in this part.

### Ego Vehicle

**Ego Vehicle**: The ego vehicle is the autonomous vehicle targeted for testing.

Related Examples:

```
ego_vehicle: Model3
```



### NPC Vehicles

**NPC Vehicles**: Scenes can be populated with NPC vehicles

Related Examples:

```
npc1: Rubicon
```



### Map

**map**: Just specify the name of the loaded map.



Related Examples:

```
path: Path
path.set_map("Town04")
```



### Scenario Mode

**Scenario Mode**:

Related Examples:

```
adaptive_targets: list of string = [npc4]
user_adaptive_npc_bm : string = adapt_npc_bm.adapt(scenario_mode: "avunit_s8")
```



### Environment

**Environment**:

Related Examples:

```
environment: Environment
#environment.clouds(8)
#environment.air(80)
#environment.rain(20mmph)
#environment.wind(40mps)
#environment.fog(5m)
```



### Behavior model

**Behavior model**:

Related Examples:

```
npc3.drive(path) with:
    set_behavior_model(behavior_type: Script, model:normal, hyperparameters: Default)
    set_behavior_logic(
        lane:"[1..4], at:start",
        position:"[-20..30], behind:ego_vehicle, at:start",
        lane:"[1..4], at:end",
        position:"[-20..30], ahead_of:ego_vehicle, at:end"
    )
```

