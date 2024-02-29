
So far, we have used implementations provided to us from the Scala API. Now we will see various implementations of the phone-book trait `Book`. It's not that we can provide a "better" implementation than the Scala collections, because we can't. This is only to demonstrate data refinement, and sometimes, we may have to provide our own implementation.

What does it mean for one of these implementations to be correct? Informally, the implementation should have externally visible behaviour that is consistent with the specification.

Let's recap back on the `Book` trait:

```scala
/** The state of a phone book, mapping names (Strings) to
* numbers (also Strings).
* state: book : String → String
* init: book = {} */
trait Book{

	/** Store number against name in the phone book.
	* post: book = book0 ⊕ {name → number} */
	def store(name: String, number: String) : Unit
	
	/** The number stored against name.
	* pre: name ∈ dom book
	* post: book = book0 ∧ returns book(name) */
	def recall(name: String) : String
	
	/** Does name appear in the book?
	* post: book = book0 ∧ returns name ∈ dom book */
	def isInBook(name: String) : Boolean
}
```

We'll consider an implementation of `Book` based on storing the names and numbers in corresponding positions of two arrays. For example the abstract mapping $$\text{book}=\{\text{"Gavin"}\to\text{"73841"}, \text{"Pete"}\to\text{"73853"}, \text{"Mike"}\to\text{"73854"}\}$$will be represented by two arrays.

The arrays will be of some finite size `MAX`, so this places a restriction on the number of entries we can store.

We also need a variable, `count` say, that records the number of entries, so we know to ignore the data in the arrays beyond this point. This will satisfy the property: $o\leq count \leq \text{MAX}$. Further, we do not want to record two different numbers against the same name. Hence, we ensure that the entries in `name[0..count)` are distinct. These two properties represent a datatype invariant.

********

So, here's the **definition**: A datatype invariant is a property that is true initially, and is maintained by each operation, and hence is true after each operation. 

********

Datatype invariants can be useful to help us come up with an efficient implementation. Datatype invariants should be stated explicitly and precisely.

The concrete state representing the mapping $$book=\{\text{names}(0)\to\text{numbers}(1),...\}$$or more concisely, $$book=\{ \text{names}(i)\to \text{numbers}(i) \;|\;i\in [0..count)\}$$The implementation will act in the same way that the specification acts on the abstract state book.

We call this relation between the concrete and abstract states the abstraction function. 

****
Definition: The abstract function is the function `abs` that takes the concrete state `c` and gives back the corresponding abstract `a`; we write $a=\text{abs}(c)$. 
****

The implementation of each function should change `c` so that $a=\text{abs}(c)$ changes in a way allowed by the abstract specification.

```scala
object ArraysBook extends Book{
	private val MAX = 1000 // max number of names we can store
	private val names = new Array[String](MAX)
	private val numbers = new Array[String](MAX)
	private var count = 0
	// Abs: book = {names(i) → numbers(i) | i ∈ [0..count)}
	// DTI: 0 ≤ count ≤ MAX ∧
	// ∀i, j ∈ [0..count)(i 6 = j ⇒ names(i) 6 = names(j))
	...
}
```

Note that all the object's variables are private. Stating the second clause of the DTI informally would have been fine. Note that `dom book = names[0..count)`.

We need to check that the abstraction function really does produce a result matching the type of the abstract state, i.e. a function of type $\text{String}\to\text{String}$. This follows from the second clause of the DTI, that the names in `names[0..count)` are distinct.

We also need to check that the initial concrete state $c_0$ agrees with the abstract initialisation condition, i.e. $\text{abs}(c_0)=\{\}$. This is immediate since we set `count = 0`.

All the operations will involve searching for a particular name. We want to avoid repeated code, so let's encapsulate that searching in a private function `find`:

```scala
// Return the index i<count s.t. names(i) = name; or
// return count if no such index exists
private def find(name: String) : Int = {
	// Invariant: name not in names[0..i) && i <= count
	var i = 0
	while(i < count && names(i) != name) i += 1
	i
}
```

The `isInBook` operation is then east.

```scala
/** Is name in book? */
def isInBook(name : String) : Boolean = find(name) < count
```

This should match our specification.

We also have the `recall` operation:

```scala
/** Return the number stored against name */
def recall(name: String) : String = {
	val i = find(name)
	assert(i < count)
	numbers(i)
}
```

As well as the `store` operation:

```scala
/** Add the maplet name -> number to the mapping */
def store(name: String, number: String): Unit = {
	val i = find(name)
	if(i == count){
		assert(count < MAX); names(i) = name; count += 1
	}
	numbers(i) = number
}
```

In general, to prove that a concrete implementation meets a specification over the abstract state space, we need to proceed as follows. Suppose that $c_0$ is an initial concrete state that satisfies the DTI, and suppose $a_0=\text{abs}(c_0)$ satisfies the abstract precondition. Then the concrete operation must terminate without error in a concrete $c$ and return a result $res$ such that:

- $c$ satisfies the DTI.
- the abstract postcondition is satisfied by $a_0$, $a=\text{abs}(c)$ and $\text{abs}(res)$.

If the abstract operation has pre- and postconditions:

- pre: $pre(a)$
- post: $returns \;res\; s.t \; post(a_o,a,res)$.

then the concrete operation should have pre- and post-conditions

- pre: $pre(abs(c)) ∧ \text{DTI}$
- post: $returns\; res\; s.t. \;post(abs(c0), abs(c), abs(res)) ∧ \text{DTI}$ 

Recall that the user of a function is responsible for ensuring that the pre-condition is satisfied. If the pre=condition is not satisfied, then the code may do anything including:

- giving a sensible answer,
- giving a non-sensible answer,
- throwing an exception
- failing to terminate.

Our implementation stored the names and numbers in the order in which they are added. If we were to have a `delete` operation, then we would need to worry about closing gaps.

However, if we store the entries in order of the names, we can implement `find` using a binary search.

The datatype invariant between the pair of arrays requires that when we alter `names` then we have to operate on `numbers` in the same way. How about an array of pairs? Can we do better?

For this question, we need to first consider the difference between abstract and concrete:

Abstract: Is a type of datatype that gives the generic description, and corresponds well with the Scala trait. Think of them as the abstract class type from Java, but with more flexibility. They contain both pre- and post-conditions.

Concrete: Concrete datatypes however give an implementation. They correspond well with the Scala class, and they obey preconditions and postconditions. They properly define the datatype invariant, essentially how they maintain consistency.

Abstract function shows the correspondence. A function from the concrete implementation to the abstract state is what we define this as such. 