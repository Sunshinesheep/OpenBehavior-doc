# Types

OpenBehavior is a dynamically typed language, meaning variables can be assigned values without explicit type declarations.

Types in OpenBehavior define the structure and interpretation of values used in scenario descriptions.

---

## Basic Types

### String

Represents textual values used in scenario definitions.

**Example:**
```python
scenario_mode = "mode_1"
model_name = "NAG-RL_agent"
```

---

### Int

Represents integer numeric values.

**Example:**
```python
speed = 10
lane_id = 2
```

---

## Special Types

### Actor

Represents an entity in the scenario, such as vehicles or pedestrians. Actors are instantiated from predefined models or templates.

**Example:**
```python
actor: "Model3"
actor: "Rubicon"
actor: "Walker02"
actor: "Carlacola"
```

### Position

Represents the spatial position of an object in the scene. Positions can be expressed as absolute values or relative references.

**Example:**
```python
position: "100"
position: "[50..80]"
position: "20, ahead_of: ego_vehicle"
position: "10, at:start"
```

---

### Lane

Represents the lane index or relative lane location of a vehicle in the road network.

**Example:**
```python
lane: "1"
lane: "2"
lane: "[1..3]"
```

---

### Orientation

Represents the heading direction of a vehicle in the scene.

**Example:**
```python
orientation: "0deg"
orientation: "90deg"
orientation: "rad(1.57)"
```

### Path

Represents a trajectory or route followed by an actor. A path can be predefined or dynamically generated.

**Example:**
```python
path: "RouteA"
path: "lane_follow"
path: "ego_relative_path"
```

<!--
### Ego Vehicle 
Represents the system under test in the scenario.
Example:
ego_vehicle: "DUT"
-->
