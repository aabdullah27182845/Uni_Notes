
We will recap with the utility function for adding up milk from the last notes:

```scala
def findsum(a : Array[Int]) : Int = {
	val n = a.size
	var total = 0; var i = 0
	while (i < n) {
		total += a(i)
		i += 1
	}
	total
}
```

We want to be able to provide the arguments on the command line, e .g. 

`> scala Milk 3 1 4 0 3`

When extra arguments are provided on the command line, they are copied into the parameter `args` of the main function

```scala
def main(args : Array[String]) : Unit
```

Note that these are Strings; we will need to convert them into `Int`s in order to use our function. We can use the method provided by the `String` class `toInt` in order to do so, just apply this method to any instance of a string and it will return an integer for appropriate strings. 

Here is the main function for this program then:

```scala
def main(args : Array[String]) : Unit = {
	val n = args.size
	val a = new Array[Int](n)
	for (i <- until n) a(i) = args(i).toInt
	print(findSum(a))
}
```

The for-loop `for(x <- xs) body` executes `body` where `x` takes each value in the sequence `xs` in turn. 


###### For loops vs while loops

The `for` loop 

``` for(i <- a until b) Body ```

is equivalent to

```scala
var i = a
while (i < b) { Body; i = i+1 }
```

assuming `Body` doesn't change `i`.

There are very useful methods built into the `Array` class. One of them is called `map`. It can shorten programs with loops with just a single statement and an anonymous function that is to be applied an each element of the array. Here is an example

```scala
val a = args.map(_.toInt)  \\ or can be written in
val b = args.mao(x => x.toInt)
```

Sometimes, it may be necessary to specify the type of the variable in the anonymous function. As you can see from this example, they are often very useful.


#### [[Exponentiation]]

We will develop three programs to calculate `x^n`, where `x` and `n` are supplied by the user.
- We'll take `x` to be a `Double`, i.e. a 64-bit floating point number;
- We'll take `n` to be a non-negative `Long`, i.e. a 64-bit integer (so we can experiment with large values of `n`)

Here's a simple and straightforward definition of this function:

```scala
def exp(x: Double, n: Long) : Double = 
	if (n==0) 1 else x*exp(x,n-1)
```

Unfortunately, if we consider the binary length of the input of this program, we find it to run in exponential time, since a 64-bit number will require $2^{64}$ recursive steps in order to evaluate this.

Before we optimise our algorithm, which is actually an easy endeavour due to the simple nature of the computation, let's first consider an iterative definition for the same algorithm:

```scala
def exp(x: Double, n: Long) : Double = {
	require(n>=0)
	var y = 1.0 ; i : Long = 0
	while (i < n) {
		y = y*x; i += 1
	}
}
```

We should check again for correctness here with the use of an invariant. Our invariant here should be that `i <= n && y = x^i`. This is a strong enough invariant which will give us our desired postconditions.

Now, let us take on the endeavour of trying to develop a faster version. The first thought of reducing repeated multiplication is with the use of squares. If we have already computed $x^{n/2}$, there is no reason to iterate over the next `n/2` terms, and instead, just square $(x^{n/2})^2$ in order to get $x^n$. Given that this only works for even integers, we can accommodate for odd numbers by doing the following: $x\cdot(x^k)^2$ where $k = \frac{n-1}{2}$.

Our code will test if `k` is even, using the test `if (k%2 == 0)`. If `k` is even, we can calculate this as follows:

```scala
y * z^k = y * z^(2 * k/2)
        = y * (z^2) ^ (k/2)
        = y * (z*z) ^ (k/2)
```

So now, here is our main code:

```scala
def exp(x:Double, n:Long) : Double = {
	require(n>=0)
	var y = 1.0; var z = x; var k = n
	while (k>0) {
		if (k%2==0) {
			z = z*z; k = k/2
		} else {
			y = y*z; z = z*z; k = k/2
		}
	} 
	y
}
```

And now, finally, our code is linear asymptotically with regards to runtime and the binary length of our `n` parameter. That concludes the summary on the third lecture of the course.