
#### Definition
Epsilon in automata, $\epsilon$ refers to the empty string. Some automata accept or reject this specific string. 

#### Definition of $\epsilon$ transitions

An $\epsilon$-transition is a transition that exists between two states $q_i$ and $q_i'$ such that no symbols are consumed for this transition to occur.

An example of this may be that $q_i \xrightarrow{\epsilon} q_i'$ and $q_i' \in F$. Then any sequence that ends with $q_i$ is also a regular sequence.

#### Definition of $\epsilon$-closure (set of all possible states reachable with only epsilon transitions)

**Formal Definition**: $\epsilon$-closure of a state $q$, notated as $\mathcal{E}(q)$ from now on in these notes, is defined as the set of states that are reachable from $q$ by only using epsilon transitions.

An example of this with the following NFA:

![[Pasted image 20241018174527.png]]