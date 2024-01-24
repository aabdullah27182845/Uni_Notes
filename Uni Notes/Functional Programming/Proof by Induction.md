
The laws we use in equational reasoning about programs have to be justified. Because we are dealing with lists, which are a recursive data type, functions on lists are often defined by recursion, so proofs about them they will usually be justified by induction.

Think about

> `pow :: Num a => a -> Int -> a`
> `pow x 0 = 1`
> `pow x n = pow x (n-1) * x`

How would we prove

$pow \; x \; (m + n) = pow \; x \; m \times pow \; x \; n$

Being honest, we would really want the n to be a natural number, and 

> `pow x 0         = 1`
> `pow x n | n > 0 = pow x (n-1) * x`

is closer to it. In writing mathematics, however, one would be more likely to say

> `pow x 0     = 1`
> `pow x (n+1) = pow x n * x`

with the observation that since $n \in \mathbb{N}$, the n + 1 cannot be zero. Older textbooks may have Haskell definitions that look like this, but the syntactic form was misleading and has been removed from the language.


#### 9.1 [[Induction over natural numbers]]

A proof of $P(n)$ for every natural number n is an infinite number of proofs, one for $P(0)$, $P(1)$, and so on... Clearly providing all of those will take a long time. A better alternative would be to provide a systematic way of generating any of those proofs.

Every natural number is either 0, or is $n + 1$ for some natural number n. To prove $P(n)$ for every natural number n is enough to prove

1. $P(0)$
2. for every natural number n, if $P(n)$ then $P(n+1)$.

Since any natural number n can be made of a 0 and some number of applications of $(+1)$, the proof can be built out of a proof of the base case and some number of proofs from the base case all the way to the argumentative case.

So, back to our `pow` problem. This is how we would prove it.

```
  pow x (m+0)
= {unit of addition}
  pow x m
= {unit of multiplication}
  pow x m * 1
= {definition of pow}
  pow x m * pow x 0
```

and assuming, temporarily, $pow \; x \; (m + n) = pow\; x\; m \times pow\; x\; n$ for some n,

```
pow x (m + (n+1))
= {definition of (+) or by associativity}
pow x ((m+n) + 1)
= {definition of pow}
pow x (m+n) * x
= {induction hypothesis}
(pow x m * pow x n) * x
= {associativity of multiplication}
pow x m * (pow x n * x)
= {definition of pow}
pow x m * pow x (n+1)
```

Then we can discharge the assumption and read this as a proof of

	if pow x (m + n) = pow x m * pow x n then
	     pow x (m + (n+1)) = pow x m * pow x (n+1)

$\forall \; n \in \mathbb{N}$.


#### 9.2 [[The take lemma]]

Sometimes proofs about lists can be reduced to proofs about natural numbers. The 'take lemma' is the observation that two lists `xs` and `ys` are equal provided that `take n xs = take n ys` $\forall \; n \in \mathbb{N}$. This is a trivial observation for finite `xs` and `ys`, and a subtle one for infinite lists, which says that there is nothing in the infinite lists which does not appear in some finite prefix of the list.

That `take n xs = take n ys` can often be provided by induction on n, noting that the case `n = 0` always holds because both sides are null. For example, `map f (iterate f x) = iterate f (f x)` because

```
take (n + 1) (map f (iterate f x))
= { definition of iterate }
take (n + 1) (map f (x : iterate f (f x)))
= { definition of map }
take (n + 1) (f x : map f (iterate f (f x)))
= { definition of take }
f x : take n (map f (iterate f (f x)))
= { induction hypothesis, with f x substituted for x }
f x : take n (iterate f (f (f x)))
= { definition of take }
take (n + 1) (f x : iterate f (f (f x)))
= { definition of iterate }
take (n + 1) (iterate f (f x)))
```

Notice that a proof about n + 1 and x requires a hypothesis about n and $f\; x$. Formally, the induction hypothesis is that 'for all x' the result holds for n.

It should be clear that a proof by induction on n, like the one above, shows only that corresponding finite prefixes on `xs` and `ys` are equal. The force of the take lemma is that this means that `xs` and `ys` must be equal even if they are infinite.

An equivalent presentation of the take lemma uses the function

> `take' n    []  | n > 0 = []`
> `take' n (x:xs) | n > 0 = x : take' (n-1) xs`

which prunes its list argument to no more than n constructors, but leaves $⊥$ after that. It can be generalised to similar proof schemes for other data types, for example

> `data Stream a = Cons a (Stream a)`

the type of infinite streams which have no end, or types where there are a several possible last constructors.


#### 9.3 [[Induction over infinite lists]]

Every finite list is either `[]`, or is `x:xs` for some finite list `xs`. To prove $P(xs)$ for every finite list is enough to prove

1. $P([])$
2. for every finite list $xs$, if $P(xs)$, then $P(x:xs)$.

Recall that

> `(++) :: [a] -> [a] -> [a]`
> `[]     ++ ys = ys`
> `(x:xs) ++ ys = x:(xs++ys)`

We prove that `(xs++ys) ++ zs = xs ++ (ys+zs)` for all finite lists `xs` by induction on `xs`. Firstly for empty lists

```
([] ++ ys) ++ zs
= {by definition of (++)}
ys ++ zs
= {definition of (++)}
[] ++ (ys ++ zs)
```

then assuming `(xs ++ ys) ++ zs = xs ++ (ys ++ zs)`

