### OBSpec: Formal Syntax Definition (BNF)

The formal syntax of **OBSpec** defines the structure of oracle formulas used to specify scenario intents and safety requirements.

$$
\begin{array}{r c l}
\mathit{obspec} & ::= & (\texttt{intentOracle} \mid \texttt{safetyOracle}) \texttt{ = } \varphi \\
\\
\varphi & ::= & \mathit{atom} \mid \neg \varphi \mid \varphi \wedge \varphi \mid \varphi \vee \varphi \mid \varphi \rightarrow \varphi \\
      & \mid & G_{[a,b]} \varphi \mid F_{[a,b]} \varphi \mid \varphi U \varphi \mid X \varphi \\
\\
\mathit{atom} & ::= & \mathit{statAtom} \mid \mathit{maneuverAtom} \mid \mathit{spatialAtom} \\
\mathit{statAtom} & ::= & \mathit{statFunc}(\mathit{signal})~\mathit{op}~\mathit{num} \\
\mathit{statFunc} & ::= & \texttt{avg} \mid \texttt{std} \mid \texttt{max} \mid \texttt{min} \\
\mathit{maneuverAtom} & ::= & \mathit{manFunc}(\mathit{maneuver})~\mathit{op}~\mathit{num} \\
\mathit{manFunc} & ::= & \texttt{count} \mid \texttt{duration} \mid \texttt{switch_count} \\
\mathit{spatialAtom} & ::= & \mathit{dist}(\mathit{entity}, \mathit{entity})~\mathit{op}~\mathit{num} \\
\mathit{entity} & ::= & \mathit{agent} \mid \mathit{point} \\
\\
\mathit{signal} & ::= & \texttt{speed}(\mathit{agent}) \mid \texttt{acc}(\mathit{agent}) \mid \texttt{brake}(\mathit{agent}) \mid \texttt{steer}(\mathit{agent}) \\
\mathit{maneuver} & ::= & \texttt{ChangingLane}(\mathit{agent}) \mid \texttt{Overtaking}(\mathit{agent}) \mid \texttt{TurningAround}(\mathit{agent}) \\
\\
\mathit{op} & ::= & > \mid < \mid >= \mid <= \mid == \mid !=
\end{array}
$$
