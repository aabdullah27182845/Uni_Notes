After working with regular languages and expressions, can we find a set of axioms that, under total closure, define the total regular expression space with an added point that each equivalence between two regular expressions is a theorem?

That's where we get to Kozen's Axioms, which dictate the following: $$\begin{align}E+(F+G) &\equiv (E + F) + G \\
E + F &\equiv F + E \\
E + \emptyset &\equiv E \\
E + E &\equiv E \\
(EF)G &\equiv E(FG) \\
\epsilon E \equiv &E\epsilon \equiv E \\
E(F+G) &\equiv EF + EG \\
(E+F)G &\equiv EG + FG \\
E\emptyset \equiv &\emptyset E \equiv \emptyset \\
\epsilon + EE^* &\equiv E^* \\
\epsilon + E^*E &\equiv E^*
\end{align}$$
And alongside that, we have the following two rules too:
1. $F + EG ≤ G ⇒ E^∗F ≤ G$ 
2. $F + GE ≤ G ⇒ F E^∗ ≤ G$
And here, $E \leq F$ means $E + F \equiv F$, which is true if and only if $L(E) \subseteq L(F)$.

---

###### Soundness and Completeness

Whenever we generate a set of axioms that we use to create theorems, we have to make sure that the system we speak about is either sound, or complete. No system can be both, which was proven thanks to Kurt Gödel. However, because of our constraints, and also with the fact that regular expressions are something deterministic (due to their deterministic patterns), we therefore can argue for both soundness and completeness under this context, and this context only.

**Proposition**: Kozen's axioms are sound, since each axiom is a valid sequence between regular expressions, and each rule is sound (i.e, if the premise is a valid equivalence, then so is the conclusion). An example to break this idea down is that if we take the last axiom, for any $E$, $F$, and $G$, if $L(F+GE) \subseteq L(G)$, then $L(FE^*) \subseteq L(G)$.

This proposition leads to **Kozen's Theorem**, which states: *All valid equivalences between regular expressions can be derived from Kozen’s axioms and rules, using the laws of equational logic.*

#### Proof of Kozen's Theorem

To prove Kozen's Theorem, it suffices that we prove soundness and completeness of our axiomatic calculi. 

**Soundness**: Here is one of 13 different cases for the proofs for this, but suppose we pick $F + GE ≤ G ⇒ F E^∗ ≤ G$ and prove this axiom's soundness. This can be done exhaustively for the other 12 axioms too.

Suppose $L(F + GE) ⊆ L(G)$. Any $w \in L(FE^*)$ has the form $fe_1e_2...e_n$, where $n \geq 0$, $f \in L(F)$, and $e_i \in L(E)$. We prove that $w \in L(G)$ by induction on $n$.

The base case of $n = 0$ follows from $L(E) \subseteq L(G)$.

For the inductive case, we need to show that $fe_1...e_n \in L(G)$. Now $fe_1e_2...e_n \in L(FE^*)$, and from here we get $fe_1e_2...e_n \in L(G)$; and so $fe_1e_2...e_ne_{n+1} \in L(G)$, which is contained in $L(G)$ by supposition. And we are done.                                                                   $\square$

**Completeness**: Since this was detailed as something "beyond the scope" of the module, I will nonetheless provide the intuition behind this instead of actually providing the formal proof.

- Regular expressions have a **semantic space** (the sets of strings they represent) and a **syntactic space** (the symbolic manipulations allowed by the axioms).
- The goal is to show that if two regular expressions are semantically equivalent, we can always transform one into the other using only the syntactic rules provided by the axioms.
- This connects the semantic world of languages with the syntactic manipulations in the formal system, ensuring that all language equivalences can be captured within the framework of Kozen's axioms.
- Atop that, when reasoning through this argument, you kind of have to show the following two things. Number 1, that every regular expression is linked to each other through recurrence/induction. And number 2, that some kind of inductive argument exists that actually does link each language to another. 

---

###### Example

Suppose we want to show that $(a+b)^* \equiv a^*(ba^*)^*$.

Note that it follows that the relation $\leq$ is transitive:
- If $E \leq F$ and $F \leq G$, then $E \leq G$. 

The proof for this being true is rather trivial. Because of how $\leq$ is defined over the languages of $E$, $F$, and $G$, all we need to show is that $\subseteq$ is transitive, and then by definition, so will $\leq$. 

I won't outline the proof for that completely, but you can see that if you define a relation $$R = \{ (a,a) : aRa \text{ under the premise that }  a \in B \implies a \in A\}$$And from there, we can chain this relation with another set $C$ and prove that the new relation is indeed closed under transitivity.

It also follows from the axioms that $\leq$ is monotonic, i.e. if $E \leq F$, then 
- $E + G \leq F + G$  if $L(E) \subseteq L(F)$, then for any $L(G)$, $L(E) \cup L(G) \subseteq L(F) \cup L(G)$ 
- $EG \leq FG$
- $GE \leq GF$
- $E^* \leq F^*$

And finally, it follows from the axioms that if $E + F \equiv G$, then $E \leq G$, since $E + G \equiv E + E + F \equiv E + F \equiv G$, where we use the idempotent axiom.

Using these extra theorems, we can now begin proving $(a + b)^* \leq a^*(ba^*)^*$ using Kozen's system.

First note that $\epsilon \leq (ba^*)^*$, and so using monotonicity, $a^* \leq a^*(ba^*)^*$.

But $\epsilon \leq a^*$, so by transitivity, $\epsilon \leq a^*(ba^*)^*$. 

Now, $a(a^*(ba^*)^*) \equiv (aa^*)(ba^*)^* \leq a^*(ba^*)^*$, by the fifth and tenth axiom, alongside monotonicity.

Similarly, $b(a^*(ba^*)^*) \equiv (ba^*)(ba^*)^* \leq (ba^*)^* \leq a^*(ba^*)^*$ by the tenth axiom and monotonicity.

So, therefore: $$
\begin{aligned}
a(a^*(ba^*)^*) + b(a^*(ba^*)^*) &\leq a^*(ba^*)^* \quad \text{(A(4))} \\
(a + b)((a^*(ba^*)^*)) &\leq a^*(ba^*)^* \quad \text{(A(8))} \\
(a + b)(\epsilon + a^*(ba^*)^*) &\leq a^*(ba^*)^* \quad \text{(eq.(1) above)} \\
(a + b) + (a + b)(a^*(ba^*)^*) &\leq a^*(ba^*)^* \quad \text{(A(8))} \\
(a + b)^*(a + b) &\leq a^*(ba^*)^* \quad \text{(rule 12)} \\
(a + b)^*(a + b) + \epsilon &\leq a^*(ba^*)^* \quad \text{(eq.(1) above, A(4))} \\
(a + b)^* &\leq a^*(ba^*)^* \quad \text{(A(11))}
\end{aligned}
$$as desired.

