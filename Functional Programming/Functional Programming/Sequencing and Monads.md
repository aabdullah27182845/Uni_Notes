
In Haskell, the notion of `Monad` abstracts from a common program structure like that of 

> `return :: a -> Parser a`
> `(>>=) :: Parser a -> (a -> Parser b) -> Parser b`

There is a predefined type class

	class Monad m where
	   return :: a -> m a
	   (>>=) :: m a (a -> m b) -> m b
	   (>>) :: m a -> m b -> m b
	   j >> k = j >>= const k

where a value of `m a` can be thought of as a computation returning a value or values of type `a`. It makes sense to define instances of `Monad` only if the components satisfy the laws

	return x >>= k            = k x
	j >>= return              = k
	j >>= (λx -> (k x >>= h)) = (j >>= k) >>= h

These laws express, roughly that `return` is the left and right unit of bind, and the third law is an associative law. 

The type of `Parser` can be made an instance, although we need a name for the type function, and a type constructor to identify that type:

> `newtype Parser a = Parser { parse :: String -> [(a, String)] }`

> `instance Monad Parser where`
> `   return x = Parser (\xs -> [(x,xs)])`
> `   p >>= f  = Parser (\xs -> [(v,zs | (a,ys) <- p 'parse' xs,`
> `                                      (v,zs) <- f a 'parse' ys])`

It might not be immediately obvious, but this satisfies the monad laws.

There are many other instances of the `Monad` class which may be illuminating, for example

	instance Monad [] where
	   return x = [ x ]
	   xs >>= f = [ y | x <- xs, y <- f x ]

In this case `xs >>= f = concatMap f xs = concat (map f xs)`. The 'computation' in a list can perhaps be thought of as a list of possible results from some sort of non-deterministic calculation. It is perhaps easier to check that the monad laws hold here.

Similarly with

	instance Monad Maybe where
	   return = Just
	   Nothing >>= f = Nothing
	   Just x >>= f = fx

where the 'computation' is one that might or might not succeed, and `Nothing` is a sort of exception representing failure.


#### 16.1 [['do' Notation]]

Haskell provides a special notation for Monad-valued expressions.

	do {x <- m;stuff}  = m >>= (λx -> do {stuff})
	do {m;stuff}       = m >> do {stuff}
	do {m}             = m

As with other constructs, the braces and semicolons are usually omitted when the `do` expressions are laid out on several lines, using the offside rule.

In the case of the Monad of lists,

```
do {x ← xs; y ← f x; return (g x y)}
= xs >>= (λx → f x >>= (λy → return (g x y)))
= concat (map (λx → f x >>= (λy → return (g x y))) xs)
= concat (map (λx → concat (map (λy → return (g x y)) (f x))) xs)
= concat (map (λx → concat (map (λy → [g x y]) (f x))) xs)
= concat (map (λx → concat [[g x y] | y ← f x]) xs)
= concat (map (λx → [g x y | y ← f x]) xs)
= concat [[g x y | y ← f x] | x ← xs]
= [g x y | x ← xs, y ← f x]
```

so the similarity between do-notation and list comprehension is deliberate. 

In terms of do notation, the monad laws are

![[Pasted image 20240111132631.png]]

The associative law justifies writing both sides of this last equation as 

$\textbf{do}$
   $x ← j$
   $y ← k\;x$
   $h\;y$

and so on, and the unit laws allow for unnecessary `return` calls to be removed.

The beauty of `do` notation is that the plumbing in

> `some :: Parser a -> Parser [a]`
> `some p = p >>= ps`
> `         where ps a      = many p >>= done a`
> `               done a as = return (a:as)`

or

> `some p = p >>= (\a -> many p >>= (\as -> return (a:as)))`

is much easier to express as

> `some p = do { a <- p; as <- many p; return (a:as)}`

where of course the `do` expression in this example is a value in the `Parser` monad. The point of the `Monad` abstraction is that there will be a `some` function with similar properties in other monads.


#### 16.2 [[Kleisli Composition]]

It might not be obvious that

	j >>= (λx -> (k x >>= h)) = (j >>= k) >>= h

is an associative law: what exactly in this expression is associative?

Define the Kleisli composition by

> `(>=>) :: Monad m => (a -> m b) -> (b -> m c) -> (a -> m c)`
> `(f >=> g) x = f x >>= g`

