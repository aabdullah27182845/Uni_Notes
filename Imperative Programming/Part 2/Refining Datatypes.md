
Our implementation stored the names and numbers in the order which they were added. However, if we store the entries in order of the names, we can implement `find` using a binary search. The datatype invariant between the pair of arrays requires that when we alter `names` then we have to operate on `numbers` the same way.

How about an array of pairs?

Scala supports pairs, in a similar way to Haskell. If `T1` and `T2` are types, then `(T1,T2)` represents the type of all pairs `(x,y)` for `x` in `T1` and `y` in `T2`.

The elements of a pair can be extracted using the operations `_1` and `_2`, e.g.:
``` val x = p._1; val y = p._2 ```

Alternatively, you can use pattern matching, but only with `val`:
` val (x,y) = p `.

Scala also supports tuples with more elements (up to 22). We can produce a new implementation of `Book` that uses an array of pairs, instead of a 'pair' of arrays. Here is an implementation using an array of pairs:

```scala
object PairArrayBook extends Book{
	private val MAX = 1000 // max number of names we can store
	private val entries = new Array[(String,String)](MAX)
	private var count = 0
	// Abs: book = {entries(i). 1 → entries(i). 2 | i ∈ [0..count)}
	// or Abs: book = {x → y | (x, y) ∈ entries[0..count)}
	// DTI: 0 ≤ count ≤ MAX ∧
	// ∀i, j ∈ [0..count)(i 6 = j ⇒ entries(i). 1 6 = entries(j). 1)
	
	/** Return the index i<count s.t. entries(i)._1 = name;
	* or return count if no such index exists */
	private def find(name: String) : Int = {
	// Invariant: name not in { entries(j)._1 | j <- [0..i) }
	// and 0 <= i <= count
	var i = 0
	while(i < count && entries(i)._1 != name) i += 1
	i
}
```

And here is the implementation of the operations

```scala
/** Return the number stored against name */
def recall(name: String) : String = {
	val i = find(name)
	assert(i < count)
	entries(i)._2
}

/** Is name in the book? */
def isInBook(name: String) : Boolean = find(name) < count

/** Add the maplet name -> number to the mapping */
def store(name: String, number: String): Unit = {
	val i = find(name)
	if(i == count){
	assert(count < MAX); count += 1
	}
	entries(i) = (name, number)
}
```

The previous code was not as clear as it might have been: we have to remember that the first entry of each pair is the name, and the second entry is the number. It would be nicer if we could write, for example, `entries(i).name` or `entries(i).number`. We could define a class 

```scala
class Entry {
	var name : String
	var number : String
}
```

and store an array of `entries` of `Entry`'s. To store a new name and number, we would do something like 

```scala
entries(i) = new Entry
entries(i).name = name; entries(i).number = number
```

That, however, is a bit long-winded.

Scala allows classes to have class parameters, so in this case, we could use 
	`Class Entry(name: String, number: String)`

In this case, the class has an empty body, so we miss off the curly brackets. We can provide values for these parameters when we create an object:

	entries(i) = new Entry(name, number)

In fact, the fields of each `Entry` can only be set when the object is created, and can't be changed subsequently; the fields are effectively immutable. 

It is often more convenient to deal with immutable objects. They can safely be shared between different datatypes. If a mutable object is shared, then an update in one place can affect another place. Thus immutable objects are easier to reason about.

If we do want to be able to update the fields we can mark them as `var`'s:

	class Entry(var name: String, var number: String)

We can define `Entry` as a case class as follows:

	case class Entry(name: String, number: String)

This offers various advantages:

- We can omit the word `new` when constructing new objects, e.g. `entries(i)=Entry(name, number)`.
- The compiler provides "natural" definitions of operators $==$, `toString` and `hashCode`
- We can perform pattern matching such as `val Entry(theName, theNumber) = entries(i)` which binds `theName` and `theNumber` to the appropriate fields.

The cost is that the classes and objects are slightly larger. You can read more about it in Chapter 15 of the Programming in Scala book. Anyways, here is the phone book using the `Entry` class:

