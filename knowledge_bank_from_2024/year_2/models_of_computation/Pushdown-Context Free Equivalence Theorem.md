A major result in automata theory is the following:

**Theorem**: A language is context-free if and only if an NPDA recognises it. 

Here is the proof overview. We break down the proof into the following steps:
1. given CFG $G$, there is an equivalent NPDA $P_{G}$.
2. Given an NPDA $N$, there is an equivalent CFG $G_{N}$ generating $L(N)$.
	1. Every NPDA can be simulated by an NPDA with one state.
	2. Every NPDA with one state has an equivalent CFG.

To prove this theorem, we will prove these decomposed Lemmas.

---

**Lemma**: Given a CFG $G$, there is an equivalent NPDA $P_{G}$.

**Proof Idea**: The stack alphabet of $P_{G}$ consists of the terminal and variable symbols and $\bot$. We describe the action of $P_G$ informally:

1. Place the start variable symbol on the stack. 
2. Repeat forever the following: Pop top-of-stack $x$. There are three cases:
	1. $x$ is a variable $A$: Non-deterministically, select a rule for $A$ and replace $A$ by the string $w$ (say) on the right hand side of the rule (so that the leftmost symbol of $w$ is at the top of the stack).
	2. $x$ is a terminal $a$: Read the next input and compare it with $a$. If they do not match, then exit (and reject this branch of non-determinism).
	3. $x = \bot$. Enter the accept state.

The claim is the following. Our NDPA $P_{G}$ recognises $L(G)$.

#TO-DO  The formal proof is out of scope apparently for this course, so research, understand and fill in this proof yourself and do a lot of examples on matching CFGs with NDPA's.


#### Applied Example


|                                                                                                                                                                                                         Implementation-level description                                                                                                                                                                                                          |                                                                         Transition Diagram                                                                         |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| $(\{q_{0}, q_{1},q_{2}\}, \{a,b\}, \{\bot, S, a, b\}, \delta, q_{0}, \bot, \{q_{2}\})$<br><br>where $\delta$ is given by<br>$$\begin{aligned}\delta(q_0, \epsilon, \perp) &= \{ (q_1, S\perp) \} \\\delta(q_1, \epsilon, S) &= \{ (q_1, aSbS), (q_1, bSaS), (q_1, \epsilon) \} \\\delta(q_1, a, a) &= \{ (q_1, \epsilon) \} \\ \delta(q_1, b, b) &= \{ (q_1, \epsilon) \} \\\delta(q_1, \epsilon, \perp) &= \{ (q_2, \epsilon) \} \end{aligned}$$ | $\epsilon, S \to aSbS$<br>$\epsilon, S \to bSaS$<br>$\epsilon, S \to \epsilon$<br>$a,a \to \epsilon$<br>$b,b \to \epsilon$<br>![[Pasted image 20241120151154.png]] |

And here is an example of a specific run with the string $abab$ being accepted.


|                                                                                                                                                                                                                       Implementation-level description                                                                                                                                                                                                                        |                                                                         Transition Diagram                                                                         |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| $\begin{array}{rl} (q_0, abab, \perp) &\to (q_1, abab, S\perp) \\ &\to (q_1, abab, aSbS\perp) \quad \text{(1)} \\ &\to (q_1, bab, SbS\perp) \\ &\to (q_1, bab, bSaSbS\perp) \quad \text{(2)} \\ &\to (q_1, ab, SaSbS\perp) \\ &\to (q_1, ab, aSbS\perp) \quad \text{(3)} \\ &\to (q_1, b, SbS\perp) \\ &\to (q_1, b, bS\perp) \quad \text{(3)} \\ &\to (q_1, \epsilon, S\perp) \\ &\to (q_1, \epsilon, \perp) \quad \text{(3)} \\ &\to (q_2, \epsilon, \epsilon) \end{array}$ | $\epsilon, S \to aSbS$<br>$\epsilon, S \to bSaS$<br>$\epsilon, S \to \epsilon$<br>$a,a \to \epsilon$<br>$b,b \to \epsilon$<br>![[Pasted image 20241120151154.png]] |

Here's the leftmost derivation of the following CFG: $S \to aSbS \to abSaSbS \to abaSbS \to ababS \to abab$

Now, onto the converse. We will be simulating simulating NPDA's by a CFG. We do this in two steps: 

1. Every NPDA can be simulated by an NPDA with one state (that accepts by the empty stack).
2. Every NPDA with one state has an equivalent CFG.

For number 2: Take a one-state NPDA $M = (\{q\}, \Sigma, \Gamma, \delta, q, \bot, \emptyset)$ that accepts by the empty stack. Define $$G_{M} = (\Gamma, \Sigma, P, \bot)$$where $P$ contains a rule $$A \to cB_{1}\dots B_{k}$$for every transition $(q, B_{1}\dots B_{k}) \in \delta(q,c, A)$ where $c \in \Sigma \cup \{\epsilon\}$. Then we have $L(M) = L(G_{M})$.

And now that we have reduced our NPDA, here is the idea of proving the other direction. Maintain all state inform