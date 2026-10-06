# OBSpec: Syntax and Semantics

OBSpec (OpenBehavior Specification) is a specification language for expressing both **Behavior Objectives** (`behaviorObjective`) and **Safety Oracles** (`safetyOracle`). It extends Signal Temporal Logic (STL) with behavior-oriented statistical and maneuver operators for autonomous driving scenario analysis.

---

## 1. Trace Definition
An execution trace $\pi = \langle \theta_0, \dots, \theta_n \rangle$ is a finite sequence of scenes, where each $\theta_t$ records the system state at simulation step $t$.

For OBSpec evaluation, we derive:
- **Continuous Signals ($s^\pi_t$):** Numerical agent signals such as `speed`, `acc`, `brake`, and `steer`.
- **Maneuver Predicates ($p^\pi_t$):** Boolean maneuver labels such as `ChangingLane`, `Overtaking`, and `TurningAround`.

Let $I_\pi = \{0, \dots, n\}$ denote the index set of the complete trace.

---

## 2. Atomic Primitive Evaluation
Statistical and maneuver functions are evaluated over the complete trace $I_\pi$, whereas spatial functions are evaluated at the current time step $t$.

### A. Statistical Functions
These functions characterize continuous signals over the complete execution trace.

* **Average:** $\lbrack\lbrack \texttt{avg}(s) \rbrack\rbrack_\pi^{I_\pi} = \frac{1}{|I_\pi|} \sum_{t \in I_\pi} s^\pi_t$

* **Standard Deviation:** $\lbrack\lbrack \texttt{std}(s) \rbrack\rbrack_\pi^{I_\pi} = \sqrt{\frac{1}{|I_\pi|} \sum_{t \in I_\pi} (s^\pi_t - \mu)^2}$, where $\mu = \lbrack\lbrack \texttt{avg}(s) \rbrack\rbrack_\pi^{I_\pi}$

* **Maximum:** $\lbrack\lbrack \texttt{max}(s) \rbrack\rbrack_\pi^{I_\pi} = \max_{t \in I_\pi} s^\pi_t$

* **Minimum:** $\lbrack\lbrack \texttt{min}(s) \rbrack\rbrack_\pi^{I_\pi} = \min_{t \in I_\pi} s^\pi_t$

### B. Maneuver Functions
These functions quantify maneuver dynamics over the complete execution trace.

* **Count (Rising Edge):** `count(p)` $= \sum_{t \in I_\pi} \mathbb{I}(p^\pi_t \wedge \neg p^\pi_{t-1})$, with $p^\pi_{-1} = \text{False}$

* **Switch Count (Total Transitions):** `switch_count(p)` $= \sum_{t \in I_\pi} \mathbb{I}(p^\pi_t \neq p^\pi_{t-1})$

* **Duration:** `duration(p)` $= \sum_{t \in I_\pi} \mathbb{I}(p^\pi_t) \cdot \Delta t$, with sampling interval $\Delta t$

### C. Spatial Functions
For agents or spatial points $A$ and $B$, `dist` is evaluated at time step $t$.

* **Agent-to-agent distance:** $\lbrack\lbrack \texttt{dist}(A,B) \rbrack\rbrack_\pi^t = \min_{x \in B_A,\; y \in B_B} \|x-y\|_2$, where $B_A$ and $B_B$ denote the occupied regions of the two agents.

* **Otherwise:** $\lbrack\lbrack \texttt{dist}(A,B) \rbrack\rbrack_\pi^t = \|\text{pos}(A,\theta_t)-\text{pos}(B,\theta_t)\|_2$, where `pos` gives an agent's reference position or a spatial point's coordinates.
---

## 3. Quantitative Semantics (Robustness)
The robustness function $\rho(\varphi, \pi, t)$ provides a numerical measure of how strongly a specification $\varphi$ is satisfied or violated. Positive robustness indicates satisfaction, while negative robustness indicates violation.

### Atomic Constraints
For an atomic constraint $\mu \equiv (f > c)$:

* If $f$ is a statistical or maneuver function, $\rho(\mu,\pi,t) = \lbrack\lbrack f \rbrack\rbrack_\pi^{I_\pi} - c$.

* If $f$ is a spatial function, $\rho(\mu,\pi,t) = \lbrack\lbrack f \rbrack\rbrack_\pi^t - c$.

Thus, statistical and maneuver constraints are trace-level properties evaluated over the complete execution trace, whereas spatial constraints are evaluated at individual time steps.

### Logical Operators
* **Negation:** $\rho(\neg \varphi, \pi, t) = -\rho(\varphi, \pi, t)$

* **Conjunction (AND):** $\rho(\varphi_1 \wedge \varphi_2, \pi, t) = \min(\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$

* **Disjunction (OR):** $\rho(\varphi_1 \vee \varphi_2, \pi, t) = \max(\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$

* **Implication ($\rightarrow$):** $\rho(\varphi_1 \rightarrow \varphi_2, \pi, t) = \max(-\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$

### Temporal Operators
Temporal operators are applied only to **time-indexed temporal formulas** (`temF`). Trace-level statistical and maneuver atoms are evaluated once over the complete execution trace and are not nested within temporal operators.

Defined over a time interval $J$:

* **Always ($G_J$):** $\rho(G_J \varphi, \pi, t) = \min_{t' \in (t \oplus J) \cap I_\pi} \rho(\varphi, \pi, t')$

* **Eventually ($F_J$):** $\rho(F_J \varphi, \pi, t) = \max_{t' \in (t \oplus J) \cap I_\pi} \rho(\varphi, \pi, t')$

* **Until ($U_J$):** $\rho(\varphi_1 U_J \varphi_2, \pi, t) = \max_{t' \in (t \oplus J) \cap I_\pi} \left(\min\left(\rho(\varphi_2,\pi,t'), \min_{t'' \in [t,t']} \rho(\varphi_1,\pi,t'')\right)\right)$

* **Next ($X$):** $\rho(X \varphi, \pi, t) = \rho(\varphi, \pi, t+1)$

Here, $t \oplus J$ denotes the time indices obtained by shifting interval $J$ relative to the current time $t$. Unbounded $G$ and $F$ range over the remaining trace.

---

## 4. Arithmetic Extensions
OBSpec supports arithmetic operators such as `+`, `-`, `*`, and `/` over numerical expressions.

**Example:** Summing lane-change occurrences across multiple agents:

`count(ChangingLane(npc1)) + count(ChangingLane(npc2)) > 2`

This objective is satisfied when the combined number of lane-change occurrences performed by `npc1` and `npc2` is greater than two.
