
Recall that we first defined the reverse function by recursion:

	reverse [] = []
	reverse (x:xs) = reverse xs ++ [x]
	              = snoc (reverse xs) xs
	              = flip snoc x (reverse xs)

Deduce from this that

	reverse = fold (flip snoc) [] where snoc xs x = xs ++ [x]

However, this algorithm is quadratic: it takes about $\frac{1}{2}n^2$ steps to reverse a list of length $n$. Why is this? Each catenation

	[] ++ ys = ys
	(x:xs) ++ ys = x:(xs ++ ys)

takes a number of steps linear in the length of its left argument. It follows that `snoc` takes a number of steps linear in its first argument, and `reverse` applies `snoc` to each tail of its argument/

The insight is that we could accumulate the answer: invent

	revcat ys xs = reverse xs ++ ys

Notice that this is intended as a specification, not the definition for execution: evaluating this would be at least as bad as the existing `reverse`. We could however use `revcat` to calculate

	reverse xs
	= {unit of (++)}
	reverse xs ++ []
	= {specification of revcat}
	revcat [] xs

and then make the `(++)` vanish, by synthesizing a `(++)`-less recursive definition of revcat

	revcat ys []
	= {specification of revcat}
	reverse [] ++ ys
	= {definition of reverse}
	[] ++ ys
	= {definition of (++)}
	ys

and for non-empty lists

	revcat ys (x : xs)
	= {specification of revcat}
	reverse (x:xs) ++ ys
	= {definition of reverse}
	(reverse xs ++ [x]) ++ ys
	= {associativity of (++)}
	reverse xs ++ ([x] ++ ys)
	= {definition of (++)}
	reverse xs ++ (x:ys)
	= {specification of revcat}
	revcat (x:ys) xs

This gives us a definition

> `reverse = revcat []`
> `         where revcat ys    []  = ys`
> `               revcat ys (x:xs) = revcat (x:ys) xs`

This one is linear in length of the list being reversed: each cell of `revcat` corresponds to one of the `cons`-es in the list, and each call does a constant amount of work before the recursive call.

The correspondence between `cons`-es and calls of `revcat` suggest that we think of a fold, but it is not a fold on `cons`-lists. Compare it with

	loop s n [] = n
	loop s n (x:xs) = loop s (s n x) xs

and by inspection, $revcat = loop\;(flip\;(:))$ so

	reverse = loop (flip (:)) []


#### 12.1 [[Flattening trees]]

The flatten function for

> `data BTree a = Leaf a | Fork (BTree a) (BTree b)`

is $flatten :: BTree \;\alpha → [\alpha]$ for which

	flatten (Leaf x) = [x]
	flatten (Fork ls rs) = flatten ls ++ flatten rs

The length of the result is the number of leaves in the tree, the size of the tree

	size = foldBTree (const 1) (+)

however in general, it takes more steps than that to produce it.

To flatten a balanced tree of size n, there will be a $(\mathrel{+ +})$ at the root that takes about $\frac{1}{2}n$ steps, below that two take $\frac{1}{4}n$ steps and so on, which amounts to about $\frac{1}{2}n\log n$ steps. If the tree has a long left spine, the algorithm can be as bad as quadratic.

As before, the insight is that to eliminate the $(\mathrel{+ +})$ we should specify

	flatcat t ys  = flatten t ++ ys

and synthesize

	flatcat (Leaf x) ys
	= {specification of flatcat}
	flatten (Leaf x) ++ ys
	= {definition of flatten}
	[x] ++ ys
	= {definition of (++)}
	x:ys

and

	flatcat (Fork ls rs) ys
	= {specification of flatcat}
	flatten (Fork ls rs) ++ ys
	= {definition of flatten}
	(flatten ls ++ flatten rs) ++ ys
	= {associativity of (++)}
	flatten ls ++ (flatten rs ++ ys)
	= {specification of flatcat}
	flatcat ls (flatcat rs ys)

so `flatcat = foldBTree (:) (.)` and `flatten t = foldBTree (:) (.) t []`. Relying on the associativity of $(\mathrel{++})$, synthesis has produced a linear algorithm from a less efficient one.


#### 12.2 [[Associativity and folds]]

When is $fold \;(\oplus)\; e = loop \;(\otimes)\; f$?

Suppose we try to prove this by induction. The assertion is an equation so is chain complete and both sides are strict by virtue of the definitions of `fold` and `loop`. Applying both sides to `[]` shows that it is necessary that $e = f$. The substantial part of the proof is

	fold (⊕) e (x:xs)
	= {definition of fold}
	x ⊕ fold (⊕) e xs
	= {lemma to be proved}
	loop (⊗) (e ⊗ x) xs
	= {definition of loop}
	loop (⊗) e (x:xs)

The essence of the result is the missing lemma, again to be proved by induction.

