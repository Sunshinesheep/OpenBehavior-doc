# Introduction to OpenBehavior

## What is OpenBehavior?

OpenBehavior is a behavior-centric scenario description language for testing autonomous driving systems (ADSs). It introduces behavior models as first-class scenario abstractions for representing diverse driving policies, and provides mechanisms for configuring, binding, and composing them within traffic scenarios.

## Why do we need OpenBehavior?

The safety of ADSs depends heavily on their ability to handle interactions among multiple traffic participants. Such interactions emerge from the decisions of different agents and may change even under similar initial traffic conditions.

Existing scenario description languages can express trajectories, actions, maneuvers, and reactive behaviors. However, the behavior mechanisms available to each participant are typically prescribed when the scenario is designed. This limits the range of driving policies and interactions that can be explored from the same scenario description.

OpenBehavior addresses this limitation by representing driving policies as behavior models. Different behavior models can be configured, bound to traffic participants, and composed with user-prescribed actions, while each model retains its own decision logic.

## What can you do with OpenBehavior?

With OpenBehavior, you can:

- Represent diverse driving policies through behavior models and reusable behavior profiles.
- Bind behavior profiles to individual agents or groups of traffic participants.
- Compose model-controlled behaviors and user-prescribed maneuvers across scenario phases.
- Configure model-specific parameters through model-aware modifiers.
- Specify safety properties and behavioral objectives using OBSpec.
- Generate and evaluate interaction-rich scenarios for ADS testing.

## Comparison with Other Languages

Existing scenario description languages provide different mechanisms for specifying participant behavior, including trajectories, behavior programs, triggered actions, and maneuvers. OpenBehavior complements these mechanisms by making behavior models explicit scenario-level elements that can be configured, bound, and composed within a scenario.

Together with OBSpec and adaptive orchestration, OpenBehavior allows scenario generation to explore both behavior-model choices and conventional scenario parameters toward user-defined testing objectives.

### Feature Comparison

```{image} images/compare.jpg
:width: 500px
:alt: Comparison Table
```

OpenBehavior makes behavior models explicit elements of the scenario description. Users can define reusable behavior profiles, bind them to traffic participants, compose them with prescribed actions across scenario phases, and expose model-specific configuration parameters. This allows a scenario to vary not only physical and maneuver parameters, but also the behavior models used by traffic participants. OBSpec further allows users to specify safety properties and desired behavioral characteristics for scenario evaluation and generation.

## Next Steps

Ready to start using OpenBehavior?

- Continue with **Getting Started** to set up the environment and run your first OpenBehavior scenario.
- Explore the **Language Reference** to learn the syntax and semantics of OpenBehavior.
- Follow the **Examples** to build your own behavior-centric scenarios.
