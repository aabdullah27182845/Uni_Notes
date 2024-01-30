
A head is a data structure that organises data in an essentially complete rooted tree, i.e., a rooted tree that is completely filled on all levels except possibly on the lowest, which is filled from the left up to a point. 

Here's an example: Binary heap, storing numbers at the nodes of the tree:

![[Heap Example 1.png]]

The height of a tree is the longest simple path from the root to a leaf. A binary heap with n nodes has height $\log n$.

A heap can be implemented by an array without any explicit pointers. In particular, a binary heap can be implemented by an array A as follows:
- Root of the binary tree is $A[1]$
- Left child of $A[i]$ is $A[2i]$
- Right child of $A[i]$ is $A[2i+1]$
- Hence, for $i > 0$, the parent of node i is the node `Parent(i) = i // 2`

The head shown above is stored as the following in the array A: $$[6,4,22,8,11,9,3,32,7,1]$$A max-head is a head that satisfies the Max-Heap property: The key of a node (except the node) is less than or equal to the key of its parent.

In the array implementation, the Max-Heap property reads: $\forall\;1<i\leq A.$heap-size: $A[i]\leq A[i/2]$.
- The maximum element of a max-heap is at the root.
- One could also define min-heaps, where the key of each node except the root is larger or equal to the key of its parent.

Here is an example of a max-heap:

![[Max-Heap Example.png]]

Note that this array A is not sorted. A does satisfy the property of a max-heap, so it passes.

We can build a max heap too. Given an array A, there is a procedure to turn A into a max-heap: `Make-Max-Heap(A)` Takes an array A of n integers and rearranges it into a max-heap of size n.

In turn,`Make-Max-Heap(A)` is based on the following procedure `Max-Heapify(A, i)`, assuming that the left and right subtrees of node i are max-heaps, `Max-Heapify` transforms the subtree rooted at the node i to a max-heap.

Here is the idea: you compare the key at node i with the keys of its children, and rearrange them in order to satisfy the max-heap property. Here is an example:

![[Max-Heapify Example.png]]

Here is the pseudocode for the `Max-Heapify(A, i)` procedure:

```
Max-Heapify(A, i)
	Input: Assume left and right subtrees of i are max-heaps.
	Output: Subtree rooted at i is a max-heap.
1 n = A.heap-size
2 l = 2i                                // A[l] is the left-child of A[i]
3 r = 2i + 1                           // A[r] is the right-child of A[i]
4 if l ≤ n and A[l] > A[i]            // Lines 4-8: Determine
5      largest = l                   // largest among A[i], A[l] and A[r].
6 else largest = i
7 if r ≤ n and A[r] > A[largest]
8      largest = r
9 if largest 6 = i
10      exchange A[i] with A[largest]
11      Max-Heapify(A, largest)
```

Since `Max-Heapify` is a subtree of size n at node i, this means the following:
- $\Theta(1)$ to find the largest among `A[i]`, `A[2i]`, and `A[2i+1]`.
- The subtree rooted at the child of node i has size upper bounded by $2n/3$
- Thus $T(n) \leq T(2n/3)+\Theta(1)$, and so by the master theorem, we have $$T(n)=O(n^0\log n)=O(\log n)$$
We can also reason about this by knowing that the height of a heap is logarithmically aligned with the amount of items that exist in the heap, so if the heap has $n$ items, the height will only be a maximum of $\log n$.

Here is the complete idea for `Make-Max-Heap`: You start from the last non-leaf node, and apply `Max-Heapify` to the subtree based at that node. Repeat the same procedure for all the previous nodes. Here is an example:

![[Make-Max-Heap Example.png]]

And here is the pseudocode for the algorithm:

```
Make-Max-Heap(A)
	Input: An (unsorted) integer array A of length n.
	Output: A heap of size n.
1 A.heap-size = A.length
2 for i = ceil((n+1)/2) - 1 downto 1
3      Max-Heapify(A, i)
```


We can perform the same type of running time analysis. The simple and loose bound for this algorithm is $O(n\log n)$, since we have $O(n)$ calls to `Max-Heapify`, each taking $O(\log n)$ time. A tighter analysis lets us discover that the asymptotic runtime of this algorithm is actually $O(n)$.

`Max-Heapify` takes linear time in the height of the node it runs on, and "most nodes have small heights." The number of nodes of height h is upper bounded by $n/2^h$, and the cost of `Max-Heapify` on a node of height h is $\leq c\cdot h$, for some $c > 0$. Hence, the cost of the algorithm is $$T(n) \leq \sum_{h=0}^{⌊\lg n⌋}\frac{n}{2^h}c\cdot h\leq c\cdot n\left(\sum_{h=0}^{\infty}\frac{h}{2^h}\right) = 2cn,$$
******

