
##### Formal Definition: 

We say that $E$ and $F$ are equivalent, written $E \equiv F$ if $L(E) = L(F)$.

Note that $\equiv$ is an equivalence relation. Relate back to first year discrete maths notes if you forgot what an equivalence relation is. But, here is the three things about it. It is reflexive, symmetrical, and transitive.

Here are some more identities:

1. Associativity: $(E + F) + G \equiv E + (F + G)$ and $(EF)G \equiv E(FG)$.
2. Commutativity: $E + F \equiv F + E$, but $EF \not\equiv FE$.
3. $E\emptyset\equiv\emptyset$ 
4. $\emptyset^*\equiv\epsilon$.
5. $E + \emptyset \equiv E \equiv\emptyset+ E$ and $E \cdot\epsilon = \epsilon\cdot E$.
6. But in general, $E + \epsilon \not\equiv E$ and $E\cdot\emptyset\not\equiv E$.

You can see that point 6 makes it so that these equivalence hold if and only if $E \not\equiv\emptyset$.

---

##### Examples

By using the following rules, we can prove that $(a+b)^* \equiv a^*(b a^*)^*$. Here is the proof.

**Proof**: Observe that $L((a+b)^*)$ is the set of all strings over $\{a,b\}$, thus, $L(a^*(ba^*)^*) \subseteq L((a+b)^*)$. Note that any $s \in L((a+b)^*)$ can be written as $$a^{n_0}ba^{n_1}...a^{n_{r-1}}ba^{n_r} \tag{1}$$where $a^n$ means $a...a$ $n$ times, each $n_i \geq 0$ and $r \geq 0$.

Here, $r$ is just the number of occurrences of $b$ in $s$, e.g. if $s$ is just the string $b$, then $n_0=n_1=0$; if $s$ is $\epsilon$ , then $n_0 = 0$.

Note that any string of the shape $ba^{n_i}$ matches the regular expression $ba^*$. Hence any string with the shape $(ba^{n_1})...(ba^{n_r})$, with each $n_i \geq 0$ and $r > 0$, matches $(ba^*)^*$. Hence, any string of the shape (1) matches $a^*(ba^*)^*$. Hence $L((a+b)^*) \subseteq L(a^*(ba^*)^*)$.                            $\square$



