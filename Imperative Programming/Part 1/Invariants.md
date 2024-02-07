
We will review our factorial function again from last time:

```
object Factorial {
   /** Calculate factorial of n
     * Pre: n >= 0
     * Post: returns n! */
   def fact(n: Int) : BigInt = {
	   require(n>=0)
	   if(n==0) 1 else fact(n-1)*n
   }
   // Main method
   def main(args: Array[String]) : Unit = {
	   print("Please input a number:")
	   val n = scala.io.StdIn.readInt()
	   // if (n>=0) {...
	   val f = fact(n)
	   print("The factorial of"+n+" is "+f)
   }
}
```

This program used recursion in order to calculate the factorial of a number. Here is another example, but this time, using a while loop:

```
  /** Calculate factorial of n
    * Pre: n >= 0
    * Post: returns n! */
def fact(n: Int) : BigInt = {
	require(n>=0)                    // assume n>=0
	var f: BigInt = 1; var i = 0    // f = i! and i<=n
	while(i<n){                    // f = i! and i<n
		 i = i+1                  // f = (i-1)! and i<=n
		 f = f*i                 // f = i! and i<=n
	 }
	 // f = i! and i = n, so f = n!
	 f
 }
```

Here are some things to consider while viewing this code:
- Variables, which can be changed, are introduced with `var`. (Compare with the earlier `val` keyword which introduces an immutable value.)
- Two commands can be written on one line by separating them with a semicolon (";"), as on line 7.
- The code `while(test) body` does the following
	 1.  evaluates `test`;
	 2. if `test = true`, executes `body`, and returns to step 1;
	 3. if `test = false`, finishes.
- Here the body of the `while` loop contains more than one command, so has to be enclosed in curly braces. Otherwise, these are unnecessary.


#### [[Invariants]]

The previous code illustrated an important pattern for proving properties of `while` loops, namely the idea of an invariant.

An invariant is a property that is true at the start and end of each iteration of the loop. In the previous code, the property `f = ! and i <= n` was an invariant.

We will explore this idea of invariants further with an example of the factorial function, but written with a `for` loop.

```
  // Calculate factorial of n, using a for loop
  // Precondition: n >= 0
  def fact(n:Int) : BigInt = {
    require(n>=0)
    var f : BigInt = 1 // f = 0!
    for(i <- 1 to n){ // f = (i-1)!
        f = f*i // f = i!
      }
      // f = n!
      f
  }
```

We cannot say `// f = i` on line 5. Invariants are easier to reason about using `while` loops than for loops, so we will tend to favour `while` loops from now on (except for trivial loops).

Do take into consideration that if some of the bounds are changed (the initial value of the while loop we saw earlier was made 1 for `i` initially instead of `0`), the invariant would still hold. This requires some extra analysis and proof from the programmer's side however.

Invariants are essentially a tool a programmer should aim to use in order to sufficiently state the correctness of a program. 

Here is the normal pattern for a program with an invariant `I` is as follows:
```
// pre
Init
// I
while (test) {
	// I and test
	Body
	// I
}
// I and not test
// post
```

We need to check:
- `Init` establishes `I`, assuming `pre`:
- `Body` maintains `I`;
- the loop terminates;
- `I and not test` implies `post`


#### [[Arrays]]

Scala has lots of sequence-like datatypes. Perhaps the most common type is arrays. If `T` is a type then `Array[T]` represents the type of arrays that hold data of type `T`. Each array has a fixed size. The `i`th entry of array `a` can be obtained using `a(i)`. The indexing is zero-based, so it begins at zero and ends at its length minus one. 

Arrays are mutable, so this means the following code is correct and can be compiled, given a(i) is of type `Int`.
	`a(i) = a(i) + 1`

We want to develop a function 
	`def findSum(a : Array[Int]) : Int = ...`
which returns the sum of the entries in `a`. If `a` has `n` entries, then the requirement is to calculate `total` such that
	`total = sum a[0..n)`.

We have postcondition: 
	**post:   returns**  `total` s.t `total = sum a[0..n)`

The precondition is simply true, so we will omit it. 

Our invariant in this program would be the sum from the 0th element of the array up till the `i`-th element of the array. We will call this "I".

Now, we can set `n` to be the size of `a` by:
	`val n = a.size`

Arrays are objects. When an object provides an operation op, then it is invoked by `obj.op`. If this operation requires an argument, then that is invoked by `obj.op(x)`

Recall our invariant, and also that our initialisation needs to establish `I`. The following code does this:

```
val n = a.size
var total = 0; var i = 0
```

When `i` gets to `n`, we'll have finished. So we are after a loop of the following form.

```
while(i<n){
	// I && i<n
	...
	// I
}
```

which we can do as follows

```
// I && i<n
total = total + a(i)
// total = sum a[0..i+1] && i<n
i = i + 1
// I
```

So now, after considering the preconditions and the invariant, we finally get to:

```
  /** Calculate sum of a
    * Post: returns sum(a) */
  def findSum(a : Array[Int]) : Int = {
    val n = a.size
    var total = 0; var i = 0
    // Invariant I: total = sum(a[0..i)) && 0<=i<=n
    while(i < n){
        // I && i<n
        total += a(i)
        // total = sum(a[0..i+1)) && i<n
        i += 1
        // I
    }
    // I && i=n
    // total = sum(a[0..n))
    total
}
```

Remember the rules for proving correctness using an invariant `I`. We've already checked all of these except the termination. For termination, the normal approach is to identify an expression `v` of the program variables, known as a `variant`, and to check
- `v` is integer valued, assuming the invariant;
- `v` is at least `0`, assuming the invariant;
- `v` is decreased at each iteration (maybe by more than one).

If these are true, the loop can do only a finite number of iterations before terminating.

Normally, the variant is a measure of how much more work needs to be done. Here, we will take our expression `v` for the variant as `n-i`.

###### Hoare Triplets

The (mathematical) notation $$\{P\}Prog\{Q\}$$is used to mean that if the program `Prog` is executed from a state that satisfies `P`, it is guaranteed to terminated in a state that satisfies `Q`. `P` is called the precondition, and `Q` is called the postcondition.


###### Formal Methods

- Invariants are useful for us to have more certainty in the correctness of our programs.
- Invariants and Hoare triplets can be used to prove a program correct.
- They can also be used to systematically derive programs - by refining from a mathematical specification to a working program.
- Systematic refinements and proofs can be done by machine (Z and the B-method).

There is a sense in which recursion and iteration are related, because they perform similar tasks.

- Recursion: solve a larger problem by breaking it up into smaller ones.
- Iteration: Start small and keep repeating until the task is done.
- Prove recursion is correct with proof by induction.
- Prove iteration is correct with invariants.
- Need to show that finite recursion terminates and that loops terminate.



#### Summary

- Invariants: A property that is true at the start and end of each iteration of the loop.
- Using invariants for deriving programs;
- Proving termination;
- Hoare triplets; preconditions and postconditions.