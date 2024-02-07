
Dynamic programming is a general powerful optimisation technique. The term "dynamic programming" was coined by Bellman in the 1950s. At that time, "programming" meant "planning, optimising", so don't think about the word or the definition too much.

The paradigm of dynamic programming is as follows. We define a sequence of subproblems with the following properties:

- The subproblems are ordered from the smallest to the largest.
- The largest problem is the problem we want to solve.
- The optimal solution of a subproblem can be constructed from the optimal solutions of smaller subproblems. (This property is called Optimal Substructure)

We then solve the subproblems from the smallest to the largest. When a subproblem is solved, store its solution, so that it can be used to solve larger subproblems.

###### Example 1: The Change-Making Problem

Here is an example of a problem solvable using dynamic programming. The Change Making Problem:

- Input: Positive integers $1=x_1<x_"<\cdots<x_n$ and $v$.
- Task: Given an unlimited supply of coins of denominations $x_1,\cdots,x_n$, find the minimum number of coins needed to sum up to $v$.

Here is the key question of dynamic programming: What are the subproblems?

For $0 \leq u \leq v$, compute the minimum number of coins needed to make value $u$, denoted as `C[u]`.

For $u=v$, `C[u]` is the solution of the original problem.

Optimal Structure: for $u \geq 1$, one has $$\text{C[u]}=1+\min\{\text{C}[u-x_i] :1\leq i\leq n  \;∧\; u\geq x_i\}$$`C[u]` can be computed from the values of `C[u']` with `u' < u`.

Here is the pseudocode for the algorithm:

```
C[0] = 0
for u = 1 to v
	C[u] = 1 + min{ C[u − xi] : 1 ≤ i ≤ n ∧ u ≥ xi }
return C[v]
```

The array `C[1..v]` has length `v`, and each entry takes $O(n)$ time to compute. Hence running time is $O(nv)$.


###### Example 2: The Knapsack Problem

Suppose a burglar finds much more loot than he had expected:
- His knapsack holds a total weight of at most $W$ kg. $(W\in\mathbb{N})$.
- There are $n$ items to pick from, each with their own weight and value respectively.

The problem here is to find the most valuable combination of items he can fit into his knapsack, without exceeding its capacity.

Example: Take $W=10$ and 

| Item | Weight (kg) | Value ($) |
| ---- | ---- | ---- |
| 1 | 6 | 30 |
| 2 | 3 | 14 |
| 3 | 4 | 16 |
| 4 | 2 | 9 |

There are two versions of this problem. Either there are only one of each item available: (items 1 and 3), or there are unlimited quantities of each items.

We will begin with the easier of the two, where there are unlimited quantities of each.

So, we ask ourselves again, what are the subproblems? We can define $$\text{K[w]}=\text{maximum value achievable with a knapsack of capacity w}$$And our optimal structure is as follows: if the optimal solution to `K[w]` includes item `i`, them removing this item leaves an optimal solution to `K[w-w_i]`.

Since we don't know which item `i` is in the optimal solution, we must try all possibilities: $$
K[w] = \max\{K[w - w_j] + v_j : i \in \{1..n\}, w_i \leq w\}$$Hence, we get the following simple and elegant algorithm:

```
1 K[0] = 0
2 for w = 1 to W
3 K[w] = max{ K[w − wi] + vi : i ∈ {1 , . . . , n}, wi ≤ w }.
4 return K[W].
```

Line 3 calls for an algorithm for computing the maximum of n elements. It implicitly contains a `for` loop, with counter `i` going from 1 to `n`.

The algorithm fills a one-dimensional table of length `W+1`. Each entry of the table takes $O(n)$ to compute. Hence, the total running time is $O(nW)$.

Suppose that only one of each item is available now. 

Now the earlier idea does not work: knowing the value of `K[w-w_i]` does not help, because we don't know whether or not item $i$ has already been used. To refine our approach, we add a second parameter $0\leq j\leq n.$ Here are the subproblems: Compute $$K[w, j] = 
\begin{cases} 
\text{maximum value achievable with a knapsack of capacity } \\ w, \text{ choosing from items } 1, \ldots, j 
\end{cases}
$$Our sequence of subproblems has two indices, w and j. The last element of the sequence is $K[W,n]$, the answer to the problem.

Our optimal substructure: If we remove item $j$ we should have an optimal solution for `j-1`. We only need to fund out whether item `j` is useful or not: $$K[w,j]=\max\{K[w-w_j,j-1] +v_j,K[w,j-1]\}$$Thus we have the following pseudocode