```
((x:xs) ++ ys) ++ zs
= {definition of (++)}
(x:(xs++ys)) ++ zs
= {definition of (++)}
x : ((xs ++ ys) ++ zs)
= {induction hypothesis}
x : (xs ++ (ys ++ zs))
= {definition of (++)}
(x:xs) ++ (ys ++ zs)
```

so the result is true for all finite lists `xs` (and all lists `ys` and `zs`).


#### 9.4 [[A second example]]

Given the definition 

> `reverse    []  = []`
> `reverse (x:xs) = reverse xs ++ [x]`

a proof that applying this function twice gives the identity function proceeds by induction.

```
reverse (reverse [])
= {definition of reverse}
reverse []
= {definition of reverse}
[]
```

and then assuming `reverse (reverse xs) = xs` for some `xs`

```
reverse (reverse (x:xs))
= {definition of reverse}
reverse (reverse xs ++ [x])
= {unjustified step}
x : reverse (reverse xs)
= {inductive hypothesis}
x : xs
```

This (as yet) unjustified step requires a lemma:

	reverse (ys ++ [x]) = x : reverse ys

which can be proven by induction on `ys`.

```
reverse ([] ++ [x])
= {definition of (++)}
reverse [x]
= {definition of reverse}
reverse [] ++ [x]
= {definition of reverse}
[] ++ [x]
= {definition of (++)}
[x]
= {definition of reverse}
x : reverse []
```

and by assuming it to be true for `ys`

```
reverse ((y:ys) ++ [x])
= {definition of (++)}
reverse (y : (ys ++ [x]))
= {definition of reverse}
reverse (ys ++ [x]) ++ [y]
= {inductive hypothesis}
(x : reverse ys) ++ [y]
= {definition of (++)}
x : (reverse ys ++ [y])
= {definition of reverse}
x : reverse (y:ys)
```


#### 9.5 [[Induction over Partials Lists]]

A partial list is one with a tail that is either $⊥$ or a smaller partial list. There is a similar principle of induction over partial lists; to prove $P(xs)$ for ever partial list $xs$ is enough to prove

1. $P(⊥)$
2. for every partial list $xs$, if $P(xs)$ then $P(x:xs)$.

For example, $xs \mathrel{+ +} ys = xs$ for all partial lists $xs$.

The case $⊥ \; \mathrel{++} ys$ is immediate from the strictness of `(++)`, since it is defined by pattern matching on its left argument. Then, assuming the result for some partial $xs$

```
(x:xs) ++ ys
= {definition of (++)}
x : (xs ++ ys)
= {inductive hypothesis}
x:xs
```



#### 9.6 [[Infinite Lists]]

An infinite list can be thought of as a limit of a sequence of partial lists, for example `[0..]` is the limit of the chain $⊥, 0 : ⊥, 0 : 1 : ⊥, 0 : 1 : 2 : ⊥, . . .$ and so on. Successive elements of this chain are related by the information ordering, $(\subseteq)$. This is the partial order that has bottom ordered before all $x$.

A sequence of finite proper lists, such as $[ ], [0], [0, 1], [0, 1, 2], . . .$ and so on, will not do to approximate $[0..]$ because each of them contains some wrong information: that there is a $[]$ at some point in the list. Not only are these finite lists not related to each other with an ordering, they are not even related to the infinite list.

A property P is called chain complete if whenever $P(xs_i)$ holds for every element $xs_i$ for a chain, it also holds for the limit of that chain. So, chain complete properties that also hold for all partial lists will be ones that also hold for infinite lists.

It is enough to know that positive properties are chain complete. These include universally quantified (mathematical) equations between Haskell-definable expressions, and conjunctions of positive properties. Inequalities need not be chain complete, nor need properties involving existential quantification be. For example "$drop \; n \; xs = ⊥$ for some $n$" is true for all partial lists, but is not true for infinite lists. It is not one equation, but the disjunction of an infinite number of equations.

The earlier proof of $(xs \mathrel{++} ys) \mathrel{++} zs = xs \mathrel{++} (ys \mathrel{++} zs)$ for finite lists can be extended to partial lists because of the fact that the `(++)` operator is strict on its left-hand side argument, so the result holds for all finite and partial lists, and since it is an equation between Haskell expressions, it is chain complete and also holds for infinite lists.

Can we extend the proof of `reverse (reverse xs) = xs` to infinite lists? Certainly it holds for bottom due to the strictness of reverse. This looks promising until we realise that performing reverse twice on a partial list gives us bottom. 

What went wrong? We used a lemma to prove this statement which is not true for infinite or even partial lists. 



#### 9.7 [[Summary: Induction schemes]]

A proof by induction on $xs$ that a property $P(xs)$ holds for all of some class of lists $xs$ involves assembling proof of components for all of the ways of constructing list in that class.

To prove that $P(xs)$ for all finite $xs$, it is enough to prove (I) $P([])$ and (II) if $P(xs)$ for some finite list $xs$, then $P(x:xs)$.

To prove that $P (xs)$ for all partial $xs$, it is enough to prove (I) $P (⊥)$ and (II) if $P (xs)$ for some partial $xs$ then $P (x : xs)$.

To prove that $P (xs)$ for all infinite $xs$ it is enough to prove (I) $P (⊥)$ and (ii) if $P (xs)$ for some partial $xs$ then $P (x : xs)$, and (III) P is chain complete (for example, because it is an equation between Haskell-definable expressions).

In the special case on an equation $E = F$ it is also possible to use the take lemma which requires that you can prove only that if $take \; n \; E = take \; n \; F$ then $take \; (n+1) \; E = take \; (n+1) \; F$.