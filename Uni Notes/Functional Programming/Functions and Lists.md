#### We will begin with an introduction by talking about [[Programs as functions]]. 
- At one level, a program is a mapping from its input to its output. Almost everything we need to know from mathematics about functions is almost what we need to know about programs.
- Some functions, for essential logical reasons cannot be programs.
- Some programs cannot be total functions (for sufficiently interesting programming languages).
- However, two programs are equivalent exactly if they implement the same function. Functions are equal if they give the same results when applied to the same argument.

#### 1.2 - [[Types]]
Here are the following types of functions you can get. A function usually maps a type X as input to a type Y as output.

> `sin :: Float -> Float`
> `home :: Person -> Address`
> `add :: (Int, Int) -> Int`
> `logbase :: Float -> (Float -> Float)`

Float is the Type of (single precision) floating point numbers, i.e 3.1415927 or 4.124295303e8. Person and Address are arbitrary types, and in some cases, are left up to the programmer to define. Int is the Type of a discrete integer value. Notice that in mathematics, we define function application as such: sin(theta). In Haskell, the need for parenthesis is not present. We can say "sin theta" and that would be an acceptable value. However, don't mistake it for this application working in your favour all the time. sin theta / 2 DOES NOT EQUAL sin (theta / 2). Function application is something we will discuss later.


#### 1.3 -[[ Sequences]]
Just as how functions encapsulate the idea of computation, sequences in Haskell capture the idea of iteration. 

The list: `[3,1,4,1,5,9,2,7]` and `[Int]` are both expressions of a sequence.

One concretely defines a list of Int's with a length of 8 and each element corresponding to that place's digit in pi. `[Int]` defines an expression of a list of Int's, particularly when defining a function that would map a list of some type to another type, which could also be a list.
For integers, the expression `[a..b]` refers to a list which contains `[a, a+1, .. , b]`. This is a very useful shorthand whenever needed. Also, there are lists of every type, even types of lists of lists. Here:

`[[1], [1,2], [3,4,5,6]] :: [[Int]]` 

One particularly nifty idea that we can use as programmers is list comprehension. The value of a list comprehension is the value of the expressions to the left of the bar. Here's an example:

`x = [j % 2 == 0 | j <- [0..100]]`

The parts after the bar are taken left-to-right: generators introduce new variables which successively take values from lists, and Boolean valued expressions are guards. Here is some code to understand this:

> `map :: (a -> b) -> [a] -> [b]`
> `map f xs = [f x | x <- xs ]`

> `filter :: (a -> Bool) -> [a] -> [a]`
> `filter p xs = [ x | x <- xs, p x ]`

> `concat :: [[a]] -> [a]`
> `concat xss = [x | xs <- xss, x <- xs]`

Notice that these apply uniformly to lists of all type a. Note that these functions may be equivalent, they are not the standard definitions.

Here is some examples of these codes running in action

> `map abs [-5..5] = [5,4,3,2,1,0,1,2,3,4,5]`
> `filter even [1..10] = [2,4,6,8,10]`
> `concat [[1],[2,3],[4,5,6]] = [1,2,3,4,5,6]`

Note: There is a standard definition of the type `String = [Char]` and lists of this type have a handy compact representation.

