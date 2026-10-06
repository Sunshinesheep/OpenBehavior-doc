### OBSpec: Formal Syntax Definition (BNF)

The formal syntax of **OBSpec** defines behavioral objectives and safety oracles over execution traces. Statistical and maneuver atoms are trace-level formulas, while spatial atoms are time-indexed formulas that can be combined with temporal operators.

$$
\begin{array}{r c l}
\mathit{obspec}
& ::= &
(\texttt{behaviorObjective} \mid \texttt{safetyOracle}) \texttt{ = } \varphi
\\
\\
\varphi
& ::= &
\mathit{temF}
\mid
\mathit{traceF}
\mid
\neg \varphi
\mid
\varphi \wedge \varphi
\mid
\varphi \vee \varphi
\mid
\varphi \rightarrow \varphi
\\
\\
\mathit{temF}
& ::= &
\mathit{spatialAtom}
\mid
\neg \mathit{temF}
\mid
\mathit{temF} \wedge \mathit{temF}
\mid
\mathit{temF} \vee \mathit{temF}
\mid
\mathit{temF} \rightarrow \mathit{temF}
\\
& \mid &
G\,\mathit{temF}
\mid
F\,\mathit{temF}
\mid
G_{[a,b]}\,\mathit{temF}
\mid
F_{[a,b]}\,\mathit{temF}
\\
& \mid &
\mathit{temF}\ U\ \mathit{temF}
\mid
X\,\mathit{temF}
\\
\\
\mathit{traceF}
& ::= &
\mathit{statAtom}
\mid
\mathit{maneuverAtom}
\\
\\
\mathit{statAtom}
& ::= &
\mathit{statFunc}(\mathit{signal})\ \mathit{op}\ \mathit{num}
\\
\mathit{statFunc}
& ::= &
\texttt{avg}
\mid
\texttt{std}
\mid
\texttt{max}
\mid
\texttt{min}
\\
\\
\mathit{maneuverAtom}
& ::= &
\mathit{manFunc}(\mathit{maneuver})\ \mathit{op}\ \mathit{num}
\\
\mathit{manFunc}
& ::= &
\texttt{count}
\mid
\texttt{duration}
\mid
\texttt{switch\_count}
\\
\\
\mathit{spatialAtom}
& ::= &
\mathit{dist}(\mathit{entity},\mathit{entity})\ \mathit{op}\ \mathit{num}
\\
\mathit{entity}
& ::= &
\mathit{agent}
\mid
\mathit{point}
\\
\\
\mathit{signal}
& ::= &
\texttt{speed}(\mathit{agent})
\mid
\texttt{acc}(\mathit{agent})
\mid
\texttt{brake}(\mathit{agent})
\mid
\texttt{steer}(\mathit{agent})
\\
\\
\mathit{maneuver}
& ::= &
\texttt{ChangingLane}(\mathit{agent})
\mid
\texttt{Overtaking}(\mathit{agent})
\mid
\texttt{TurningAround}(\mathit{agent})
\\
\\
\mathit{op}
& ::= &
>
\mid
<
\mid
>=
\mid
<=
\end{array}
$$
