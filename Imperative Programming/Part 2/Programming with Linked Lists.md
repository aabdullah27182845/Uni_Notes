
Recall that $L(a)$ is defined to be the list of nodes reachable from $a$ by following `next` references. Here are the implementations of `find` and `store`:

```scala
/** return (n in L(list) && n.name=name)
  * or n=null if no such Node exists */
private def find(name: String) : Node = {
	var n = list
	// Inv: for all n1 in L(list,n), n1.name != name
	while(n != null && n.name != name) n = n.next
	n
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

Deleting an entry from a linked list is a bit more convoluted. We have to find the node `n` before that entry, and short circuit it, changing `n.next` to point to the following node. But deleting the first entry in the list has to be treated as a special case. This is messy.

For this, we need a better technique. We can use a dummy header node: a node that does not contain any real data, but whose `next` field points to the first proper node. We need to initialise the linked list appropriately.

```scala
class LinkedListBook extends Book{
	private var list = new Node("?", "?", null)
	// Abs: book = { n.name -> n.number | n <- L(list.next) }
	// DTI: L(list) is finite and
	// the names in L(list.next) are distinct
	...
}
```

Note that now `book` is formed from the names and numbers in nodes starting from `list.next`.

We arrange for `find` to find the node before the one containing the given name, or the last node if there is no such. That's what we'll want in order to implement `delete`.

```scala
/** Return the node before the one containing name.
  * Post: book = book_0 && returns n s.t. n in L(list) &&
  * (n.next.name=name or n.next=null if no such Node exists)*/
private def find(name:String) : Node = {
	var n = list
	// Invariant: name does not appear in the nodes up to and
	// including n; i.e.,
	// for all n1 in L(list.next, n.next), n1.name != name
	while(n.next != null && n.next.name != name) n = n.next
	n
}
```

Also, `isInBook` and `recall` are easily adapted to use the new version of `find`.

```scala
/** Is name in the book? */
def isInBook(name: String): Boolean = find(name).next != null

/** Return the number stored against name */
def recall(name: String) : String = {
	val n = find(name); assert(n.next != null); n.next.number
}
```

For `store`, if the name doesn't already exist, we store the new data in the old header cell.

```scala
/** Add the maplet name -> number to the mapping */
def store(name: String, number: String): Unit = {
	val n = find(name)
	if(n.next == null){ // store new info in current list header
		list.name = name; list.number = number
		list = new Node("?", "?", list)
	}
	else n.next.number = number
}
```

Alternatively, we could have created a new cell to hold the new data:

```scala
if(n.next == null){
	val n1 = new Node(name, number, list.next); list.next = n1
}
```

To delete a name and number now, we find the node `n` before the node containing that name, and then short-circuit that node. Also, return a `Boolean` indicating whether the name was found in the list.

```scala
/** Delete the number stored against name (if it exists);
  * return true if the name existed. */
def delete(name: String) : Boolean = {
	val n = find(name)
	if(n.next != null){ n.next = n.next.next; true }
	else false
}
```


###### Companion Objects

So far, we have considered the class `Node` as being defined at the top level. In some ways, this is the easiest approach. However, it means that `Node` is globally visible. This is undesirable, because we would like to hide the implementation, i.e. we would like to make the class `Node` private.

We could try putting the definition of `Node` inside the `LinkedListBook` class definition:

```scala
class LinkedListBook extends Book{
	private class Node(var name: String, var number: String, var next: Node)
	...
}
```

However, if we have two different `LinkedListBook` objects, `llbook1` and `llbook2`, then their `Node`'s will be of different types, which will make it difficult to compare them.

Definitions inside the `LinkedListBook` class should apply to particular objects formed from that class template. By contrast, if we want definitions to apply to all such `LinkedListBook` objects, they should be inside the companion object:

```scala
object LinkedListBook{
	private class Node(var name: String, var number: String, var next: Node)
}
```

The `LinkedListBook` object is known as the companion object of the `LinkedListBook` class, and defines types/values/operations that relate to all objects of the type, as opposed to those that operate on a single object of the type.

`Node` is defined as `private` inside the companion object, but it is still visible within the `LinkedListBook` class. References to `Node` need to be written as `LinkedListBook.Node`, e.g.

```scala
private var list : LinkedListBook.Node = null
...
val n1 = new LinkedListBook.Node(name, number, list)
```

A class defines types/values/operations on a particular object of that class; for example, the `store`, `recall`, and `isInBook` operations will be defined in the `LinkedListBook` class because they are operations on a particular object. 

The companion object defines types/values/operations that apply more generally to objects of the class; for example, the `Node` type will be defined `LinkedListBook` companion object, because we want the same type to apply to all nodes.

Companion objects and classes should be defined in the same file and have the same name. They can see one another's private fields.


###### Garbage Collection

When we delete a name, the node that holds that name is no longer reachable. At this point, the language implementation recycles that storage automatically, so it can be reused. This is known as garbage collection. Sometimes it is necessary to explicitly remove a reference to a node to allow garbage collection to go ahead. 

By contrast, old-fashioned languages like C/C++ force the programmer to explicitly manage the de-allocation of memory, which provides extra opportunities for bugs:

- The programmer might fail to deallocate storage that is no longer in use: a memory leak;
- The programmer might deallocate storage that is still in use.


###### Keeping the names in order

An alternative design would keep the list ordered in alphabetical order of the names. This would make it easy to print out the names in alphabetical order. It would also make it easy to print out the names in alphabetical order. It would also mean that if we search for a name `name` and get a node `n` with `n.name > name`, we know that `name` is not in the list.

We defined `Node` not as a case class. The reason is that we want to be able to write `n1 == n2` to test if `n1` and `n2` are the same node. However, if we made `Node` a case class, the Scala compiler would create a default implementation of `==`, such that `n1 == n2` would be equivalent to 

```scala
n1.name == n2.name && n1.number == n2.number && n1.next == n2.next
```

In particular, the last clause does a similar check on the `next` nodes, so this might traverse the whole list - causing particular difficulties if the lists contain loops.


###### Back to Linked Lists

![[Linked_List_Variants.png]]

Linked lists are generally useful for a variety of abstract datatypes, such as:

- Dynamically sized containers.
- Queues
- Stack
- Double-ended Queue (Deque)

Depending on the datatype required and the cost of operations, we might want to modify the structure.

You may want to refer to Lecture 14 notes in order to see some different implementations of Linked Lists and their wider 