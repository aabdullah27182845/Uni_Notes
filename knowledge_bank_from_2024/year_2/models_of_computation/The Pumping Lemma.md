
The pumping lemma is a powerful technique that for proving that certain languages are not regular.

Before we begin with the pumping lemma, we can first start with a claim:
- **Claim**: $B = \{ 0^n1^n : n \geq 0\}$ is not regular.

Without the pumping lemma, we will have to do with an informal argument: If there was a DFA that recognises $B$, it would need to remember the number of zeroes read from the input string. This would require that our DFA can store an arbitrarily large number, but every DFA has only a finite amount of memory, given the fixed number of states.

We need to be careful, both:
- $L_1 = \{ w \in \{0,1\}^* : w\text{ has an equal number of 0s and 1s}  \}$.
- $L_2 = \{ w \in \{0,1\}^* : w\text{ has an equal number of occurences of 01 and 10 as substrings} \}$
seem to require infinite memory to recognise. 

However, $L_1$ is not regular, whereas $L_2$ is.

With this out of the way, here is the definition of the Pumping Lemma.

---

### Definition

**Pumping Lemma**: If $A$ is a regular language, then there exists a number $p$ - the pumping length - such that $s \in A$ of length at least $p$, then $s$ may be divided into three pieces, $s = xyz$, satisfying:
1. for each $i \geq 0$, $xy^iz \in A$ (words "pumped up" from $s$ belong in $A$)
2. $|y| > 0$
3. $|xy| \leq p$

Note that without the second rule, this lemma is vacuously true since $\epsilon^i = \epsilon$ for all $i \geq 0$.

The pumping lemma is a complex statement, it is equivalent to $$\begin{align}
\forall L \in \text{Reg} \; \exists p \geq 1 \; \forall s \in L \; \exists x, y, z \in \Sigma^* \; \forall i \geq 0: \\ \; |s| \geq p \implies s = xyz \land |xy| \leq p \land |y| > 0 \land xy^i z \in L \end{align}
$$

###### Proof

Let $M = (Q, \Sigma, \delta, q_0, F)$ be a DFA that recognises $A$, and let $p = |Q|$.

