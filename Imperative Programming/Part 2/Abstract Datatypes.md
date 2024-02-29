
We will begin these notes by defining what abstract data types are. Essentially, a module puts data and functionality of a "thing" in one place. Abstract datatype gives the generic idealised description to this "thing".

The `state` refers to what is represented. `init` gives the initial configuration of the `state`. Preconditions and postconditions are defined with the use of these properties.

Here's more keywords: `traits` essentially are a list of "promises", or a contract that is to be fulfilled by the implementation of the trait. You're familiar with interfaces in Java, so these are the equivalent. This is lightwork stuff, but the application will get harder. 


###### Traits

In Scala, `traits` realise the idea of an abstract datatype and are similar to interfaces in other programming languages, as mentioned before. Here is an example:

```scala
/** IntSet is an interface */
trait IntSet {
	def isEmpty: Boolean
	def contains(x: Int): Boolean
	def insert(x: Int)
	def delete(x: Int)
}
```

These methods and fields can be implemented by different classes

```scala
/* Different implementations extend it */
class ArrayIntSet(MAX: Int) extends IntSet {..}
class BitmapIntSet(MAX: Int) extends IntSet {..}
```

Here is some more notation. **Mix-in composition** refers to a the idea that classes can implement more than one trait. This is also very familiar to you, as seen before in Java.

The only difference between traits and interfaces is that in Scala, traits can have both abstract and concrete methods and fields. This is a bit different to interfaces in Java. Some may say traits are closer to abstract classes than interfaces. 

Traits cannot have instance variables, so they can't maintain state. While concrete methods in traits can access state, the state in traits can only be represented by abstract fields.


###### A Spelling Checker

We'll write a very simple application to check the spelling of words. The user will provide a word on the command line, and the program will say whether or not it is a valid word. There are lots of files on the web that give lists of English words, we will use one of them called `knuth_words.txt`.

However, we want to read the words into a software object, to allow fast access. We therefore want to represent a set of words internally. 

```terminal
$ scala SpellCheck invarient
word invarient not found
$ scala SpellCheck invariant
invariant is a valid word
```

In our case here, we will use mutable Sets to represent these words. Mutability defines the flexibility of the collections/datatype, so for example, an immutable object will not have its values changed. You can still implement immutable objects to have differing methods, but all they will do is copy the object and make another one with the change. The original object will be picked up by the garbage collector (usually).

Here, the base of the mutable sets is the trait `scala.collection.mutable.Set`

New sets can be created by 

```scala
val s = scala.collection.mutable.Set(2, 4, 6, 8)
val empty = scala.collection.mutable.Set[Int]()
```

An element `x` can be added to set `s` by `s.add(x)` or `s += x`. The expression `s.contains(x)` tests whether the set `s` contains `x`. The expression `s.size` gives the cardinality of the set. There are many more operations on sets, such as `map` or `filter`.

Since sets are mathematical objects, we can therefore write mathematical specifications of their behaviours. Here is an abstract representation of them in Scala:

```scala
/** A set of values of type A.
* state: S : P A
* init: S = {} */
trait Set[A]{
	/** Add elem to the set.
	* post: S = S0 ∪ {elem} */
	def add(elem: A)
...
}
```

$S_0$ represents the value of $S$ at the start of the function call; this is a standard convention.

```scala
trait Set[A]{
	...
	/** Test if this set contains elem.
	* post: returns b s.t. S = S0 ∧ b = (elem ∈ S)
	* or, more simply
	* post: S = S0 ∧ returns elem ∈ S */
	def contains(elem: A): Boolean
	
	/** The size of the set
	* post: S = S0 ∧ returns #S */
	def size: Int
	
	// ...and about 100 more operations...
}
```

Note that, by convention, because the `size()` operation has no arguments and no side-effects, we can leave out the parentheses.

To store the words of the dictionary, we'll use a particular implementation of `scala.collection.mutable.Set[String]` namely the `HashSet`. This concrete implementation represents that the implementation is based on a hash table. 

We can create the dictionary by 

```scala
// The dictionary
val words = new scala.collection.mutable.HashSet[String]
```

We then want to read in words from the file, and add them to `dictionary`. We only want to include words that contain only lower case letters. The following helper function tests whether a particular word should be included:

```scala
def include(w:String) = w.forall(_.isLower)
```

Scala provides a class `scala.io.Source`; each `Source` represents a source of textual data. We can create a Source associated with a file `fname` by `scala.io.Source.fromFile(fname)`. We can then call the operations `getLines` on this to get a sequence of strings corresponding to the lines of the file. We can then iterate over this sequence.