Now, what are the applications of this data structure? Well, here are 2:
- Sorting: heapsort, an in-place sorting algorithm with worst case complexity $O(n\log n)$
- Efficient implementation of priority queues: 
	- A max heap is a max priority queue
	- A min heap is a min priority queue

Max-priority queues can be used to schedule jobs on a shared computer. And min-priority queues can be used to simulate events in time.

Actual implementations often have a handle in each heap element that allows access to an object in the application, and objects in the application often have a handle to access the heap element.

We will talk more about heap-sort now, a sorting algorithm based on the heap data structure. Here is the idea: 
- We begin by building our max-heap with our algorithm above. 
- Starting from the root, we place the maximum element into the correct place in the array by swapping it with the element in the last position in the array.
- We 'discard' the last node - decrement the heap size, and call `Max-Heapify` on the smaller structure with the possibly incorrectly-placed root.
- Repeat the earlier two steps until only one node (the minimum) is left, and is therefore in the correct place in the array.

This algorithm behaves like insertion sort, since it sorts in place, but its asymptotic complexity is $O(n\log n)$.

Here is a visual example to see how this works in action:

![[Heap sort visual example.png]]

And here is the algorithm written in pseudocode

```
Heapsort(A)
1 Make-Max-Heap(A)
2 for i = A.heap-size downto 2
3      exchange A[1] with A[i]
4      A.heap-size = A.heap-size − 1
5      Max-Heapify(A, 1)
```

The loop invariant here is the subarray `A[i+1..n]` to be sorted, and the remaining elements in `A[1..i]` are $\leq$ than the elements in `a[i+1..n]`.

And we can actually dissect the asymptotic complexity, since the `Make-Max-Heap` runs in $O(n)$, the `for` loop is executed $O(n)$ tomes. The exchange operation takes $O(1)$, and `Max-Heapify` takes $O(\log n)$.

Therefore, our total asymptotic runtime is $O(n\log n)$

---
#### [[Priority Queues]]

A priority queue is an abstract data structure for maintaining a set of elements, each with an associated value called a key. Max-priority queues give priority to the elements with larger keys, and min-priority queues give priority to the elements with smaller keys.

Here are the operations supported by a max-priority queue:

1. `Insert(S,x,k)` inserts element `x` with key `s` into set `S`.
2. `Maximum(S)` returns the element of S with the largest key.
3. `Extract-Max(S)` removes and returns the element of S with the largest key.
4. `Increase-Key(S,x,k)` increases value of `x`'s key to `k`. This requires `k` to be at least as large as `x`'s current key value.

Operations that are supported by a min-priority queue are `Insert(S,x)`, `Minimum(S)`, `Extract-Min(S)` and `Decrease-Key(S,x,k)`.

The implementation of a max-priority queue can be done with an unordered-sequence. Simply, store the elements `e` and their keys as pairs in an unordered sequence, implemented as an array of a doubly-linked list.
- Implement `Insert(S,x,k)` by inserting the pair `(e,k)` at the end of the sequence; takes $O(1)$ time.
- Implement `Extract-Max(S)` by inserting all elements of the sequence and removing the maximum; takes $\Theta(n)$ time.

See how laborious this would become if the priority queue was in the length of millions or billions? We can do far better with the use of a heap implementation!

A head offers a good compromise between insertion and extraction. Both operations take $O(\log n)$ (logarithmic) time. Finding the maximum however takes constant time since it's the first element of the heap.

And extracting the maximum can be done as following. First, check if the heap is non-empty, then make a copy of the maximum element. Make the last node in the tree the new root. `Heapify` the array, but less the last node. Then, return the copy of the maximum. Here is some pseudocode to aid with the explanation:

```
Heap-Extract-Max(A)
1 if A.heap-size < 1
2      error “heap underflow”
3 max = A[1]
4 A[1] = A[A.heap-size]
5 A.heap-size = A.heap-size − 1
6 Max-Heapify(A, 1)
7 return max
```

And to increase the key value, here is how it is done: Check that the key is greater than or equal to the current key value. Update the key value and traverse the tree upward comparing the element to its parent and swapping keys if necessary, until our element's key is smaller than its parent key. Here is some pseudocode to aid with the explanation yet again:

```
Heap-Increase-Key(A, i, key)
1 if key < A[i]
2      error “new key is smaller than current key”
3 A[i] = key
4 while i > 1 and A[Parent(i)] < A[i]
5      exchange A[i] with A[Parent(i)]
6      i = Parent(i)
```

And last, but certainly not least, we can insert a key into the heap as well, We insert a new node in the very last position in the tree with key $-\infty$. Then, we increase the $-\infty$ key to `k` using `Heap-Increase-Key`

```
Heap-Insert(A, key)
1 A.heap-size = A.heap-size + 1
2 A[A.heap-size] = −∞
3 Heap-Increase-Key(A, A.heap-size, key)
```

