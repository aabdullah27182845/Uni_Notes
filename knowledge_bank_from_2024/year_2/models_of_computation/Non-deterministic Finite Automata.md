
#### Motivation Behind NDFA:
There is a following attempt at a proof that will fail if we use regular DFAs. Here is the theorem and the attempt of a proof.

Theorem: Regular languages are closed under concatenation, i.e., if $A_1$ and $A_2$ are **regular**, then so is $A_1 \cdot A_2$. 

**Proof Attempt:**

Let $L(M_i) = A_i$. Our aim is to construct $M$ that accepts $w$ if and only if $w$ can be broken into $w_1$ and $w_2$ (so that $w = w_1w_2$) whereby $M_1$ accepts $w_1$ and $M_2$ accepts $w_2$. 

Unfortunately, $M$ does not know where specifically to break to. This motivates the introduction of non-deterministic finite automaton.

With the motivation completed, we can now move onto the formal definition of the NFA.

### Formal Definition

A Non-deterministic finite automaton is a 5-tuple $(Q, \Sigma, \delta, q_0, F)$ such that:
- $Q$ is the set of all of the states of the automaton. $Q \neq \emptyset$.
- $\Sigma$ is the set of all possible inputs of the automaton, specifically, the alphabet of the automaton. $\Sigma \neq \emptyset$
- $\delta$ is the transition function, defined as the following: $\delta : Q \times (\Sigma \cup \{\epsilon\}) \to \mathcal{P}(Q)$. A note for the distinction of this definition of $\delta$ will be made here.
- $q_0 \in Q$ is the start state.
- $F \subseteq Q$ is the set of accepting (or final) states.

Here, note that the distinction of the transition function $\delta$ is an important one. The transition function is a function that maps from the tuple of states and symbols, including the empty string (meaning that no action can cause a state change), to the power set of the set of states, meaning that the transition function doesn't map to a single state, but a set of multiple states. 

You can imagine this as the NFA model introducing entropy in the system, and making it non deterministic.


### Example:

![[Pasted image 20241016104139.png]]
This NFA is the same as the following DFA.

![[Pasted image 20241016104225.png]]

##### Denoting Transition Notation on an NFA

Fix an NFA $( N = (Q, \Sigma, \delta, q_0, F) )$.