```scala
/** Initialise dictionary with all words from file fname */
def initDict(fname: String) = {
	val allWords = scala.io.Source.fromFile(fname).getLines()
	// Should word w be included?
	def include(w: String) = w.forall(_.isLower)
	for(w <- allWords; if include(w)) words += w
} 
```

The following function tests whether a given string `w` appears in the dictionary

```scala
/** @return <code>true</code> if w is in the dictionary */
def isWord(w: String) : Boolean = words.contains(w)
```

Here is the main method then:

```scala
object SpellCheckNoModule{

	/** The dictionary (arguably should be in separate module) */
	val words = new scala.collection.mutable.HashSet[String]
	
	/** Initialise dictionary with all words from file fname */
	def initDict(fname: String) = ...
	
	/** @return <code>true</code> if w is in the dictionary */
	def isWord(w: String) : Boolean = ...
	
	def main(args: Array[String]) : Unit = {
		assume(args.length==1, "Needs one argument")
		val w = args(0)
		initDict("knuth_words.txt")
		if(isWord(w)) println(w+" is a valid word")
		else println("word "+w+" not found")
	}
}
```

The implementation mixes the representation of the dictionary with the user interface. It would be better to implement the dictionary in a different module.

- If we wanted to change the implementation - for example to use an ordered array of `String`'s, combined with binary search - we would have to search through the program to find the parts to change. This would be better if it were all together in a single module.
- Later, we will want a dictionary in a different program. Implementing the dictionary in a different module will allow us to reuse that module.

We will therefore implement the dictionary as a class `Dictionary`. This will allows us to create several of these objects, perhaps based on different files. We will provide the name of the file when we construct this object.

```scala
/** Each object of this class represents a dictionary, in which
* words can be looked up.
* @param fname the name of a file containing a suitable list
* of words, one per line. */
class Dictionary(fname: String){
	...
}
```

This will allow us to write for example, `val dict = new Dictionary("knuth_words.txt")`. Here is the complete dictionary class:

```scala
/** Each object of this class represents a dictionary, in which
* words can be looked up.
* @param fname the name of a file containing a suitable list
* of words, one per line. */
class Dictionary(fname: String){

	/** A Set object holding the words */
	private val words = new scala.collection.mutable.HashSet[String]
	
	/** Initialise dictionary with all words from file fname */
	private def initDict(fname: String) = ... // as before
	
	// Initialise the dictionary now (on constructing this object)
	initDict(fname)
	
	/** @return <code>true</code> if w is in the dictionary */
	def isWord(w: String) : Boolean = words.contains(w)
}
```

`words` and `initDict` are marked as private, which means they cannot be accessed from outside the class. Code outside the class may use it only via its public function `isWord`.

In OOP, it is good practice to keep member data `private`. This makes sure that the state is not messed up by direct access to the data. Access is limited to functions within the class which are able to amend the data.

When an object is created, all the code outside `defs` is executed; here, `words` is initialised and `initDict(fname)` is called.

So, here is the revised program now:

```scala
/** A simple application to check the spelling of words */
object SpellCheck{
	def main(args: Array[String]) : Unit = {
		assume(args.length==1, "Needs one argument")
		val w = args(0)
		val dict = new Dictionary("knuth_words.txt")
		if(dict.isWord(w)) println(w+" is a valid word")
		else println("word "+w+" not found")
	}
}
```


###### Our Second Application: The phone book

We will now consider a different application, namely a phone book. We will look at several different implementations. We will want to add people's names and numbers to the book, and then look up a person's number. The program will be in two parts: the phone book itself and the user interface.

Abstractly, the state of the phone book is a mapping from names to phone numbers. Modelling each of these as a string, the abstract state is a mapping $$\text{state}:book:\text{String}\to\text{String}$$for example: $$book=\{ \text{"Gavin"}\to\text{"73841"}, \text{"Pete"}\to\text{"73853"}, \text{"Mike"}\to\text{"73854"}\}$$We can capture the requirements of the phone book as a trait:

```scala
/** The state of a phone book, mapping names (Strings) to
* numbers (also Strings).
* state: book : String → String
* init: book = {} */
trait Book{
	...
}
```

We want to be able to add a particular name and number to the phone book. This corresponds to adding $\text{name}\to\text{number}$ to $book$:

```scala
/** Store number against name in the phone book
* post: book = book0 ⊕ {name → number} */
def store(name: String, number: String) : Unit
```

