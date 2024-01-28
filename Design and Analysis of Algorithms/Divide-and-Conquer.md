
When faced with a new algorithmic problem, one should consider applying one of the following approaches: 
- [[Divide-and-Conquer]] :: divide the problem into two subproblems, solve each problem separately and merge the solutions.
- [[Dynamic programming]] :: express the solution of the original problem as a recursion on solutions of similar smaller problems. Then instead of solving only the original problem, solve all sub-problems that can occur when the recursion is unravelled, and combine their solutions.
- Greedy approach :: build the solution of an optimisation problem one piece at a time, optimising each piece separately.
- Inductive approach :: express the solution of the original problem based on the solution of the same problem with one fewer item; a special case of dynamic programming and similar to the greedy approach.

In these notes, we will only discuss Divide-and-conquer, which is our focus. The divide-and-conquer strategy solves a problem by:
- Breaking it into subproblems
- Recursively solving these subproblems and then combining their answers.

Usually step 2 is unnecessary for certain algorithms. The work of divide-and-conquer is usually done in three places, which is:
- Dividing the problem into subproblems.
- At the tail end of recursion, when the subproblems are so small they are solved outright.
- In the gluing together of the intermediate steps.

We will explore an example of such divide-and-conquer algorithm - Merge Sort

##### Merge Sort

Informal Description : It sorts a subarray `A[p..r) := A[p..r-1]`, with the use of Divide, Conquer, and Combine.

Divide: The algorithm splits our given subarray into two subarrays at the midpoint. If the array has 2 midpoints, the smaller one is chosen just as convention.

Conquer: The algorithm recursively sorts the split subarrays.

Combine: The algorithm does most of its work here, by merging the sorted subarrays into a single sorted array with the use of a procedure called `merge`.

Here is an example of merge sort in action:
![[Screenshot 2024-01-22 143612.png]]

Here is the pseudocode for Merge Sort

```
Merge-Sort(A,p,r)
Input : An integer array A with indices p < r
Output: The subarray A[p..r) is sorted in non-decreasing order.
1 if r > p + 1
2     q = (p + r) // 2
3     Merge-Sort(A,p,q)
4     Merge-Sort(A,q,r)
5     Merge(A,p,q,r)
```

And as for merge, here is the input and output for this procedure:
- Input : Array A with indices p, q, r such that
	- p < q < r
	- Subarrays `A[p..q)` and `A[q..r)` are both sorted.
- Output: The two sorted subarrays are merged into a single sorted subarray in `A[p..r)`

Now, the pseudocode for this procedure:

```
Merge(A, p, q, r)
1  n1 = q − p
2  n2 = r − q
3  Create array L of size n1 + 1
4  Create array R of size n2 + 1
5  for i = 1 to n1
6      L[i] = A[p + i − 1]
7  for j = 1 to n2
8      R[j] = A[q + j − 1]
9  L[n1 + 1] = ∞
10 R[n2 + 1] = ∞
11 i = 1
12 j = 1
13 for k = p to r − 1
14     if L[i] ≤ R[j]
15         A[k] = L[i]
16         i = i + 1
17     else A[k] = R[j]
18         j = j + 1
```

Naturally, the running time of this algorithm is linear time, since the first two `for` loops take `Θ(n1 + n2) = Θ(n)`. The last `for` loop makes `n` iterations, each taking constant time for `Θ(n)` time, therefore being a linear timed algorithm.

A remark: The test in line 14 ensures that this algorithm is left-biased and therefore stable for any erroneous inputs. If `A[i] = A[j]` and `A[i]` appears before `A[j]` in the input array, then in the output array the element pointing to `A[i]` appears to the left of the element pointing to `A[j]`.

The worst-case running time of merge sort is $Θ(n \log n)$, which is far better than the worst-case running time of insertion sort, which was $Θ(n^2)$. Merge sort is stable due to it being left biased.

##### Analysis of Runtime

We often use a recurrence to express the running time of a divide-and-conquer algorithm.

Let T(n) = running time on a problem of size n.

- If n is small, use constant-time brute force solution.
- Otherwise, we divide the problem into subproblems, each 1/b the size of the original.
- Let the time to divide a size-n problem be D(n).
- Let the time to combine solutions be C(n).

