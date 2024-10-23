
The equivalence of regular expressions and finite automaton is a fundamental result in Automata Theory. 

### Kleene's Theorem

Let $L \subseteq \Sigma^*$. The following are equivalent: 
- $L$ is regular, i.e., for some finite automaton $M$, $L = L(M)$.
- $L$ is denoted by some regular expression $E$, i.e. $L = L(E)$.

**Proof**: We can prove Kleene's theorem by proving that (ii) implies (i) and (i) implies (ii).

Here is the first part, proving that (ii) implies (i). We show that there is a systematic way of transforming $E$ into an NFA $N_E$, so that $L(E) = L(N_E)$, by recursion on the structure of $E$.

Base Cases: For each of the three cases, $E = \epsilon$, $E = \emptyset$, or $E = a$ where $a \in \Sigma$, there is an NFA $N_E$ such that $N_E$ recognises $L(E)$.

Inductive Cases: Take regular expressions $E$ and $F$. Suppose $N_E$ and $N_F$ are NFA's that recognise $L(E)$ and $L(F)$ respectively. We have proved already that regular languages are closed under union, concatenation, and star by constructing NFA's that recognise $L(N_E) \cup L(N_F)$, $L(N_E) \cdot L(N_F)$, and $(L(N_E))^*$ respectively. By definition, these NFA's are equivalent to $E + F$, $EF$, and $E^*$ respectively.

And this proof is done. Although not very intuitive, it is indeed correct. Perhaps an example may be better in order to understand this. Suppose we want to construct an NFA for the following regular expression: `(0+01*0)*0`. 

The first step is to create an NFA that accepts `0` and another NFA that accepts `1` as a string. Here is what that looks like:

![[base_case_nfa]]
And we can add to that with the following:
![[nfa_added]]

And from there, we can add more to our construction until we end up with the following:
![[Pasted image 20241021234638.png]]
(I got too lazy to draw but these constructions remain equivalent to what we were performing when proving NFA';s are closed under these operations).

Finally, we get the following NFA:
![[Pasted image 20241021234850.png]]



**Now to prove the other implication:** (i) implies (ii)

We will begin with $M = (Q, \Sigma, \delta, q_0, F)$, for $X \subseteq Q$, and $q, q' \in Q$, we construct by induction on the size of $X$, a regular expression: $$E_{q,q'}^X$$whose denotation is the set of all strings such that: there is a path from $q$ to $q'$ in $M$ labelled by $w$ (i.e., $q \xRightarrow{w} q'$) such that all intermediate states along that path lie in $X$.

It suffices to prove the following **Lemma**: For any $X\subseteq Q$, for any $q, q' \in Q$, there is a regular expression $E_{q,q'}^X$, satisfying $L(E_{q,q'}^X) =$ $$\{ w \in \Sigma^* : q\xRightarrow{w}q' \text{ in } M \text{ with all intermediate states of seq. in } X\}$$We prove this lemma by induction on the size of $X$.

**Basis**: $X = \emptyset$. Let $a_1,...,a_k$ be all the symbols in $\Sigma \cup \{\epsilon\}$ such that $q' = \delta(q,a_i)$, for $q \neq q'$, take $$E_{q,q'}^{\emptyset} = \begin{cases} a_1 + a_2 + \cdots + a_k \quad\quad \text{if } k \geq 1  \\ 0 \quad\quad\quad\quad\quad\quad\quad\quad\quad\,\,\text{if } k = 0\end{cases}$$and for $q = q'$, we have $$E_{q,q'}^{\emptyset} = \begin{cases} a_1 + a_2 + \cdots + a_k + \epsilon \quad\quad \text{if } k \geq 1  \\ 0 \quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\,\,\,\,\,\text{if } k = 0\end{cases}$$
**Inductive Step:** For a non-empty $X$, choose $r \in X$, and call it the separating state. Now, any path from $q$ to $q'$ with all intermediate states in $X$, either
1. never visits $r$
2. visits $r$ for the first time, followed by finite number of loops from $r$ back to itself without visiting $r$ in between, but staying in $X$, and finally followed by a path from $r$ to $q'$.

Thus, we take $$E_{q,q'}^X = E_{q,q'}^{X\setminus{\set{r}}} + E_{q,r}^{X\setminus\set{r}}\cdot(E_{r,r}^{X\setminus\set{r}})^*\cdot E_{r,q'}^{X\setminus\set{r}}$$Finally, the expression $\sum_{f\in F} E_{q_0, f}^Q$ denotes $L(M)$.

As a rule of thumb, the best possible $r$ is the one that separates the automaton as much as possible. (Could it be the initial starting state? Ask)

And with that, our proof has come to a conclusion. $QED \quad\square$.

---

##### Example:

Consider the NFA $M = (\set{a,b,c}, \set{0,1}, \delta, a, {a})$ where $\delta$ is given by

|     | 0           | 1         | $\epsilon$  |
| --- | ----------- | --------- | ----------- |
| a   | $\emptyset$ | $\set{b}$ | $\emptyset$ |
| b   | $\set{b}$   | $\set{c}$ | $\emptyset$ |
| c   | $\set{b}$   | $\set{a}$ | $\emptyset$ |
Here is the visual graph for the NFA:

![[Pasted image 20241022141108.png]]
We pick $b$ as the separating state: the resulting regular expression is: $$E_{a,a}^{\set{a,b,c}} = E_{a,a}^{\set{a,c}} + (E_{a,b}^{\set{a,c}})^* \cdot E_{a,c}^{\set{a,c}}$$
And by inspection, you can see that $E_{a,a}^{\set{a,c}} = \epsilon$, $E_{a,b}^{\set{a,c}} = 1$ and $E_{b,a}^{\set{a,c}} = 11$.

If we pick $c$ to be our separating state, we get the following: $$E_{b,b}^{\set{a,c}} = E_{b,b}^{\set{a}} + E_{b,c}^{\set{a}} \cdot (E_{c,c}^{\set{a}})^* \cdot E_{c,b}^{\set{a}}$$where $E_{b,b}^{\set{a}} = 0 + \epsilon$, $E_{b,c}^{\set{a}} = 1$, $E_{c,c}^{\set{a}} = \epsilon$, and $E_{c,b}^{\set{a}} = 0 + 11$.

Hence, putting it all together, we get:

$E_{a,a}^{\set{a,b,c}} = \epsilon + 1(0 + \epsilon + 1\epsilon^*(0+11))^*11 \equiv \epsilon + 1(0 + 10 + 111)^*11$, i.e. $L(M) = L(\epsilon + 1(0 + 10 + 111)^*11)$.

However, if we continued with $b$ as our separating state, we would be left with a different regular expression. 


