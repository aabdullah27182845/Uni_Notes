
Recall from [[Expressions, Types and Polymorphism]] that a data type declaration like

> `data Either a b = Left a | Right b`

introduced three named things:

- a type-value function of types, `Either` that constructs a new type for any two types a and b;
- an injection $Left :: a\;→Either\;a\;b$, and
- an injection $Right :: b\;→Either\;a\;b$ in such a way that every proper value of $Either\;a\;b$ is either in the image of $Left$ or the image of $Right$, but never both.

The functions `Left` and `Right` are constructors which are necessarily invertible. The data declaration also allows constructors to be used in pattern matching, so we could define functions

> `left  (Left x)  = x`
> `right (Right y) = y`

as the left inverses of the constructors, known as selectors. Haskell also provides a convenient abbreviation for the selector declarations:

> `data Either a b = Left {left :: a} | Right {right :: b}`

Care: the inverses are partial functions, and the claim is that the constructors have inverses on the left.

If pattern matching were not allowed we would still need in addition a discriminator such as

> `isLeft (Left _) = True`
> `isLeft (Right _) = False`

to make a complete set of deconstructors. Then anything that can be written in Haskell using pattern matching can be written in terms of `isLeft`, `left`, and `right`, without resorting to pattern matching.

These are not the only set of functions that would do as deconstructors for the `Either` type. The single general function

> `either :: (a -> c) -> (b -> c) -> Either a b -> c`
> `either left right (Left x)  = left x`
> `either left right (Right x) = right x`

will do, because

	left = either id undefined
	right = either undefined id
	isLeft = either (const True) (const False)

More generally any constructor can have any number of arguments, for example

> `data Pair a = Pair {first :: a, second :: a}`

The type `Pair a` is not quite the same as the type $(\alpha, \beta)$ of pairs $(x,y)$ of elements $x::\alpha$ and $y :: \beta$ because $Pair\;\alpha$ has elements which are both $\alpha$. The pun between the name of the type function `Pair` and its unique constructor $Pair :: \alpha → \alpha → Pair\;\alpha$ is common. The obvious single deconstructor for this is

> `pair :: (a -> a -> b) -> Pair a -> b`
> `pair p (Pair x y) = p x y`

From which you can recover `first = pair const` and `second = pair . flip const`. 

In the same way the natural single deconstructor for $(\alpha,\beta)$ is $uncurry$, and $fst = uncurry\;const$ and $snd=uncurry\;(flip\;const)$. It is worth mentioning the predefined type

> `data Maybe a = Nothing | Just a`

where different constructors have different number of arguments. There is a corresponding predefined deconstructor function

> `maybe :: b -> (a -> b) -> Maybe a -> b`
> `maybe nothing just  Nothing = nothing`
> `maybe nothing just (Just x) = just x`

In the special case where constructors have no arguments, they are distinct constants, for example

> `data Bool = False | True`
> `data Day = Sunday | Monday | Tuesday | Wednesday | Thursday | Friday | Saturday`

These are not strings: you have to explain how to print them, for example by

> `instance Show Day where`
> `     show Sunday = "Sunday"`
> `     show Monday = "Monday"
> `     ...`

Even though they are distinct there is no equality test unless you instantiate

> `instance Eq Day where`
> `     Sunday == Sunday = True`
> `     Monday == Monday = True`
> `     .....`
> `     _      == _      = False`

This requires 49 cases in total but can be reduced to 8 with the use of pattern matching. Although it might perhaps be better to define

> `daynum :: Day -> Int`
> `daynum Sunday = 0`
> `daynum Monday = 1`
> `               ...`

> `instance Eq Dat where`
> `    d1 == d2 = daynum d1 == daynum d2`


####  11.1 [[Strictness and Polynomial Types]]

These data types are made of sums (coproducts or alternatives) of products (or tuples). Constructors are never strict: the construction of a tuple necessarily distinguishes (say) $⊥ :: Pair\;\alpha$ and $Pair \;⊥\;⊥$ and so on. Since the predefined pairs and other tuples are also products, the same is true for them.

However, pattern matching on constructors is strict, even where there is only one constructor:

> `zero :: (a,b) -> Int`
> `zero (x,y) = 0`

is strict in its argument: $zero ⊥ = ⊥$. A less strict function is defined by

> `zero' :: (a,b) -> Int`
> `zero' xy = 0`