We, therefore get the following recurrence: $$
f(x) = \begin{cases}c,& \text{if } n\leq l\\ 
aT(\frac{n}{b}) + D(n) + C(n), & \text{if } n > l
\end{cases}
$$
Let's return to our merge sort algorithm. For simplicity's sake, assume $n = 2^k$. 

For $n=1$, the running time is a constant c.

For $n=2$, the time taken for each step is:
- Divide: Compute our midpoint - constant time
- Conquer: Recursively solve 2 subproblems, each of size n/2; so, $2T(n/2)$ 
- Combine: Merge two arrays of size n, so $C(n) = Θ(n)$. 

More precisely, the recurrence for merge sort is $$T(n) = \begin{cases}c,& \text{if } n=1\\ 
2T(n/2)+f(n), & \text{if } n > l
\end{cases}$$
Where $f(n)$ is a function which runs in linear time.

We can solve this recurrence with the use of guess and test. The expression can contain constants that will be determined later. We can use induction to find the constants and show that the solution works.

Guess: There is some constant $a > 0$ such that $T(n) \leq an\log n$ for all $n \geq 2$ that are powers of 2.

Test: For $n = 2^k$, by induction on $k$. Base case: k = 1 $$T(2) = 2c + 2d \leq a2\log2\;\;\;\;\text{if } a\geq c + d$$
Inductive step: assume $T(n) \leq an\log n$ for $n=2^k$. Then for $n'=2^{k+1}$, we have: $$
\begin{aligned}
    T(n') &\leq 2a\frac{n'}{2}\log{\frac{n'}{2}} + dn' \\
    &= an'\log{n'} - an\log{2} + dn \\
    &\leq an'\log{n'} \quad \text{if } a \geq d
\end{aligned}
$$In summary, choosing $a \geq c+d$ ensures $T(n)\leq an\log n$, and thus $T(n)=O(n\log n)$. A similar argument can be used to establish the asymptotic lower bound of this algorithm with the same function $n\log n$.

Guess and test is great, but unfortunately, it is somewhat slow to do. To get to the actual solution, we can use the recursion tree. The idea is well exemplified in the case of merge-sort. The recurrence is $$T(n) = \begin{cases}c,& \text{if } n=1\\ 
2T(n/2)+f(n), & \text{if } n > l
\end{cases}$$as stated earlier.

Assume $n=2^k$ for simplicity. First unfolding: cost of $f(n)$ plus cost of two subproblems of size $n/2$. The second unfolding causes each of the two $n/2$ subproblems to split in two. We can continue the unfolding until the problem size gets down to 1. Here is a diagram: ![[Pasted image 20240122154805.png]]
In total, there are $\log{n} + 1$ levels. And we can analyse that each level requires $C_0(n) = f(n)$ cost to execute. So in total, we will end up with a linear-logarithmic runtime.

The total cost of the algorithm is the sum of the costs of all levels: $$T(n)=\sum_{l=0}^{\log n - 1}C_l(n)+cn$$Using the relation $d′ n ≤ Cl(n) ≤ dn$ for $l<\log{n}$, we obtain the bounds $$d′ n \log n + c n ≤ T(n) ≤ d n \log n + c n$$Hence, $T(n) = Θ(n log n)$.


##### The Master Theorem

Suppose $$T(n) ≤ aT(\lceil dn/b\rceil) + O(n^d)$$for some constants $a>0$ and $b>1$ and $d \geq 0$. Then, $$
T(n) = \begin{cases}O(n^d),& \text{if } d > \log_ba\\ 
O(n^d\log_bn),& \text{if } d = \log_ba \\
O(n^{\log_ba}),& \text{if } d < \log_ba
\end{cases}$$So, for merge-sort, we get a linear logarithmic time since $a=b=2$ and $d=1$. The master theorem gives $T(n)=0(n\log n)$.

We can prove the master theorem, and we will do so with the use of a recursion tree argument. We first assume that n is a power of b. The size of the subproblems decreases by a factor of b at each recursion, and reaches the base case after $log_bn$ divisions.

Since the branching factor is a, the level k of the tree comprises of $a^k$ subproblems, each of size $n/b^k$.

