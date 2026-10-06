# OBSpec: Syntax and Semantics

OBSpec (OpenBehavior Specification) is a specification language for expressing both **Behavioral Objectives** (`behaviorObjective`) and **Safety Oracles** (`safetyOracle`). It extends Signal Temporal Logic (STL) with behavior-oriented statistical and maneuver operators for autonomous driving scenario analysis.

---

## 1. Trace Definition

An execution trace

$\pi = \langle \theta_0, \dots, \theta_n \rangle$

is a finite sequence of scenes, where each $\theta_t$ records the system state at simulation step $t$.

For OBSpec evaluation, we derive:

- **Continuous Signals ($s^\pi_t$):** Numerical agent signals such as `speed`, `acc`, `brake`, and `steer`.
- **Maneuver Predicates ($p^\pi_t$):** Boolean maneuver labels such as `ChangingLane`, `Overtaking`, and `TurningAround`.

Let

$I_\pi = \{0, \dots, n\}$

denote the index set of the complete trace.

---

## 2. Atomic Primitive Evaluation

Statistical and maneuver functions are evaluated over the complete trace $I_\pi$, whereas spatial functions are evaluated at the current time step $t$.

### A. Statistical Functions

Statistical functions characterize continuous signals over the complete execution trace.

* **Average:**

$$
\llbracket \texttt{avg}(s) \rrbracket_\pi^{I_\pi}
=
\frac{1}{|I_\pi|}
\sum_{t \in I_\pi} s^\pi_t
$$

* **Standard Deviation:**

$$
\llbracket \texttt{std}(s) \rrbracket_\pi^{I_\pi}
=
\sqrt{
\frac{1}{|I_\pi|}
\sum_{t \in I_\pi}
(s^\pi_t-\mu)^2
},
\qquad
\mu =
\llbracket \texttt{avg}(s) \rrbracket_\pi^{I_\pi}
$$

* **Maximum:**

$$
\llbracket \texttt{max}(s) \rrbracket_\pi^{I_\pi}
=
\max_{t \in I_\pi} s^\pi_t
$$

* **Minimum:**

$$
\llbracket \texttt{min}(s) \rrbracket_\pi^{I_\pi}
=
\min_{t \in I_\pi} s^\pi_t
$$

### B. Maneuver Functions

Maneuver functions quantify maneuver dynamics over the complete execution trace.

* **Count:**

$$
\llbracket \texttt{count}(p) \rrbracket_\pi^{I_\pi}
=
\sum_{t \in I_\pi}
\mathbb{I}
\left(
p^\pi_t \wedge \neg p^\pi_{t-1}
\right),
\qquad
p^\pi_{-1} = \mathrm{False}
$$

`count` measures the number of maneuver occurrences by counting rising edges of the corresponding maneuver predicate.

* **Switch Count:**

$$
\llbracket \texttt{switch\_count}(p) \rrbracket_\pi^{I_\pi}
=
\sum_{t \in I_\pi}
\mathbb{I}
\left(
p^\pi_t \neq p^\pi_{t-1}
\right)
$$

* **Duration:**

$$
\llbracket \texttt{duration}(p) \rrbracket_\pi^{I_\pi}
=
\sum_{t \in I_\pi}
\mathbb{I}(p^\pi_t)
\cdot \Delta t
$$

where $\Delta t$ is the sampling interval.

### C. Spatial Functions

For agents or spatial points $A$ and $B$:

$$
\llbracket \texttt{dist}(A,B) \rrbracket_\pi^t
=
\begin{cases}
\displaystyle
\min_{x \in B_A,\; y \in B_B}
\|x-y\|_2,
& \text{if both $A$ and $B$ are agents},\\[6pt]
\|\mathrm{pos}(A,\theta_t)-\mathrm{pos}(B,\theta_t)\|_2,
& \text{otherwise}.
\end{cases}
$$

Here, $B_A$ and $B_B$ denote the occupied regions of the two agents, and `pos` gives an agent's reference position or a spatial point's coordinates.

---

## 3. Quantitative Semantics (Robustness)

The robustness function $\rho(\varphi,\pi,t)$ provides a numerical measure of how strongly an OBSpec specification $\varphi$ is satisfied or violated.

- $\rho(\varphi,\pi,t) > 0$ indicates satisfaction.
- $\rho(\varphi,\pi,t) < 0$ indicates violation.

### Atomic Constraints

For an atomic constraint

$\mu \equiv (f > c)$,

the robustness is defined as:

$$
\rho(\mu,\pi,t)
=
\begin{cases}
\llbracket f \rrbracket_\pi^{I_\pi} - c,
& \text{if $f$ is a statistical or maneuver function},\\[4pt]
\llbracket f \rrbracket_\pi^t - c,
& \text{if $f$ is a spatial function}.
\end{cases}
$$

Thus, statistical and maneuver constraints are trace-level properties whose values are computed over the complete execution trace, whereas spatial constraints are evaluated at individual time steps.

### Logical Operators

* **Negation:**

$$
\rho(\neg \varphi,\pi,t)
=
-\rho(\varphi,\pi,t)
$$

* **Conjunction (AND):**

$$
\rho(\varphi_1 \wedge \varphi_2,\pi,t)
=
\min
\left(
\rho(\varphi_1,\pi,t),
\rho(\varphi_2,\pi,t)
\right)
$$

* **Disjunction (OR):**

$$
\rho(\varphi_1 \vee \varphi_2,\pi,t)
=
\max
\left(
\rho(\varphi_1,\pi,t),
\rho(\varphi_2,\pi,t)
\right)
$$

* **Implication ($\rightarrow$):**

$$
\rho(\varphi_1 \rightarrow \varphi_2,\pi,t)
=
\max
\left(
-\rho(\varphi_1,\pi,t),
\rho(\varphi_2,\pi,t)
\right)
$$

### Temporal Operators

Temporal operators are applied only to **time-indexed temporal formulas** (`temF`). Trace-level statistical and maneuver atoms are evaluated once over the complete execution trace and are not nested within temporal operators.

For a temporal interval $J$:

* **Always ($G_J$):**

$$
\rho(G_J\varphi,\pi,t)
=
\min_{t' \in (t \oplus J) \cap I_\pi}
\rho(\varphi,\pi,t')
$$

* **Eventually ($F_J$):**

$$
\rho(F_J\varphi,\pi,t)
=
\max_{t' \in (t \oplus J) \cap I_\pi}
\rho(\varphi,\pi,t')
$$

* **Until ($U_J$):**

$$
\rho(\varphi_1 U_J \varphi_2,\pi,t)
=
\max_{t' \in (t \oplus J) \cap I_\pi}
\left(
\min
\left(
\rho(\varphi_2,\pi,t'),
\min_{t'' \in [t,t']}
\rho(\varphi_1,\pi,t'')
\right)
\right)
$$

* **Next ($X$):**

$$
\rho(X\varphi,\pi,t)
=
\rho(\varphi,\pi,t+1)
$$

where $t \oplus J$ denotes the time indices obtained by shifting interval $J$ relative to the current time $t$. Unbounded `G` and `F` range over the remaining trace.

---

## 4. Arithmetic Extensions

OBSpec supports arithmetic operators such as `+`, `-`, `*`, and `/` over numerical expressions.

**Example: Summing lane-change occurrences across multiple agents**

```text
behaviorObjective =
    count(ChangingLane(npc1))
  + count(ChangingLane(npc2)) > 2