```
for j = 0 to n
	K[0, j] = 0
for w = 0 to W
	K[w, 0] = 0
for j = 1 to n
	for w = 1 to W
		if wj > w
			K[w, j] = K[w, j − 1]
		else K[w, j] = max{ K[w − wj, j − 1] + vj, K[w, j − 1] }
return K[W, n]
```

The algorithm fills out a two-dimensional table, with `W+1` rows and `n+1` columns. Each table entry takes constant time, so the running time is $O(nW)$.


###### Example 3: Longest increasing subsequence

Let $S = (a_1,\cdots, a_n)$ be a sequence of numbers. A subsequence of S is a sequence of the form $T=(a_{i_1},a_{i_2},\cdots,a_{i_k})$ where $1\leq i_1<i_2<\cdots<i_k<n$. An increasing subsequence is one in which $$a_{i_1}<a_{i_2}<\cdots<a_{i_k}.$$So, here is the problem:
- The input: A sequence of numbers $S = (a_1,...,a_n)$.
- The task: Find the longest increasing subsequence of S.

Here is an example: The longest increasing subsequences of $$(5, 2, 8, 6, 3, 6, 1, 9, 7)$$are $(2,3,6,9)$ and $(2,3,6,7)$.

So, let's ask ourselves again. What is the subproblem here? Essentially, for $1\leq j\leq n$, find the longest subsequence among the increasing subsequences ending at $j$.

Here is our optimal structure: Suppose that a longest increasing subsequence contains $a_i$, namely $T=(a_{i_1},...,a_i,...,a_j)$. Then, $(a_{i_1},...,a_i)$ must be longest subsequence among the increasing subsequences ending at `i`. Here, `i` could be any index such that $i<j$ and $a_i < a_j$.

We can compute the length with the following $$\text{L[j]}=\text{length of longest increasing subsequence ending at j.}$$To compute `L[j]`, we need the values of `L(i)` for $i < j$: $$L[j] = 1 +\max\{L(i):1\leq i<j,a_i<a_j\}$$So, this begs the question, how can we recover the longest subsequence itself?

While computing `L[j]`, write down the position `P[j]` of the penultimate entry of the longest increasing subsequence ending at `j`. The optimal subsequence can be reconstructed from these back-pointers.

Here is the example: $S = (5,2,8,6,3,6,1,9,7)$

![[Pasted image 20240205174503.png]]

And here is the pseudocode for the following algorithm:

```
L[1] = 1; P[1] = NIL
k = 1                           // longest incr. subseq. found so far ends at k
for j = 2 to n                  // determines where longest incr. subseq. ends
	L[j] = 1; P[j] = NIL
	for i = 1 to j − 1          // finds longest incr. subseq. ending at j
		if A[i] < A[j] ∧ L[i] ≥ L[j]
			L[j] = 1 + L[i]; P[j] = i
		if L[j] > L[k]
			k = j
Create new array B of length L[k]
for j = L[k] downto 1          // writes the longest incr. subseq. into B
	B[j] = A[k]; k = P[k]
```

This function runs in $O(n^2)$ due to the nested `for` loops in lines 3-9.


###### [[Dynamic Programming]] vs [[Divide-and-Conquer]]

DP is an optimisation technique and is applicable only to problems with optimal substructure. D&C is not normally used to solve optimisation problems. Both DP and D&C split the problem into parts, and then combine them into a solution of the larger problem.

- In D&C, the subproblems are significantly smaller than the original problem, and they do not overlap.
- In DP, the subproblems are not significantly smaller and usually overlap.

In D&C, the dependency of the subproblems can be represented by a tree. In DP, it can be represented by a directed path from the smallest to the largest problem (more precisely, by a directed acyclic graph).


###### Example 4: The Edit Distance

Here is the idea: Find how many edits are needed to transform one word into another. An edit is an insertion, or deletion, or character substitution.

Here is an example: Take SNOWY and SUNNY $$\text{SNOWY} \xrightarrow{\text{ins}} \text{SUNOWY} \xrightarrow{\text{sub}} \text{SUNNWY} \xrightarrow{\text{del}} \text{SUNNY}
$$The edit distance, therefore is 3, since its the minimum number of edits needed to transform one word into another.

An alignment of two words is an arrangement of their letters on two lines, with the letters of one word on one line and the letters of the other word on the other line, possibly containing blank letters.

Here are two examples:

![[Pasted image 20240205181206.png]]

