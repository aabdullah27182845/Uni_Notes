Standard sudoku puzzles consist of a $9 \times 9$ grid of squares, some of which are blank and the others which contain a single digit digit drawn from 1 to 9.

![[Pasted image 20240109183131.png]]

The solution is the unique square obtaining by filling the empty squares with single digits so that each row, each column, and each of the non-overlapping $3 \times 3$ blocks contains all digits only once.

We don't deign to solve the puzzle, what we do is write a function that computes all possible completions of a puzzle.

> `type Solver = Grid -> [ Grid ]`


#### 7.1 [[Specifying The Problem]]

A grid will be a matrix of digits. The contents of a cell in the grid will be a digit or blank: we might use a data-type with ten values and have the type checker make sure that we always have valid contents in a cell, but it will be easier not to. We assume the input contains only ten valid characters. For pragmatic reasons, blanks are represented by a visible character. Similarly we choose to represent a matrix as a list of rows, and a row as a list of cells, so that we can use familiar list operations.

```
> type Grid = Matrix Digit
> type Digit = Char

> type Matrix a = [ Row a ]
> type Row a = [ Cell a ]
> type Cell a = a

> digits :: [ Digit ]
> digits = [’1’..’9’]

> blank :: Digit -> Bool
> blank = (== ’.’)
```

Perhaps the most straightforward way of defining a solver

> `solve :: Solver`
> `solve = filter legal . expand . freedoms`

is to list all possible choices available for each cell, consider all possible grids that can be made to match those possibilities, and filter for the ones that are legal solutions.

	freedoms :: Grid -> Matrix Freedoms
	expand   :: Matrix Freedoms -> [ Grid ]
	legal    :: Grid -> Bool

We can represent the freedom for each cell as a list of possible digits

> `type Freedoms = [ Digits ]`

> `freedoms :: Grdi -> Matrix Freedoms`
> `freedoms = map ( map choice )`
> `          where choice d | blank d   = digits`
> `                         | otherwise = [d]`

Expanding a matrix of such lists is the Cartesian product for matrices.

> `expand :: Matrix Freedoms -> [ Grid ]`
> `expand = cp . map cp`

where the Cartesian product for lists

> `cp :: [[a]] -> [[a]]`
> `cp      []  = [[]]`
> `cp (xs:xss) = [ x:ys | x <- xs, ys <- cp xss]`

lists all possible ways of choosing exactly one value from each list in a list of lists. For example, `cp [[1,2],[3],[4,5]] = [[1,3,4],[1,3,5],[2,3,4],[2,3,5]]`. (Oh look, `cp` is a fold.)

Finally, an acceptable grid is one in which no row, column or box contains duplicates:

> `legal :: Grid -> Bool`
> `legal g = all nodups (rows g) &&`
> `          all nodups (cols g) &&`
> `          all nodups (boxs g)`

The most direct implementation of `nodups` is

> `nodups :: Eq a => [a] -> Bool
> `nodups    []  = True`
> `nodups (x:xs) = x 'notElem' xs && nodups xs`

where the function

	notElem x xs = all (/= x) xs

is predefined.

This test takes $O(n^2)$ steps on a list of length n. We might choose to sort the list and check that it is strictly increasing, and sorting can be done in $O(n \times \log n)$ steps so this is $O(n \times \log n)$. However for small problems, here n = 9, it is not clear that using an efficient sorting algorithm is worthwhile because of the constant of proportionality.


#### 7.2 [[Rows, Columns, and Boxes]]

Since a matrix is given by a list of its rows, the function `rows` is just the identity function on matrices:

> `rows :: Matrix a -> [ Row a ]`
> `rows = id`

and the function `cols` computes the transpose of a matrix:

> `cols :: Matrix a -> [ Row a ]`
> `cols   [xs]   = [ [x] | x <- xs ]`
> `cols (xs:xss) = zipWith (:) xs (cols xss)`

The predefined function

	zipWith :: (a -> b -> c) -> [a] -> [b] -> [c]

zips together a pair of lists, like zip, but by applying its function argument to each pair of corresponding elements to produce the corresponding result.

Dividing a list into groups of n can be done

> `by :: Int -> [a] -> [[a]]`
> `by n [] = []`
> `by n xs = take n xs : by n (drop n xs)`

The function that identifies the squares that are ninths of the grid

> `boxs :: Matrix a -> [ Row a ]`
> `boxs = map concat . concat . map cols . by 3 . map (by 3)`

divides each row into threes, parts the rows into threes, transposes the small matrices in each part, and then joins the whole back into a single list of the boxes.

This might be better illustrated by a similar transformation of a smaller $4 \times 4$ matrix into quarters:

![[Pasted image 20240109185703.png]]

One of the many virtues of functional programming is that it encourages this holistic style of programming, which manages whole objects rather than fiddling with many subscripts.


#### 7.3 [[Infeasibility]]

This completes the implementation of

> `solve :: Solver`
> `solve = filter legal . expand . freedoms`

This implements exactly the function that maps a Sudoku problem to its solution. It is however entirely useless for solving Sudoku problems.

The uniqueness of the solution can be enforced by as few as 17 non-blank squares in the puzzle, so there are $9^{9 \times 9 - 17} ≈ 10^{61}$ potential solutions to be checked. There are about $3 \times 10^7$ seconds in a year, so checking $10^{61}$ grids at the rate of a million a second would take the order of $3 \times 10^{47}$ years, which compares unfavourably with estimates of the age of the universe of the order of $10^{10}$ years.

Even a relatively easy problem such that given earlier in the notes will have many fewer than half of the squares filled in. No technological improvement is going to help here, nor is any realistic amount of concurrent computation. (Suck it type 3 civilisation)

This algorithm is not slow, it is infeasible.


#### 7.4 [[Holistic Reasoning]]

Thinking about whole data structures makes it easier to reason about programs, helped by the point-free (or tacit) style which encourages the omission of unnecessary arguments, so

> `solve = filter legal . expand . freedoms`

rather than

> `solve xss = filter legal (expand (freedoms xss))`

Not only are there no subscripts, nor individual elements of the grids. mentioned in the program. Point-free style here omits all mention of the grid itself, except perhaps the type.

For example, the grid arranging functions are all their own inverses

	rows . rows = cols . cols = boxs . boxs = id

at least on $9 \times 9$ matrices. Moreover,

	map rows . expand = expand . rows
	map cols . expand = expand . cols
	map boxs . expand = expand . boxs

on all $9 \times 9$ matrices of choices. There are also a wealth of laws about standard functions like

	map    f . concat         = concat . map (map f)
	filter p . concat         = concat . map (filter p)
	map    f . filter (p . f) = filter p . map f
	filter (all p) . cp       = cp . map (filter p)