The cost at level l is upper bounded by $c a^l × ( n/b^l )^d = c n^d × ( a/b^d )^l$, for a suitable constant $c > 0$.
Thus, the total cost is upper bounded by
$$T(n) ≤ c nd(1 + \frac{a}{b^d} + (\frac{a}{b^d})^2 + · · · +(\frac{a}{b^d})^{\log_b n}).$$
Now, there are three cases:
1. $a < b^d$, i.e. $d > \log_b a$: the geometric series sums up to a constant. Hence, $T(n) = O(n^d)$.
2. $a = b^d$, i.e. $d = \log_b a$: the geometric series sums up to $1 + \log_b n$.
Hence,$T(n) = O(n^d \log n)$.
3. $a > bd$, i.e. $d < \log_b a$: the geometric series sums up to $Θ((\frac{a}{b^d})^{\log_b n})$. Since $(\frac{a}{b^d})^{log_b n} = \frac{n^{log_ba}}{n^d}$, we have $T(n) \leq c n^d d > \log_b d > \log_b Θ(\frac{n^{log_ba}}{n^d}) = Θ(n^{log_ba})$. Hence, $T(n)=O(n^{log_ba})$.

And now we have proved our theorem. QED.

We may have proved this, but can be extend this to any arbitrary n? [Well, feel free to review lecture 3 in the recordings again and see how Elias showed us how to convert these non-integer form versions of recurrences into an integer form one and solve it that way]

Here, consider the recurrence $$T(n) = 2T(\sqrt{n})+\log n$$which at first sight, does not fit the form of the Master Theorem. Here's a trick however. By introducing the variable $k=\log n$, we get $$T(n)=T(2^k)=2T(2^{k/2})+k$$Substituting $S(k)=T(2^k)$ into the above equation, we get $$S(k)=2S(k/2)+k$$
By the Master Theorem, we have $S(k) = O(k\log k)$, and so $$T(n) = O(\log n \log\log n).$$

##### Examples of Divide-and-Conquer Algorithms

In the following, we will see divide-and-conquer algorithms for
- integer multiplication
- matrix multiplication
- search (sorted array)
- selection 
- Fast Fourier Transform

###### Integer multiplication

Here's an old observation of Carl Gauss: The product of complex numbers $$(a + bi)(c+di)=ac-bd+(bd+ad)i$$can be done with just three real-number multiplications $$ac,\;\;bd,\;\;(a+c)(c+d)$$because $bc + ad = (a + b)(c + d) − ac − bd$.

We can try exploiting this trick in order to reduce the number of multiplications we may need to do for integer multiplication.

The answer to that question is indeed yes. We can split each of n-bit numbers x and y into their left and right halves, which are each n/2 bits long:

![[Pasted image 20240122172958.png]]

Since $$\begin{aligned}
    xy &= (2^{n/2}\text{x}_L+\text{x}_R)(2^{n/2}\text{y}_L+\text{y}_R) \\
    &= 2^n\text{x}_L\text{y}_L + 2^{n/2}(\text{x}_L\text{y}_R+\text{x}_R\text{y}_L)+\text{x}_R\text{y}_R \\
\end{aligned}$$compute $xy$ by four (n/2)-bit multiplications, three additions and two multiplications by powers of 2. Unfortunately, if we use our Master Theorem, we are still left with an algorithm that runs in quadratic time.

Using Gauss' trick, three (n/2)-bit multiplications suffice: $$\text{x}_L\text{y}_L,\;\;\; \text{x}_R\text{y}_R,\;\;\;(\text{x}_L+\text{x}_R)(\text{y}_L+\text{y}_R)$$Reducing the number of multiplications from 4 to 3 may not look impressive, but this little saving occurs at every level of the recursion, so it adds up. Thanks to this, if we now use the Master Theorem to calculate the asymptotic runtime of this algorithm, we are left it $T(n) = O(n^{\log_2 3}) ≈ O(n^{1.59})$, a significant improvement. And our job is done. Further optimisations aren't in scope for this module.


###### Matrix Multiplication

