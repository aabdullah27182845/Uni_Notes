**Theorem**: A language $L$ is regular if and only if $\equiv_L$ has finite index. Check [[The Pumping Lemma]] for the definition of $\equiv_L$. 

Moreover, the index is the size (= number of states) of the smallest DFA recognising $L$.

**Note**: The pumping lemma is not a characterisation of regular languages: it is not an *if and only if* statement. In contrast, the Myhill-Nerode Theorem is indeed a characterisation of regular languages.


**Proof**: It suffices to prove the following two statements:
1. If $L$ is recognised by a DFA with $k$ states, then $L$ has an index of at most $k$.
2. If $L$ has a finite index $k$, then it is recognised by a DFA with $k$ states.

Here is the notation:
For $x \in \Sigma^*$, let $\hat{\delta}(q,x) = q'$ if and only if $q \xrightarrow{x}^* q'$.

For the proof of the first statement, suppose $L$ is recognised by a DFA $M = (Q, \Sigma, \delta, q_0, F)$. Note that $$\hat{\delta}(q_0, x) = \hat{\delta}(q_0, y) \implies x \equiv_L y.$$Take $x_1,...,x_{k+1} \in \Sigma^*$, all distinct. Since $M$ has only $k$ states, by the pigeonhole principle, for some $1 \leq i < j \leq k+1$, we have $\hat{\delta}(q_0, x_i) = \hat{\delta}(q_0, x_j)$, and so $x_i \equiv_L x_j$. It follows from there that $\equiv_L$ has at most $k$ equivalent classes. And that's done for the first part.

Proving the second part, we assume that $L$ has a finite index. Define a structure $M = (Q, \Sigma, \delta, q_0, F)$ as follows: $$\begin{align}
Q \quad &= \quad \{[x] : x \in \Sigma^*\} \\
q_0 \quad &= \quad [\epsilon] \\
\delta([x], a) \quad &= \quad [xa] \\
F \quad &= \quad \{[w] : w \in L\}
\end{align}$$We need to verify: (a) $M$ is a DFA, and (b) $M$ recognises $L$.

For (a), $|Q|$ is the index $L$ which is finite, and $\delta$ is a well-defined function because $x \equiv_L y$ implies $xa \equiv_L ya$ for any $a \in \Sigma$.

For (b), for any $w \in \Sigma^*$, $w \in L(M)$ if and only if $[\epsilon] \xrightarrow{w}^* [\epsilon] \in F$ if and only if $w \in L$.                     $\square$

---
##### Applying the Theorem on an Example

**Example**: $L = \{ ww : w \in \{0,1\}^*  \}$ is not a regular language.

Take any distinct $i,j \geq 0$. We have $0^i1 \not\equiv 0^j1$ because $0^i10^i1 \in L$, but $0^j10^j1 \notin L$. Hence $\equiv_L$ has an infinite index. Therefore, by the Myhill-Nerode Theorem, our language $L$ is not regular.

**Example**: $L_n = \{ w : \text{the } n\text{-th last letter of }  w\text{ is 1} \}$

Is $L_n$ regular for all $n$? Yes. We can construct an NFA:
![[Pasted image 20241030183110.png]]
And this NFA recognises our language earlier. Since every NFA has an equivalent regular expression, the regular expression for this is the following: $$(0 + 1)^* 1 \,  \underbrace{(0 + 1) \cdots (0 + 1)}_{n - 1}$$Since it is regular, this begs the question: What is the index of $\equiv_{L_n}$?

To answer this question, suppose $w$ and $w'$ differ somewhere in their last $n$ letters, say $w = x1y$ and $w' = x'0y'$ where $|y| = |y'| = m < n$. Then $w \not\equiv_{L_n} w'$ because $w0^{n-1-m} \in L_n$, but $w'0^{n-1-m} \notin L_n$. So the index of $\equiv_{L_n}$ is at least $2^n$.


**Example:** $L_n = \{w : \text{the } n\text{-th last letter of } w \text{ is 1}\}$.

What is the index of $\equiv_{L_n}$?

Suppose $w$ and $w'$ differ somewhere in their last $n$ letters, say $w = x1y$ and $w' = x'0y'$ where $|y| = |y'| = m < n$. Then $w \not\equiv_{L_n} w'$ because $w0^{n-1-m} \in L_n$ but $w'0^{n-1-m} \notin L_n$. So the index of $\equiv_{L_n}$ is at least $2^n$.

Above, there is an NFA which recognises $L_n$ with $n + 1$ states.

Hence an NFA can be exponentially more compact than the smallest DFA.
