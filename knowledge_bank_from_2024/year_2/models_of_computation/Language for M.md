
Let $M = (Q, \Sigma, \delta, q_0, F)$ be a DFA. For $w = a_1a_2\dots a_n$ a string over $\Sigma$, write $q \xrightarrow{w}* q'$ if there exists states $q_0, q_1, ..., q_{n-1}, q_n = q'$ (not necessarily distinct), and transitions     $$q \xrightarrow{a_1} q_1 \xrightarrow{a_2} \cdots \xrightarrow{a_n} q_n = q'.$$
$L(M)$, the language (recognised or accepted by $M$), consists of all strings $w$ over $\Sigma$ satisfying $q_0 \xrightarrow{w}* q'$ where $q' \in F$. Refer to [[Deterministic Finite Automata]] for the definition of $F$.
Here's an example of what a certain word from a language based on $M$ may look like.
- case $n = 0$: $q \xrightarrow{\epsilon}* q'$ if and only if $q = q'$
- case $n = 1$: $q \xrightarrow{a}* q'$ if and only if $q \xrightarrow{a} q'$

Notice that the asterisk is a condenser - the whole string of words and their maps are all contained within the arrow with the asterisk. 

###### Note:
A language is called **regular** if some DFA recognises it.

#### Formal Definition for DFA

The **language** recognised (or accepted) by the DFA $M$, denoted by $L(M)$, is the set of all strings that are accepted by $M$. It is defined as: $$L(M) = \{w \in \Sigma^* \,|\, q_0 \xrightarrow{w} q', q\in F \}$$
This essentially means that $L(M)$ is a set comprising of all strings that are acceptable by $M$.

#### Example

Revising our previous example, $M_1 = (Q, \Sigma, \delta, q_0, F)$ where 
- $Q = \{q_1, q_2, q_3\}$
- $\Sigma = \{0,1\}$
- $q_1$ is the start state; $F = {q_2}$

$\delta$ is given by the following table


|       | 0     | 1     |
| ----- | ----- | ----- |
| $q_1$ | $q_1$ | $q_2$ |
| $q_2$ | $q_3$ | $q_2$ |
| $q_3$ | $q_2$ | $q_2$ |
Therefore, $L(M)$ for this specific automata $M$ is defined as every possible string that has the following property:
- Contains at least one 1,
- After the last 1 in the given string, an even number of 0's follow the string.

The set $L(M_1)$ is defined as:

$L(M_1) = \{ w \in \{0,1\}^* \,|\, w \text{ contains at least one 1 and has an even number of 0's after the last 1}\}$.   

Where:

- $\{0, 1\}^*$ represents the set of all binary strings.
- $w$ is a binary string.

### Example Strings in $L(M_1)$:

- Strings like `10`, `1100`, `111000`, `101010`, `1`, and `1010` are all in $L(M_1)$, because they contain at least one `1` and the number of `0`s after the last `1` is even.
- Strings like `11`, `1000`, or `1011` are not in $L(M_1)$, because they either have an odd number of `0`s following the last `1`, or do not meet the condition regarding the final `0`s.



### Formal Definition for NFA:

