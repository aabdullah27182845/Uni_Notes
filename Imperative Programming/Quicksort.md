
Totally unbiased opinion, but this algorithm is definitely one of the "greatest hits". It has an average runtime of $O(n\log n)$, but quadratic in the worst case. It can be implemented to work "in-situ"

The key idea of the algorithm is to partition the input array around a pivot element. 
- Pick an element of the array (the pivot)
- Rearrange the array so that all elements left to the pivot are less than the pivot and all elements to the right are greater than the pivot.

The pivot is considered to be sorted since it's in its 'rightful position'.

Here is a high-level description of the algorithm:
- If $n \leq 1$ return
- $p = choosePivot(A,n)$ 
- Partition $A$ around $p$.
- Recursively sort the first part of the array.
- Recursively sort the second part of the array.

To simplify matters, we will pick the pivot to be in the first position every time we make a recursive call.

The recursive calls will correspond to sorting a segment of the array. Hence, we define the main `QSort` function as follows:

```scala
/** Sort the segment a[l..r), in situ
* post: a[0..l) = a0[0..l) ∧ a[r..N ) = a0[r..N ) ∧
* a[l..r) is a sorted permutation of a0[l..r) */
def QSort(l: Int, r: Int) : Unit = ...
```

and `a` will be a global variable.

Conventionally, in post-conditions, $a_0$ represents the value of $a$ at the start of the function call.


###### The Partition Function

The function `def partition(l: Int, r: Int) : Int = ...` that partitions the non-empty segment `a[l..r)`; i.e., if the value of `a(l)` is initially `x`, it will rearrange `a[l..r)` so that the pivot ends in some position `k` and all smaller elements are located to the left of `k` and all elements greater or equal than the pivot are located to the right of `k`. The rest of array remains untouched. We also return the index `k` where `x` ends up.

To sort the segment `a[l..r)`, we do this partition, then recursively sort the segments `a[l..k]` and `a[k+1..r]`.

```scala
/** Sort the segment a[l..r), in situ */
def QSort(l: Int, r: Int) : Unit = {
	if(r-l > 1){ // nothing to do if segment empty or singleton
		val k = partition(l,r)
		QSort(l,k); QSort(k+1,r)
	}
}
```

To partition the segment `a[l..r)`, we initially keep `x` in position `l`, and rearrange the other elements following this pattern $$a[l + 1..i) < x = a(l) ≤ a[j..r) ∧ l < i ≤ j ≤ r$$and in addition $$a[0..l) = a_0[0..l) ∧ a[r..N) = a_0[r..N) ∧ a[l..r) \;\;\;\text{is a permutation of}\;\;\;\; a_0[l..r)$$
Here is the partitioning algorithm:

```scala
/** Partition the segment a[l..r)
* @return k s.t. a[l..k) < a(k) <= a(k..r) and l <= k < r */
def partition(l: Int, r: Int) : Int = {
	val x = a(l) // pivot
	// Invariant a[l+1..i) < x = a(l) <= a[j..r) && l < i <= j <= r
	// && a[0..l) = a_0[0..l) && a[r..N) = a_0[r..N)
	// && a[l..r) is a permutation of a_0[l..r)
	var i = l+1; var j = r
	while(i < j){
		if(a(i) < x) i += 1
		else{ val t = a(i); a(i) = a(j-1); a(j-1) = t; j -= 1 }
	}
	// swap pivot into position
	a(l) = a(i-1); a(i-1) = x
	i-1 // position of the pivot
}
```