Let $X$ be a $p \times q$ matrix and Y be a $q \times r$ matrix. The product $Z = X\cdot Y$ is a $p \times r$ matrix where $$Z_{ij} = \sum_{k=1}^qX_{ij}\cdot Y_{kj}$$Here's the standard algorithm: The above definition yields an algorithm requiring $p \times q \times r$ multiplications and $p\times (q-1)\times r$ additions. In case $p = q = r$, the total cost is $2n^3 -n^2 = O(n^3)$ operations. Can we do better though?

Here, we will learn about Strassen's divide-and-conquer method from 1969. View X and Y as each composed of four $n/2 \times n/2$ blocks. $$X=\left[\begin{array}{ll}A & B \\ C & D\end{array}\right] \quad Y=\left[\begin{array}{ll}E & F \\ G & H\end{array}\right]$$Then $XY$ can be expressed in terms of these blocks, which behave as singletons. $$X Y=\left[\begin{array}{ll}A & B \\ C & D\end{array}\right]\left[\begin{array}{ll}E & F \\ G & H\end{array}\right]=\left[\begin{array}{ll}A E+B G & A F+B H \\ C E+D G & C F+D H\end{array}\right]$$We use a divide-and-conquer strategy. To compute size-n product $XY$, recursively compute eight size-(n/2) products: $$AE,BG,AF, BH, CE, DG, CF, DH$$then do some $O(n^2)$-time additions. Our running time therefore is $T(n) = 8T(n/2) + O(n^2)$, which gives $T(n)=O(n^3)$ thanks to the Master Theorem. This is unimpressive. Just like how we did it with integer multiplication, we can do the same thing with matrix multiplication: 

Size- $n X Y$ can be computed from just seven size- $(n / 2)$ subproblems.
$$
X Y=\left[\begin{array}{cc}
P_5+P_4-P_2+P_6 & P_1+P_2 \\
P_3+P_4 & P_1+P_5-P_3-P_7
\end{array}\right]
$$
where
$$
\begin{array}{ll}
P_1=A(F-H) & P_5=(A+D)(E+H) \\
P_2=(A+B) H & P_6=(B-D)(G+H) \\
P_3=(C+D) E & P_7=(A-C)(E+F) \\
P_4=D(G-E) &
\end{array}
$$

The new running time is $T(n)=7 T(n / 2)+O\left(n^2\right)$; hence
$$
T(n)=O\left(n^{\log _2 7}\right) \approx O\left(n^{2.81}\right) .
$$
And with that, we have come to an end for our optimisation. This is impressive enough, AI beat us though lmao, read up if you want.


###### The Search Problem

This problem will be laid out truly in an algorithmic fashion:
- Input :: A subarray `A[p..r)` of distinct integers sorted in increasing order, and an integer `z`
- Output :: True if `z` appears, and False otherwise.

Pseudocode: 

```
BinSearch(A,p,r,z)
	if p >= r
		return False
	else if q = (p+r) // 2
		if z = A[q]
			return True
		else if z < A[q]
			BinSearch(A,p,q,z)
	else BinSearch(A,q+1,r,z)
```

We can perform the Master Theorem and find out that the asymptotic runtime of this algorithm is $T(n) = O(\log n)$. And this is more than good enough for us.


###### Selection

We mention the ith-order statistic of a set of n (distinct) elements, which is the i-th smallest element. The median is the $\lfloor (n+1)/2 \rfloor$-order statistic.

The selection problem therefore:
- Input :: A set of n (distinct) numbers and a number i, with $1 \leq i \leq n$
- Output :: The i-th order statistic of the set.

The upper bound of this problem is $O(n\log n)$:
- Sort the numbers using merge sort
- Traverse the array in linear time.

Question is, do we really need to sort the list? Is there a faster method available to us?

In fact, there is. We can use the divide-and-conquer approach to find the i-th smallest element in O(n) time, even in the worst case. The algorithm `select` is based on two ideas:
- Idea 1 :: pick an element of the array, say `A[q]`, and call this the pivot. Partition the array into three subarrays, one containing the elements smaller than `A[q]`, one containing `A[q]`, and one containing elements larger than `A[q]`. Reduce the search for the i-th element to one of the subarrays.
- Idea 2 :: Choose the element `A[q]` in such a way that the subarray of `A[q]` are the subarray of elements smaller than `A[q]` are of comparable size. To do so, divide the array A into small groups, find the median of each group and compute the median of the medians. Choose `A[q]` to be the median of medians.