where $f\oplus g$ is a function $f$ overwritten by $g$, i.e. $$(f\oplus g)(x) = \begin{cases}
g(x) \;\;\text{if}\; x \in \text{dom} g \\
f(x) \;\;\text{if}\; x \in \text{dom} f - \text{dom} g \\
\text{undefined otherwise}
\end{cases}$$and $\text{dom} f$ is the set of values where $f$ is defined $$\text{dom}\;f=\{x|(x\to y)\in f\}$$We also want to be able to look up a particular name in the phone book. That is, we want to be able to find `book(name)`, if initially the name was in the domain of the book. 

```scala
/** Return the number stored against name.
	* pre: name ∈ dom book
	* post: book = book0 ∧ returns book(name) */
def recall(name: String) : String
```

The above operation has a non-trivial precondition, so it's useful to have an operation to test if that precondition is true, i.e. if `name` appears in the book

```scala
/** Does name appear in the book?
	* post: book = book0 ∧ returns name ∈ dom book */
def isInBook(name: String) : Boolean
```

Later, we will produce an object `MapBook` that implements the `book` trait. We can then implement the main program as follows.

```scala
object PhoneBook{
	def main(args: Array[String]) : Unit = {
	val book = MapBook
	var done = false // are we finished yet?
	while(!done){
		val cmd = readLine("Recall (r), store (s) or quit (q)? ")
		cmd match{
			case "r" => ...
			case "s" => ...
			case "q" => done = true
			case _ => println("Please type ‘r’, ‘s’ or ‘q’.")
		} // end of match
		} // end of while
	} // end of main
}
```

Here are the implementations of the cases:

```scala
cmd match{
	case "r" => {
		val name = readLine("Name to recall? ")
		if(book.isInBook(name)) println(book.recall(name))
		else println(name+" not found.")
	}
	case "s" => {
		val name = readLine("Name to store? ")
		val number = readLine("Number to store? ")
		book.store(name,number)
	}
	...
}
```

Scala has a trait, `scala.collection.mutable.Map` which almost exactly meets our requirements. The trait `scala.collection.mutable.Map[A, B]` corresponds to mappings of type $A\to B$.

An object implementing this trait can be created by 

```scala
val m = scala.collection.mutable.Map[A, B]()
```

corresponding to the empty mapping.

Each object `m` that implements the `scala.collection.mutable.Map[A, B]` trait provides the following operations:

- `m.apply(key: A) : B` retrieves the value which is associated with the given key.
- `m.update(key: A, value: B)` adds a new key/value pair to this map.
- `m.contains(key: A) : Boolean` tests whether this map contains a binding for a key.

and many more other operations. Many objects that are "function-like" have an operation called `apply`. An application of the form `o.apply(args)` can be abbreviated to `o(args)`. Array indexing is an example of this.

The following implementation of `Book` is a wrapper around a `Map`

```scala
object MapBook extends Book{

	private val map = scala.collection.mutable.Map[String, String]()

	/** Store number against name in the phone book */
	def store(name: String, number: String) = map.update(name, number)

	/** The number stored against name */
	def recall(name: String) : String = {
		assert(map.contains(name)); map(name)
	}

	/** Does name appear in the book? */
	def isInBook(name: String) : Boolean = map.contains(name)
}
```

Note that `map` is annotated with the word `private`. This means that `map` is private to this object, and not accessible from elsewhere.

Our main `PhoneBook` object uses a single `Book` object. We therefore defined `MapBook` as an object, as we only want a single instance. If we want several objects of the same type then we need to define an appropriate class.

```scala
class MapBook extends Book{ ... }
```

where the body of the definition is precisely as before.

Our main `PhoneBook` object could then create two `MapBook` objects by 

```scala
val book1 = new MapBook; val book2 = new MapBook
```

These are independent: each has its own internal state.

Here's a test suite that tests `MapBook` and several other implementations of `Book`.

```scala
class TestBook extends AnyFunSuite{
	for(book <- List(MapBook, ArraysBook, PairArrayBook, EntryArrayBook)){
		book.store("Gavin", "73841"); book.store("Pete", "73853")
		test(book.toString+": recall of first stored value"){
			assert(book.isInBook("Gavin")); assert(book.recall("Gavin")==="73841")
		}
		test(book.toString+": recall of second stored value"){
			assert(book.isInBook("Pete")); assert(book.recall("Pete")==="73853")
		}
		test(book.toString+": recall of unstored value"){
			assert(!book.isInBook("Mike"))
			intercept[AssertionError]{book.recall("Mike")}
		}
	}
}
```

