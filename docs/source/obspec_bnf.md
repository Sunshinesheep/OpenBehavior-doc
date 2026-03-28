### OBSpec: Formal Syntax Definition (BNF)

The formal syntax of **OBSpec** defines the structure of oracle formulas used to specify scenario intents and safety requirements.

$$
\begin{array}{r c l}
\langle obspec \rangle & ::= & (\text{intentOracle} \mid \text{safetyOracle}) \text{ = } \langle \varphi \rangle \\
\\
\langle \varphi \rangle & ::= & \langle atom \rangle \mid \neg \langle \varphi \rangle \mid \langle \varphi \rangle \ (\wedge \mid \vee \mid \rightarrow) \ \langle \varphi \rangle \\
                        & \mid & G_{[a,b]}(\langle \varphi \rangle) \mid F_{[a,b]}(\langle \varphi \rangle) \mid \langle \varphi \rangle \ U \ \langle \varphi \rangle \mid X \ \langle \varphi \rangle \\
\\
\langle atom \rangle    & ::= & \langle statFunc \rangle(\langle signal \rangle) \ \langle op \rangle \ \langle num \rangle \\
                        & \mid & \langle eventFunc \rangle(\langle predicate \rangle) \ \langle op \rangle \ \langle num \rangle \\
                        & \mid & \text{dist}(\langle agent \rangle, \langle agent \rangle \mid \text{target}) \ \langle op \rangle \ \langle num \rangle \\
\\
\langle statFunc \rangle & ::= & \text{avg} \mid \text{std} \mid \text{max} \mid \text{min} \\
\langle eventFunc \rangle & ::= & \text{count} \mid \text{duration} \mid \text{switch\_count} \\
\\
\langle signal \rangle   & ::= & (\text{speed} \mid \text{acc} \mid \text{brake} \mid \text{steer})(\langle agent \rangle) \\
\langle predicate \rangle& ::= & (\text{ChangingLane} \mid \text{Overtaking} \mid \text{TurningAround})(\langle agent \rangle) \\
                        & \mid & \langle signal \rangle \ \langle op \rangle \ \langle num \rangle \\
\\
\langle op \rangle      & ::= & \text{'>'} \mid \text{'<'} \mid \text{'>='} \mid \text{'<='} \mid \text{'=='} \mid \text{'!='} \\
\langle agent \rangle   & ::= & \text{ego} \mid \text{npc}_{1} \mid \text{npc}_{2} \mid \dots \\
\langle num \rangle     & ::= & \mathbb{R}
\end{array}
$$