Here's our input and output to this algorithm,
- An input subarray, containing distinct numbers, and an array element `A[q]` (the pivot)
- An output subarray and an array index `q'` such that:
	- The output subarray consists of the same set of numbers as the input.
	- All but the last element of the output subarray are less than the pivot
	- The last element is the pivot.
	- The other output subarray consists of numbers bigger than the pivot.

It is easy to see that the partition task can be implemented in `O(n)` time, with `n = r - p + 1`. One has only to go through the elements of A, and to copy the elements into one of two arrays, B and C, depending on whether they are bigger or smaller than the pivot. Then, the two arrays can be used to build an array A' with the desired properties.

Here is the complete algorithm:
- Input: An array A of n distinct numbers.
- Output: The i-th smallest element.

1. Divide the n inputs into `n // 5` groups of 5 elements each, and at most one group of the remaining `n % 5` elements.
2. Find the median of each of the `n // 5 + 1` groups.
3. Use select to find the median-of-medians, call it x.
4. Use x as pivot, to partition the input array into three subarrays.
5. Compute the number of elements in the lower subarray, and denote it k.
6. Three cases:
	a) If i = k + 1, return x
	b) If i < k + 1, call select to find the i-th element of the lower subarray.
	c) If i > k + 1, call select to find (i-k-1)-th element of the upper subarray.

We can analyse the running time of this algorithm too. Let T(n) define it as such. By definition, $T(n) = \sum_jT_j(n),$ where $T_j(n)$ is the cost of implementing line j of the program.

1. Line 1 (dividing the input array) costs O(n) time
2. Line 2 (computing n/5 “baby medians”) costs O(n)
3. Line 3 (finding the median of medians) costs T(n/5)
4. Line 4 (partitioning) costs O(n)
5. Line 5 (computing size of subarrays) costs O(1)
6. Line 6 (selecting within a subarray) costs at most T(|Smax|), where |Smax| is the size of the largest subarray.

Assuming that T(n) is non-decreasing, we have the recurrence $$T(n) \leq T(n/5) + T(|S_{max}|)+O(n)$$By definition, at least half of the n/5 groups have "baby medians". Each of these groups has at least 3 elements > x, except for the group containing x and, possibly, for the group with fewer than 5 elements. 

Thus, the number of elements > x is at least $$3(\lceil\frac{1}{2}\lceil\frac{n}{5}\rceil\rceil - 2) \geq \frac{3n}{10}-6$$Hence, the size of the lower subarray is upper bounded by $7n/10 + 6$.

We need an average one. A similar argument applies to the upper subarray. 
- At least half of the n/5 groups have 3 elements < x, except for the group containing x and, possibly for the group with fewer than 5 elements.
- The size of the upper subarray is upper bounded by $7n/10 + 6$ as stated before. 

We can try to use guess and check in order to find out the complete asymptotic runtime of our algorithm. Assuming that T(n) is non-decreasing, we have the recurrence $$T(n) \leq T(n /5) + T(7n/10 + 6) + bn$$for some constant b > 0. We can guess. There is some c > 0 such that $T(n) \leq c\cdot n\;\;\forall\; n > 0$. Substituting the guess into the recurrence, we get: $$\begin{aligned} T(n) & \leq c\lceil n / 5\rceil+c\lfloor 7 n / 10+6\rfloor+b n \\ & \leq c n / 5+c+7 c n / 10+6 c+b n \\ & =9 c n / 10+7 c+b n \\ & =c n+(-c n / 10+7 c+b n)\end{aligned}$$which is at most $c\cdot n$ provided that $-cn/10 + 7c + bn \leq 0$ or equivalently, $$c \geq 10bn/(n-70)$$now, if $n \geq 140$, we have $n/(n-70) \leq 2$. Hence the equality is satisfied if $n ≥ 140$ and $c ≥ 20b$.

Onto a Lemma: There is some c > 0 such that $T(n) \leq c\cdot n \;\;\forall\;n>0$. Here's the proof:

Let $a = \max{\{T(n)/n,n \leq 140\}}$
Define $c = max{a,20b}$

Base case, For every $n \leq 140$, $T(n) \leq c\cdot n$ by construction. 

