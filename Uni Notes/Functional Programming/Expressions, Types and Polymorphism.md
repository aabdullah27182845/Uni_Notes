Haskell programs are statically typeable, meaning that the type of an expression can be established from the components without evaluating the expression. The language is strongly typed: an expression has no value unless its type can be inferred, or checked against a claimed type. Its value has to be one of the value of its type.

Given an expression, the interpreter checks that the expression is well-formed, then that is consistently typed, and only then does it evaluate the expression and print it.

#### 3.1 [[Well-formed, well-typed expressions]]

Well-formedness is purely syntactic: parenthesis match, operators have arguments, constructs are complete. This can be checked in a time bounded by the size of the expression. Purely syntactic errors in programs are caught here.

Type safety involves information about semantics, but not explicitly the values of expressions.
- Types can be checked or inferred in a bounded amount of time (sufficiently computable).
- The Haskell type system is so constructed that this check is also finite. In certain cases it can be abused but that is the exception, not the rule.
- Many semantic errors can be caught as type errors.

Calculating the value of an expression, though, can take for ever: for example, the value of an expression can be unboundedly large. Some errors will necessarily only turn up during evaluation. Take 2^2^2^2^2^2 which is 2^(2^65536), a stupidly large number.


#### 3.2 [[Names and Operators]]

Names consist of a letter, followed by perhaps some alphanumeric characters, followed perhaps by some primes (single quote characters).

If the initial letter is upper case, the name may be that of a type (like `Int`) or type constructor (like `Either`) or type class (like `Eq`), or it might be the name of constructor (like `Left` or `True`). If the initial letter is lower case, the name is either that of a variable or of a type variable.

Symbols made of a sequence of one or more non-alphanumeric characters behave mostly like infix binary operators. Some of these can be defined just like any other function

> `x +++ y = if even x then y else -y`
> `x // y = 2 / (1/x + 1/y)`

Get the gist of that right? They are left-associative by default, but the language allows for declaring them otherwise (like ^ which is right-associative). Parenthesis convert operators into names: $x + y = (+) \; x \; y = (x+) \; y = (+y) \; x$ Conversely, backquotes convert a name into an operator as we saw in [[Numbers less than 100]]  with 'div' and 'mod'.


#### 3.3 [[Types]]

There are some built-in types, such as `Int`, `Float`, `Char`. There are also some built-in type constructors, in particular the function type `a -> b` for any types a and b. The types of lists, such as `[Int]`, will turn out to be built-in only in as much as there is a special syntax. Similarly, tuples such as pairs `(Int, Char)`, triplets `(Int, Char, Bool)` and so on. There are also empty tuples, () whose sole value is ().

Declarations like 
	`type String = [Char]`
introduce type synonyms, new names for existing types.

You might think that Bool has to be built-in, but it could be, and is defined by
	`data Bool = False | True`
This declaration introduces a new type, Bool and new constants False and True. Similarly, there are predefined types. 

Here are a couple examples:
	`data Maybe a = Nothing | Just a`
	`data Either a b = Left a | Right b`

As well as introducing a range of types, `Maybe a` for each type `a` and `Either a b` for each type `a` and type `b`, these declarations introduce:
	`Nothing :: Maybe a`
	`Just :: a -> Maybe a`
	`Left :: a -> Either a b`
	`Right :: b -> Either a b`
Values of Either Int Char include Left 42 and Right 'x'.

A data declaration something akin to a record in other programming languages.

> `data PairType a b = Pair a b`

introduces a family of types `Pairtype a b` for each pair of types a and b, together with a function:
	`Pair :: a -> b -> Pairtype a b`

This new type is equivalent to `(a,b)`.

The function `Pair`, exceptionally for the names of values in Haskell, has a name spelled with an upper case initial, which marks it out as a constructor: a function which by the form of its definition must be invertible. Constructors can appear in patterns on the left hand side of a function definition, such as

> `sumPair :: PairType Int Int -> Int`
> `sumPair (Pair x y) = x + y`

Value constructors and type constructors come from two different name-spaces, so you can use the same name for both things.


#### 3.4 [[Polymorphic Types]]

Many standard functions have types that mark them out as being applicable to arguments of many types, for example

> `map :: (a -> b) -> [a] -> [b]`
> `reverse :: [a] -> [a]`

Types with lower case names are type variables, and these types should be read as being "for all types a and b, map can have type `(a -> b) -> [a] -> [b]`", and "for all types a, reverse can have type `[a] -> [a]`".

When a polymorphic function is applied to an argument of a more constrained type, the polymorphic type is constrained to match.

If you want an instance of reverse that applies only to lists of Char, and cannot accidentally be applied to an `[Int]`, the expression `reverse :: [Char] -> [Char]` will do that. Makes sense logically.