Let $( q \xRightarrow{w} q' )$ be defined by:

- $( q \xRightarrow{\epsilon} q' )$ if $( q = q')$ or there is a sequence $( q \xrightarrow{\epsilon} \cdots \xrightarrow{\epsilon} q' )$ of one or more $( \epsilon)$-transitions in $N$ from $q \to q'$.

- For $w = a_1 \cdots a_{n+1}$ where each $( a_i \in \Sigma )$, $( q \xrightarrow{w} q' )$ if there are $( q_1, q'_1, \cdots, q_{n+1}, q'_{n+1} )$ (not necessarily all distinct) such that $$q \xrightarrow{\epsilon} q_1 \xrightarrow{a_1} q'_1 \xRightarrow{\epsilon} q_2 \xrightarrow{a_2} q'_2 \xRightarrow{\epsilon} \cdots \xRightarrow{\epsilon} q_n \xrightarrow{a_{n+1}} q'_{n+1} \xRightarrow{\epsilon} q'$$
Intuitively, this notation means the following:

*"There is a sequence of transitions from $q$ to $q'$ in $N$ in which the symbols in $w$ occur in the correct order, but with 0 or more $\epsilon$-transitions, before or after each one"*.

**Exercise:** Writing $w = a_1a_2...a_{n+1}$. show that $q \xRightarrow{w} q'$ is equivalent to: there exists $q_1, ..., q_n$ such that $q \xRightarrow{a_1} q_1 \xRightarrow{a_2} q_2 \cdots\cdots \xRightarrow{a_{n+1}} q'$. Essentially, the objective here is that we need to show that the whole transition of a given sequence of symbols can be written piece-wise, with their $\epsilon$-transitions inscribed.

**Solution**: 

*You can find a formal attempt to the solution to this exercise at the bottom of this file in the appendix.*

Here is my intuitive and not so formal albeit correct approach to answering the question. 

Assuming that $a_i$ is a single symbol, and using the definitions above, we can say that $q_i \xRightarrow{a_i} q_{i+1}$ is the same as: $$q_i \xRightarrow{\epsilon}\{q_i' : q_i \in \mathcal{E}(q_i)\}\xrightarrow{a_i} q_{i+1}' \xRightarrow{\epsilon}\{q_{i+1} : q_{i+1} \in \mathcal{E}(q_{i+1}')\}$$With a little abuse of the notation prescribed earlier in our definition, but retaining the idea nonetheless. The whole idea here now is to make sure that at least one of the states inside of the set has a viable deterministic mapping to $q_{i+1}'$ first, and then we need to prove that $q_{i+1} \in \{p_{i+1} : p_{i+1} \in \mathcal{E}(q_{i+1}')\}$. 

The first statement is assumed to be true by our earlier definition, so that is already done for us.

Proving the second statement requires some thought. An intermediary $\epsilon$ given state must link to the end state $q_{i+1}$ given our initial predicate that $q_i \xRightarrow{a_i} q_{i+1}$. Therefore, that is also  


*Intuitively, this really means that the whole sequence $q \xRightarrow{w} q'$ can be decomposed into double arrowed transitions from each state to another inside of the sequence of symbols inscribed in $w$.*
##### Language of an NFA

$L(N)$, the language recognised by $N$, consists of all strings $w$ over $\Sigma$ satisfying $q_0 \xRightarrow{w}q$, where $q$ is an accepting state.


#### Intuitive Example

![[Pasted image 20241016161416.png]]
This NFA need not be detailed, it's visual representation should be enough to convey that this NFA determines whether a number is either a multiple of 2 or a multiple of 3. You can't tell straight by looking at a number if it is a multiple of 2 or 3, hence the $\epsilon$-transitions. The double and the triple wheel is an indicator of either being a multiple of three of a multiple of 2.




#### Appendix


**Attempt at the Exercise**:

Before we begin with the solution, it's key to understand what the following means:
- **$\epsilon$-Closure of a state $q$, denoted $\mathcal{E}(q)$:** The set of states reachable from $q$ by any number of $\epsilon$ transitions (including zero transitions). Formally, it's the set of states $q'$ such that $q \xRightarrow{\epsilon}q'$.

This concept allows us to consider all the possible states that can be reached from a given state without consuming any input symbols. With this new tool, we are going to redefine the problem.

First expand $q \xRightarrow{w} q'$ as:
- There exists $q_0​,q_1​,q_1′​,q_2​,q_2′​,…,q_n​,q_n′​,q_{n+1}​$ such that:
- $q_0 =  q$
- For each $i$ from $1$ to $n+1$,
	- $q_{i-1} \xRightarrow{\epsilon} q_i$
	- $q_i \xrightarrow{a_i} q_i'$
	- $q_i' \xRightarrow{\epsilon} q_i$

Now, having a look at this, you can see that after each input symbols, we allow $\epsilon$-transitions.

Since $\epsilon$-transitions don't consume symbols, we can consider the set of all states reachable via $ϵ$-transitions from a given state. The idea is, since $q \xRightarrow{\epsilon} q_1$, $q_1' \xRightarrow{\epsilon} q_2$, we can "compress" the original sequence by considering direct transitions on input symbols.

Let $S_0 = \mathcal{E}(q)$ be the $\epsilon$-closure of the state $q$. From this point on, we need to prove the following two implications:
- (a) If $q \xRightarrow{w} q'$ based on the definition earlier, then there exists states $q_1, q_2, ..., q_n$ such that $q \xrightarrow{a_1} q_1 \xrightarrow{a_2} q_2 \xrightarrow{a_3} ... \xrightarrow{a_{n+1}} q'$. 
- (b) If there exists $q_1, q_2, ..., q_n$ such that $q \xrightarrow{a_1} q_1 \xrightarrow{a_2} q_2 \xrightarrow{a_3} ... \xrightarrow{a_{n+1}} q'$, then $q \xRightarrow{w} q'$ according to our original definition.

We can prove (a) by doing the following. Since $q\xRightarrow{w}q'$ means: