
The fold on lists, an instance of `foldr`, is

> `fold :: (a -> b -> b) -> b -> [a] -> b`
> `fold cons nil    []  = nil`
> `fold cons nil (x:xs) = cons x (fold cons nil xs)`

and `fold (:) [] = id`. It captures a possible pattern of computation for many functions on lists.

```
sum = fold (+) 0
product = fold (*) 1
concat = fold (++) []
map f = fold ((:) . f) []
```

notice that none of these equations is recursive: only equation defining fold are recursive. We might hope to be able to prove things about the others, such as 

```
sum (xs ++ ys) = sum xs + sum ys
product (xs ++ ys) = product xs * product ys
concat (xs ++ ys) = concat xs ++ concat ys
map f (xs ++ ys) = map f xs ++ map f ys
```

without resorting to induction for every one of them.

What is needed is a proof that

	fold c n (xs ++ ys) = fold c n xs ⊕ fold c n ys

Setting out to prove, just once, will reveal what relationship has to exist between $c$, $n$, and $(⊕)$. 

	fold c ([] ++ ys)
	= [ definition of (++)]
	fold c n ys

	fold c n [] ⊕ fold c n ys
	= [definition of fold]
	n ⊕ fold c n ys

so these will be equal if $x = n ⊕ x \; \; \forall \;x$.

	fold c n ((x:xs) ++ ys)
	= [definition of (++)]
	fold c n (x : (xs++ys))
	= [definition of fold]
	c x (fold c n (xs ++ ys))
	= [inductive hypothesis]
	c x (fold c n xs ⊕ fold c n ys)

	fold c n (x : xs) ⊕ fold c n ys
	= [definition of fold]
	c x (fold c n xs) ⊕ fold c n ys

and these will be equal if $x' \; c' \; (y ⊕ z) = (x' \; c' \; y) ⊕ z$. Furthermore, 

	fold c n (⊥ ++ ys)
	= [definition of (++)]
	fold c n ⊥
	= [fold is strict in the list]
	⊥

	fold c n ⊥ ⊕ fold c n ys
	= {fold is strict in the list}
	⊥ ⊕ fold c n ys

and these will be equal if $(⊕)$ is strict, that is if the operator is strict in its left argument.

None of these three properties involves any recursion so they can be checked by induction-free proofs.


#### 10.1 [[Fusion]]

The most generally useful property of folds is that, given the right properties of $f, g, h, a$ and $b$, 

	f . fold g a = fold h b

These are functions of a list so the proof of equality is by induction on an argument list

	(f . fold g a) ⊥
	= [definition of (.)]
	f (fold g a ⊥)
	= [fold is strict in the list]
	f ⊥

	fold h b ⊥
	= [fold is strict in the list]
	⊥

so f must be strict.

	(f . fold g a) []
	= [definition of (.)]
	f (fold g a [])
	= [definition of fold]
	f a

	fold h b []
	= [definition of fold]
	b

so `b = f a`

	(f . fold g a) (x:xs)
	= [definition of (.)]
	f (fold g a (x:xs))
	= [definition of fold]
	f (g x (fold g a xs))
	= [definition of (.)]
	(f . g x) (fold g a xs)

	fold h b (x:xs)
	= [definition of fold]
	h x (fold h b xs)
	= [inductive hypothesis]
	h x ((f . fold g a) xs)
	= [definition of (.) twice]
	(h x . f) (fold g a xs)

and these will be equal at least if `h x . f =  f . g x`, or equivalently if $h \; x \; (f \; y) = f \; (g \;x\;y)$.

![[Pasted image 20240110132120.png]]

Most of the laws that we have used that are about functions that are folds have been instances of fusion. We have also been relying on a special case of fusion to show that some function $f$ on lists is a fold, because

	f
	= {unit of composition}
	f . id
	= {fold of constructors}
	f. (fold (:) [])
	= {fusion}
	fold h (f [])

provided that $f$ is strict, and $f \;(x:xs)=h\;x\;(f\;xs)$.


#### 10.2 [[Left and Right Folds]]

One intuition about fold is that it produces a right-heavy expression where the arguments replace the constructors of a list:

$fold\;(⊕)\;e\;[x_0,x_1,x_2,...,x_n]\;\;\;=(x_0⊕(x_1⊕(x_2⊕...(x_n⊕e)...)))$ 

There is a predefined function $foldr$ which when restricted to lists agrees with `fold`. We might compute a similar left-heavy expression

$loop\;(⊕)\;e\;[x_0,x_1,x_2,...,x_n]\;\;\;=(...(((e⊕x_0)⊕x_1)⊕x_2)⊕...x_n)$ 

and might specify this by

$loop\;s\;n = fold \;(flip\;s)\;n\cdot reverse$

and calculate from this that it is strict; that

