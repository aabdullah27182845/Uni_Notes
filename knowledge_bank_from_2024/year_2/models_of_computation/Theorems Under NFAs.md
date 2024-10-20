
---
**Theorem**: Regular languages are closed under concatenation.

**Proof**: Take NFA's $N_1$ and $N_2$.

An NFA $N$ can be obtained that recognises $L(N_1)\cdot L(N_2)$ from the disjoint union of $N_1$ and $N_2$ by making the the start state of $N$ to be the start state of $N_1$. And from there, create $\epsilon$-transitions from each and every accepting state inside $N_1$ to the start state of $N_2$. Therefore, the accepting states of $N_2$ are now the accepting states of $N$.

##### Formal Proof

More formally, Let $N_1 = (Q_1, \Sigma_1, \delta_1, q_1, F_1)$ and $N_2 = (Q_2, \Sigma_2, \delta_2, q_2, F_2)$. we define $N = (Q, \Sigma, \delta, (q,1), F)$ as
- $q = q_1$
- $\Sigma = \Sigma_1 = \Sigma_2$
- $Q = Q_1 \times \{1\} \cup Q_2 \times \{2\}$.
- $F = F_2 \times \{2\}$.
- $\delta((r,1), a) = \{ (r', 1) : r' \in \delta_1(r,a)\}$                     for $a \neq \epsilon$ or $r \notin F_1$ 
- $\delta((r,1), \epsilon) = \{ (r', 1) : r' \in \delta_1(r, \epsilon)\} \cup \{ (q_2, 2)\}$     for $r \in F_1$.
- $\delta((r,2), a) = \{ (r', 2) : r' \in \delta_2(r, a) \}$

To prove equivalence over the construction of this NFA, we have to prove that the language this NFA accepts is equivalent to the concatenation of the two languages of $N_1$ and $N_2$. We will be doing this by double inclusion yet again:

**Proving $L(N_1)\cdot L(N_2) \subseteq L(N)$:**

Suppose $w \in L(N_1)\cdot L(N_2)$, then there exists $u = a_1,...,a_n \in L(N_1)$ and $v = b_1,...,b_m \in L(N_2)$ with $w = uv$.

Therefore, there exists $r_1,...,r_n \in Q_1$ with $r_n \in F_1$ such that, in $N_1$, $$q_1 \xRightarrow{a_1} r_1 \xRightarrow{a_2} ...... \xRightarrow{a_n} r_n$$and $s_1, s_2, ..., s_m \in Q_2$ such that, in $N_2$, $$q_2 \xRightarrow{b_1} s_1 \xRightarrow{b_2}...... \xRightarrow{b_m} s_m$$Then, in $N$ we have a sequence $$(q_1,1) \xRightarrow{a_1} (r_1, 1) \xRightarrow{a_2} ...... \xRightarrow{a_n} (r_n, 1) \xrightarrow{\epsilon} (q_2, 2) \xRightarrow{b_1} (s_1, 2) \xRightarrow{b_2}......\xRightarrow{b_m} (s_m,2)$$with $(s_m, 2) \in F$. Hence $w \in L(N)$.

**Now, to prove $L(N) \subseteq L(N_1)\cdot L(N_2)$:**

Suppose $w = a_1...a_k \in L(N)$, then there exists $r_1,...,r_n \in Q$ such that $$(q_1, 1) \xRightarrow{a_1} r_1 \xRightarrow{a_2}...\xRightarrow{a_n} r_n$$where $r_n \in F$. By definition of $F$, there exists an $s_k \in F_2$ such that $r_k = (s_k, 2)$. The definition of $\delta$ implies that there is exactly one $\epsilon$-transition to get from the first to the second component, i.e. there are states $s_1, ..., s_n \in Q_1$ and $s_{n+1},..., s_{k-1} \in Q_2$ such that $$(q_1,1) \xRightarrow{a_1} (s_1, 1) \xRightarrow{a_2} ...... \xRightarrow{a_n} (s_n, 1) \xrightarrow{\epsilon} (q_2, 2) \xRightarrow{a_{n+1}} (s_{n+1}, 2) \xRightarrow{a_{n+2}}......\xRightarrow{a_k} (s_k,2)$$Then, in $N_1$, we have the sequence $q_1 \xRightarrow{a_1} s_1 \xRightarrow{a_2} ... \xRightarrow{a_n} s_n$ with $s_n \in F_1$, and in $N_2$, we have the sequence $q_2 \xRightarrow{a_{n+1}} s_{n+1} \xRightarrow{a_{n+2}} ...... \xRightarrow{a_k} s_k$ with $s_k \in F_2$.

Hence, $u = a_1...a_n \in L(N_1)$ and $v = a_{n+1}...a_k \in L(N_2)$, and therefore $w = uv \in L(N_1)\cdot L(N_2)$. $\square$

---

**Theorem**: Regular languages are closed under the star operator (\*). Have a look at [[Operations on a DFA]] to remind yourself what the star operator does.

We will go straight into the formal definition without explaining the intuition first, since this proof is somewhat self explanatory. #TO-DO if it is not.

##### Formal Proof

Take an NFA $N_1 = (Q_1, \Sigma, \delta_1, q_1, F_1)$ that recognises $A_1$.

Define $N = (Q \cup \{ q_0 \}, \Sigma, \delta, q_0, F_1 \cup \{q_0\} )$ where $$
\delta(q, a) = 
\begin{cases} 
  \delta_1(q, a) & q \in Q_1 \text{ and } q \notin F_1 \\
  \delta_1(q, a) & q \in F_1 \text{ and } a \neq \epsilon \\
  \delta_1(q, a) \cup \{ q_1 \} & q \in F_1 \text{ and } a = \epsilon \\
  \{ q_1 \} & q = q_0 \text{ and } a = \epsilon \\
  \emptyset & q = q_0 \text{ and } a \neq \epsilon 
\end{cases}
$$Now that we have defined our $N$ with this construction, its time again to prove equivalence through double inclusion.

**Proof for $L(N_1)^* \subseteq L(N)$:**

Obviously $\epsilon \in L(N)$ because $q_0 \in F$.

Suppose $w \in L(N_1)^*$ and $w \neq \epsilon$ then there exists $k \geq 1$ and $v_1,...,v_k$ such that $w = v_1...v_k$ and $v_i = a_{i_1}...a_{i_{n_i}} \in L(N_1)$. Then for each $i$ there exists $r_{i_1},...,r_{i_{n_i}} \in Q_1$ such that $$q_1 \xRightarrow{a_{i_1}} r_{i_1} \xRightarrow{a_{i_2}}... \xRightarrow{a_{i_{n_i}}}r_{i_{n_i}}$$with $r_{i_{n_i}} \in F_1$. Then $N$ we have the sequence $$q_0 \xrightarrow{\epsilon} q_1 \xRightarrow{a_{11}}r_{11} \xRightarrow{a_{12}}...\xRightarrow{a_{1n_1}}r_{1n_1} \xrightarrow{\epsilon} q_1 \xRightarrow{a_{21}}... \xRightarrow{a_{kn_k}} r_{kn_k}$$with $r_{kn_k} \in F$. Hence $w \in L(N)$.

Now that we are done with the first half, its time to prove the other half.

**Proof for $L(N) \subseteq L(N_1)^*$ :**

If $w = \epsilon$, then $w \in L(N_1)^*$ by definition of the star operator.

Suppose $w = a_1...a_n \in L(N)$, then there exists $r_1,...,r_n \in Q$ such that $$q \xrightarrow{\epsilon} q_1 \xRightarrow{a_1}r_1 \xRightarrow{a_2}...\xRightarrow{a_n} r_n$$with $r_n \in F$.

Let $k-1$ be the number of occurrences of the "new" $\epsilon$-transitions $r_j \xrightarrow{\epsilon} q_1$ with $r_j \in F_1$. If we split the transitions sequence at these transitions, we get $k$ transition sequences $q_1 \xRightarrow{v_i} r_i$ such that $w = v_1...v_k$ for each $v_i \,\, v_i \in L(N_1)$.

Hence $w = v_1...v_k \in L(N_1)^*$                                                                                            $\square$

