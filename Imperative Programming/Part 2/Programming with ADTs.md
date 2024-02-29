
The Scala API includes many useful abstract datatypes, such as various types of sequences, sets, and mappings. We can program with these, treating them as mathematical objects.

Elsewhere we have seen how some of these abstract datatypes are implemented: but we can use them without knowing anything about the internal implementation. This is known as data abstraction.

A popular word game "Doublets", invented by Lewis Carroll, is to find a path of words, each differing from the previous in a single letter, linking two given words. Carroll used the example of finding such a path linking `head` to `tail` and gave the solution `head`, `tail`, `teal`, `tell`, `tall`, `tell`, although there are other solutions of this length.

In these notes, we will write a program to find such paths.

Scala has a class of lists, very similar to Haskell lists. The type of lists holding data of type `A` is written as `List[A]`. A list holding values `a, b, c, ..., z` can be written as `List[A](a,b,,...,z)`; in particular, the empty list can be written as `List[A]()`. The type parameter can be omitted for non-empty lists.

If `xs` is a list, then `xs.head` and `xs.tail` are its head and tail. `x:xs` represents the value `x` `cons`-ed onto the front of `xs`. Lists also have lots of operations like `map` and `filter`.

Note that lists are immutable, meaning you can't change a list once you've created it; however, you can construct new lists from the first list; and if a variable references a list, you can change the variable to reference a different list.

In our word-path program, we'll represent a path as a list of `String`s: 

```scala
type Path = List[String]
```

for example,

```scala
List("head", "heal", "teal", "tell", "tall")
```

For later, let's write a function to print a path

```scala
/** Print the path, separating the elements with commas
  * Pre: path is non-empty */
def printPath(path: Path) = {
	print(path.head)
	for(w <- path.tail) print(", "+w)
	println()
}
```

In the word paths program, we want to define a function `def neighbours(w: String) : List[String] = ...` that returns all the words that differ from `w` in precisely one position. We return the result as List but the actual order is unimportant.

Recall that earlier, we implemented a dictionary. We can initialise a dictionary, e.g. 

```scala
val dict = new Dictionary("knuth_words.txt")
```

and look up a word `w1` by `dict.isWord(w1)`.

Here is the dictionary in recap:

```scala
/** Each object of this class represents a dictionary, in which
  * words can be looked up.
  * @param fname the name of a file containing a suitable list
  * of words, one per line. */
class Dictionary(fname: String){
/** A Set object holding the words */
private val words = new scala.collection.mutable.HashSet[String]

	/** Initialise dictionary with all words from file fname */
	private def initDict(fname: String) = ...

	// Initialise the dictionary now (on constructing this object)
	initDict(fname)

	/** @return <code>true</code> if w is in the dictionary */
	def isWord(w: String) : Boolean = words.contains(w)
}
```

By using the `scaladoc`, we can generate HTML documentation of the `Dictionary` class by typing the following:

```
> scaladoc Dictionary.scala
```

###### Neighbours

Consider the word `w1` that is formed from `w` by replacing the `i`-th character `c`. We could define `w1` by

```scala
val w1 = w.take(i) + c + drop(i+1)
```

or 

```scala
val w1 = w.patch(i, List(c), 1)
```

We want to consider all such `w1`, provided `c` is different from the current `i`-th character of `w` and that `w1` is in dictionary.

We can define the `neighbours` function as follows, building up the neighbours we have found in a list `result`:

```scala
def neighbours(w: String) : List[String] = {
	var result = List[String]() // build up the result
	for(i <- 0 until w.length; c <- ’a’ to ’z’)
	if(c != w(i)){
		val w1 = w.patch(i,List(c),1) // replace ith character
		// of w with c
		if(dict.isWord(w1)) result = w1 :: result
	}
	result
}
```

We want a function 

```scala
def findPath(start: String, target: String)
```

That searches for a path from `start` to `target`. Clearly, if the search succeeds, the function should return the path that is found. But what should it return if no path is found?

Recall the Haskell `Maybe` type:

```haskell
data Maybe a = Just a | Nothing 
```

Scala has a similar type `Option[A]`, kind of like `Optional<T extends Object>` in Java, which works in the same way as `Maybe` here. We therefore have `findPath` return a result of type `Option[Path]`:

```scala
def findPath(start: String, target: String) : Option[Path]
```


###### Breadth-first Search

We're now ready to consider how to solve the word path puzzle, to find a shortest path from a starting word `start` to a target word `target`, where each word in the path is a neighbour of the previous.

We start by considering the neighbours of `start`. If any equals `target`, we are done. We then consider the neighbours of the neighbours of `start`. If any equals `target`, we are done.

We then consider the neighbours of the neighbours of the neighbours, and so on.