```
loop s n []
= [specification]
(fold (flip s) n . reverse) []
= [composition]
fold (flip s) n (reverse [])
= [definition of reverse]
fold (flip s) n []
= [definition of fold]
n
```

and that

```
loop s n (x:xs)
= [specification]
(fold (flip s) n . reverse) (x:xs)
= [composition]
fold (flip s) n (reverse xs ++ [x])
= [unjustified lemma (yet)]
fold (flip s) (fold (flip s) n [x]) (reverse xs)
= [definition of fold, twice]
fold (flip s) (flip s x n) (reverse xs)
= [definition of flip]
fold (flip s) (s n x) (reverse xs)
= [composition]
(fold (flip s) (s n x) . reverse) xs
= [specification]
loop s (s n xs) xs
```

And now, this justifies defining

> `loop s n    []  = n`
> `loop s n (x:xs) = loop s (s n x) xs`

and this is essentially the same as the predefined `foldl`


#### 10.3 [[Scans]]

One commonly needs to think about 'partial sums'. The natural thing to think of first for lists is

$scan\;c\;n\;=\;\;map\;(fold\;c\;n)\cdot tails$

where the tails of a list are all the suffix segments, in decreasing order of length.

> `tails :: [a] -> [[a]]`
> `tails     [] = [[]]`
> `tails (x:xs) = (x:xs): tails xs`

There is a very similar standard function `tails` in `Data.List`.

In the way you probably expect, `tails` can be cast as a `fold` because

```
tails (x:xs)
= {definition of tails}
(x:xs) : tails xs
= {head (tails xs) = xs}
(x: head ys) : ys where ys = tails xs
```

so `tails = fold g [[]]` where $g\;x\;ys = (x : head\; ys) : ys$. This means that if the conditions of fusion are satisfied, `scan` can be expressed as a `fold`.

Firstly, `map (fold c n)` is strict; then

	  map (fold c n [[]])
	= {definition of map}
	  [fold c n []]
	= {definition of fold}
	  []

and then 

```
map (fold c n) (g x ys)
= {definition of g}
map (fold c n) ((x:head ys):ys)
= {definition of map}
(fold c n (x:head ys) : map (fold c n) ys)
= {definition of fold}
c x (fold c n (head ys)) : map (fold c n) ys
= [f . head = head . map f]
c x (head zs) : zs where zs = map (fold c n) ys
```

from which we conclude that

```
scan c n
= [specification]
map (fold c n) . tails
= [fusion]
fold h [n] where h x zs = c x (head zs) : zs
```

Notice that executing the specification directly gives a quadratic algorithm: for a list $xs$ of length $n$ there are about $\frac{1}{2} n^2$ applications of c. However, there are only $n$ applications of $h$, each of which calls $c$ exactly once. The result is a linear algorithm for

> `scan c n = fold h [n] where h x zs = c x (head zs):zs`

The predefined function `scanr` is equal to `scan`, and even has the same strictness.


#### 10.4 [[Aside: the names of fold and loop]]

In text books, you will find fold being called `foldr`. It would have been natural for the arguments to be in the same order as the constructors appear in the type of lists, but the name and the argument order of `foldr` have been the same at least since David Turner's SASL in the mid 1970s. The first argument has also been called `f` and the second either `r` or `e`, and you will find `foldr f e` in textbooks. 

Do bear in mind where the `cons` of a certain type turn lists into a type of object (like a constructor would), `snoc` is the opposite of that, where it turns the object into a list by appending. 


#### 10.5 [[Aside: Strictness]]

The function `tails` defined above is strict, but `Data.List.tails` is not. However, the implementation of `scan` as a fold is strict, because folds are innately strict themselves.

Had we defied `tails` to be non-strict,

> `tails xs = xs : if null xs then [] else tails (tail xs)`

it would not have been possible to implement it by a fold. The rest of the derivation of the implementation of `scan` as a fold is sound.

You might argue that the efficient implementation of `scan` is not a faithful implementation of `map (fold c n) . tails` if `tails` is not strict.


#### 10.6 [[Aside: Fusion and Computability]]

The statement of the fold fusion law `f . fold g a = fold h b` was proved by showing that `f (fold g a xs) = fold h b xs` by induction on `xs`, but no mention was made in [[Fusion]] of chain completeness. In fact the three conditions for fold fusion are only enough to prove this equality for finite or partial `xs`.

To complete the proof by induction for infinite `xs` it is necessary to show that the proposition is chain complete with respect to `xs`.

Fortunately if both sides are computable functions of `xs`, as they would be if they were Haskell-definable functions, an equation is chain complete.

However an equation proved to be true on all partial lists does not necessarily hold at infinite lists if the two sides are non-computable functions of the list, and fold fusion cannot necessarily be applied to such functions.