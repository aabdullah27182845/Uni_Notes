
(Note that from this point onwards, we will mostly follow definitions in Kozen's Automata and Computability).

A (non-deterministic) pushdown automaton is like an NFA, except it has a stack (pushdown store) for recording a potentially unbounded amount of information, in a last-in-first-out (LIFO) fashion.

![[Pasted image 20241120104423.png]]
In each step, the NPDA pops the top symbol off the stack; based on this symbol, the input symbol currently reading, and its current state, it can:
1. Push a sequence of symbols (possibly $\epsilon$) onto the stack.
2. Move its read head one cell to the right.
3. Enter a new state.

According to the transition rule $\delta$ of the machine.

We allow $\epsilon$-transition: an NPDA can pop and push without reading the next input symbol or moving its read head.

Note: an NPDA can only access the top of the stack symbol in each step.

Here is an example of an NPDA being used to create well-balanced strings of parentheses.

| Descriptive Format                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Visual Format                        |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| Here is the Intuitive description of an NPDA:<br>1. IF input symbol is "\[", <br>THEN push "\[" onto the stack.<br><br>2. IF input symbol is "\]" AND top of stack is "\["<br>THEN pop.<br><br>3. IF all of input read AND top of stack is "$\bot$"<br>THEN ACCEPT. ("$\bot$" is the initial stack symbol).<br><br>See to the right, we use "`[[][]]`" as an input.<br><br>Think of an NPDA as representing an algorithm <br>(for a decision problem) with memory access in <br>the form of a stack. | ![[Pasted image 20241120105717.png]] |

---

### Formal Definition

A non-deterministic pushdown automaton (NPDA) is a 7-tuple $(Q, \Sigma, \Gamma, \delta, q_{0}, \bot, F)$ where $Q$, $\Sigma$, $\Gamma$, $\delta$, and $F$ are all finite sets, and
- $Q$ is the set of states.
- $\Sigma$ is the input alphabet
- $\Gamma$ is the stack alphabet
- $\delta : Q \times (\Sigma \cup \{\epsilon\}) \times \Gamma \to \mathcal{P}(Q \times \Gamma^*)$ is a finite transition function.
- $q_{0} \in Q$ is the start state.
- $\bot \in F$ is the initial stack symbol.
- $F \subseteq Q$ is the set of accept states.

Note that an NPDA is strictly more powerful than a deterministic PDA. We shall not consider the latter specifically here because of this reason.


#### Configuration

A configuration of $M$ is an element of $Q \times \Sigma^* \times \Gamma^*$ describing the current state, the portion of the input yet unread (i.e. under and to the right of the input head) and the current stack contents.

The start configuration is $(q_{0}, w, \bot)$, i.e. $M$ always starts in the start state with its input head scanning the leftmost input symbol and the stack containing only $\bot$.

The next-configuration relation $\to$ describes how $M$ moves from one configuration to another in one step. Formally, 
- If $(q, \gamma) \in \delta(p, a, A)$ then for any $v \in \Sigma^*$ and $\beta \in \Gamma^*$, $$(p, av, A\beta) \to (q, v, \gamma \beta)$$(The input symbol $a$ is "consumed"; $A$ was popped and $\gamma$ was pushed, and the new state is $q$.)
- If $(q, \gamma) \in \delta(p, \epsilon, A)$ then for any $v \in \Sigma^*$ and $\beta \in \Gamma^*$, $$(p, v, A\beta) \to (q, v, \gamma \beta)$$(no input symbol has been "consumed".)

---

We define the reflexive, transitive closure of $\to$, written $\xrightarrow{*}$, as follows: $$\begin{align}
C \xrightarrow{0} D &\iff C = D \\
C \xrightarrow{n+1}D &\iff \exists E \text{ s.t } E \wedge E \to D 
\end{align}$$and define $C \xrightarrow{*} D$ just if $C \xrightarrow{n} D$ for some $n \geq 0$. I.e. $C \xrightarrow{*} D$ if and only if $D$ follows from $C$ in 0 or more steps of the relation $\to$.

Formally, we say that $M$ accepts an input $x$ by final state if for some $q \in F$, and $\gamma \in \Gamma^*$, we have $(q_{0}, x, \bot) \xrightarrow{*} (q, \epsilon, \gamma)$. Configurations of the form $(q, \epsilon, \gamma)$ where $q \in F$ and $\gamma \in \Gamma^*$ are called accepting.

The language of $M$, written $L(M)$, is defined to be the set of strings accepted by $M$.

There is also another accepting convention:

*M accepts an input $x$ by empty stack if for some $q \in Q$*, $$(q_{0}, x, \bot) \xrightarrow{*} (q, \epsilon, \epsilon)$$N.B. $F$ is irrelevant in the definition of acceptance by empty stack. 

The two accepting conventions are equivalent.

---

### Example

Take constructing an NPDA that recognising $\{ ww^R : w \in \{0, 1\}^*  \}$.

1. We need to push the input symbols onto the stack, one at a time..
2. Non-deterministically, guess that the middle of the string has been reached at some point during (1), and then change into popping off the stack for each symbol read, checking to see if they are the same.
3. If they are always the same symbols, and the stack empties at the same time as the input is finished, accept.

Our 7-tuple would look like the following: $$(\{q_{0}, q_{1}, q_{2}\}, \{0,1\}, \{0,1,\bot\}, \delta, q_{0}, \bot, \{q_{2}\})$$where $$
\delta : 
\begin{cases}
(q_0, 0, Z) \mapsto \{ (q_0, 0Z) \} \\
(q_0, 1, Z) \mapsto \{ (q_0, 1Z) \} \\
(q_0, \epsilon, Z) \mapsto \{ (q_1, Z) \} \\
(q_1, 0, 0) \mapsto \{ (q_1, \epsilon) \} \\
(q_1, 1, 1) \mapsto \{ (q_1, \epsilon) \} \\
(q_1, \epsilon, \perp) \mapsto \{ (q_2, \epsilon) \}
\end{cases}
$$Where $Z = 0, 1, \bot$.

Here is the transition graph for this NPDA: $$\begin{align}
1, Z \to 1Z \\
0, Z \to 0Z
\end{align}$$![[Pasted image 20241120144032.png]]
We use the following notation used here: In the transition graph, we represent the transition $(q', \gamma) \in \delta(q,a,Z)$ by an edge, labelled "$a, Z \to \gamma$", that joins node $q$ to $q'$.


| Derivation                                                                                                                                                                                                                                                                                 | Diagram                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------ |
| $\begin{aligned} (q_0, 011110, \perp) &\to (q_0, 11110, 0\perp) \\ &\to (q_0, 1110, 10\perp) \\ &\to (q_0, 110, 110\perp) \\ &\to (q_1, 110, 110\perp) \\ &\to (q_1, 10, 10\perp) \\ &\to (q_1, 0, 0\perp) \\ &\to (q_1, \epsilon, \perp) \\ &\to (q_2, \epsilon, \epsilon) \end{aligned}$ | ![[Pasted image 20241120144032.png]] |

And it makes more sense to show both the implementation-level description alongside the transition diagram.

| Implementation-level description                                                                                                                                                                                                                                                         | Transition Diagram                   |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| $(\{q,q'\}, \{[,]\}, \{\bot, [\}, \delta, q, \bot, \{q'\})$<br><br>where<br><br>$\delta : \begin{cases}(q, [, \perp) \mapsto \{ (q, [\perp) \} \\(q, [, [) \mapsto \{ (q, [[) \} \\(q, ], [) \mapsto \{ (q, \epsilon) \} \\(q, \epsilon, \perp) \mapsto \{ (q', \epsilon) \}\end{cases}$ | ![[Pasted image 20241120145152.png]] |