>`['a'..'z'] = "abcdefghijklmnopqrstuvwxyz"
>`concat ["al","pha","bet","ic","al","ly"] = "alphabetically"`


#### 1.4 [[Script Files]]

Definitions are written in script files. In our case, script files have names ending in `.lhs` which stands for literate Haskell Script. Lines starting with `>` are read as definitions, and the rest are comments. To prevent accidents caused by typos, there has to be a blank line between program lines and comment lines.

There are also `.hs` files which all lines are definition lines unless explicitly stated with a comment block, specified by `{- -}` or if it's just one line, then use `--`.  

Pretty short segment, onto the next one!

#### 1.5 [[Composition of functions]]

You will almost certainly be familiar with composition of functions: the composition $f \circ g$ is $(f \circ g)(x) = f(g(x))$ , or in Haskell terms:

> `(f . g) x = f (g x)`

Operators whose names are made of symbols, rather than letters, can appear infixed; and the definition means the same as

> `(.) f g x = f (g x)`

where the parenthesis make (.) behave like an ordinary name. The composition operator is a perfectly good function

> `(.) :: (b -> c) -> (a -> b) -> (a -> c)`

The peculiar order of the types is down to the odd convention that we write arguments to the right of functions, but function types have their argument type on the left! Look here:
- b to c is applied to a to b
- a to b gives b so the argument for b to c is indeed b and it's consistent.
- Now, b is then given c, which encapsulates the output function well.

Composition is associative, so $f \circ (g \circ h) = (f \circ g) \circ h$ , so just as one writes sums like $1 + 2 + 3 + ... + n$ without parenthesis, it is perfectly OK to write compositions $f \circ g \circ h \circ i \circ j \circ k$ without parenthesis. This is quite common program structure.

#### 1.6 [[Practiced Example: Most common words in a text]]

Here is a question. What are the n most common words in a given text?

The input is a text which is a list of characters, containing visible characters like `'B'` and `','`, and blank characters like spaces and newlines `(' ' and '\n')`. The Haskell type `Char` is the type of characters, and the type of lists whose elements are of type `Char` is `[Char]`. 

The output should be something like:

> `hill: 10`
> `a: 4`
> `names: 3`
> `witch: 2`

This output is also a list of characters, in fact, it is the string `"hill: 10\n a: 4\n names: 3\n which: 2\n"`

Applying `putStr` to a string has the effect of outputting its characters in turn. So we need to design a function

> `mostCommon :: Int -> [Char] -> [Char]`

The expression `mostCommon n xs` evaluates to a string of this form, for printing. One scheme would be to:

1. convert all the letters in the text to lower case, so as not to distinguish between words such as "The" and "the";
2. break the text up into words, where by definition a word is a maximal length sequence of visible characters;
3. bring all duplicated words together, by sorting the list of words into alphabetical order;
4. divide the list of words into runs of the same word;
5. replace each run by a single copy with a count of the number of times it occurs;
6. sort these runs into descending order of count;
7. take the first n elements of the sorted list of runs;
8. convert each run into the characters which will represent it, and concatenate these strings into a single string.

Many of these steps are off-the-library functions in Haskell. Their combination is a composition of functions, right to left.

First of all, for clarity, some types:

> `type Text = [Char]`
> `type Word = [Char]`
> `type Run = (Int, Word)`

1. We need a function `canonical :: Char -> Char` that converts upper case letters to lower case and (design decision) turns everything else into a blank. This function will then be applied in the following way:    
		`map canonical :: Text -> Text`
    character by character to each character in the text.
2. The standard function
		`words :: Text -> [Word]`
	breaks a list of characters into a list of words, where a word is itself a list of characters. Words are non-blanks separated by blanks.
3. To sort a list of words, we need a function `sort :: [Word] -> [Word]`. There is a library function
		`sort :: Ord a => [a] -> [a]`
	which will sort lists of any type, provided there is an ordering on that type. Fortunately, the ordering on lists of characters is exactly what we need.
4. To identify the runs we need a function of type `[Word] -> [Run]` that replaces runs of identical words with a pair: the length of the run, and the common word. There is a library function:
		`group :: Eq a => [a] -> [[a]]`
	which divides a list of any type, provided that there is an equality test on that type, into maximal lists of adjacent equal elements.
5. We need a function `codeRun:: [Word] -> Run` that will take one of these runs to a length/representative pair.
6. The sort function will order these runs, but into descending order of count. One thing we could do is to the use the standard function $reverse$ to reverse the sorted list $reverse \circ sort$.
7. There is a standard function `take` which returns an initial segment of the list no longer than a chosen length.
8. We need a function `showRun :: Run -> String` to turn each run into a corresponding list of characters, then
		`concat . map showRun`
	will produce the required string.

Here is the complete program:

```
> mostCommon :: Int -> Text -> String
> mostCommon n = concat . -- :: [String] -> String
> map showRun . -- :: [Run] -> [String]
> take n . -- :: [Run] -> [Run]
> reverse . -- :: [Run] -> [Run]
> sort . -- :: [Run] -> [Run]
> map codeRun . -- :: [[Word]] -> [Run]
> group . -- :: [Word] -> [[Word]]
> sort . -- :: [Word] -> [Word]
> words . -- :: Text -> [Word]
> map canonical -- :: Text -> Text
```

and apart from the library functions, all of which are things we could write but need not because someone already has, we need only write


> `canonical :: Char -> Char`
> `canonical c   | isLower c = c`
>             `| isUpper c = toLower c`
>             `| otherwise = ' '`


which uses Haskell conditional equations and library functions to manage characters. The function

> `codeRun :: [Word] -> (Int, Word)`
> `codeRun xs = (length xs, head xs)`

is only ever applied to a non-empty list of identical elements and returns the length of that list (length is a standard function) and a representative element. Here, head is a standard function that returns the first element of a list.

> `showRun :: (Int, Word) -> String`
> `showRun (n,w) = concat [" ", w, ": ", show n, "\n"]`

`showRun` constructs a String containing the word, some punctuation, a numeral representing its frequency, and a newline character. Here is what it would look like when run in the ghc interactive environment:

```
Main> putStr hill
When the Anglo-saxons invaded Britain it is clear that they
took over many place names as names, without understanding their
meaning. The evidence is to be found in names like Penhill,
where Old English hyll was added unnecessarily to a word which
represented Old Welsh pann, hill. A Penhill in Lancashire
developed into Pendle Hill, a name which means hill-hill-hill.
England also has a Torpenhow Hill, or hill-hill-hill-hill.
*Main> putStr (mostCommon 5 hill)
hill: 10
a: 4
names: 3
which: 2
to: 2
```

(This may not be true, don't believe everything you read)


#### 1.7 [[How to read Haskell Expressions]]

One of the stumbling blocks in reading Haskell when you first meet it is the idiosyncratic syntax for function application. It soon becomes natural!

Expressions like $f \; x$ represent an application of a function called $f$ to an argument $x$. 
- No need for brackets. $(x)$ is the same as $x$ so why bother with them?

The value of $f$ in $f \; x$ had better be a function; but the same is true about expressions like $x \; f$ where $x$ had better be a function.
- This also goes true for $phu \; bar$ where the function $phu$ is being applied to the argument $bar$. 

Function application binds to the left, so if you write $f \; x \; y$ you get the same expression as if it had been $(f \; x) \; y$ 
- In this case, $f$ is applied to $x$ which itself returns a function that gets applied to $y$. 

On the other hand, operators such as + and ++ made out of symbols are treated differently. Expressions like $x+y$ are treated as the application of a function, which can be referred to as $(+)$, to $x$ and $y$.

Operators bind less tightly than function application, so $p \; q + r \; s$ is the same as $(+) (p \; q)(r \; s)$. 
- So, for example $f \circ g$ is the same as $(\circ) \; f \; g$, and $(f \circ g) \; x$ is the same as $(\circ) \; f \; g \; x$. But neither of these are the same as $f \circ g \; x$ which means $f \circ (g \; x)$. 