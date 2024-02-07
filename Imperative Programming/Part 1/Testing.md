We will begin by talking about bugs in software. A software bug is an error, flaw, mistake, failure, fault or simply an "undocumented feature" in a computer program that prevents it from behaving as intended. 

Bugs will happen regardless of experience or effort of the developer. In the industry, there are around 10-100 bugs per 1000 lines of code after coding, and 1-5 bugs per 1,000 lines of code that aren't detected after delivery.

The reason as to why they occur so often is because Humans have limited ability to anticipate all possible inputs and outputs, and specify the desired response to each set of valid inputs. Humans also tend to not understand all possible execution paths of their program.

We can deal with unanticipated inputs is by rejecting invalid inputs. We do this by documenting pre-conditions, checking inputs for validity, and throwing exceptions for invalid inputs. Another option is to use a neutral value or to find the closest legal value.

The first version of `exp` had precondition `n >= 0`, but checked in the `main` function that the precondition was met:

```
object RecExp{
	/** Calculate x^n Pre: n >= 0 Post: returns x^n */
	def exp(x: Double, n: Long) : Double = if(n==0) 1 else x*exp(x,n-1)
	
	def main(args: Array[String]) : Unit = {
		val x = args(0).toDouble
		val n = args(1).toLong
		if(n>=0) println(x.toString+"^"+n+" = "+exp(x,n))
		else println("Error: second argument should be non-negative")
	}
}
```

If the precondition for the function is not met, then what should we do? The real answer to the question here is that "it depends".  We can designate hierarchy of errors:
- Level 1 :: If the error can be fixed safely, then fix it. If need be, warn the user.
- Level 2 :: If the error could be caused by user input then throw exception up to calling code, since the calling code should have enough context to fix the problem.
- Level 3 :: If the error should not happen under normal circumstances, then trip an assertion.

###### Scala Exceptions

`require` throws an `IllegalArgumentException` which can be caught and dealt with. `assert` throws an `AssertionError` and is meant to be more serious.

```
// Attempts to use exp function without checking precondition.
// Does something else when precondition fails
def main(args:Array[String]) : Unit = {
	val x = args(0).toDouble; val n = args(1).toLong
	var ans = 0.0
	try {
	ans = exp(x,n)
	} catch {
	case iae: IllegalArgumentException =>
	{println("Using library function"); ans=Math.pow(x,n.toDouble)}
	case ae: AssertionError => {/*Stop!*/}
	}
	println(x.toString+"^"+n+" = "+ans)
}
```

Assertions therefore check for "impossible" conditions. They catch bugs in development and they serve as documentation - a type of insurance against future analysis. They are also good sanity checks for your program.


###### Test-driven Development

"There were so many assumptions in the requirements that were easy to miss or were deemed too trivial by the requirements gatherers. These began showing up when writing tests", (Gaurav Sood)

You write code to make tests pass instead of writing tests to verify your program. You may invest time early in writing tests which leads to saving time later. TDD reduces bug density.

You can take the following steps in order to follow this way of development:

1. Write tests for a new feature
2. Compile
3. Fix compilation errors
4. Run new test and see it fail
5. Write code to make test pass
6. Run all tests and see them past
7. Refactor as needed

Onto Unit testing. Unit testing is performed on an individual unit of a program such as a function. They are usually small and check only one functionality. The tests are independent from each other, and independent in as much as possible from the implementation.

Writing unit tests also allows re-testing if you subsequently change your code. Of course, no feasible amount of testing is guaranteed to find all bugs. "Program testing can be used to show the presence of bugs, but never to show their absence". (E. Dijkstra)

Here is a `main` method that tests `exp`

```
import FastExp.exp
def main(args: Array[String]) : Unit = {
	assert(exp(2.0,3) == 8.0)
	assert(exp(2.0,8) == 256.0)
	assert(exp(2.0,0) == 1.0)
}
```

`assert` raises an exception if its argument is false. We've seen that it's similar to `require`, which is conventionally used for preconditions. If a test fails then `assert` by default will "stop the world" and no other tests will be run.

However, it's normally better to use a proper testing framework, to allow tests to be re-run. `ScalaTest` and `ScalaCheck` are testing frameworks for Scala.

