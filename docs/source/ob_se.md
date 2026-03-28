# OpenBehavior: Execution Semantics

OpenBehavior describes a complete scenario execution. Its semantics are defined as a set of **Traces**, where each trace $\pi = \langle \theta_0, \theta_1, \dots, \theta_n \rangle$ represents a concrete execution sequence of scenes.

---

## 1. Scene Definition ($\theta$)
A **Scene** $\theta$ is a snapshot at a specific time instant that captures the state of both the physical world and the orchestration logic.

| Component | Formalism | Description |
| :--- | :--- | :--- |
| **Map** | $\theta(\mathit{map})$ | The static road network configuration. |
| **Timestamp** | $\theta(\mathit{time}) \in \mathbb{R}_{\ge 0}$ | The continuous simulation time. |
| **Environment** | $\theta(\mathit{env})$ | Contextual conditions (e.g., weather, lighting). |
| **Ego State** | $\theta(\mathit{ego})$ | Physical state of the Ego vehicle. |
| **Agent States** | $\theta(\mathit{agents})$ | States of all NPCs, pedestrians, and obstacles. |
| **Active Phase** | $\theta(\mathit{phase})$ | The currently executing orchestration phase $P$. |
| **Active Binding** | $\theta(\mathit{binding})$ | The mapping of agents to their active Behavior Profiles $\langle \mathcal{M}, \mathcal{L} \rangle$. |

---

## 2. Nondeterministic Resolution & Consistency
A scenario specification defines a *space* of possible traces. A concrete trace $\pi$ must resolve all nondeterministic elements at initialization ($\theta_0$):

* **Behavior Binding**: For agents using the `choose` construct, one option $o$ is selected at $\theta_0$ and remains fixed: 
    $$\forall i \in [0, n], \theta_i(\mathit{binding}) = \theta_0(\mathit{binding})$$
* **Parameter Concretization**: Any range parameters (e.g., `speed: [10..30]`) are instantiated to a specific scalar $v \in [10, 30]$ at the start of the trace.

---

## 3. Composition Logic
The execution flow is governed by hierarchical composition operators:

### Sequential Composition ($P_1 ; P_2$)
The trace is partitioned at step $k$:
* $\pi_{0..k}$ must satisfy the constraints of Phase $P_1$.
* $\pi_{k+1..n}$ must satisfy the constraints of Phase $P_2$.
* $P_1$ must satisfy its termination criteria at $\theta_k$.

### Parallel Composition ($P_1 \parallel P_2$)
The trace satisfies the constraints of both $P_1$ and $P_2$ simultaneously.
* The composite phase terminates at step $m = \max(\mathit{end}(P_1), \mathit{end}(P_2))$.

---

## 4. Phase Termination Logic
Transitions between phases occur based on the following rules for a phase $P$ active in $\pi_{k..m}$:

1.  **Duration-Bounded Maintenance**: 
    If $P$ specifies a duration $D$:
    * $\theta_m(\mathit{time}) - \theta_k(\mathit{time}) = D$.
    * If behavioral goals are achieved before $D$ elapses, agents enter a **maintenance state** (e.g., station-keeping) until the time limit is reached.
2.  **Logic-Based Termination**:
    If no duration is specified, the phase terminates at the earliest step $m$ where all agents $\alpha \in \mathit{Agents}(P)$ satisfy their terminal constraints $C_{\mathit{end}}$:
    $$\forall \alpha \in \mathit{Agents}(P), \theta_m \models C_{\mathit{end}}^{\alpha}$$

---

## 5. Global Lifecycle & Feasibility
The simulation must adhere to high-level constraints:

* **Ego-Dominated Termination**: The entire trace $\pi$ terminates at $\theta_n$ if and only if the Ego vehicle reaches its destination or a critical system termination is triggered (e.g., `GlobalTerm(\theta_n) = True`).
* **Preemption**: Upon global termination, all active NPC phases are immediately halted, regardless of their local progress.
* **Physical Invariants**:
    * All agents must remain within the feasible regions of $\theta(\mathit{map})$.
    * Kinematic limits (max speed, acceleration) must be respected.
    * Agents must follow lane directionality (no illegal reversing).
    * **No Initial Overlap**: Distinct actors in $\theta_0$ must have non-overlapping physical positions.