The lemma is chain complete. If $xs = \bot$ conclude that $x\oplus\bot=\bot\;\;\forall\;x$, so $(\oplus)$ must be strict in its second argument. If $xs = []$ conclude that $e\otimes x = x\oplus e$. The substantial part of the proof of the lemma is 

	loop (⊗) (e ⊗ x) (y:ys)
	= {definition of loop}
	loop (⊗) ((e ⊗ x) ⊗ y) ys
	= {suppose (a ⊗ b) ⊗ c = a ⊗ (b . c)}
	loop (⊗) (e ⊗ (x . y)) ys
	= {induction hypothesis}
	(x . y) ⊕ fold (⊕) e ys
	= {suppose (a . b) ⊕ c = a ⊕ (b ⊕ c)}
	x ⊕ (y ⊕ fold (⊕) e ys)
	= {definition of fold}
	x ⊕ fold (⊕) e (y:ys)

Notice that this is a proof for all values of x, and the induction hypothesis is that it holds for a particular finite list and all values in the left-hand side argument, in particular $(x \odot y)$. 

Collecting the requirements:

	fold (⊕) e = loop (⊗) e

is provided for right-strict $(⊕)$, provided $e\otimes x = x\oplus e$ and provided there is a $(\odot)$ for which $a \otimes(b \odot c) = (a \otimes b) \otimes c$ and $(a \odot b) \oplus c = a \oplus (b \oplus c)$. The obvious case is when all three of the operations are equal, are right-strict, are associative, and have $e$ as a left and right unit.

	sum     = fold (+) 0 = loop (+) 0
	product = fold (*) 1 = loop (*) 1
	concat  = fold (++) [] /= loop (++) []

The last inequality is possible because $xs \mathrel{++} \bot \neq \bot$. The fold form produces output when applied to an infinite list of lists provided at least one of them is non-empty, but the `loop` form cannot produce any output from an infinite input.


#### 12.3 [[Bounding Space]]

One reason for preferring `loop (+) 0` to `fold (+) 0` is that the fold is generally obliged to build up the whole expression before any evaluation:

```
fold (+) 0 [1, 2, 3, 4]
= 1 + fold (+) 0 [2, 3, 4]
= 1 + (2 + fold (+) 0 [3, 4])
= 1 + (2 + (3 + fold (+) 0 [4]))
= 1 + (2 + (3 + (4 + fold (+) 0 [ ])))
= 1 + (2 + (3 + (4 + 0)))
= 1 + (2 + (3 + 4))
= 1 + (2 + 7)
= 1 + 9
= 10
```

whereas the loop can safely evaluate the expression as it goes. In practice, because of lazy evaluation

```
loop (+) 0 [1, 2, 3, 4]
= loop (+) (0 + 1) [2, 3, 4]
= loop (+) ((0 + 1) + 2) [3, 4]
= loop (+) (((0 + 1) + 2) + 3) [4]
= loop (+) ((((0 + 1) + 2) + 3) + 4) [ ]
= (((0 + 1) + 2) + 3) + 4
= ((1 + 2) + 3) + 4
= (3 + 3) + 4
= 6 + 4
= 10
```

the same space build-up can happen. To prevent it, loop would have to be made strict in this argument.

> `loop' s   n      [] = n`
> `loop' s (!n) (x:xs) = loop' s (s n x) xs`

The $!$ decoration ensures that the argument is evaluated before the recursive call. 


#### 12.4 [[Fast Exponentiation]]

On the face of it, calculating $x^n$ appears to require about $n$ multiplications. But multiplication is associative, so $x^{2n} = (x^2)^n$ and $x^{2n}$ can be calculated in only one more multiplication than $x^n$. So we could specify $pow \;x\;n=x^n$ and synthesize

	pow x 0 = 1
	pow x n  | even n = pow (x*x) (n `div` 2)
	        | odd n = pow x (n-1) * x

This function will be called no more than $2\log n$ times in $x^n$. 

However, just like the fold version of product, this function must unnecessarily build up a big expression before any evaluation. Specify $power \;y\;x\;n = pow \;x\;n\times y$ and synthesize

```
> power y x 0 = pow x 0 × y
>             = 1 × y
>             = y
> power y x n | even n = pow x n × y
>                      = pow (x × x) (n div 2) × y
>                      = power y (x × x) (n div 2)
> power y x n | odd n = pow x n × y
>                     = (pow x (n − 1) × x) × y
>                     = pow x (n − 1) × (x × y)
>                     = power (x × y) x (n − 1)
```

We could also abstract on the multiplication:

> `power (*) y x n -- x^n*y`
> `      | n == 0 = y`
> `      | even n = power (*) y (x*x) (n 'div' 2)`
> `      | odd n  = power (*) (x*y) x (n-1)`

Notice that the development of this code used only the associativity of $(\times)$, so it will calculate other repeated operations such as repeated matrix multiplication.