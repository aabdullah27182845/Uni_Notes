
In these notes, we'll see two rather different searching problems:
- To find the integer square root of a given positive integer `y`, i.e. to find a non-negative integer `a` such that `a^2 <= y < (a+1)^2`
- To search for a particular value `x` in a sorted array `a`, i.e. finding an index `i` such that `a(i)=x`, if such an `i` exists.

In each case, we will encounter two algorithms: one being a simple linear search, and the second algorithm being a divide-and-conquer algorithm which will solve the problem in logarithmic time.


###### Integer Square Root

Given an integer `y`, we want to find an integer `a` such that `a^2 <= y < (a+1)^2`. Clearly this only makes sense if `y` is greater or equal to zero; we will assume this as a precondition. One way to solve this problem would be by a linear search:

```scala
// Pre: y >= 0
// Post: returns a s.t. a^2 <= y < (a+1)^2
def linearSqrt(y: Int) : Int = {
require(y >= 0)
	// Invariant I: a^2 <= y
	var a = 0
	while((a+1)*(a+1) <= y) a = a+1
	// a^2 <= y < (a+1)^2
	a
}
```

The linear search program illustrates an important technique for coming up with invariants. Writing the postcondition as $$a^2 \leq y \;∧\; y <(a+1)^2$$we took the invariant to be the first of these conjuncts: $$I \;\hat{=}\;a^2\leq y$$We also took the guard to be the negation of the second conjunct, i.e. $$guard\;\hat{=}\;(a+1)(a+1)\leq y.$$When the loop terminates, we will have the invariant and not the guard, and this correctly implies the postcondition, meaning that we have found our correct answer.

Unfortunately, this algorithm takes $\Theta(\sqrt{n})$ steps. We can do better.

In the previous program, at each point of the desired result was somewhere in the infinite range from some integer to infinity. In our next program, we will set the desired result to be in a finite range from `a` to `b`. That is, we will use the invariant: $$a^2\leq y < b^2 \;∧\;0\leq a < b.$$At each step, we will roughly half the size of this range, by picking the midpoint of the range and subsequently searching either the first half or the second half to the range subject to conditions.

Here is the algorithm for the binary search algorithm: 

```scala
def binarySqrt(y : Int) : Int =
	require(y >= 0)
	// Deal with y=0 or 1
	if (y <= 1) return y
	// Invariant I: a^2 <= y < b^2 and 0 <= a < b
	var a = 0; var b = y
	while(a+1 < b){
	val m = (a+b)/2 // a < m < b
	if(m*m <= y) a = m else b = m
}
// a^2 <= y < (a+1)^2
```

This algorithm poses some questions, specifically about the limits we set in our conditional statement. We should also go through and rigorously check whether the postcondition is met.


###### Binary Search (In Array)

Suppose we have an array `a[0..N)` and we want to find whether a particular value `x` occurs in `a`, and if so the index at which it occurs. We can do so using a straightforward linear search, with invariant $$(\forall j\in[0..i)\cdot a(j)\neq x) \;∧\;0\leq i \leq N$$
```scala
var i = 0
// Invariant: for all j in [0..i), a(j) != x and 0 <= i <= N
while(i < N && a(i) != x) i = i+1
```

Note that this finds the first occurrence of `x`. Also note that this sets `i` to `N` if `x` does not appear in the array.

Now suppose we have an ordered array `a[0..N)`. Again we want to find whether a particular value `x` occurs in `a`, and if so the index at which it occurs.

In fact, the program will find an index `i` in `[0..N]` such that `a[0..i) < x <= a[i..N)`, which we can picture as follows:

![[Pasted image 20240131142029.png]]

Once we have found `i` such that `a[0..i) < x <= a[i..N)`, we can then use a piece of code such as 

```scala
if (i<N && a(i)==x) println("Found at position "+i)
else println("Not Found. Nearest index "+i)
```

Alternatively, suppose we want to insert the value `x` into the array, while keeping it sorted, we will insert it at position `i` (this is the basis for the Insertion Sort algorithm).


###### Testing

Many programs go wrong on boundary cases; the following test suite concentrates on such boundary cases. It also checks for correctness when the value searched for appears never, once, or more than once.

```scala
class BinarySearchTests extends AnyFunSuite{
	val a = Array(2,4,6,6,8,10); val b = Array(2,2,4,6,6,8,10,10)
	test("1. before first"){ assert(search(a,0) === 0) }
	test("2. matches singleton at start"){ assert(search(a,2) === 0) }
	test("3. matches repeated value at start"){ assert(search(b,2) === 0) }
	test("4. matches singleton in middle"){ assert(search(a,4) === 1) }
	test("5. matches repeated value in middle"){ assert(search(a,6) === 2) }
	test("6. missing item"){ assert(search(a,7) === 4) }
	test("7. matches singleton at end"){ assert(search(a,10) === 5) }
	test("8. matches repeated value at end"){ assert(search(b,10) === 6) }
	test("9. greater than last"){ assert(search(a,11) === 6) }
	test("10. empty array"){ assert(search(Array(), 5) === 0) }
}
```

We can partition the set of all searches over non-empty arrays into a finite number of equivalence classes, depending upon the number of times the searched value `x` occurs, and how `x` compares with the minimum and maximum elements of the arrays. Here is a table that shows which test considers which partition, and partitions that cannot occur marked with a dash.

| # | $x < \min$ | $x = \min$ | $\min <x<\max$ | $x=\max$ | $\max < x$ |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 0 | 1 | - | 6 | - | 9 |
| 1 | - | 2 | 4 | 7 | - |
| $> 1$ | - | 3 | 5 | 8 | - |
If the program works correctly for one test in a partition, it is likely to work for all inputs in that partition.

By comparison with the previous example, we might consider using an invariant such as `a(i) < x ≤ a(j)`. But how can we find `i` and `j` to establish this? We could invent fictitious entries, and carefully avoid accessing those entries. 

However, things will be a lot easier if we use the following invariant $$a[0..i)<x\leq a[j..N) ∧ 0 ≤ i ≤ j ≤ N$$We can picture this as follows:

![[Pasted image 20240131143336.png]]

In effect, this says that we have narrowed the search range down to `[i..j)`. At each iteration, we will pick a value `m` roughly in the middle, and then continue searching in either the range `[i..m)` or the range `[m+1..j)`.

```scala
// invariant I: a[0..i) < x <= a[j..N) && 0 <= i <= j <= N
var i = 0; var j = N
while(i < j){
	val m = (i+j)/2 // i <= m < j
	if(a(m) < x) i = m+1 else j = m
}
// I && i = j, so a[0..i) < x <= a[i..N)
```