This is parametric polymorphism and the value of a parametrically polymorphic expression is very constrained by its type. For example, there is a standard function
	`either :: (a -> c) -> (b -> c) -> Either a b -> c`

Such a function has to satisfy
	`either f g (Left x) = ...`
	`either f g (Right x) = ...`
where the right-hand sides are both of type c, whatever that type happens to be. There is in each case essentially only one possible expression known to be of that type: f x and g y respectively.

A parametrically polymorphic function has to operate uniformly on all possible instances of its argument type. The type of reverse requires that it treats all lists the same: it cannot inspect the elements of the list because it cannot know what they are. It turns out that a consequence of this is that for any `fun :: [a] -> [a]`
	`map f . fun = fun . map f`

You can easily see that this is true for reverse; it is also true for any other function of the same type; but the force of the result is that a function does not satisfy this equation cannot have that type.

Such results, theorems that follow from the types, were called theorems for free in a paper by Phil Wadler.


#### 3.5 [[Selectors, Discriminators, Deconstructors]]

A constructor like `PairType` makes a value (the jargon is a product) which behaves like a record with two fields. These can be recovered by selector functions

> `first :: PairType a b -> a`
> `first (Pair x y) = x`
> `second :: PairType a b -> b`
> `second (Pair x y) = y`

The constructors of a type like an Either make a value (a sum) which behaves like a union of two possible fields. The selectors for this type are partial functions.

> `left :: Either a b -> a`
> `left (Left x) = x`
> `right :: Either a b -> b`
> `right (Right y) = y`

but they are little use without a discriminator that tells you which kind of value you have, for example

> `isLeft :: Either a b -> Bool`
> `isLeft (Left x) = True`
> `isLeft (Right x) = False`

Can you see why the declaration of Left and Right was important in Either when we first encountered it in [[Types]]? It's so you can create discriminators for these functions. Here is another example for `Maybe`.

> `just :: Maybe a -> a`
> `just (Just a) = a`

> `isPresent :: Maybe a -> bool`
> `isPresent (Just a) = True`
> `isPresent (Nothing) = False`

From here, discriminators and selectors of a type will be referred to as deconstructors. Informally, the community using destructors, which is not a good usage. 

Deconstructors for a type are enough to let you write functions on that type without pattern matching. For example
	`either :: (a -> c) -> (b -> c) -> Either a b -> c`
	`either f g e = if isLeft e then f (left e) else g (right e)`

Unlike the constructors, deconstructors are not unique: the function `either` on its own is itself a perfectly good deconstructor for the `Either` type, and would more naturally have been defined by pattern matching:

> `either :: (a -> c) -> (b -> c) -> Either a b -> c`
> `either f g (Left x) = f x`
> `either f g (Right y) = g y`


#### 3.6 [[Type Inference]]

How does Haskell check the types of expressions? The essential rule is that if `f` is applied to `x`, then `f` needs to have a function type `a -> b`, and `x` needs to have type `a`, and the resulting application `f x` has type `b`.

A definition such as
	`flip f x y = f y x`
need not have an explicit type signature because Haskell can deduce a type for `flip` from this rule. The algorithm allocates a type variable to each name:
	$flip \; :: \; \alpha$         $f \; :: \; \beta$          $x \; :: \; \gamma$          $y \; :: \; \delta$
and the result of each application, then assembles the constraints:

$flip \; f \; \; \; \; \; \ \; \; \; \; \; ⇒ \alpha = \beta → \epsilon$ 
$(flip \; f ) \; x \; \; \; \; \; ⇒ \epsilon = γ → ζ$  
$((flip \; f ) \; x) \; y ⇒ ζ = δ → η$ 
$f \; y \; \; \; \; \; \; \; \; \; \; \; \; \; \; \; \; ⇒ β = δ → θ$
$(f \; y) \; x \; \; \; \; \; \; \; \; \; \; ⇒ θ = γ → ι$ 

and then finally because both sides are equal, $\iota = \eta$. These constraints can be met and then found out with substitution.

$α \; \; \; \;= β → \epsilon$ 
    $= (δ → θ) → γ → ζ$
    $= (δ → γ → ι) → γ → δ → η$
    $= (δ → γ → ι) → γ → δ → ι$ 

which is the type with which flip is declared. 

When you are doing type inference by hand, you need not be quite so systematic. You can anticipate some of the later substitutions because, for example, you can see that flip is applied to three arguments in turn, so its type must have the form $α → β → γ → δ$ and you can start from there.

If you give a type declaration, it will be checked against the inferred type. Type declarations, as well as good documentation, have the advantage of improving the explanation of any type errors found.