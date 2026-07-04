# Getting Started

This guide describes how to set up the OpenBehavior environment, run your first OpenBehavior scenario, and reproduce the experimental results presented in our paper.

## Prerequisites

### 1. Install CARLA (0.9.13)

- **Download:** [Carla Releases (0.9.13)](https://github.com/carla-simulator/carla/releases)
- **Configuration:** Extract the package and add the following environment variables to your `~/.bashrc`:

```bash
export CARLA_ROOT=/home/xxx/CARLA_0.9.13
export PYTHONPATH=$PYTHONPATH:${CARLA_ROOT}/PythonAPI/carla/dist/carla-0.9.13-py3.7-linux-x86_64.egg:${CARLA_ROOT}/PythonAPI/carla/agents:${CARLA_ROOT}/PythonAPI/carla/agents/navigation:${CARLA_ROOT}/PythonAPI/carla:${CARLA_ROOT}/PythonAPI/examples:${CARLA_ROOT}/PythonAPI
```

### 2. Install Apollo 8.0

- **Clone Repository:**

```bash
git clone -b v8.0.0 https://github.com/ApolloAuto/apollo.git
```

- **Installation:** Please follow the [Official Apollo Manual](https://github.com/ApolloAuto/apollo) for the complete installation instructions.

### 3. Install the Carla–Apollo Bridge

- **Source:** [MaisJamal/carla_apollo_bridge](https://github.com/MaisJamal/carla_apollo_bridge)
- **Installation:** Follow the bridge's `README.md` for the integration steps.

---

## Configure the Environment

### Scenario Runner Environment (`scen`)

#### 1. Install System Dependencies

```bash
sudo apt update && sudo apt install openjdk-17-jdk
```

#### 2. Install ANTLR 4.10.1

```bash
sudo apt curl
curl -O https://www.antlr.org/download/antlr-4.10.1-complete.jar
sudo cp antlr-4.10.1-complete.jar /usr/local/lib/
sudo gedit ~/.bashrc

# Add to ~/.bashrc
export CLASSPATH=".:/usr/local/lib/antlr-4.10.1-complete.jar:$CLASSPATH"
alias antlr4='java -jar /usr/local/lib/antlr-4.10.1-complete.jar'
alias grun='java org.antlr.v4.gui.TestRig'

source ~/.bashrc
```

#### 3. Create the Conda Environment

```bash
conda create -n scen python==3.7 -y
conda activate scen

# Install ANTLR runtime and other dependencies
pip install antlr4-python3-runtime==4.10 websocket websocket-client graphviz

# Install project dependencies
pip install -r requirements.txt
```

### Evaluation Environment (`law`)

#### 1. Create the Conda Environment

```bash
conda create -n law python==3.7 -y
conda activate law
```

#### 2. Install Project Dependencies

```bash
pip install -r requirements_judge.txt
```

---

## Running OpenBehavior

### 1. Start CARLA

```bash
./CarlaUE4.sh
```

### 2. Run an OpenBehavior Scenario

```bash
conda activate scen

python openbehavior_runner.py --sync --openbehavior OpenBehavior_Osc/obehavior_s1.osc --reloadWorld
```

### 3. Run Adaptive Orchestration

```bash
conda activate law

cd judgement

python OBOrchestration.py
```

---

## Reproducing the Baselines

### OSG

Run the OSG baseline using:

```bash
python RSGen/scripts/search/run.py
```

### AVUnit

Run the AVUnit baseline using:

```bash
python Law_Judgement/GeneticAlgorithm.py
```