We can make a key observation that every alignment identifies a sequence of edits that transforms one word into the other. The number of edits is equal to the number of mismatched columns. Hence, **edit distance = minimum number of mismatched columns, with the minimisation running over all possible alignments**.

So, writing the problem down formally:

- Input : Two strings (i.e. character arrays) `x[1..m]` and `y[1..n]` 
- Task : Compute the edit distance between them.

So, what is our subproblem here then? Suppose, for $1\leq i\leq m$ and $1 \leq j \leq n,$ find the edit distance between the prefixes `x[1..i]` and `y[1..j]`, denoted as `E[i,j]`. Our task is to compute `E[m,n]`.

So therefore, `E[i,j]` is the edit distance between the prefixes `x[1..i]` and `y[1..j]`. Let's express `E[i,j]` in terms of appropriate subproblems. The respective rightmost columns in an alignment of `x[1..i]` and `y[1..i]` must be one of three cases:

![[Pasted image 20240206130309.png]]

- Case 1: Cost = 1, and it remains to align `x[1..i-1]` with `y[1..j]`.
- Case 2: Cost = 1, and it remains to align `x[1..i]` with `y[1..j-1]`.
- Case 3: Cost = 1, if (`x[i] != y[j]`) and 0 otherwise, and it remains to align `x[1..i-1]` with `y[1..j-1]`.

Writing $\delta(i,j):= 1$ if `x[i] != y[i]` and 0 otherwise, we have: $$E[i,j]=\min\{\,E[i-1,j]+1,E[i,j-1]+1,E[i-1,j-1]+\delta(i,j)\,\}$$The answers to all the subproblems `E[i,j]` with $0\leq i\leq m$ and $0\leq j\leq n$, form a two-dimensional array.

Computing `E[i,j]` for  $0\leq i\leq m$ and $0\leq j\leq n$,
- Initialisation: Set `E[0,0]` := 0, and for $1\leq i\leq m$, set `E[0,0]` := i, and for $1\leq i\leq n$, set `E[0,j]` := j.
- For $1\leq i\leq m$ and $1\leq i\leq n$, we can fill in the array `E[i,j]` row by row, from top to bottom, moving from left to right across each row, using the relation $$E[i,j]=\min\{\,E[i-1,j]+1,E[i,j-1]+1,E[i-1,j-1]+\delta(i,j)\,\}$$The running time for this algorithm comes out to be pseudo-polynomial, with $O(mn)$ as the asymptotic upper bound.


###### Example 4: Longest Simple Path Problem

A path in a graph is said to be simple if it does not visit the same vertex twice. Our problem therefore is to find such a path in a graph.

Formally:
- Input: A weighted directed graph $G = (V,E)$ and two vertices $a,c \in V$.
- Task: Find `l[a,c]` which is defined to be the longest simple path from `a` to `c`.

Suppose we know that the longest simple path from a to c passes through b. Then, we may be tempted to consider `l[a,b]` and `l[b,c]` as subproblems.

Unfortunately, in general, `l[a,c]` does not equal `l[a,b]` + `l[b,c]` since these longest simple paths may intersect with each other and will no longer be a simple path from a to c.

Here is an example:

![[Pasted image 20240206132040.png]]

See how `l[a,c]` = 6, while `l[b,c]` = 3 and `l[a,b]` = 4. 

The longest Simple path problem does not have optimal substructure. This may unveil some insight on what can be optimised using dynamic programming. 

Here is my insight: A problem may be thought of a set. If we cannot break this problem into disjoint, whole subsets in any way possible, then we cannot use dynamic programming. This word of 'optimal substructure' refers to this.

Back to the problem: We can say that this solution is not compositional. The lesson of this example is to know that DP cannot be applied to every optimisation problem. For DP to be applicable, the problem must have optimal substructure.


###### Example 5: Travelling Salesman Problem

Starting from his hometown, a salesman will conduct a journey in which each target city is visited exactly once before he returns home. Given the pairwise distance between cities, what is the best order in which to visit them, so as to minimise overall distance travelled?

Model the problem as a complete (undirected) graph with vertex-set $\{1,\cdots, n\}$ and edge lengths given as a matrix $D=(d_{ij})$.

Our task therefore is to find a tour that starts and ends at 1, includes all other vertices exactly once, and has a minimum total length. Here is an example:

![[Pasted image 20240206133004.png]]

What is the optimal tour from A?