which really is the constant zero function, because $zero' \;⊥\;=0\neq\;⊥$. These types are called lifted because the values you want have all been 'lifted' up the ordering by putting a $⊥$ underneath them.

Sometimes you really do not need the lifted type. A type like

> `data Value a = Value a`
> `data Count a = Count a`

might be used to distinguish a `Count Int` used for one purpose from a `Value Int` used for another. The type-checker would prevent one from being confused with the other.

However you would like the type of `Value Int` to be the same as `Int`: you do not want to distinguish $Value⊥$ from $⊥$. Just for this there is a declaration

> `newtype Value a = Value a`

which makes the constructor `Value` be strict.


#### 11.2 [[Recursive Types]]

We have seen types like

> `List a = Nil | Cons a (List a)`

which is isomorphic to $[\alpha]$, in which the actual constructors are $[]$ and $(:)$. Types like this include values with arbitrary numbers of constructors, like infinite lists.

The obvious deconstructors for $[\alpha]$ are null, head, and tail, but the natural single function is

> `list :: (a -> [a] -> [b]) -> b -> [a] -> b`
> `list cons nil    []  = nil`
> `list cons nil (x:xs) = cons x xs`

This looks superficially like fold, but notice how it's not recursive. The arguments to `list` would naturally be in the other order, and there are instances of this function is some libraries with the arguments the other way around. 

We might represent sets of things by

> `data Set a = Empty | Singleton a | Union (Set a) (Set a)`

but the values of these are trees, so equality is not structural equality. If you really wanted them to behave like sets, you would want something like

> `instance Eq a => Eq (Set a) where`
> `     xs == yxs = (xs 'subset' ys) && (ys 'subset' xs)`

and then you would need to defined a recursive function subset, which could be done by calling two Nothings true as the base case and then recursively stepping down if the values of both sets are equal, but terminate and return false even if one of the values is false. 


#### 11.3 [[Deriving standard instance]]

It can be tedious to define obvious instances of classes for types, so Haskell can provide these automatically. For example

> `data Either a b = Left a | Right b`
> `                                   deriving (Eq, Ord, Read, Show)`

makes default definitions for $Eq$, $(Either\;a\;b)$ and so on. The instance of $Read$ makes $read :: String → Either \;\alpha\;\beta$ be the function that parses your input to the interpreter to turn it unto an $Either \;a\;b$, and the $Show$ makes $show :: Either \;\alpha\;\beta → String$ produce the representation that the interpreter would use to print an answer.

The equality defined by deriving $Eq$ is structural equality, which is what you want for $Either \;\alpha\;\beta$, and will itself require $Eq \; \alpha$ and $Eq \; \beta$; but structural equality is not what you want for the set definition.

The ordering derived for $Either \;\alpha\;\beta$ makes all $Left \;x$ be less than all $Right \;y$ and otherwise compares $Left \;x < Left \;y$ if and only if $x < y$ and so on.

The class $Enum \;\alpha$ includes

> `class Enum a where`
> `   succ     :: a -> a`
> `   pred     :: a -> a`
> `   toEnum   :: Int -> a`
> `   fromEnum :: a -> a`

used in translating list enumerations like $[x..y]$. The derived instance for `Day` would make `fromEnum Tuesday = 2` and `succ Friday = Saturday`, and so on.

The class `Bounded a` includes

> `class Bounded a where`
> `   minBound, maxBound :: a`

The derived instance of `Bounded Day` would make `minBound = Sunday` and `maxBound = Saturday`.


#### 11.4 [[Maps]]

The function $map :: (\alpha → \beta) → ([\alpha] → [\beta])$ can be generalised from lists to other datatypes with one argument. One of the properties is that `map id` is the identity on $[\alpha]$, another that `map f . map g = map (f . g)`. 

The map for `Pair` is

> `mapPair :: (a -> b) -> (Pair a -> Pair b)`
> `mapPair f (Pair x y) = Pair (f x) (f y)`

that for `Maybe` is

> `mapMaybe :: (a -> b) -> (Maybe a -> Maybe b)`
> `mapMaybe f Nothing = Nothing`
> `mapMaybe f (Just x) = Just (f x)`

that for `Set` is

> `mapSet :: (a -> b) -> (Set a -> Set b)`
> `mapSet f Empty = Empty`
> `mapSet f (Singelton x) = Singleton (f x)`
> `mapSet f (Union xs ys) = Union (mapSet f xs) (mapSet f ys)`