A `ScalaTest` script is simply a Scala program such as the following: 

```
import org.scalatest.funsuite.AnyFunSuite
// or import org.scalatest.FunSuite with
// ScalaTest 3.0 or earlier
import FastExp.exp

class TestExp extends AnyFunSuite{ // FunSuite in ScalaTest 3.0
	test("2^3 = 8"){ assert(exp(2.0,3) == 8.0) }
	test("2^8 = 256"){ assert(exp(2.0,8) == 256.0) }
	test("2^0 = 1"){ assert(exp(2.0,0) == 1.0) }
}
```

The function `test` takes a string which names the test, and some code defining the test.

If an assertion fails, the error message you get back might not be very useful. An assertion such as 

```
test("2^3 = 8"){ assert(exp(2.0,3) === 8.0) }
```

gives a better error message.

A test can also check that a function throws an exception, for example: 

```
test("negative exponent"){
	intercept[IllegalArgumentException]{ exp(2.0,-1) } }
```

When `ScalaTest` encounters a test, it does not run it immediately, but it adds it to a collection. This means that the following code with give an error

```
class TestTest extends AnyFunSuite{ // FunSuite in ScalaTest 3.0
	var x = 0
	test("x=0"){ assert(x===0) }
	x = 1
	test("x=1"){ assert(x===1) }
}
```

Hence, setting up an individual test should be included in the test: 

```
var x = 0
//...
test("x=1"){
	x=1
	assert(x===1)
}
```


###### Floating point calculations

So far we have been able to give a definitive pass to tests. Most of the test we might want to try fail however. Here is an example:

`test("0.1^10 = 1E-10"){ assert(exp(0.1,10) === 1e-10) }`

gives

```
-  0.1^10 = 1E-10 *** FAILED ***
   1.0000000000000011E-10 did not equal 1.0E-10 (TestExpApprox.scala:17)
```

Why is this? Floating point equality tests are rarely a good idea. Calculations can be off by a relative error due to representation error or a rounding error.

```
import org.scalatest.funsuite.AnyFunSuite
import FastExp.exp
import org.scalactic.Tolerance._
class TestExpApprox extends AnyFunSuite{
	//...
	// Floating point comparison should allow for rounding
	test("0.1^10 = 1E-10"){ assert(exp(0.1,10) === 1e-10) }
	test("0.1^10 ~= 1E-10"){ assert(exp(0.1,10) === 1e-10 +- 1e-25) }
}
```

Now, we move onto black box testing. This treats a component such as a function or an object as a "black box".
- Tests influenced by knowledge of component specification and interface.
- Tests have no knowledge about internal organisation of a component.

The advantages of doing so is that it has a stricter focus on specification and the user's point of view. The tests can be truly independent of the developers. The tests can be written as soon as the specification is known - before development work starts.

White box testing on the other hand has knowledge of a component's implementation. A test for an object might also have privileged access to its private data. Tests influenced by knowledge of components internals, and tests may check that invariants are not violated.

The advantages of doing this is that testing it more thorough, with the possibility of covering many different paths through the code. The exposure of data allows us to check preconditions, invariants, etc.

One testing technique is to divide all possible inputs into equivalence classes, in which the behaviour of the component ought to be the same. Test one input from each equivalence class: assuming that because two inputs from the same class should behave the same then they actually will. 

For example, inputs to `CalculateArgument` of a complex number might be split into 5 classes. Weak equivalence class testing is to to test one representative from each equivalence class for each input, whereas Strong equivalence class testing tests one representative in the Cartesian product.


###### Boundary Value Testing

Equivalence classes naturally gives us boundaries: e.g. what happens to `CalculateArgument` when the input is close to the origin? Dogma says that most code fails on inputs which are near to the boundaries. Testing should be concentrated near to the boundary: if the precondition of a function is `age >= 16` then test that it gives an error at `age = 15` and is successful at `age = 16`.

A change in a system can break seemingly unrelated tasks. Preventing such regressions can be done by changing the system, adding test to demonstrate correctness, running the entire test suite again, and then to fix more possible regressions. 

If tests are not run immediately, then it will be hard to discover what caused a regression.


Here's a closing remark: "Any fool can write code that a computer can understand. Good programmers write code that humans can understand." (Martin Fowler)