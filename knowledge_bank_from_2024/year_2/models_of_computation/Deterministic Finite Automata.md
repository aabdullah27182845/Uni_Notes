
## Formal Definition: 

Deterministic Finite Automaton is a a finite state machine that accepts or rejects a given string of symbols.

A deterministic finite automaton $M$ is a 5-tuple, $(Q, \Sigma, \delta, q_0, F)$ consisting of
- a finite set of states $Q$
- a finite set of inputs symbols called the alphabet $\Sigma$
- a transition function $\delta : Q \times \Sigma \to Q$.
- an initial start state $q_0 \in Q$
- a set of accept states $F \subseteq Q$

Let $a_0a_1a_2\dots a_n$ be a string over the alphabet $\Sigma$. The automaton $M$ accepts the string $w$ if a sequence of states $r_0, r_1, \dots ,r_n$ exists in $Q$ with the following conditions:

1. $r_0 = q_0$
2. $r_{i+1} = \delta(r_i, a_{i+1})$ for $i = 0, ..., n-1$ 
3. $r_n \in F$

In words, the first condition says that the sequence of states begins properly with the starting state $q_0$. The second condition simply states that the sequence of states are related to each other according to the transition function $\delta$. Obviously, if our sequence is something infeasible by our automaton, it wouldn't be a string that would be accepted by our automaton.

The last condition simply states that the final state that our input string gives a state that belongs in the states that are accepting.


### Example

![[Pasted image 20241013211408.png]]

Suppose our $M$ is defined as such: $M = (Q, \Sigma, \delta, q_0, F)$ where

- $Q = \{S_0, S_1\}$
- $\Sigma = \{0,1\}$
- $q_0 = S_1$
- $F = S_1$

And $\delta$ is defined with the following relation:


|       | 0     | 1     |
| ----- | ----- | ----- |
| $S_1$ | $S_2$ | $S_1$ |
| $S_2$ | $S_1$ | $S_2$ |

The table is transmittable into a drawing, but simply put, this state only accepts strings which only have an even number of 0's. 


#### Note

Sets mentioned here have constraints on it, and here are the following:
- $Q$, the set of all possible states has to be a non-empty finite set, therefore $Q \neq \emptyset$.
- $\Sigma$, the alphabet of the automata also is non empty, therefore $\Sigma \neq \emptyset$.
- $\delta$ is a total function over $Q$ and $\Sigma$, therefore, $\delta \neq \emptyset$, or better yet, $\delta$ is not an empty relation.
- $q_0$, by proxy of the constraint on $Q$ cannot be empty or undefined too.
- $F$, the set of accepting states can in fact be empty. An automaton may exist that has no accepting states. $F \subseteq Q$ and $F$ can be $\emptyset$.

Also note that when designing DFAs, it's good practice to draw out their structure from the language provided of the DFA rather than trying to define the DFA from the definition.