Inductive case: Suppose that the condition $T(n) ≤ c n$ holds for all n up to $n_0 \geq 140$. Then, for $n=n_0 +1$ we have $$\begin{aligned} T(n) & \leq T(\lceil n / 5\rceil)+T(\lfloor 7 n / 10+6\rfloor)+b n \\ & \leq c\lceil n / 5\rceil+c\lfloor 7 n / 10+6)\rfloor+b n \\ & \leq c n,\end{aligned}$$by construction.

So, in conclusion, select finds the i-th smallest element in O(n) time, and our best sorting algorithm by far is merge sort, which takes linear-logarithmic time.

It seems that finding the i-th smallest element of an array is much easier than sorting the whole array. Is this true?
- Yes, if the sorting algorithm is based on comparisons between elements of the array.
- No, if we know that the entries of the input array are contained in an interval of size $k = O(n)$, then in that case, there exists a sorting algorithm that runs in $O(n)$ time.

Here's a theorem: The running time of every comparison based sorting algorithm is $\Omega(n\log n)$. Here is the proof: Consider the decision tree of a comparison-based algorithm on input sequence $a_1,a_2,a_3,...$ The depth of the tree is the worst case time complexity of the algorithm.

Our aim is then to obtain a lower bound on the depth of a decision tree. The decision tree has n! leaves.
- By construction, every leaf if labelled by a permutation of our sequence.
- Every permutation must appear as the label of a leaf. Why? Because every permutation could be a valid output.

Fact: Every binary tree of depth d has at most $2^d$ leaves. Thus the depth of a decision tree - and the worst case complexity of the algorithm is at least $\log(n!)$, and we can prove this to be asymptotically similar to $n\log n$.

We can however try to sort without comparing. This is done by assuming that each of the n input elements is an integer in the range 0 to k.
- Counting sort determines for each input element x the number of elements less than x.
- If m elements are less than x, then x belongs to (m+1)-th position.
- This scheme ahs to be modified slightly to handle multiple elements with the same value.

Here is a pseudocode of this Counting sort algorithm:
- Input: An array `A[1..n]` of elements with keys $a_i \in \{0,...,k\}$.
- An array B consisting of a sorted permutation of A.

```
Create array C of size k+1
for i = 0 to k
	C[i] = 0
for j = 1 to n
	C[A[j]] = C[A[j]] + 1
for i = 1 to k
	C[i] = C[i] + C[i-1]
for j = n downto 1
	B[C[A[j]]] = A[j]
	C[A[j]] = C[A[j]] - 1
```

Here is an example of this program running:

![[Pasted image 20240122192654.png]]

The first and third `for` loops take linear time based on the number of keys defined in our input. The second and fourth `for` loop take a linear runtime. The last for loop also runs in linear runtime, concluding that this algorithm does indeed run in linear runtime.


###### The Fast Fourier Transform

FFT is a fundamental algorithm that is based on a divide-and-conquer approach and can be used to multiply polynomials in linear-logarithmic time. It can be adapted to multiply integers in $O(n\log n\log\log n)$ steps.

Given two polynomials of degree n-1 $$\begin{aligned}A(x) &= a_0 + a_1x + ... + a_{n-1}x^{n-1} \\ B(x) &= b_0 + b_1x + ... + b_{n-1}x^{n-1}\end{aligned}$$compute their product (of degree 2n-2) $$A(x) · B(x) = c_0 + c_1x + · · · c_{2n−2}x^{2n−2},$$where $$c_k = \sum_{i=0}^{k}a_ib_{k-i}$$The question therefore is, can you reduce integer multiplication to polynomial multiplication?

We can represent a polynomial $A(x)$ in two ways
- the coefficient form $A(x) = a_0 + a_1x + · · · + a_{n−1}x^{n−1}$
- or the point-value form $(A(v_0),...,A(v_{n-1}))$, for some fixed values $v_0,...,v_{n-1}$

