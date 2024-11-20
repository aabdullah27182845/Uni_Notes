Parse trees are graphical representations of a derivation. Each derivation determines a parse tree.

Parse Trees are *ordered* trees, the children at each node are ordered

| Derivation                                                                                                 | Parse Tree                           |
| ---------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| $$\begin{array}{l} S \Rightarrow a S a \\ \Rightarrow a b S b a \\ \Rightarrow a b c b a \\ \end{array} $$ | ![[Pasted image 20241112143554.png]] |
The parse tree of a derivation abstracts away from the order in which variables are replaced in the sequence.


###### Example

Here is the derivation of a specific well bracketed string. Given we define our CFG as the following: $C = (\{S\}, \Sigma, \mathcal{R}, S)$ with $\mathcal{R} : S \to (S) \mid SS \mid \epsilon$ 

| Derivation                                                                                                                                                                                                                                                                                                                                              | Parse Tree                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| $$ \begin{array}{l} S \Rightarrow \frac{S \ S}{\phantom{S}} \\ \Rightarrow \frac{(S) \ S}{\phantom{S}} \\ \Rightarrow \frac{(S \ S) \ S}{\phantom{S}} \\ \Rightarrow^2 \frac{((S) \ (S)) \ S}{\phantom{S}} \\ \Rightarrow^3 (((\epsilon) ((S))) (S)) \\ \Rightarrow^2 (((\epsilon) ((\epsilon))) (\epsilon)) \\ = ((()) (())) () \end{array} $$<br><br> | ![[Pasted image 20241113140357.png]] |


###### Example

Here is the derivation of arithmetic expressions, with definitions for only addition and multiplication. Here, we define our CFG to be $C = (\{E,T,F\}, \{+,\times,( , ), x\}, \mathcal{R}, E)$ with the following six rules $$\begin{align}
E \to E + T \mid T  \\
T \to T \times F \mid F \\
F \to (E) \mid x 
\end{align}$$

| Derivation                                                                                                                                                                                                                                     | Parse Tree                           |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ |
| $$\begin{array}{l} E \Rightarrow E + T \\ \Rightarrow E + T \times F \\ \Rightarrow T + T \times F \\ \Rightarrow T + F \times F \\ \Rightarrow F + F \times F \\ \Rightarrow F + F \times x \\ \Rightarrow^* x + x \times x \end{array}$$<br> | ![[Pasted image 20241113141647.png]] |

---

## Ambiguity

Say that two expressions are *essentially different* if they determine different distinct parse trees. In some CFGs, the same string may have essentially different derivations.

We will now call CFGs with this property **ambiguous**.

Here is a simple example of an Ambiguous CFG. Take $C = (V, \Sigma, \mathcal{R}, S)$ with $\mathcal{R}$ having the following rules: $$E \to E + E \mid E \times E \mid x$$The string $x+x\times x$ has essentially two different derivations.

$$
\begin{array}{ll}
E \Rightarrow \underline{E \times E} & \quad E \Rightarrow \underline{E + E} \\
\Rightarrow \underline{E + E \times E} & \quad \Rightarrow x + E \\
\Rightarrow x + \underline{E \times E} & \quad \Rightarrow x + \underline{E \times E} \\
\Rightarrow x + x \times E & \quad \Rightarrow x + x \times E \\
\Rightarrow x + x \times x & \quad \Rightarrow x + x \times x \\
\end{array}
$$

| Derivation                                                                                                                                                                                                                                                                                                                                                                                 | Parse Tree                           |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------ |
| $$\begin{array}{l} E \Rightarrow \underline{E \times E} \\ \Rightarrow \underline{E + E \times E} \\ \Rightarrow x + \underline{E \times E} \\ \Rightarrow x + x \times E \\ \Rightarrow x + x \times x \\ \\ E \Rightarrow \underline{E + E} \\ \Rightarrow x + E \\ \Rightarrow x + \underline{E \times E} \\ \Rightarrow x + x \times E \\ \Rightarrow x + x \times x \end{array}$$<br> | ![[Pasted image 20241113144324.png]] |

---

## Leftmost Derivations

A ***leftmost derivation*** is one in which every step, the leftmost occurring variable is the one chosen for replacement.

Observe the example earlier shown in the ambiguity section of these notes, regarding the CFG with the following rules: $E \to E + E \mid E \times E \mid x$.

###### Formal Definition of Ambiguity

A CFG $G$ is ambiguous just in case there is some word in $L(G)$ which has two (or more) different leftmost derivations.

Note: each parse tree of a string identifies a leftmost derivation for it. There is a one-to-one correspondence between parse trees and leftmost derivations.

In general, the questions of whether a given CFG is ambiguous, or whether two CFGs are equivalent, are very difficult to answer. They fall into the category of *undecidable* decision problems.


#### Inherently Ambiguous Languages

Sometimes, the language generated by an ambiguous grammar has an equivalent unambiguous grammar. 

Some languages can only be generated by ambiguous grammars. They are called *inherently ambiguous*. An example of an inherently ambiguous grammar is the following: $$L = \{ 0^i 1^j 2^k : i = j \,∨\, j = k  \}$$

