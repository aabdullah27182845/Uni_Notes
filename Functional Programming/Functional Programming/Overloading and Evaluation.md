
#### 4.1 [[Evaluation]]

Evaluation reduces expressions to values: what is a value? One way of thinking of this is that is is an expression that cannot be evaluated further, often called a normal form. 
- Haskell evaluates expressions by rewriting expressions, replacing left-hand sides of the equations by right-hand sides, until it reaches a normal form.
- There are several strategies for reducing an expression to normal form. These are:
	- Eager
	- Normal Order
	- Lazy

Here the costs are similar, but the order is different.

> `inf :: Integer`
> `inf = 1 + inf`

> `const :: a -> b -> a`
> `const x y = x`

where `const` is standard: `const x y = x`. In an eager evaluation, `const 3 inf` will never be properly evaluated, but in lazy evaluation, the answer of 3 will be given correctly.

Eager evaluation may fail to find a normal form. If there is a normal form, normal order reduction will find it, but might be expensive. Lazy evaluation is safe as normal order, but is usually less expensive because it tries to avoid duplicating work.


#### 4.2 [[Recursive Types]]

If the type being defined appears on the right hand side of a data definition, such as

> `data List a = Nil | Cons a (List a)`

it allows for recursive values: Nil, Cons 1 Nil, Cons 2 (Cons 2 Nil), … are all of type List Int. In fact, the built-in list type `[a]` is like this, but with constructors `[]` and `(:)`. The notation `[1,2,3]` is shorthand fir `1 : (2 : (3 : []))`.

Functions over recursive types are also naturally defined by recursion. Functions over lists will often be naturally expressed by two equations: one for the empty case and one for the non-empty case, and the non-empty case will often include a recursive call of the same function.

Take for example
	`map f xs = [f x | x <- xs]`
then you can calculate that
	`map f     [] = []`
	`map f (x:xs) = f x : map f xs`
and these two equations will do perfectly well as a definition.


#### 4.3 [[Recursion]]

This function calculates a factorial:

> `fact :: Integer -> Integer`
> `fact 0 = 1`
> `fact n = n * fact (n-1)`

That is, $fact \; n = n!$, at least for $n \geq 0$ . Why is this?

Provided that $n > 0$, the RHS is $n \times fact \; (n-1) = n \times (n-1)!$, and if $n = 0$ the RHS is $1 = 0!$. This is effectively an induction proof in which the inductive hypothesis is that the recursive calls on the RHS satisfy the invariant that $fact \; n = n!$.

Now, why is this recursion safe, whereas the one for `inf` is not? Because the recursive calls are all smaller in the sense that we can fine a variant which is smaller for all recursive calls, and bounded below: in this case the argument n will do. If recursion is to terminate since the base case is well defined and the recursive cases are all headed towards it, then it will eliminate.


#### 4.4 [[Type Classes]]

Some functions (such as equality) are polymorphic, but not in the same regular way as parametric polymorphism. For example

> `data UPair a = UPair a a`

might represent unordered pairs, for which you would want `UPair x y` to equal `UPair y x`. The mechanism for this ad-hoc polymorphism in Haskell is the type class. The type of equality is
	`(==) :: Eq a => a -> a -> Bool`
which you can read as "provided that a is an `Eq` type, `a → a → Bool`". What does it take to be an `Eq`-type? A definition of `(==)`!

The concept of an `Eq`-type is introduced by a class declaration:
	`class Eq a where`
	`(==) :: a -> a -> Bool`
and a type such as `UPair a` is put that in that class by matching instance declaration:

```
> instance Eq a => Eq (UPair a) where
> 	Upair p q == UPair r s | p==r      = q==s
> 	                       | p==s      = q==r
> 	                       | otherwise = False  
```

or more succinctly

```
> instance Eq a => Eq (UPair a) where
> UPair p q == UPair r s = (p==r && q==s)||(p==s && q==r)
```

The instance requires `Eq a` in order to implement the equality tests on the components. It would be usual to make sure that any definition of `(==)` behaved like equality, or at least an equivalence: that it was reflexive, and transitive. Anything else would be confusing, but nothing enforces this.

In fact, the class declaration for `Eq` is more like
	`class Eq a where`
		`(==) :: a -> a -> Bool`
		`x == y = not (x /= y)`
		`(/=) :: a -> a -> Bool`
		`x /= y = not (x == y)`
so that it also provides an inequality test. However, an instance need only provide either an implementation of `(==)` or one of `(/=)` and the equation in the class declaration will define the other.

Similarly, the instance of the equality class for lists
	`instance Eq a => Eq [a] where`
		`[]   == []   = True`
		`x:xs == y:ys = x == y && xs == ys`
		`_    ==    _ = False`
includes equality on values of a (justified by the `Eq a` definition earlier) and a recursive call of the equality test on lists.

The underlines are dummy arguments, which need not to be named because their values are not used. Names could be used, but the convention of the un-named underline is good documentation because the reader does not have to check that a named argument is in fact not used.


#### 4.5 [[Non-termination]]

What is the value of an expression like `1 'div' 0`? What is the value of an expression like `length [0..]`? The interpreter gives it no value, but when we are doing mathematics, it is useful to have a value (so that all functions are total). By definition, this is a special value  `⊥`, or rather there are values `⊥` of every type. We identify all kinds of error with this value.

So every constructed type has an additional `⊥` value distinct from the defined values. There are three Boolean values, and ten pairs of Booleans: the nine actual pairs in which the components take each value of three values, plus an entirely undefined value.

There are many partially defined lists such as `⊥: ⊥: []`, which is a list of exactly two unknown things, that is `[⊥,⊥]`, and $1:2:⊥$ which is a list of at least two things, the first two of which are known.

Haskell can not be expected to 'produce' a bottom when given an expression whose value is $⊥$. Sometimes it will produce an error message, sometimes it will remain silent for a very long time, sometimes the Haskell interpreter will crash … in principle, anything can happen.

Notice that you cannot expect to test for $⊥$ in a program. It is provably impossible to decide mechanically whether an arbitrary computation will terminate. The equation $f \; x =(x \neq ⊥)$ makes sense as a mathematical definition, but

> `f x = x /= bottom where bottom = bottom`

does not compute this function. It is incomputable by the decidability problem.


#### 4.6 [[Strict Functions]]

Some functions always demand the value of their arguments, even with normal order reduction. For example, arithmetic functions like `(+)` need both their arguments to be evaluated: $0 + undefined$ and $undefined + 1$ are both undefined; however `const undefined 1` is undefined, but `const 0 undefined` equals zero.

A normal $f$ is strict if $f \; ⊥ \; = \; ⊥$, so (+) is strict in both arguments, and `const` is strict in its first argument but not in its second. Eager evaluation would make all functions strict, so normal order evaluation is more faithful to the mathematical intention. Crucially, constructors are not strict, and in particular, `(:)` is neither strict in the first element of the list nor in the rest of the list. This allows lazy computations to produce infinite lists a little bit at a time.

Strictness is a way of checking in the mathematics whether a value is needed in the computation. A function which uses its arguments is necessarily strict in that argument. A good compiler might analyse the strictness of functions to decide whether the value of an argument is going to be needed, and use the more efficient eager strategy on that argument.


#### 4.7 [[Information ordering and computable functions]]

This is definitely not on the undergraduate syllabus. It is left out unless further revision may be less exhausting... 