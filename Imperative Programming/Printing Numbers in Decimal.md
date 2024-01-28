
We begin this section by specifying our problem: Given a positive integer `t`, we want to calculate its decimal digits, and store them in an array `d[0..N)`, with the least significant digit at the start of the array.

For example, for a given `t = 12345`, we will set `n=5` and 

```
d(0) = 5, d(1) = 4, d(2) = 3, d(3) = 2, d(4) = 1
```

Let's write `t@i` for the digit t that should be put in `d(i)`:
	`t@i = (t div 10^i) mod 10`

Our correctness condition will be that we end up with the correct values :

	pre  : t > 0
	post : (∀i ∈ [0..N) . q d(i) = t@i) ∧ t < 10^N) 

Our first program will calculate the digits from right-to-left. Looking at the correctness condition, it seems sensible to have an invariant

	I_1 = = ∀i ∈ [0..n) . q d(i) = t@i

i.e. all the digits calculated so far are correct. Since `t` is an `Int, t < 2^31 < 10^10`, so 10 digits are enough.

```scala
val N = 10
val d = new Array[Int](N)
```

we will also have the invariant

	0 <= n <= N

Here's a very simple program following that invariant:

```scala
// Invariant: I = (for all i in [0..n), d(i) = t@i) && 0<=n<=N
// Variant: N-n
var n = 0
while(n < N){
	var x = 1; for(j <- 1 to n) x *= 10 // Set x = 10^n
	d(n) = (t/x)%10 // d(n) = t@n
	n = n+1 // I
}
// I && n=N, so all digits calculated
```

And here is the main method that is executed at runtime:

```scala
def main(args:Array[String]): Unit ={
	// Get the argument from the command line
	require(args.size>0); val t = args(0).toInt; require(t>0)
	// Initialise array. 2^31 < 10^10, so 10 digits is enough to
	// represent an Int
	val N = 10; val d = new Array[Int](N)
	... // Above code to calculate the entries for d
	// Print out the digits
	for(i <- n-1 to 0 by -1) print(d(i))
	println()
}
```

But the calculation of the digits is rather inefficient, as it calculates `10^n` from scratch in each iteration; and it also calculates leading 0's if `t` happens to be less than `10^9`.

Here is an iteration of this algorithm in order to make it better. 

It would be better to store the value of `10^n` in a variable `x` from one iteration to the other. That is, we strengthen the invariant by adding a clause: `x = 10^n`

This will also allow us to stop once `t < x`:

```scala
// Invariant: I = (for all i in [0..n), d(i) = t@i) && x = 10^n
var n = 0; var x = 1
while(t >= x){
	d(n) = (t/x)%10 // d(n) = t@n
	x *= 10 // x = 10^(n+1)
	n = n+1 // I
}
// I && t < x = 10^n, so all digits calculated
```

WARNING: This algorithm doesn't function as intended (incorrect) for `10^9 < t < 2^31` since the iterated `x` keeps overflowing. (Do try to run this in Scala to see).

Let's ignore this for now. To prove termination, we could take the variant to be `10*t - x`; but then we need to add the clause `x <= 10*t` to the invariant. Alternatively, we could take the variant to be `1 + floor(log10 t) − n`; and again we will need to add the clause `x <= 10*t` to the invariant.

The previous program used three multiplications/divisions on each iteration. We can do better. Rather than dividing `x = 10^n` on each iteration, we can use a variable `u` to store the value of `t div 10^n` from one iteration to the next. That is, we strengthen the invariant with the clause 

	`u = t div 10^n`

Note that the termination condition `t < 10^n` is equivalent to `u = 0`.

And here is the code alongside the invariant:

	Î = (∀i ∈ [0..n) . q d(i) = t@i) ∧ u = t div 10^n

```scala
var n = 0; var u = t // I
while(u != 0){
	d(n) = u%10 // d(n) = (t div 10^n)%10 = t@n
	u = u/10 // u = t div 10^(n+1)
	n = n+1 // I
}
// I && t<10^n, so all digits have been calculated
```

The final program will calculate the digits from left-to-right. Thinking about the correctness condition, it seems sensible to have an invariant including

	I_1̂= (∀i ∈ [k..n) q d(i) = t@i) ∧ 0 ≤ k ≤ n

i.e. we've calculated the `n-k` most significant digits correctly. We'll need to work out the value of `n` initially, and set `k = n`.

At each iteration, we'll calculate the value for `d(k-1)` and decrease `k`. This will continue until `k=0`.

We need to calculate the value for `d(k-1)`, i.e.

	t@k-1 = (t div 10^(k-1)) mod 10 = (t mod 10^k) div (10^k-1)

I have proved this equality somewhere in the Good-Notes booklet in the iPad. Bearing in mind that we'll later have to calculate `t@k-1, t@k-2, ...`, it makes sense to use variables `v` and `x` such that our invariant is:

	I_2̂= v = t mod 10k ∧ x = 10k

And so, here is the code for this improved version:

```scala
// Find the number of digits (n); calculate 10^n at the same time.
// (Ignore overflow.)
var n = 1; var x = 10 // Invariant x = 10^n
while(t >= x){
	n = n+1; x = 10*x
}
// t < x = 10^n
// Invariant:
// (for all i in [k..n), d(i) = t@i) && v = t%(10^k) && x = 10^k
var k = n; var v = t
while(k > 0){
	k = k-1; x = x/10 // v = t % 10^(k+1), x = 10^k
	d(k) = v/x // d(k) = (t%10^(k+1))/10^k = (t/10^k)%10 = t@k
	v = v%x // v = (t % 10^(k+1)) % 10^k = t % 10^k
}
```

And so, the code above can be embedded into the `main` function. Alternatively, we could print out the digits as we calculate them.

A good invariant will explain how the program works. In the final program above, the clause 

	∀i ∈ [k..n) . d(i) = t@

explains what we have achieved so far. The clauses `v = t mod 10^k ∧ x = 10^k` explain the roles of `v` and `x`. These clauses are necessary to justify that the value written into `d(k)` was correct. Having a clear statement of the values held in these variables helped come up with correct code, and avoided errors such as off-by-one errors.

If you have some state that is carried forward from one iteration to the next, then the invariant should explain that.

Finally, the clause `0 <= k <= n` helped document the range of the control variable `k`.