Here's a theorem: The Travelling Salesman Problem is NP-hard. Therefore, TSP is solvable in polynomial time if and only if $P = NP$, which is unlikely.

Here is the brute force method of finding the solution to TSP: Evaluate every possible route and return the best one. The cost of this however is $\Omega(n!)$ since there are $(n-1)!$ possible routes and computing the length of each route costs $\Omega(n)$. 

With the use of dynamic programming, we can get a much more efficient problem, but it still wouldn't be a polynomial time.

Here are the subproblems: For every subset $S \subseteq \{1,\cdots,n\}$ containing 1, and for every element $j \in S$, $j \neq 1$, find the shortest path that starts from 1, and ends in `j`, and passes only once through all the other nodes in $S$. Define `C[S,j]` to be the length of such path.

The subproblems form a sequence ordered by the size of S and by the value of j. While $|S|=n$, we have the shortest path from 1 to j passing through all the other nodes. After all the subproblems with $|S| = n$ are solved, the solution of the original problem is obtained by:
- adding the node 1 in the end of the shortest path from 1 to j, so that it becomes a cycle.
- finding the shortest cycle. Explicitly, the length of the shortest cycle is $$L_{\min}=\min\{C[\{1,\cdots,n\},j]\;+\;d_{j,1} : 1 < j \leq n\}.$$

Therefore, our optimal substructure is the following: for $1 < j\leq n$, $$C[S,j]=\min\{C[S \backslash\{j\},i] + d_{ij} : i \in S\backslash\{1,j\}\}$$Our running time therefore can be analysed.

Since there are $2^n$ subsets of of $[n]$, and for each subset, there are at most $O(n)$ values for j. Hence, the array $C[i,j]$ contains $O(n2^n)$ entries.

Computing one entry of the array $C[S,j]$ takes at most $O(n)$. Therefore, our total running time is $O(n^22^n)$, which is far better than our factorial time from the brute force solution.


###### Back to Example 3

We have seen a dynamic programming algorithm that finds a longest increasing subsequence of an array `A[1..n]` in time $O(n^2)$. Can we do better?

The answer to that question is yes. We will see an algorithm that runs in $O(n\log r)$ where r is the length of the longest increasing subsequence.

Here is the idea. For a fixed $j\in\{1,\cdots,n\}$, let:

- r be the length of the LIS.
- $K[1,...,r]$ be the array where `K[i]` is the position of the smallest last element of an increasing subsequence of an increasing subsequence of length `i` in the array.
- `P[j]` be the index of the penultimate element in an increasing subsequence ending at `j` and having maximal length.

Here is an example: Let the following be true, `A[1] = 4` , `A[2] = 8` , `A[3] = 2` , `A[4] = 1` , `A[5] = 9`. For `j=5` we have `r=3`, `P[5]=2`, and `K[1]=4`.

Here is a fact, for `j=n`, the length r, the value `K[r]`, and the array `P[1,...,n]` are enough to reconstruct the LIS: `K[r]` is the position of the last element of the LIS, `P[K[r]]` is the position of the second to last, `P[P[K[r]]]` is the position of the third to last, and so on.

Observe that, for every `j`, one has $A[K[1]]<A[K[2]]<\cdots<A[K[r]]$.

Here is the algorithm for computing the following:

- Start frofor j = 2 to n , do the following: Set P[j] = K[i − 1].m $j=1$, Set $r=1$, $K[1]=1$, $P[1]=\text{NIL}$, and $K[0] = \text{NIL}$.
1. if $A[j] > A[K[r]]$,
	update r to r + 1,
	set $K[r] = j$ and P[j] = K[r − 1]
2. else
	use binary search to find the smallest index $i ∈ \{1, . . . , r\}$ such
	that$ A[j] ≤ A[K[i]]$.
	Update $K[i]$ to j.'

The `for` loop runs in `O(n)` time, and each execution takes at most $O(\log r)$, the time of binary search. Therefore, the total time is $O(n\log r)$.

Here is the pseudocode for the following:

```
1 k = K[r]
2 for j = r downto 1
3 B[j] = A[k]; k = P[k]
```

and

```
1 r = 1; K[1] = 1; P[1] = NIL; K[0] = NIL
2 for j = 2 to n
3 i0 = 1; i1 = r + 1
4 while i0 < i1
5 im = b(i0 + i1)/2c
6 if A[j] ≤ A[K[im]]
7 i1 = im
8 else i0 = im + 1
9 if i0 > r
10 r = r + 1
11 K[i0] = j; P[j] = K[i0 − 1]
```