The first form is good for evaluation a polynomial at a given point: Horner's rule takes O(n) arithmetic operations. $$A(x_0) = a_0 + x_0(a_1 + x_0(a_2 + · · · + x_0(a_{n−2} + x_0a_{n−1}) · · · ))$$takes $O(n)$ arithmetic operations. Here is how we can convert the representations:
- Evaluation: coefficient form => point-value form with the use of Horner's rule.
- Interpolation: point-value form -> coefficient form by solving the following linear system: $$\left(\begin{array}{ccccc}1 & x_0 & x_0^2 & \ldots & x_0^{n-1} \\ 1 & x_1 & x_1^2 & \ldots & x_1^{n-1} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ 1 & x_{n-1} & x_{n-1}^2 & \ldots & x_{n-1}^{n-1}\end{array}\right)\left(\begin{array}{c}a_0 \\ a_1 \\ \vdots \\ a_{n-1}\end{array}\right)=\left(\begin{array}{c}y_0 \\ y_1 \\ \vdots \\ y_{n-1}\end{array}\right)$$This matrix above is also called a Vandermonde matrix. It has the determinant: $$\prod_{0\leq j<k<n}(x_k-x_j)$$It is non-singular when the $x_i$'s are distinct.

Thus, A(x) is uniquely determined by its point-value representation, and can be found by Gaussian Elimination using $O(n^3)$ arithmetic operations. We can directly compute $A(x)$ with $O(n^2)$ arithmetic operations using Lagrange's formula: $$
P(x) = \sum_{i=0}^{n} y_i \prod_{j=0, j\neq i}^{n} \frac{x - x_j}{x_i - x_j}
$$To multiply two polynomials together un coefficient form took us a quadratic number of arithmetic operations. We can improve this by a clever choice of evaluation points:

Let's focus on speeding up the evaluation of a polynomial: Given a polynomial: $$A(x) = a_0 + a_1x + ... + a_{n-1}x^{n-1}$$in coefficient form, select appropriate points $v_0,...,v_{n-1}$ to compute $A(v_0), . . . , A(v_{n−1})$ as fast as possible. 

Assume that n is even, or even better a power of 2, and observe that $$A(x)=B(x^2)+xC(x^2),$$where B and C have degree bound n/2.

We can save a lot, if we evaluate $A(x)$ at pairs of opposite points x and -x: $$\begin{aligned}A(x) &= B(x^2) + xC(x^2) \\ A(-x) &= B(x^2) - xC(x^2)\end{aligned}$$And so we can apply this recursively to $B(x^2)$ and $C(x^2)$. But wait! To apply the same trick to B and C, we need to select points to get both $B(x^2)$ and $C(x^2)$? Which seems impossible, unless we use complex numbers.

Here's the pseudocode for FFT:

```
fft(a_list)
	if n = 1
		return a
	u = fft(a[0,2,..,n-2])
	v = fft(a[1,3,...,n-1])

	 w_n = e^(2pi*i/n); x = 1
	 for k = 0 to n/2 - 1
	 y_k = u_k + x*v_k
	 y_{k+n/2} = u_k - x*v_k
	 x = x*w_n
  return y
```

This algorithm performs $O(n\log n)$ arithmetic operations.

What about the opposite process of getting the coefficients $a=(a_0,...,a_{n-1})$ of A from its values? It turns out that the same algorithm works. Why is that the case?

FFT computes $$y = \mathcal{V}(\omega)a,$$where
$$
\mathcal{V}(\omega)=\left(\begin{array}{ccccc}
1 & 1 & 1 & \ldots & 1 \\
1 & \omega & \omega^2 & \ldots & \omega^{n-1} \\
1 & \omega^2 & \omega^4 & \ldots & \omega^{2(n-1)} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & \omega^{n-1} & \omega^{2(n-1)} & \ldots & \omega^{(n-1)(n-1)}
\end{array}\right)
$$is the Vandermonde matrix of the n-th roots of unity. Interpolation asks to compute the inverse.

To conclude, in order To multiply two polynomials A(x) and B(x) in coefficient form:
- Use FFT to convert to point-value form (evaluation)
- Multiply in point-value form (linear number of operations)
- Use (inverse) FFT to convert back to coefficient form (interpolation)

And now, a Theorem: The product of two polynomials of degree-bound n can be computed with O(n log n) arithmetic operations, with both input and output representations in coefficient form. We can adapt the algorithm for multiplying integers. The Schönhage–Strassen algorithm, based on FFT, takes O(n log n log log n) time to multiply two integers of n bits.

