
Here's the kicker. Every NFA can be written as a DFA, where both of these automaton have the same language $L$.

Writing this formally:

**Observation**: Every DFA is an NFA.

We can conclude that every DFA is an NFA if we can find that the languages accepted by these two automaton are exactly the same. This in fact is true by the following theorem:

**Theorem:** Every NFA has an equivalent DFA.

**Proof**: Fix an NFA $N = (Q_N, \Sigma_N, \delta_N, q_N, F_n)$, we construct a following DFA $\mathcal{P}N = (Q_{\mathcal{P}N}, \Sigma_{\mathcal{P}N}, \delta_{\mathcal{P}N}, q_{\mathcal{P}N}, F_{\mathcal{P}N})$ such that $L(N) = L(\mathcal{P}N)$:
- $Q_{\mathcal{P}N} =^{\text{def}} \{ S_n : S_n \subseteq Q_N \}$.
- $\Sigma_{\mathcal{P}N} =^{\text{def}} \Sigma_N$
- $S \xrightarrow{a} S'$ in $\mathcal{P}N$ if and only if $S' = \{ q' : \exists q \in S . (q \xRightarrow{a} q' \text{ in } N) \}$.
- $q_{\mathcal{P}N} =^{\text{def}} \{ q : q_N \xRightarrow{\epsilon} q\}$.
- $F_{\mathcal{P}N} =^{\text{def}} \{ S \in Q_{\mathcal{P}N} : F_N \cap S \neq \emptyset  \}$.

Even though this definition is rigorous and correct, it is still incredibly difficult to decipher. So, here is a visual example of an NFA and a DFA accepting the following language: *All words that begin with a string of 0's followed by a string of 1's*. 

![[Pasted image 20241018181558.png]]

Note: this construction is more than sufficient. We have $\{q_0\}$ as a redundant state.

Thus, to prove that both languages are equivalent, we can do so by double inclusion: $L(N) \subseteq L(\mathcal{P}N)$ and $L(\mathcal{P}N) \subseteq L(N)$.

We shall begin with $L(N) \subseteq L(\mathcal{P}N)$:

Suppose $\epsilon \in L(N)$. Then $q_N \xRightarrow{\epsilon} q'$ for some $q' \in F_N$. Hence $q' \in q_{\mathcal{P}N}$, and so $q_{\mathcal{P}N}=\{ q'' : q_N \xRightarrow{\epsilon} q'' \} \in F_{\mathcal{P}N}$ i.e $\in L(\mathcal{P}N)$.

Now take any non-null $u = a_1...a_n$. Suppose $u \in L(N)$. Then there is a sequence of $N$-transitions $$q_{N} \xRightarrow{a_1} q_1 \xRightarrow{a_2}......\xRightarrow{a_n} q_n \in F_N \tag{1}$$Since $\mathcal{P}N$ is deterministic, feeding $a_1,...,a_n$ to it results in the following $\mathcal{P}N$ transitions: $$q_{\mathcal{P}N} \xrightarrow{a_1} S_1 \xrightarrow{a_2} ...... \xrightarrow{a_n} S_n \tag{2}$$where $$\begin{align}
S_1 &= \{q' : \exists q \in q_{\mathcal{P}N}.(q \xRightarrow{a_1}q' \text{ in } N)\} \\
S_2 &= \{q' : \exists q \in S_1.(q \xRightarrow{a_1}q' \text{ in } N)\} \\
\vdots &\quad \quad \vdots
\end{align}$$By definition of $\delta_{\mathcal{P}N}$, from (1), we have $q_1 \in S_1$, $q_2 \in S_2$, $...$, and so $q_n \in S_n$, and hence $S_n \in F_{\mathcal{P}N}$ because $q_N \in F_N$. Thus, (2) shows that $u \in L(\mathcal{P}N)$. Thus, we are done with the first half of the proof.

Now, onto proving that $L(\mathcal{P}N) \subseteq L(N)$:

Suppose $\epsilon \in L(\mathcal{P}N)$. Then $q_{\mathcal{P}N} \in F_{\mathcal{P}N}$, i.e. $F_N \cap \{ q : q_N \xRightarrow{\epsilon} q \neq \emptyset \}$, or equivalently, for some $q' \in F_n$, $q_N \xRightarrow{\epsilon} q'$. Hence $\epsilon \in L(N)$.

Now suppose for some non-null, $u = a_1...a_n \in L(\mathcal{P}N)$, i.e. there exists a sequence of $\mathcal{P}N$ transitions of the form $$q_{\mathcal{P}N} \xrightarrow{a_1} S_1 \xrightarrow{a_2} ...... \xrightarrow{a_n} S_n$$with $S_n \in F_{\mathcal{P}N}$, i.e. with $S_n$ containing some $q_n \in F_N$. Now since $q_n \in S_n$, by definition of $\delta_{\mathcal{P}N}$, there is some $q_{n-1} \in S_{n-1}$ with $q_{n-1} \xRightarrow{a_n} q_n$ in $N$.

Working backwards this way, we can build up a sequence of $N$-transitions of the form $$
q_N \xRightarrow{a_1} q_1 \xRightarrow{a_2} \cdots \xRightarrow{a_n} q_n \in F_n. \tag{□}
$$Hence, $u \in L(N)$. $QED$.

---

**Intuitive Understanding of the Proof:** $\epsilon$-transitions indicate to us which states we could be in. Our proof here, poorly conveying this idea, is indicating that each state in our DFA is a possible state that we could be in from the previous state. 

To create the transition function $\delta_{\mathcal{P}N}$ from the NFA, we have to talk about the $\epsilon$-closure of each state, and then construct each state as a subset of states we could be at, given the transitions that have just occurred. You can see how $\epsilon$-closure is important, since we aren't allowed those in DFA. We need to construct our subsets so that every $\epsilon$-transition is also taken care of.

Another intuitive point: **Either I did, or I did not make that $\epsilon$-transition. This means that there are $2^n$ different possible arrangements, hence the power-set construction.** This point is counter intuitive in trying to reason and build $\delta_{\mathcal{P}N}$, but it does attest to the fact that a DFA would be exponentially larger than an NFA.

And every state that includes accepting states in its set is also an accepting state. So assume that $q_i$ is the only accepting state of a given NFA. Now, every time we see a subset $S \subseteq Q$, if $q_i \in S$, then $S$ is an accepting state.