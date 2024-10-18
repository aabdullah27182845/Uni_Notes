
### Definitions

Let $A$ and $B$ be languages. Define
- **Union** : $A \cup B = \{x : x \in A \text{ or } x \in B\}$.
- **Concatenation** : $A \cdot B = \{xy : x \in A \text{ and } y \in B\}$. Note that this definition of concatenation is not commutative, $A\cdot B \neq B \cdot A$.
- **Star**: $A^* = \{ x_1x_2...x_k : k \geq 0 \text{ and each } x_i \in A \}$. Note that $\epsilon$ ([[Epsilon]]) is also part of the star definition, for when $k = 0$.

### Example

Take $A = \{good, bad\}$ and $B = \{boy, girl\}$. Then

- $A \cup B = \{good, bad, boy, girl\}$
- $A \cdot B = \{goodboy, goodgirl, badboy, badgirl\}$
- $A^* = \{\epsilon, good, bad, goodgood, goodbad, goodgoodgood, ... \}$, meaning that informally, you can define $A^*$ as $$A^* = \{\epsilon\} \,\cup\, A\,\cup\,(A\cdot A)\,\cup\,(A\cdot A\cdot A) \,\cup\, \dots $$ 
## [The Product Construction Theorem]

**Theorem**: Regular languages are closed under union, i.e,  $A_1$ and $A_2$ are regular languages, so is $A_1 \cup A_2$.

**Proof:** The intuition behind the proof for this theorem is that we can simulate both the DFAs that have $A_1$ and $A_2$ as their languages simultaneously.

Consider $M_1$ and $M_2$ such that $L(M_1) = A_1$ and $L(M_2) = A_2$, and let $M_1 = (Q_1, \Sigma_1, \delta_1, q_1, F_1)$ and $M_2 = (Q_2, \Sigma_2, \delta_2, q_2, F_2)$. 

We can construct $M = (Q, \Sigma, \delta, q_0, F)$ to recognise $A_1 \cup A_2$:
- $Q = Q_1 \times Q_2$, therefore $Q = \{(r_1,r_2) : r_1 \in Q_1 \text{ and } r_2 \in Q_2\}$.
- $\delta((r_1,r_2), a) = (\delta_1(r_1, a), \delta_2(r_2, a))$.
- $q_0 = (q_1,q_2)$
- $F = (F_1 \times Q_2) \cup (F_2 \times Q_1)$, therefore, $F = \{ (r_1,r_2) : r_1 \in F_1 \text{ or } r_2 \in F_2 \}$. Don't mind the order of the states, just take this to mean that any tuple that has either $r_1$ from $Q_1$ and $r_2$ from $Q_2$, but at least one of them needs to be an accepting state from $M_1$ and $M_2$ respectively.

Now that we have defined this object, we need to show that the language of $M$ is actually a subset of $M_1 \cup M_2$.

Step 2 of the Proof: Show $L(M) \subseteq L(M_1) \cup L(M_2)$

Let $w = a_1a_2...a_n \in L(M)$. Then there are $M$ transitions (\*)$$(q_0^1,q_0^2) \xrightarrow{a_1}(q_1^1,q_1^2) \xrightarrow{a_2} \cdots \cdots \xrightarrow{a_n}(q_n^1,q_n^2),$$with $q_0 = q_1, q_1^1,  ..., q_n^1 \in Q_1$ and $q_0^2 = q_2, q_2^1, ..., q_n^2 \in Q_2$.

Since $M$ accepts $w$, we have $(q_n^1, q_n^2) \in F$. Using the definition of $M$'s transition function, we can unpack (\*) to get a sequence of of $M_1$ transitions. $$q_0^1 \xrightarrow{a_1} q_1^1 \xrightarrow{a_2} \cdots \xrightarrow{a_n}q_n^1,$$and a sequence of $M_2$ transitions $$q_0^2 \xrightarrow{a_1} q_1^2 \xrightarrow{a_2} \cdots \xrightarrow{a_n}q_n^2.$$Since $(q_n^1,q_n^2) \in F$, we have that either $q_n^1 \in F_1$, in which case $w \in L(M)$, or $q_n^2 \in F_2$, in which case $w \in L(M_2)$ and we are done with this part of the proof.

To finish, we next show that at least $L(M_1) \subseteq L(M)$ (we can repeat this for $M_2$ too).

Let $w = a_1a_2...a_n \in L(M_1)$. By definition, for some $q_0^1 = q_1, q_1^1,...,q_n^1 \in Q_1$ with $q_n^1 \in F_1$, there is a sequence of transitions $$q_0^1 \xrightarrow{a_1} q_1^1 \xrightarrow{a_2} \cdots \xrightarrow{a_n}q_n^1,$$Now since $\delta_2$ is a function for any $q \in Q_2$, $a \in \Sigma$, there is a $q'$ such that $q \xrightarrow{a} q'$.
Hence, there are $q_0^2 = q_2, q_1^2, ..., q_n^2 \in Q$ such that $$$(q_0^1,q_0^2) \xrightarrow{a_1}(q_1^1,q_1^2) \xrightarrow{a_2} \cdots \cdots \xrightarrow{a_n}(q_n^1,q_n^2)$$is a valid sequence of $M$-transitions.
Since $(q_n^1, q_n^2) \in F \times Q_2 \subseteq F$, we have $w \in L(M)$. 

$QED$. 

