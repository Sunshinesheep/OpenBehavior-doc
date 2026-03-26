# How to start
## 🚀 Getting Started
This section describes the required environment setup and the steps to reproduce our experimental results.

### 🛠️ Experiment Setup

#### 1. Install Carla (0.9.13)
* **Download:** [Carla Releases (0.9.13)](https://github.com/carla-simulator/carla/releases)
* **Configuration:** Extract the package and add the following environment variables to your `~/.bashrc`:
    ```bash
    export CARLA_ROOT=/home/xxx/CARLA_0.9.13
    export PYTHONPATH=$PYTHONPATH:${CARLA_ROOT}/PythonAPI/carla/dist/carla-0.9.13-py3.7-linux-x86_64.egg:${CARLA_ROOT}/PythonAPI/carla/agents:${CARLA_ROOT}/PythonAPI/carla/agents/navigation:${CARLA_ROOT}/PythonAPI/carla:${CARLA_ROOT}/PythonAPI/examples:${CARLA_ROOT}/PythonAPI
    ```

#### 2. Setup Apollo 8.0
* **Clone Repository:**
    ```bash
    git clone -b v8.0.0 https://github.com/ApolloAuto/apollo.git
    ```
* **Installation:** Please follow the [Official Apollo Manual](https://github.com/ApolloAuto/apollo) for full installation.

#### 3. Setup Carla-Apollo Bridge
* **Source:** [MaisJamal/carla_apollo_bridge](https://github.com/MaisJamal/carla_apollo_bridge)
* **Installation:** Follow the bridge's `README.md` for specific integration steps.

---

### 📦 Environment Configuration

#### **Step A: Prepare for Scenario Runner (`scen`)**
1.  **System Dependencies:**
    ```bash
    sudo apt update && sudo apt install openjdk-17-jdk
    ```
2.  **ANTLR 4.10.1 Setup:**
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
3.  **Conda Environment:**
    ```bash
    conda create -n scen python==3.7 -y
    conda activate scen
    # Install antlr4 runtime, websocket, and graphviz
    pip install antlr4-python3-runtime==4.10 websocket websocket-client graphviz
    # Install dependency
    pip install -r requirements.txt
    ```

#### **Step B: Prepare for Judgement (`law`)**
1.  **Conda Environment:**
    ```bash
    conda create -n law python==3.7 -y
    conda activate law
    ```
2.  **Python Dependencies:**
    ```bash
    pip install -r requirements_judge.txt
    ```

---

​         

### 🛠 Running the Experiments


1.  **Run carla**

    ```bash
    ./CarlaUE4.sh
    ```


2.  **Run an OpenBehavior's scenario**

    ```bash
    conda activate scen
    
    python openbehavior_runner.py --sync  --openbehavior OpenBehavior_Osc/obehavior_s1.osc --reloadWorld
    ```
<!--original: python openbehavior_runner.py --sync  --openscenario2 OpenBehavior_Osc/avunit_s1.osc --reloadWorld -->


3. **Run OBOrchestration** <!-- original: Run OSCFuzz -->

    ```bash
   conda activate law
   
   cd judgement
   
   python OBOrchestration.py
   ```
   <!-- python OBOrchestration.py -->

   **Reproduction of OSG**

   You can run it by

   ```bash
   python RSGen/scripts/search/run.py
   ```

   

   **Reproduction of AVUnit**

   You can run it by

   ```bash
   python Law_Judgement/GeneticAlgorithm.py
   ```
