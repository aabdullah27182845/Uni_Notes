
We will be seeing some key principles which are used in good programming practice and in object-oriented programming:
- Modularisation
- Abstraction
- Encapsulation

We will also be seeing invariants again, but in this case, we need to maintain some meaningful property of a data structure.

This is about to burn some shmucks. Toy programs are small, simple, solitary, and short-lived. Real programs are large, complex, designed by teams, and its long-lasting.

Here are some conceptual tools for managing complexity:
- Decomposition/modularity :: Complex systems composed of components/modules, each module should play a single, well-understood role, components should have well-defined interfaces.
- Abstraction & Encapsulation :: Components should be viewed as black boxes and black boxes with identical specifications should be interchangeable; implementation details should be irrelevant when using a component.
- Reusability: Reuse existing components whenever possible and design new components with reusability in mind.

Object-oriented languages provide linguistic machinery to support and enforce the three principles.

In order to write large programs, it is important to split the problem into manageable chunks, or modules. Each module should separate the interface from the implementation (MVC).
- The interface describes what the module does.
- The implementation does it.'

Somebody who wants to use the module should only have to read the interface.

###### Functions

Functions can be seen as a form of modularisation.

- The interface is the signature, together with a precise description of what the code does, in terms of pre- and post-conditions.
- The implementation is the local variables together with the algorithms used.

Somebody who wants to use the function should only have to read the interface.


###### Modules

A module collects together related data and operations upon that data.

- The interface is the interfaces of the operations provided by the module, perhaps linked to an abstract view of the state;
- The implementation is the internal data, plus the implementation of the operations.

Benefits of using modules include:

- The code becomes easier to understand if broken down into manageable chunks.
- We can reuse the module, perhaps in a different program.
- We can improve the implementation of a module, without changing the interface, thereby improving the performance of the program without changing the correct behaviour.


###### Objects

In an OOP language, modules are objects and classes. An object is simply a software artefact with some data, and some operations on that data. Each object has a specification that describes what its operations do, and an implementation that provides code to do it.

Data is private to the object: other parts of the program can access the data only via the operations; we don't want code elsewhere in the program to be able to mess with the object's internal state.

If an object `obj` has an operation `op( ... )` then we can invoke it with argument `args` using that notation `obj.op(args)`.

We've seen this when getting the size of an array with `array.size()`.

The identifier `this` represents the current object, so we can write `this.op(args)` to call the operation `op` defined within the current object. We can abbreviate this to `op(args)` - the notation we've been using so far.


###### Classes

Most objects are instances of classes that define the data and operations for a whole family of similar objects.

The power of Object-oriented Programming stems from the ability to use many powerful and general pre-defined classes, and the ability to define new classes.

Here is an example: If our program manipulates text, we might want to define A `Text` class, which would allow us to create and manipulate objects representing a sequence of characters. The interface might include operations for getting the length of a text, read the character at a given position, or insert the character at a given position.

Without knowing anything about how the new class is implemented, we can already write code that uses it.

```scala
buffer = new Text("abcaadefa")
for (i <- buffer.length() to 1 by -1)
if (buffer.charAt(i-1) == ’a’) buffer.insert(i,’b’)
```

Code becomes easier to write, maintain, understand, and reuse.

Specifying and implementing a class are different tasks, which can be accomplished by different people.

Multiple instances of a class have separate identities. Object identities can be copied without making a copy of the object: this is known as aliasing or sharing

```scala
var a = new ArrayIntSet(100) // a is one object
var b = new ArrayIntSet(100) // b is separate object
// new constructs the object and returns its identity
a eq b /* false */
a.insert(23)
a.contains(23) /* true */
b.contains(23) /* false */
b = a // a is now aliased to b
// a & b share identity. (Original b is lost)
a eq b /* true */
a.insert(34)
b.contains(34) /* true */
```

There are two views on when the objects are equal. Either, they are the same object (referencing the same object in memory), or they have the same content.

```scala
class Point (val x:Int, val y:Int)
val a = new Point(0,0)
val b = a //b is just an alias for a
val c = new Point(0,0)
a equals b // true
a == b // true (short for equals)
a equals c // false
a == c // false
```

To check for content equality; we can override equals

```scala
class Point (val x:Int, val y:Int){
override def equals(other: Any): Boolean =
other match {
case b:Point => this.x == b.x && this.y == b.y
case _ => false}
}
val a = new Point(0,0)
val c = new Point(0,0) // c has same content
val d:Any = c
// Content checks
a equals c // true
a equals d // true
a == c // true
a == d // true
```


