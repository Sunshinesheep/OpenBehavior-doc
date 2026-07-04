# Introduction to OpenBehavior

## What is OpenBehavior?

OpenBehavior is a behavior-centric scenario description language for testing autonomous driving systems (ADSs). It enables users to describe traffic scenarios with heterogeneous behavior models, allowing traffic participants to exhibit adaptive and interactive driving behaviors instead of following predefined motion scripts.

## Why do we need OpenBehavior?

The safety of ADSs fundamentally depends on their ability to handle complex multi-agent interactions. Critical scenarios, such as cut-ins or chain-reaction braking, emerge from dynamic and autonomous interactions rather than static replays. However, most existing scenario description languages describe agent behaviors using predefined scripts, where traffic participants follow fixed motions or event-triggered actions once a scenario starts.

These limitations motivate the introduction of behavior models into scenario descriptions. Unlike predefined scripts, behavior models equip traffic participants with reactive decision-making capabilities, enabling them to continuously adapt their actions according to surrounding traffic conditions.

## What can you do with OpenBehavior?

With OpenBehavior, you can:

- Describe diverse traffic scenarios using a behavior-centric language.
- Bind heterogeneous behavior models to traffic participants.
- Specify high-level testing intents using OBSpec.
- Generate diverse and interactive scenarios through adaptive orchestration.
- Evaluate autonomous driving systems (ADSs) in different simulation environments.
- Discover safety-critical failures caused by complex traffic interactions.

## Comparison with Other Languages

OpenBehavior extends conventional scenario description languages by introducing adaptive behavior models and intent-guided scenario generation, enabling richer interactions and more effective exploration of safety-critical scenarios.

### Feature Comparison

<!--![Comparison Table](images/compare.png)-->
```{image} images/compare.png
:width: 500px
:align: left
:alt: Comparison Table

Unlike existing scenario description languages, which rely on predefined trajectories or event-triggered actions, OpenBehavior introduces explicit behavior models that can be independently bound, switched, and reused across scenarios. Combined with model-aware modifiers and OBSpec, it supports both physical and behavioral variations, enabling richer scenario generation for testing.
