
We will begin with our problem. Given an array of integers `a` of size `N`, and for `p` and `q` such that $0\leq p\leq q\leq N$, we'll define the segment `a[p..q)` to be the entries `a(i)` for $p \leq i < q$. 

We will define the segment sum $segsum(a,p,q)$ to be the sum of the entries in that segment, i.e. $\sum_{i=p}^{q-1}a(i).$ This sum can be calculated by the following function

```scala
// Pre: 0 <= p <= q <= a.size
// Post: returns sum a[p..q).
def segsum(a: Array[Int], p: Int, q: Int) : Int = {
	var sum = 0; var i = p
	while (i < q){ sum += a(i); i += 1 }
	sum
}
```

Note that this takes $O(q-p)$ operations, which is $O(N)$ in the worst case. Note also that $segsum(a,p,p) = 0$.

We are interested in finding the maximum segment sum in an array `a`, i.e. $$max\{segsum(a,p,q) \;|\;0\leq p\leq q \leq N\}$$That is the maximum segment sum for $(p,q)$ in the region $A$ in the figure below. If all the entries of `a` are positive this is essentially the sum of all the elements of the list. Correspondingly, if all the elements in the list are negative, then the maximum segment sum is just the empty segment.

![[Pasted image 20240201153607.png]]

We will se three different algorithms for this, which have complexities $O(N^3)$, $O(N^2)$, and $O(N)$ respectively.


###### The First Algorithm

The idea of the first algorithm is straightforward: we calculate the segment sum for all segments, and keep track of the maximum. We will use the invariant $$I \;\hat{=}\;mss=\max\{segsum(a,p,q)\;|\;0\leq p\leq q\leq n\} ∧ 0 \leq n \leq N$$`mss` is the maximum `segsum(a,p,q)` for `(p,q)` in the region A.

This gives the following code:

```scala
var mss = 0; var n = 0
// Invariant I: mss = max{segsum(a,p,q) | 0 <= p <= q <= n}
// && 0 <= n <= N
while(n<N){
	n = n+1
	// Consider segsum(a,p,n) for 0 <= p <= n
	... // see next slide
}
// mss = max{segsum(a,p,q) | 0 <= p <= q <= N}
```

We use an inner loop to consider the `segsum(a,p,n)` for all `p` with $0\leq p\leq n$. We will use a variable `m` to record those values of `p` we've considered so far; i.e. we will have considered all `p` with $0\leq p<m$. The invariant therefore records this. $$\begin{aligned}
\begin{split}
J \hat{=} \text{mss} = \max \Bigl( \{ & \text{segsum}(a, p, q) | 0 \leq p < q < n \} \cup \\
\{ & \text{segsum}(a, p, n) | 0 \leq p < m \} \Bigr) \wedge 0 \leq m \leq n + 1 \wedge n \leq N
\end{split}
\end{aligned}
$$Here is the following code from this:

```scala
var m = 0
// Invariant:
// J = mss = max( {segsum(a,p,q) | 0 <= p <= q < n} U
// {segsum(a,p,n) | 0 <= p < m} )
// && 0 <= m <= n+1 && n <= N
while(m<=n){
	mss = mss max segsum(a,m,n)
	m = m+1
}
// mss = max( {segsum(a,p,q) | 0 <= p <= q < n} U
// {segsum(a,p,n) | 0 <= p <= n} )
// = max{segsum(a,p,q) | 0 <= p <= q <= n}
```

Here, in the inner loop, we could have omitted the case `m=n` because `segsum(a,m,m)=0 ≤ mss`. The code might be clearer using a `for` loop:

```scala
var mss = 0
for (n <- 0 to N; m <- 0 to n) mss = mss max segsum(a,m,n)
```

The program runs in time $O(N^3)$: each call to `segsum` takes time $O(N)$, the inner loop calls `segsum` $O(N)$ times, and the inner loop runs $O(N^3)$ times.


###### Second Algorithm

The inner loop in the first algorithm calculated each `segsum(a,m,n)` for $0\leq m\leq n$ independently, in increasing order of `m`.

If instead we calculate these in decreasing order of `m`, we can exploit the fact that $$segsum(a,m,n)=a(m)+segsum(a,m+1,n)$$to calculate each segment sum from the previous using a single addition. We therefore strengthen the invariant for the inner loop with a conjunct `ss = segsum(a,m,n)`. $$\begin{aligned}
\begin{split}
J \hat{=} \text{mss} = \max \Bigl( \{ & \text{segsum}(a, p, q) | 0 \leq p < q < n \} \cup \\
\{ & \text{segsum}(a, p, n) | m \leq p < n \} \Bigr) \wedge 0 \leq m \leq n \leq N \wedge \text{ss} = \text{segsum}(a, m, n)
\end{split}
\end{aligned}
$$This gives the following code for the inner loop.

```scala
// mss = max{segsum(a,p,q) | 0 <= p <= q < n}
// Consider all segsum(a,p,n) for 0 <= p <= n
var m = n; var ss = 0 // mss = mss max ss -- no need
// Invariant: J where
// J = mss = max( {segsum(a,p,q) | 0 <= p <= q < n} U
// {segsum(a,p,n) | m <= p <= n} )
// && 0 <= m <= n <= N && ss = seqsum(a,m,n)
while(m>0){
	m = m-1
	ss = ss + a(m)
	mss = mss max ss
}
// mss = max( {segsum(a,p,q) | 0 <= p <= q < n} U
// {segsum(a,p,n) | 0 <= p <= n} )
// = max{segsum(a,p,q) | 0 <= p <= q <= n}
```

This algorithm uses $O(N^2)$ additions. Adding `ss` to the state and the invariant allowed us to avoid repeating additions, and so calculate $$\max\{\text{segsum}(a,p,n)\;|\;0\leq p\leq n\}$$in $O(n)$ steps. We can still do better.


###### The Third Algorithm

For the third algorithm, we store the maximum segment sum for all segments ending at the current position, `n`, in a variable `mrss` ("maximum right segment sum"). $$\begin{align*}
\text{mss} &= \max\{\text{segsum}(a, p, q) \ | \ 0 \leq p < q \leq n\} \wedge \\
\text{mrss} &= \max\{\text{segsum}(a, p, n) \ | \ 0 \leq p \leq n\} \wedge \\
& \quad 0 \leq n \leq N
\end{align*}
$$Suppose we know $$mrss = \max\{segsum(a,p,n-1)\;|\;0\leq p\leq n-1\}$$from the previous iteration of the main loop, and we want to calculate $\max\{segsum(a,p,n-1)\;|\;0\leq p\leq n-1\}$. There are two cases for the value of `p` that provides the maximum such segment sum.
- Taking $p = n$ might provide the maximum, namely `segsum(a,n,n)=0`.
- Taking $p\leq n - 1$ might provide the maximum: $$\begin{align*}
\text{mss} &= \max\{\text{segsum}(a, p, q) \ | \ 0 \leq p < q \leq n\} \wedge \\
\text{mrss} &= \max\{\text{segsum}(a, p, n) \ | \ 0 \leq p \leq n\} \wedge \\
& \quad 0 \leq n \leq N
\end{align*}
$$
So the maximum such segment sum is `(mrss + a(n-1)) max 0`.

We therefore use the invariant 