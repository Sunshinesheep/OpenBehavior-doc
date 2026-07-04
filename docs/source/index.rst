Welcome to OpenBehavior's documentation!
========================================

Explore the guides, language reference, and examples for OpenBehavior.

.. figure:: images/tool.png
   :align: center

   Overview of the OpenBehavior Framework

What is OpenBehavior?
---------------------

**OpenBehavior** is a behavior-oriented scenario description language for autonomous driving testing.

Unlike traditional scenario description languages that script traffic participants with predefined trajectories or fixed behaviors, OpenBehavior treats each traffic participant as an autonomous agent driven by a configurable **Behavior Model**. This enables realistic interactions between the ego vehicle and surrounding traffic participants while allowing users to customize driving policies for different testing objectives.

The framework consists of three complementary components:

* **OpenBehavior** – describes scenarios using adaptive behavior models instead of scripted motions.
* **OpenSpec** – specifies safety and behavioral requirements for evaluating autonomous driving systems.
* **Adaptive Orchestration** – automatically explores behavior models and scenario parameters to generate interaction-rich test scenarios.

Core Workflow
-------------

The overall workflow consists of four stages:

1. **Describe** a logical scenario using OpenBehavior.
2. **Assign** behavior models to traffic participants.
3. **Execute** the scenario in the target simulator together with the ADS.
4. **Evaluate** the execution using OpenSpec specifications, whose feedback guides the next round of scenario generation.

Key Features
------------

🚗 **Behavior-Oriented Scenario Modeling**

Traffic participants are modeled as autonomous agents rather than scripted actors. Users can assign different behavior models to represent various driving styles and decision-making policies.

📋 **Customizable Behavioral Specifications**

OpenSpec supports not only traditional safety properties, but also behavioral objectives, allowing users to evaluate how autonomous driving systems behave under different interaction patterns.

🔍 **Adaptive Scenario Generation**

Instead of manually tuning scenario parameters, OpenBehavior automatically searches both behavior-model and parameter spaces to discover challenging interaction scenarios.

📈 **Rich Interactive Behaviors**

By combining configurable behavior models with adaptive search, OpenBehavior can generate diverse multi-agent interactions that are difficult to construct using conventional scripted scenarios.

Getting Started
---------------

Start with :doc:`Introduction_to_OpenBehavior` to learn the language basics and build your first OpenBehavior scenario.

.. note::

   This project is under active development.

Contents
--------

.. toctree::
   :maxdepth: 2
   :caption: GETTING STARTED:

   Introduction_to_OpenBehavior
   getting_started
   OpenBehavior_example

.. toctree::
   :maxdepth: 2
   :caption: LANGUAGE REFERENCE:

   Types
   generate_scenarios_by_openbehavior
   ob_se
   overall_BNF
   obspec_bnf
   obspec

.. toctree::
   :maxdepth: 2
   :caption: EXECUTION BACKENDS:

   OpenBehavior_connected_to