then the monad laws can be expressed as properties of `(>=>)`

	return >=> k    = k
	j >=> return    = j
	j >=> (k >=> h) = (j >=> k) >=> h

For example, the Kleisli composition in the list monad is

	(f >=> g) x
	= f x >>= g
	= [z | y <- f x, z <- g y]
	= (concat . map g.f) x


#### 16.3 [[Monadic Join]]

In the list monad, `xs >>= f = concat (map f xs)`. In every monad, it turns out, this same factorisation is possible. The equivalent of `map` is of course $(<\$>)$, and the equivalent of `concat` is

> `join :: Monad m => m (m a) -> m a`
> `join m = m >>= id`

so in particular in the list monad

	join m
	= m >>= id
	= concat (map id m)
	= concat m

and in general

	m >>= f = join (f <$> m)

This means that it is possible to define the operations on a monad by giving not `return` and `(>>=)` but return, `(<$>)` and `join`. Sometimes, that is a more intuitive presentation.

In any case, the Kleisli composition is then `f >=> g = join . (g <$>) . f`


#### 16.4 [[Applicative and Functors]]

In any monad, you can define an operation that looks like application of a monadic function to a monadic argument.

	(<*>) :: Monad m => m (a -> b) -> m a -> m b
	fs <*> xs = do { f <- fs; x <- xs ; return (f x)}

It is convenient for now also to have a different name for

	pure :: Monad m => a -> m a
	pure = return

In the case of the list monad, `pure` makes a singleton list and `apply` would do all of the applications of a function to an argument that you would find in the Cartesian product of a list of functions and a list of arguments. In the case of the Parser monad, `pure` makes a parser that successfully returns a given value without consuming anything from the string, and `apply` would parse a function followed by an argument, and the result is a parse in which the two bits of input are both consumed and the result is the result of applying the function to the argument.

This `apply` function is well behaved in many ways:

	pure id <*> v              = v
	pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
	pure f <*> pure x          = pure (f x)
	u <*> pure y               = pure ($ y) <*> u

Here $(\$y)$ is a right-section of the application operator $f \;\$\; x = f\;x$.

These are the qualifications for being an instance of the `Applicative` type class:

	class Applicative m where
	   pure :: a -> ma
	   (<*>) :: m (a -> b) -> m a -> m b

so every monad is necessarily an `applicative`

If you are bothered that `applicative` is not a noun, you are right: it turns out to be an `applicative functor`. Why is that? Given an applicative `m` it is possible to define

	(<$>) :: Applicative m => (a -> b) -> (m a -> m b)
	f <$> xs = pure f <*> xs

which necessarily obeys the laws for map

	id <$> xs = xs
	(f . g) <$> xs = f <$> (g <$> xs)

or equivalently

	(id<$>) = id
	((f .g) <$>) = (f<$>).(g<$>)

so every monad is a legitimate instance of the class

	class Functor m where
	  fmap :: (a -> b) -> m a -> m b
	(<$>) = fmap

In fact, on the dubious grounds that "because you can, you should", the three classes are defined in the standard libraries by headings that say

	class Functor m where ...
	class Functor m => Applicative m where ...
	class Applicative m => Monad m where ...

which obliges you, when defining an instance of `Monad` also to define the instance of `Applicative` and when defining that also the instance of `Functor`. The intention is that the explicit definitions in `Functor` will be more efficient than the general one that can be derived from `Applicative`, and those in `Applicative` than those can be derived from `Monad`.


#### 16.5 [[Monadic Input and Output]]

The reason that monads first became such an important part of Haskell is that they capture the idea of sequencing effects, and this gives a way of sequencing the effects of input and output without leaving the functional programming language.

In the `Parser` monad the effects being sequenced are the extent to which each parser consumes the input string. Parsers which appear in the sequence in a `do` expression consume parts of the input string which appear in the same sequence in the string.

In the same way the effects in the `IO` monad are interactions with the real world, which happen in the order described by their sequence in a `do`. A thing of type `IO a` is an interaction with the real world which yields a value of type `a`, so for example

	readFile :: FilePath -> IO String

so when applied to the name of a file `readFile` produces an `IO` value from which you can get a `String` containing the sequence of characters in the file.

Simple output operations like

	putStr :: String -> IO ()

have nothing significant to return, so produce an `IO` value from which you can get $()$ which is the only value of type $()$ of null-types.