It would be good to be able to capture this commonality. The type class `Functor` does roughly this:

> `class Functor f where`
> `    fmap :: (a -> b) -> f a -> f b`

It does not capture the invariants that `fmap` preserves identity and composition: these are expected properties of any function that you define as an instance of `fmap`.

Notice that $f$ is not a type: it is a $type → tpye$ function, so the instance are for example

> `instance Functor [] where`
> `   fmap f    []  = []`
> `   fmap f (x:xs) = f x : fmap f xs`

> `instance Functor Pair where`
> `   fmap f (Pair x y) = Pair (f x) (f y)`

> `instance Functor Maybe where`
> `fmap f Nothing = Nothing`
> `fmap f (Just x) = Just (f x)`

The effect of these definitions is that if $f :: \alpha → \beta$ you can use `fmap` as a $[\alpha] → [\beta]$ or as a $Maybe \;\alpha → Maybe \;\beta$ and so on.

There is no instance of `Functor Either`, because the type constructor expects too many arguments, but there is an instance of $Functor \; (Either \;\alpha)$, as a functor on the second type. But do take that second instance with a grain of salt.


#### 11.5 [[Folds]]

Recall that the fold for a data type $T$ acts to replace each of the constructors of $T$ with the arguments of the fold function, and when applied to the constructors returns the identity $T → T$.

Where the type is not recursive neither is the fold function

> `foldPair :: (a -> a -> t) -> Pair a -> t`
> `foldPair pair (Pair x y) = pair x y`

> `foldMaybe :: b -> (a -> b) -> Maybe a -> b`
> `foldMaybe nothing just Nothing = nothing`
> `foldMaybe nothing just (Just x) = just x`

> `foldEither :: (a -> c) -> (b -> c) -> Either a b -> c`
> `foldEither left right (Left x) = left x`
> `foldEither left right (Right y) = right y`

These non-recursive functions are all the same as the corresponding deconstructors. The fold function for a recursive type of leaf-labelled binary trees like

> `data BTree a = Leaf a | Fork (BTree a) (BTree a)`

would itself be a recursive function

> `foldBTree :: (a -> b) -> (b -> b -> b) -> BTree a -> b`
> `foldBTree leaf fork (Leaf x) = leaf x`
> `foldBTree leaf fork (Fork l r) = fork (foldBTree leaf fork l)`
> `                                      (foldBTree leaf fork r)`

or perhaps more sensibly

> `foldBTree leaf fork = rec`
> `    where rec (Leaf x) = leaf x`
> `          rec (Fork l r) = fork (rec l) (rec r)`

Here is another kind of tree, a rose tree:

> `data RTree a = RTree a [RTree a]`

which is never empty, and where each node can have any number of children. There is a natural map on rose trees

> `instance Functor RTree where`
> `      fmap f (RTree x ts) = node x (map (foldRTree node) ts)`

These ideas generalise to a bushy tree with other arrangements of children, provided the map function on the list of children can be replaced by the appropriate `fmap`

> `data Bush t a = Bush a (t (Bush t a))`

so that $Bush \;[]\;a$ is essentially the same as $RTree \;a$, and $Bush\;Maybe\;a$ is a type of non-empty lists, and so on.

> `instance Functor t => Functor (Bush t) where`
> `     fmap f (Bush x ts) = Bush (f x) (fmap (fmap f) ts)`

where on the right hand side the first, outer, call of `fmap` is that for the functor t, and the second, inner one is a recursive call of `fmap` for `Bush t`, and

> `foldBush :: Functor t => (a -> t b -> b) -> Bush t a -> b`
> `foldBush bush (Bush x ts) = bush x (fmap (foldBush bush) ts)`

Just as with lists, once the `foldBush` function is defined, `fmap` could be defined by

> `instance Functor f => Functor (Bush f) where`
> `       fmap f = foldBush (Bush . f)`

There is no type class with a general folding function in it, because folds have very different types from each other, depending on the numbers and types of constructors.

However, in general, it is possible to write a map function as an instance of the corresponding fold.


#### 11.6 [[Unfolds]]

There is an `unfold` to lists which is dual to fold from lists.

> `unfold null head tail = rec`
> `              where rec x | null x    = []`
> `                          | otherwise = head x : rec (tail x)`

The fold has parameters that replace the constructors of its list argument, and this unfold has parameters that echo the list deconstructors making decisions about placing constructors in the result.

