
##### Formal Definition
Regular Expressions are compact notations that describe the language completely of a given NFA or a DFA. Fix a $\Sigma$. We define simultaneously regular expressions $E$ and the language denoted by $E$, written $L(E)$, by induction over the following rules.
- The constants $\epsilon$ and $\emptyset$ are regular expression; language $L(\epsilon) = \{ \epsilon \}$ and $L(\emptyset) = \emptyset$.
- For $a \in \Sigma$, $a$ is a regular language; $L(a) = \{ a \}$.
- If $E$ and $F$ are regular expressions, then so too are $(E + F)$, $(E \cdot F)$ , and $(E^*)$; where
	- union is defined as $L(E)\cup L(F) = L((E+F))$.
	- concatenation defined as $L(E)\cdot L(F) = L((E\cdot F))$.
	- star is defined as $L((E)^*) = (L(E))^*$

Because some notations aren't complete conventions, here are some disambiguities for some notation:

-   `+` is sometimes written $\cup$ or $|$, and $(E\cdot F)$ is sometimes written $(EF)$.
- Parenthesis may sometimes be omitted, for instance, we assume:
	- The regular expression operators have the following order of precedence (in decreasing order): star, concatenation, and union.
	- E.g. `01*` means `0(1*)`, not `(01)*`. And `0 + (10)`.
	- Union and concatenation associate to the left, e.g. $E\cdot F\cdot G$ means $(E\cdot F) \cdot G$, but however, union and concatenation are inherently associative operators, so the order doesn't matter.

**Examples**:

- `01* + 1` is formally $((0\cdot (1^*)) + 1)$
- `(0+1)01*0` is formally $((((0+1)\cdot 0)\cdot (1^*)))$

**Other Examples:**0*10*

- `0*10*` denotes words that have exactly one 1
- `(0+1)*1(0+1)*` denotes words that have at least one 1.
- `0(0+1)*0 + 1(0+1)*1 + 0 + 1` denotes every string of 1s, every string of 0s, every string which ends and starts with a zero, and every string that ends and starts with a 1. You can say that since the two latter cases are stricter and are subsets of the former statements, you can say simply "every string that starts and ends with the same letter".

From here, we shall say $w$ **matches** $E$ whenever $w \in L(E)$.