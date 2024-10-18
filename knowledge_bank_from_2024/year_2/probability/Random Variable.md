
Definition: A random variable is a function defined over the sample space $\Omega$. Informally, its defined as the following: a random variable represents an observable in our experiment; something we can "measure".

So, mathematically, we can define a random value as a map $X : \Omega \to \mathbb{R}$. Refer to [[Probability Space]] for the definitions of these variables.

Formally, for a function $X$ from $\Omega$ to $\mathbb{R}$ to be a random variable, we require that the subset $$\{\omega \in \Omega : X(\omega) \leq x\}$$of $\Omega$ is an event in $\mathcal{F}$, for every $x \in \mathbb{R}$. This really just means that every able real value has some sort of bound applied to our measured variable.

An example of this idea is that rolling a dice will result in giving you values that are only either 1,2,3,4,5, and 6. Every random value can't assign values that surpass the reals.