The equivalent for the binary trees might be

> `unfoldBTree :: (b -> Bool) -> (b -> a) -> (b -> b) -> (b -> b) -> b -> BTree a`

> `unfoldBTree single value left right = rec`
> `           where rec x | single x  = Leaf (value x)`
> `                       | otherwise = Branch (rec (left x)) (rec (right x))`

Then, for example, the function that unfolds a non-empty list into a balanced tree would be

> `build = unfoldBTree (null.tail) head left right`
> `        where left  xs = take (length xs 'div' 2) xs`
> `              right xs = drop (length xs 'div' 2) xs`


#### 11.7 [[Generalising folds and unfolds]]

The usual presentation of `fold` for lists

> `fold :: (a -> b -> b) -> b -> [a] -> b`
> `fold cons nil    []  = nil`
> `fold cons nil (x:xs) = cons x (fold cons nil xs)`

uses pattern matching, but it could be written in terms of deconstructors

> `fold cons nil = rec`
> `     where rec xs | null xs   = nil`
> `                  | otherwise = cons (head xs) (rec (tail xs))`

but it can also be written in terms of the single deconstructor

> `list :: (a -> [a] -> b) -> b -> [a] -> b`
> `list cons nil     [] = nil`
> `list cons nil (x:xs) = cons x xs`

In terms of list, you can see that

> `fold cons nil = rec where rec = list (shape rec cons) nil`

Here, the shape function

> `shape f c x xs = c x (f xs)`

captures that the $(:)$ constructor has a recursive instance of the list type as its second argument, and applied $f$ to that sub-list.

An example of the use of this fold function, with

> `shift = shape (*10) (+)`

so that `shift d n = d + 10*n` in place of $(:)$ in a list

	fold shift 0 [1,2,3,4,5] = 54321

converts a list of decimal digit values into the integer which they represent.

The corresponding unfold function would take an argument which had a type reminiscent of the list deconstructor

> `unfold :: ((a -> b -> [a]) -> [a] -> b -> [a]) -> b -> [a]`
> `unfold list = rec where rec = list (shape rec (:)) []`

The declaration of `rec` is similar to that for the fold, supporting the claim that this is a recursion with the same structure as the recursive type. However, in the case of the recursion for fold, the list was the real deconstructor and `cons` and `nil` were arguments for that fold. In this definition, the `cons` and `nil` are the real constructors, and the list is the argument to unfold.

For example

> `unshift cons nil 0 = nil`
> `unshift cons nil x = cons (x 'mod' 10) (x 'div' 10)`

is the deconstructor-shaped function that when used with unfold undoes the mapping from a list of digits to an integer.

	unfold unshift 54321 = [1,2,3,4,5]

You can show that `fold shit 0 . unfold unshift` is the identity on natural numbers. The composition in the other order is the identity on lists of digits that have no leading zeros.

Similar constructions work for other data types, for example for

> `data BTree a = Leaf a | Fork (BTree a) (Btree a)`

the natural deconstructor is

> `btree leaf fork (Leaf x)   = leaf x`
> `btree leaf fork (Fork l r) = fork l r`

then the fold can be written in this style

> `foldBTree :: (a -> b) -> (b -> b -> b) -> BTree a -> b`
> `foldTree btree = rec`
> `                where rec = btree Leaf (bshape rec Fork)`

where this time both components of a `Fork` are trees so both arguments to `Fork` need recursive calls of $foldBTree$

> `bshape f fork l r = fork (f l) (f r)`

The corresponding unfold is then

> `unfoldBTree :: ((a -> BTree a) -> (b -> b -> BTree a) -> b -> BTree a) -> b -> BTree a`
> `unfoldBTree leaf fork = rec`
> `                 where rec = btree leaf (bshape rec fork)`

and in this form the unfold to a `BTree` that divides a non-empty list into a balanced binary tree is

> `build = unfoldBTree split`
> `      where split leaf fork [x] = leaf x`
> `            split leaf fork xs = uncurry fork (halve xs)`
> `            halve xs = splitAt (length xs 'div' 2) xs`

or slightly more efficiently, unfolding from a pair of a list and its length

> `build = unfoldBTree split`
> `   where split leaf fork (1,[x]) = leaf x`
> `         split leaf fork (n, xs) = fork (n', ls) (n-n', rs)`
> `                                 where n' = n 'div' 2`
> `                                       (ls,rs) = splitAt n' xs`



