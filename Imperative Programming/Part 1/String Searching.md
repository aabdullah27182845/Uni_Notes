
Recapping from the last notes in [[Testing]], If a test fails, how do you find the bug that caused it? Unit tests help you focus because they indicate which components have test suites which are now failing. There are a number of less sophisticated techniques.

We can try the "ooga-booga" strategy of printing variables in order to see the states. This can also be better enhanced by logging it to a text file if the input of the code is too long, or for general safekeeping purposes.

You can test whether particular properties hold using an assertion. This also helps document your code. For example, you can test whether your claimed invariant really does hold using an assertion; for example, in the loop of `fastExp` where `SlowExp` is the slow, but trusted implementation of exponentiation.

Of course, this will slow your implementation down. You can also test whether your variant is really being decreased, saving the previous value of the variant in a variable, say variant; for example:

```
val oldVariant = variant
variant = ...
assert(variant<oldVariant, ...
```

If you're not sure which function is causing problems, you can try logging each function call, and return from a function; for example 

```
println("Starting exp: x = "+x+"; n = "+n)
...
println("Finishing exp: result = "+y)
```

Also, explicitly testing the precondition and/or postcondition can help detect bugs. However, it's far easier to debug a function in isolation than after you've inserted the function into a larger program - hence the need for unit testing.


###### Comparing Strings

Suppose we want to compare two Strings to see if they are equal. It's easiest if we start by converting them into arrays of `Char`'s: `val a = sa.toArray; val b = sb.toArray`. We then want a function:

```
/** Do a and b hold the same characters?
* Post: returns a[0..a.size) = b[0..b.size) */
def equals(a: Array[Char], b: Array[Char]) : Boolean = ...
```

that tests if the arrays are equal. We can start by checking the arrays are the same size. And this will make our algorithm more efficient.

If the arrays are the same size, letting `n = a.size`, we want to test if `a[0..n) = b[0..n)`; more precisely, we want to establish the postcondition. It seems sensible to compare the elements one at a time, which suggests our invariant may be: $$ I \;\hat{=}\; equal = (a[0..k) = b[0..k)) ∧ 0 ≤ k ≤ n.$$This leads to the following straightforward code:

```
	var k=0; var equal = true; val n = a.size
	while(k<n){
		equal = equal && a(k)==b(k)
		k = k+1
	}
	// equal = (a[0..n)=b[0..n))
	equal
```

With the previous code, if `equal` became `false`, it remains as such subsequently. But the program continues to run and check the rest of the elements needlessly. We can certainly improve this program further:

```
	var k=0; var equal = true; val n = a.size
	while(k<n && equal){
		equal = equal && a(k)==b(k)
		k = k+1
	}
	// does equal = (a[0..n)=b[0..n)) here?
	equal
```

The initialisation and loop body are unchanged, so clearly the same invariant holds. But does this give us the right answer at the end? And also, does the order of evaluation in our `while` guard matter too? 

It does give us the right answer once we analyse enough and also it depends. In this algorithm, it doesn't but in certain cases, we might get an `ArrayIndexOutOfBounds` exception.

The guard of the `while` loop ensures that `equal = true` when we enter the loop body. Hence the assignment `equal = equal && a(k)==b(k)` which can be simplified:

```
	var k=0; var equal = true; val n = a.size
	while(k<n && equal){
		equal = a(k)==b(k)
		k = k+1
	}
	equal
```

We can simplify this even further, to get

```
	var k=0; val n = a.size
	while (k<n && a(k)==b(k)) k = k+1
	k == n
```

The invariant in this case is $$a[0..k)=b[0..k) ∧ 0 \leq k \leq n$$Here, the order of evaluation of the `while` guard does matter. Do some analysis to find out why!

The quick and dirty version jumps out of the function as soon as a mismatch is found. 

```
	var k=0; val n = a.size
	while(k<n)
		if(a(k)!=b(k)) return false else k = k+1
	true
```

Note that the keyword `return` is necessary here in order to jump out of the function. The invariant is again $$a[0..k)=b[0..k) ∧ 0 \leq k \leq n$$Reasoning about a function written in this way is a little messy, as one needs to also check the right thing is done at the premature `return`.


###### String Searching

Now consider the problem of searching for an occurrence of one string `patt` of size `K` in another string `line` of length `N`.

More precisely, we want to set a Boolean variable to true if the smaller pattern exists in the larger string. Thus, we can capture the postcondition as

```
post: returns found s.t.
	found = (line[i..i + K) = patt[0..K)), for some i ∈ [0..N − K + 1))
```

This suggests using a variable `j` to record the values of `i` that we've tried so far; i.e. we use the invariant $$\begin{aligned} & \text { found }=(\text { line }[i . . i+\mathrm{K})=\operatorname{patt}[0 . . \mathrm{K}), \text { for some } i \in[0 . . \mathrm{j})) \\ & \wedge 0 \leq \mathrm{j} \leq \mathrm{N}-\mathrm{K}+1\end{aligned}$$Using this invariant, we get this code structure:

```
var j = 0; var found = false
while(j <= N-K && !found){
	found = line[j..j+K) == patt[0..K) // <-- ** Pseudocode **
	j = j+1
}
// I && (j=N-K+1 || found)
// found = ( line[i..i+K) = patt[0..K) for some i in [0..N-K+1) )
```

where the pseudocode `found = line[j..j+K) == patt[0..K)` is just an instance of our earlier problem of comparing two arrays of characters for equality, so we can adapt that code.

Here's the search function:

```
def search(patt: Array[Char], line: Array[Char]) : Boolean = {
	val K = patt.size; val N = line.size
	// Invariant: I: found = (line[i..i+K) = patt[0..K) for
	// some i in [0..j)) and 0 <= j <= N-K
	var j = 0; var found = false
	while(j <= N-K && !found){
		// set found if line[j..j+K) = patt[0..K)
		// Invariant: line[j..j+k) = patt[0..k)
		var k = 0
		while(k<K && line(j+k)==patt(k)) k = k+1
		found = (k==K)
		j = j+1
	}
	// I && (j=N-K+1 || found)
	// found = ( line[i..i+K) = patt[0..K) for some i in [0..N-K+1) )
	found
}
```

The `unix` utility `grep` takes a string `patt` and a file name `file`, and prints every line from `file` that contains `patt`. We can use the `search` function to implementation `grep`:

```
object Grep {
	/** Does patt appear in line? */
	def search(patt: Array[Char], line: Array[Char]) : Boolean = ...
	
	def main(args: Array[String]) : Unit = {
		require(args.size==2)
		val patt = args(0).toArray
		val file = args(1)
		val lines = scala.io.Source.fromFile(file).getLines()
		for(line <- lines)
		if(search(patt,line.toArray)) println(line)
	}
}
```
