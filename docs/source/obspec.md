# OBSpec: Full Formal Semantics

OBSpec (OpenBehavior Specification) is a unified language for specifying both **Intent Oracles** and **Safety Oracles**. It is built upon Signal Temporal Logic (STL) with extensions for autonomous driving behavior analysis.

---

## 1. Trace Definition
An execution trace $\pi = \langle \theta_0, \dots, \theta_n \rangle$ is a sequence of snapshots. 
Each snapshot $\theta_t$ contains:
- **Physical State ($s^\pi_t$):** Continuous signals (e.g., `speed`, `acc`).
- **Semantic State ($p^\pi_t$):** Boolean maneuver labels (e.g., `ChangingLane`).

---

## 2. Atomic Primitive Evaluation
We define the evaluation of primitives over a time interval $I \subseteq [0, n]$:

### A. Statistical Functions (Intensity & Instability)
These functions quantify the characteristics of a continuous signal $s$ (e.g., acceleration variance for aggressiveness):
* **Standard Deviation:** $\llbracket \text{std}(s) \rrbracket_\pi^I = \sqrt{\frac{1}{|I|} \sum_{t \in I} ( s^\pi_t - \mu )^2 }$
* **Average:** $\llbracket \text{avg}(s) \rrbracket_\pi^I = \frac{1}{|I|} \sum_{t \in I} s^\pi_t$
* **Extrema:** $\llbracket \text{max}(s) \rrbracket_\pi^I = \max_{t \in I} (s^\pi_t)$ and $\llbracket \text{min}(s) \rrbracket_\pi^I = \min_{t \in I} (s^\pi_t)$

### B. Event Functions (Frequency & Persistence)
These functions operate on semantic predicates $p$ to capture discrete maneuver patterns:
* **Count (Rising Edge):** $\llbracket \text{count}(p) \rrbracket_\pi^I = \sum_{t \in I} \mathbb{I}( p^\pi_t \wedge \neg p^\pi_{t-1} )$
* **Switch Count (Total Transitions):** $\llbracket \text{switch\_count}(p) \rrbracket_\pi^I = \sum_{t \in I} \mathbb{I}( p^\pi_t \neq p^\pi_{t-1} )$
* **Duration:** $\llbracket \text{duration}(p) \rrbracket_\pi^I = \sum_{t \in I} \mathbb{I}(p^\pi_t) \cdot \Delta t$

### C. Spatial Metrics
* **Distance:** $\llbracket \text{dist}(A, B) \rrbracket_\pi^t = \| \text{pos}_A(\theta_t) - \text{pos}_B(\theta_t) \|_2$

---

## 3. Quantitative Semantics (Robustness)
The robustness function $\rho(\varphi, \pi, t)$ provides a numerical measure of how strongly a specification $\varphi$ is satisfied ($\rho > 0$) or violated ($\rho < 0$).

### Atomic Constraints
For a constraint $\mu \equiv (f > c)$, where $f$ is an atomic function:
$$\rho(\mu, \pi, t) = \llbracket f \rrbracket_\pi - c$$

### Logical Operators
* **Negation:** $\rho(\neg \varphi, \pi, t) = -\rho(\varphi, \pi, t)$
* **Conjunction (AND):** $\rho(\varphi_1 \wedge \varphi_2, \pi, t) = \min(\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$
* **Disjunction (OR):** $\rho(\varphi_1 \vee \varphi_2, \pi, t) = \max(\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$
* **Implication ($\rightarrow$):** $\rho(\varphi_1 \rightarrow \varphi_2, \pi, t) = \max(-\rho(\varphi_1, \pi, t), \rho(\varphi_2, \pi, t))$

### Temporal Operators
Defined over a time interval $J$:
* **Always ($G_J$):** $\rho(G_J \varphi, \pi, t) = \min_{t' \in t \oplus J} \rho(\varphi, \pi, t')$
* **Eventually ($F_J$):** $\rho(F_J \varphi, \pi, t) = \max_{t' \in t \oplus J} \rho(\varphi, \pi, t')$
* **Until ($U$):** $\rho(\varphi_1 U_J \varphi_2, \pi, t) = \max_{t' \in t \oplus J} \left( \min \left( \rho(\varphi_2, \pi, t'), \min_{t'' \in [t, t']} \rho(\varphi_1, \pi, t'') \right) \right)$
* **Next ($X$):** $\rho(X \varphi, \pi, t) = \rho(\varphi, \pi, t+1)$

---

## 4. Arithmetic Extensions
OBSpec supports element-wise arithmetic operations (e.g., `+`, `-`, `*`, `/`) between expressions.
**Example:** Summing lane changes for multiple agents:
`count(ChangingLane(npc1)) + count(ChangingLane(npc2)) > 2`