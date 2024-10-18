
Definition: You've seen this millions of times already, but here it is. A probability space is characterised by a 3-tuple $(\Omega, \mathcal{F}, \mathbb{P})$ where

- $\Omega$ is the sample space.
- $\mathcal{F}$ is the set of total events.
- $\mathbb{P}$ is a function that maps from $\mathcal{F}$ to $[0,1]$, where an event is assigned some probability, 0 being impossible and 1 being certain.

### Axioms

1. $\mathbb{P}(\Omega) = 1$  and $\Omega \in \mathcal{F}$
2. If $A \in \mathcal{F}$, then so too is $\Omega \setminus A$. Another extension of this idea is also that if $\{A_i, i \in \mathcal{I}\}$ where $\mathcal{I}$ is either a finite or countably infinite set, and $A_i \in \mathcal{F}$, then so too is $\bigcup_{i\in\mathcal{I}} A_i \in \mathcal{F}$ 
3. For events $\{A_i, i \in I ,\forall A_i \in \mathcal{F}\}$, if $\forall A_i, A_j,\quad A_i \cap A_j = \emptyset$, then $$\mathbb{P}(\bigcup_{i \in I}A_i) = \sum_{i\in I}\mathbb{P}(A_i)$$
There are definitely some other axioms omitted from this definition currently, but that's only because the theorems and examples that will be worked on later will not require the need for the other axioms.