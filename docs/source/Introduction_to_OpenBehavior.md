# Introduction to OpenBehavior

## What is OpenBehavior?

OpenBehavior is a behavior-centric scenario description language for testing autonomous driving systems (ADSs). It enables users to describe traffic scenarios with diverse behavior models, allowing traffic participants to exhibit adaptive and interactive driving behaviors instead of following predefined motion scripts.

## Why do we need OpenBehavior?

The safety of ADSs fundamentally depends on their ability to handle complex multi-agent interactions. Critical scenarios, such as cut-ins or chain-reaction braking, emerge from dynamic and autonomous interactions rather than static replays. However, most existing scenario description languages describe agent behaviors using predefined scripts, where traffic participants follow fixed motions or event-triggered actions once a scenario starts.

These limitations motivate the introduction of behavior models into scenario descriptions. Unlike predefined scripts, behavior models equip traffic participants with reactive decision-making capabilities, enabling them to continuously adapt their actions according to surrounding traffic conditions.

## What can you do with OpenBehavior?

With OpenBehavior, you can:

- Describe traffic scenarios using a behavior-centric language.
- Bind heterogeneous behavior models to traffic participants.
- Specify testing objectives using OBSpec.
- Customize traffic behaviors through reusable behavior models.
- Evaluate autonomous driving systems (ADSs) under complex traffic interactions.

## Comparison with Other Languages

Existing scenario description languages primarily focus on describing physical scenes and predefined traffic behaviors. OpenBehavior extends this paradigm by introducing adaptive behavior models and intent-guided scenario generation, enabling richer interactions and more effective exploration of safety-critical scenarios.

### Feature Comparison

```{image} images/compare.png
:width: 500px
:align: left
:alt: Comparison Table



Unlike existing scenario description languages, which rely on predefined trajectories or event-triggered actions, OpenBehavior introduces explicit behavior models that can be independently bound, switched, and reused across scenarios. Combined with model-aware modifiers and OBSpec, it supports both physical and behavioral variations, enabling richer scenario generation for testing.

## Next Steps

Ready to start using OpenBehavior?

- Continue with **Getting Started** to set up the environment and run your first OpenBehavior scenario.
- Explore the **Language Reference** to learn the syntax and semantics of OpenBehavior.
- Follow the **Examples** to build your own behavior-centric scenarios.