Suppose $s = a_1...a_n \in L(M)$, where $n \geq p$. Let $q_1,...,q_n \in Q$ be such that $$q_1 \xrightarrow{a_1}q_2\xrightarrow{a_2}...\xrightarrow{a_p}q_p\,......\xrightarrow{a_n} q_n \in F$$By the pigeonhole principle, all $q_i$ cannot be distinct. This means that $q_j = q_{j'}$ for some $0 \leq j < j' \leq p$. Thus, the above transformation sequence is $$q_0 \xrightarrow{x}^* q_j \xrightarrow{y}^* q_{j'}(=q_j) \xrightarrow{z}^* q_n \in F$$
where $x = a_1...a_j$, $y = a_{j+1}...a_{j'}$, and $z = a_{j'+1}...a_n$. We have $|xy| \leq p$ and $|y| > 0$, and for every $i \geq 0$, $xy^iz \in L(M)$ as $$q_0 \xrightarrow{x}^* q_j \xrightarrow{y}^* q_j \dots \xrightarrow{z}^* q_n \in F \quad \square$$and with that, our proof is complete. Notice that all we had to show was that these paths are distinct and can be broken into those three specific sequences.        

---

###### Example

Take our initial example, that $B = \{0^i1^i : i \geq 0\}$ is not a regular language.

Take $s = 0^p1^p \in B$. Since $|s| \geq p$, by the Lemma, there are $x, y, z$ such that $s = xyz$ where $|xy| \leq p$, and $|y| \geq 0$. Hence, $x = 0^a$, $y = 0^b$, and $z = 0^{p-a-b}1^p$, where $b > 0$, and $a+b \leq p$.

The Lemma further asserts: for each $i \geq 0$, $xy^iz=0^a0^{bi}0^{p-a-b}1^{p} \in B$. In particular, if we take $i = 2$, we get: $0^a0^{2b}0^{p-a-b}1^b$, which simplifies to $0^{p+b}1^b$, and because of our constraint on $|y| > 0$ or else our statement is vacuously true, we get a contradiction, and so our initial premise that $B$ was a regular language is incorrect.

**Note**: Observe that the pumping lemma is quite tricky to use. The trick is to identify the appropriate word to "pump". It is often useful to "pump down" i.e. ($i = 0$). Another thing to know about it, is that if you think via logic, the pumping lemma is a universal statement beyond the length of $p$. This means that only one counterexample of length greater than $p$ is enough for you conclude that the language you're dealing with is not regular.

We can call this the **witness string**, and then make the following conclusion about this:
- **Proving a subset of $L$ is non-regular is not sufficient to prove that $L$ is non-regular**. A regular language could, in theory, contain non-regular subsets. *confirm this fact*
- **Using a single witness string that fails the Pumping Lemma, however, is sufficient to prove that $L$ is non-regular**. This is because the Pumping Lemma requires that all sufficiently long strings in $L$ must satisfy the pumping property if $L$ were regular. Failing to satisfy this property even once is enough to conclude that $L$ as a whole is non-regular.


---

#### Pumping Lemma Reversed

**Pumping Lemma in Contra positive form**: If for all $p \geq 1$, there exists an $s \in L$ with $|s| \geq p$ such that for all $x,y,z \in \Sigma^*$ with $s = xyz$, $|y| > 0$, and $|xy| \leq p$, there is an $i \geq 0$ such that $xy^iz \notin L$, then $L$ is not regular.

You can already begin to see how the logic behind this is quite difficult to understand conceptually (well, you (talking about myself) slept through proof systems in year 1, so it makes sense). So, here is a poem to understand it better.

<div style="text-align: center; max-width: 600px; margin: 0 auto; font-family: Arial, sans-serif; font-size: 18px; color: #333; background-color: #fff; padding: 20px; border-radius: 10px; box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);">
    <p style="margin: 20px 0; line-height: 1.5;">
        “Any regular language L has a magic number p<br>
        And any long-enough word in L has the following property:<br>
        Amongst its first p symbols is a segment you can find<br>
        Whose repetition or omission leaves x amongst its kind.”
    </p>

    <p style="margin: 20px 0; line-height: 1.5;">
        “So if you find a language L which fails this acid test,<br>
        And some long word you pump becomes distinct from all the rest,<br>
        By contradiction you have shown that language L is not<br>
        A regular guy, resilient to the damage you have wrought.”
    </p>

    <p style="margin: 20px 0; line-height: 1.5;">
        “But if, upon the other hand, x stays within its L,<br>
        Then either L is regular, or else you chose not well.<br>
        For w is xyz, and y cannot be null,<br>
        And y must come before p symbols have been read in full.”
    </p>

    <p style="margin: 20px 0; line-height: 1.5;">
        “As mathematical postscript, an addendum to the wise:<br>
        The basic proof we outlined here does certainly generalise.<br>
        So there is a pumping lemma for all languages context-free,<br>
        Although we do not have the same for those that are r.e.”
    </p>
</div>

By Martin Cohn and Harry Mairson.

---
###### Appendix

Let $x,y \in \Sigma^*$ be strings and $L \subseteq \Sigma^*$.

We say that $x$ and $y$ are [indistinguishable], written $x \equiv_L y$, if for every $z \in \Sigma^*$, $xy \in L$ if and only if $yx \in L$.

**Fact**: $\equiv_L$ is an equivalence relation. We define the index of $L$ to be the number of equivalence classes of the form $\equiv_L$. The index of $L$ is either finite or infinite.

Take $\Sigma = \{ 0, 1 \}$.

1. **$L_3 = \{ w : w \text{ has even length} \}$** — $u \equiv_{L_3} v$ iff $|u| \equiv |v| \,(\text{mod } 2)$. Now, $\equiv_{L_3}$ has two equivalence classes:
   - $[\epsilon] = [00] = [10] = \cdots = \{ w : |w| \text{ even} \}$
   - $[0] = [1] = [010] = [110] = \cdots = \{ w : |w| \text{ odd} \}$

2. **$L_1 = \{ w : w \text{ has equal numbers of 0s and 1s} \}$**
	For any $i, j \geq 0$, if $i \neq j$ then $0^i \not\equiv_{L_1} 0^j$ (because $0^i 1^i \in L_1$ but $0^j 1^i \notin L_1$). 
	Therefore, the index of $L_1$ is infinite.