###### Encapsulation

Separation between external interface of component and its internal implementation is what encapsulation is all about. Methods not in the interface hidden by marking them as private.

```scala
class ArrayIntSet(MAX: Int) {
	private var elems = new Array[Int](MAX)
	private var size = 0
	def isEmpty = (size == 0)
	def contains(x: Int) = { ... }
	private def find(x: Int): Int = { ... } ...
}
```

All data is private, meaning that it is hidden and inaccessible.


###### Types of Objects

There are two types of objects in Scala.

- Singleton objects :: created without a corresponding class and used to hold utility methods or values that are global to the application; they are defined by using the object keyword followed by the object name.
- Standard objects: created from a corresponding class and used to represent instances of that class; they are created by using the new keyword followed by the class name.


###### Inheritance

The editor `Ewoks` stores the text of a file in a Text class

```scala
class Text(init: Int) {
	/* text = buffer[0..gap) ++ buffer[max-len+gap..max) */
	private var buffer = new Array[Char](init)
	private var len = 0
	private var gap = 0
	private def max = buffer.length
	def length = len
	def charAt(pos: Int) = {..}
	def insert(pos: Int, ch: Char) = {..}
..
}
```

If we want to be able to rewrite a line of the text, then we need some way to extract the current line. Thus means we want an enhanced version of the class with more operations.

```scala
class PlainText extends Text {
// The additional variables and methods go here...
}
```

The variables inside the class definition define the state

```scala
var linelen = new Array[Int](MAXLINES);
var nlines = 0;
```

Now we consider the methods. Some methods of `PlainText` are inherited from `Text`. Some are added and so need to be implemented, e.g. `getRow`. And some have a changed specification, so must be overridden. Methods that override in the superclass can be identified with the tag `override`:

```scala
class PlainText extends Text { ...
	def getRow(pos: Int) {...}
		override def insert(pos: Int, ch: Char) {
		// Do the same as the method in the superclass ..
		super.insert(pos,ch)
		// .. and then update the linelen values as necessary
	}
}
```

Inheritance can be helpful when `B` is a kind of `A` (e.g. an Apple is a kind of Fruit).

The $\text{is-a}$ relationship should be rigid, so it holds for as long as `B` exists.

Non-rigid relationships should be modelled with roles/attributes, not with inheritance.


###### Dynamic Building

Consider the following method defined in the class `Text`.

```scala
def insertString(pos: Int, s: String) {
	for (i <- 0 until s.length){
		insert(pos+i, s.charAt(i));
	}
}
```

It invokes the `insert` method, but executes a different version depending on the class of the object on which it is invoked.

```scala
val t:Text = new PlainText()
t.insertString(0,’’Hello’’)
```

Using the insert method from `Text` would be bad because the line map won't update. Using the method from `PlainText` is better. 

The dynamic type of the object determines the method implementation to invoke.


###### Dangers of Inheritance

Inheritance is dangerous: Changing an implementation of a super-class can break the program if the subclasses rely on a particular implementation.

For example, reimplementing `insertString` as follows in the `Text` class means that the implementation in `PlainText` no longer works.

```scala
def insertString(pos: Int, s:String){
	val n = s.length
	makeRoom(n); moveGaps(pos)
	s.getChars(0,n,buffer,pos)
	gap+= n; len += n
}
```

This uses a fast `String.getChars` method to copy characters, but `PlainText` no longer works. The underpinning issue is that the interface between a class and its subclasses is hard to specify precisely and the subclasses break encapsulation.


###### Composition

Composition is a concept that models a $\text{has-a}$ relationship. It enables creating complex types by combining objects of other types.

This means that a class `Robot` can contain an object of another class `Battery` and another object from the class `MotionController`.

In our case, we could make `PlainText` to contain an object of the class `Text`.

```scala
class PlainText{
	private val text:Text = new Text();
	def length = text.length
}
```


###### Inheritance vs Composition

- Inheritance provides code reusability through the hierarchy. Composition is more flexible and makes classes more loosely coupled.
- Inheritance can lead to deep and complex hierarchies, making the code harder to understand and maintain. In contrast, composition allows for a flatter and more manageable class structure.
- Changes in the superclass can have unintended effects on subclasses, but changes in one component do not affect others.
- Inheritance is preferred when there is an ”is-a” relationship between classes, like a ”Car” is-a ”Vehicle.” Composition is preferred when there is a ”has-a” relationship between classes, like a ”Car” has-a ”Engine".