Put another way, we consider the words reachable in one step from `start`; then the words reachable in two steps from `start`; and so on. This is the exact definition of breadth-first search.


###### Queues

In order to implement the breadth-first search, we will use a queue: a sequence-like data structure that allows elements to be accessed in a first-in-first-out (FIFO) order. The class `scala.collection.mutable.Queue[A]` represents mutable queues that can hold data of type `A`. If `q` is a queue of this type, the main operations on `q` are:

- `q.enqueue(x)`
- `q.dequeue()`
- `q.isEmpty()`, and etc.

We can specify queues by the following:

```scala
/** A queue of data of type A.
  * state: q : seq A
  * init: q = [] */
class Queue[A]{
	/** Add x to the back of the queue.
	  * post: q = q0 ++ [x] */
	def enqueue(x: A)
	
	/** Remove and return the first element of the queue.
	  * pre: q 6 = []
	  * post: q = tail q0 ∧ returns head q0
	  * or post: returns x s.t. q0 = [x] ++ q */
	def dequeue(): A
	
	/** Is the queue empty?
	  * post: q = q0 ∧ returns q = [] */
	def isEmpty: Boolean
}
```

To implement the breadth-first search, we create a queue, initially containing just start. We then repeatedly remove an element `w` from the queue. We then consider each neighbour `w1` of `w`. If `w1` equals `target`, we are done. Otherwise, we add `w1` to the end of the queue.

If we get to a state where the queue is empty, we have explored all words reachable from `start` without finding `target`; hence there is no path from `start` to `target`.

More precisely - because we want to be able to reconstruct the path - we store `Path`s in the queue. For efficiency, we store these paths in reverse order. Thus each element of the queue is of the form `List(w, w1, w2, w3, ..., wn, start)` representing that `start, wn, ..., w1, w` is a path from `start` to `w`; we continue searching from `w`.

```scala
/** Find a minimum length path from start to target.
  * @return Some(p) for some shortest Path p if one exists;
  * otherwise None. */
def findPath(start: String, target: String) : Option[Path] = {
	val queue = scala.collection.mutable.Queue(List(start))

	while(!queue.isEmpty){
		val path = queue.dequeue(); val w = path.head
		for(w1 <- neighbours(w)){
			if(w1==target) return Some((target::path).reverse)
			else queue += w1::path
		} // end of for
	} // end of while
	None // no solutions found
} // end of findPath
```

The above algorithm works in theory, but in practice it is hopelessly inefficient, because it repeats a lot of work.

For example, the algorithm considers paths that revisit a word, for example `List("head", "heap", "head")`.

Further, it considers paths that reach a word for which another path has been reached. For example, it considers the path `List("head", "heal", "heap")`, even though it had found the path `List("head", "heal", "heap")`. 

The solution is to avoid exploring a path further if it reaches a word that has previously been seen. Hence, we use a variable `seen` that holds the set of words that we've previously seen. Here is the better algorithm:

```scala
/** Find a minimum length path from start to target.
  * @return Some(p) for some shortest Path p if one exists;
  * otherwise None. */
def findPath(start: String, target: String) : Option[Path] = {
	val queue = scala.collection.mutable.Queue(List(start))
	// Keep track of the words we’ve already considered
	val seen = new scala.collection.mutable.HashSet[String]
	seen += start
	
	while(!queue.isEmpty){
		val path = queue.dequeue(); val w = path.head
		for(w1 <- neighbours(w)){
			if(w1==target) return Some((target::path).reverse)
			else if(!seen.contains(w1)){seen += w1; queue += w1::path}
		} // end of for
	} // end of while
	None // no solutions found
} // end of findPath
```

Here is the main function then:

```scala
var dict : Dictionary = null // The dictionary

def main(args: Array[String]) : Unit = {
	val t0 = System.currentTimeMillis()
	// parse arguments
	val errMsg =
		"Usage: scala WordPaths [-d dict-file] start target"
	var i=0; var start = ""; var target = ""
	var dictFile = "knuth_words.txt"
	while(i<args.length){
		if(args(i)=="-d"){ dictFile = args(i+1); i += 1 }
		else if(start=="") start = args(i)
		else{ require(target=="", errMsg); target = args(i) }
		i += 1
	}
	require(target!="", errMsg)
	...
}

def main(args:Array[String]) : Unit = {
	...
	// check lengths agree; initialise dictionary
	require(target.length == start.length,
		"start and target words should be the same length")
	dict = new Dictionary(dictFile)
	
	// The main stuff
	val optPath = findPath(start, target)
	optPath match{
		case Some(path) => printPath(path)
		case None => println("No path found")
	}
	
	println("Time taken: "+(System.currentTimeMillis()-t0))
}
```


The program solves Lewis Carroll's example in less than one second, finding the following path:

```scala
head, hear, heir, hair, hail, tail
```