```scala
object EntryArrayBook extends Book{
	private val MAX = 1000 // max number of names we can store
	private case class Entry(name: String, number: String)
	private val entries = new Array[Entry](MAX)
	private var count = 0
	// Abs: book =
	// { entries(i).name -> entries(i).number | i <- [0..count) }
	// ={ name -> number | Entry(name,number) in entries[0..count)}
	// invariant: 0 <= count <= MAX &&
	// values of entries(i).name distinct, for i in [0..count)
	...
}
```


###### Linked Lists

The array-based implementations of the phone book that we've seen so far only have been able to deal with a bounded number of names and numbers. Also, they waste space when the arrays are only partially full.

Next, we will see a different implementation that overcomes these shortcomings: the data structure will grow as required to store the data. The implementation will use several "nodes", each containing a name and number, and linked to the next node.

We will implement a class, each object of which will represent a phone book, encapsulating such a linked list.

Each `next` filed is a reference to the next node in the list. A reference simply records where the node is stored in memory, plus some typing information. There is a special reference `null` which indicates that there is no next node. Also, there is a program variable `list` which holds a reference to the first node in the list.

All program variables that correspond to objects actually store references to those objects. For example, after the declaration `val xs = List(1,2,3)`, the variable `xs` stores a reference to the place in memory where the `List` is stored. If `xs` is passed to a function, in fact it is the reference that is passed, rather than the whole list; this requires far less copying of data.

Scala automatically follows the reference where appropriate, so the referencing is transparent. However, if a reference variables holds the `null` value, then calling an operation on that reference is an error.

We can define nodes of a linked list as follows:

```scala
class Node(var name: String, var number : String, var next: Node)
```

Each node contains a reference to the next, so collectively they represent a list. We can create an implementation of the phone book by encapsulating a linked list.

```scala
class LinkedListBook extends Book{
	private var list : Node = null
	// list represents the mapping composed of (n.name -> n.number)
	// maplets, when n is a node reached by following 0 or more
	// next references.
	// Abs: book = { n.name -> n.number | n <- L(list,null) }
	...
}
```

Informally, each `LinkedListBook` object represents the mapping composed of `n.name -> n.number` maplets, where $n$ is a node reached by following 0 or more `next` references from `list`.

More formally, let's define the list of nodes reachable by following `next` references, starting from `a`, and continuing up but not including $b$. $$\begin{aligned}
L(a,b) = [], \qquad\qquad \text{if } a = b \\
a:L(a.\text{next},b), \text{if } a \neq b
\end{aligned}$$We will sometimes abbreviate `L(a,null)` to `L(a)`. Therefore, the abstraction function for the linked list will look like $$Abs:book=\{n.\text{name}\to n.\text{number } |\; n\in L(\text{list})\}$$Our intention is that the lists are finite, which implies that they are acyclic; and that names are not repeated. Our datatype invariant is then $$\text{DTI}: L(list) \text{ is finite, and the names in } L(\text{list}) \text{ are distinct.}$$For each operation, we need to search to see if the given name is already in the list. So let's encapsulate this in a function. The function traverses the list, comparing the name in the node with the name we're looking for.

```scala
/** Find the node containing name, if it exists
* Post: book = book_0 && returns n s.t.
* (n in L(list) && n.name=name)
* or n=null if no such Node exists */
private def find(name: String) : Node = {
	var n = list
	// Invariant: name does not appear in the nodes before n:
	// for all n1 in L(list, n), n1.name != name
	while(n != null && n.name != name) n = n.next
	n
}

/** Is name in the book? */
def isInBook(name: String): Boolean = find(name) != null

/** Return the number stored against name */
def recall(name: String) : String = {
	val n = find(name); assert(n != null); n.number
}

/** Add the maplet name -> number to the mapping */
def store(name: String, number: String): Unit = {
	val n = find(name)
	if(n==null){
		val n1 = new Node(name, number, list)
		list = n1
	}
	else n.number = number
	}
```

In `store`, if the name is new, we add the new name to the front of the list. The code for this could be shortened to `list = new Node(name, number, list)`.





