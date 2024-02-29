
The naïve solver from the previous lecture

> `solve = filter legal . expand . freedoms`

was too inefficient because it might have to consider as many as $9^{9 \times 9 - 17}$ possible matrices of choices.


#### 8.1 [[Pruning]]

One way of cutting down on the scale of the search is to remove from the freedoms in each cell those elements that are already committed to another cell in the same row, column or box. We need a function `prune` which satisfies

	filter legal . expand = filter legal . expand . prune

But how?

A single row can be pruned by:

> `pruneRow :: Row Freedoms -> Row Freedoms`
> `pruneRow row = map (remove (ones row)) row`

removing from each cell of the row any element that is already fixed somewhere in that row. The committed values are the ones that appear as a singleton

> `ones :: [[a]] -> [a]`
> `ones row = [ d | [d] <- row ]`

or, if you prefer

> `ones = map head . filter singleton`

Removing elements from a cell is almost a filter, 

> `remove :: Freedoms -> Freedoms -> Freedoms`
> `remove xs [d] = [d]`
> `remove xs ds = filter ('notElem' xs) ds`

but had better leave the fixed choices themselves in place.

This function satisfies

	filter nodups . cp = filter nodups . cp . pruneRow

because any element of the Cartesian product of a row of choices contains every one of the singletons, and so can only be free of duplicates if it does not choose one of these from any other cell.


#### 8.2 [[Promoting to rows, columns, and boxed]]

Writing $n$ for `nodups`, and $r,c$ and $b$ for `rows`, `cols`, and `boxs`, recall that

	legal g = all n (r g) && all n (c g) && all n (b g)

So 

	filter legal = filter (all n . b) . filter (all n . c) . filter (all n . r)

and if  $d$ is one of $r,c$ and $b$,

```
filter (all n · d) · expand
= { map id = id and d · d = id }
map (d · d) · filter (all n · d) · expand
= { map f · map g = map (f · g) }
map d · map d · filter (all n · d) · expand
= { map f · filter (p · f ) = filter p · map f }
map d · filter (all n) · map d · expand
= { map d · expand = expand · d }
map d · filter (all n) · expand · d
= { definition of expand = cp · map cp }
map d · filter (all n) · cp · map cp · d
= { filter (all p) · cp = cp · map (filter p) }
map d · cp · map (filter n) · map cp · d
= { map f · map g = map (f · g) }
map d · cp · map (filter n · cp) · d
= { filter n · cp = filter n · cp · pruneRow }
map d · cp · map (filter n · cp · pruneRow ) · d
= { map f · map g = map (f · g) }
map d · cp · map (filter n · cp) · map pruneRow · d
= { d · d = id }
map d · cp · map (filter n · cp) · d · d · map pruneRow · d
= { derivation above, reversed }
filter (all n · d) · expand · d · map pruneRow · d
```

Because the three filters commute with each other we can use this calculation three times to deduce that

> `prune :: Matrix Freedoms -> Matrix Freedoms`
> `prune = pruneBy boxs . pruneBy cols . pruneBy rows`
> `       where pruneBy f = f . map pruneRow . f`

will do.

This suggests that we would be better to try

> `pruneSolve :: Solver`
> `prunesolve = filter legal . expand . prune  freedoms`

Sadly, this doesn't help much.

However, as in other walks of like, one `prune` has only a small effect. The same calculation shows that we can have two `prunes` as we need. What if we have as many as are needed for one more to make no difference?

> `bowlsolve :: Solver`
> `bowlsolve = `
> `       filter legal . expand . repeatedly prune . freedoms`

This turns out to be enough to solve easy problems.

The function `repeatedly` is not standard: it might be defined by

> `repeatedly :: Eq a => (a -> a) -> a -> a`
> `repeatedly f x | fx == x   = x`
> `               | otherwise = repeatedly f fx`
> `                 where fx = f x`

but it could equally have been defined by

> `repeatedly f = `
> `    fst . head . dropWhile (uncurry (/=)) . pairs . iterate f`
> `   where pairs xs = xs 'zip' tail xs`

We can be sure that repeatedly pruning cannot go on forever looking for a fixed point, but we are not certain. Unfortunately, however many times we prune, the more devious Sudoku problems will still have so much freedom left that this solver takes too long.


#### 8.3 [[Parsimonious Expansion]]

Another strategy that human solvers apply is to look for cells that are highly restrained, and then consider the consequences of making each possible choice there.

Suppose we can define

	expand1 :: Matrix Freedoms -> [ Matrix Freedoms ]

to expand only one cell. This ought to satisfy

	expand ~ concat . map expand . expand1

meaning that they are equal up permutation.

A good choice of cell on which to perform this expansion is any with a minimal number of choices, but not yet fixed. There might be no choices, in which case there is no expansion; or there might be at least two.

![[Pasted image 20240110100531.png]]

```
> expand1 :: Matrix Freedoms -> [ Matrix Freedoms ]
> expand1 fss = [ ass+[as++[c]+bs]+bss | c <- cell ]
>    where 
>      (ass, row:bss) = break (any optimal) fss
>      (as, cell:bs)  = break optimal row
>      optimal cell   = length cell == n
>      n              = minimum (length fss)
> length = filter (/=1) . map length . concat
```

where the predefined function break satisfies

	break p xs = (takeWhile (not . p) xs, dropWhile (not . p) xs)

and divides a list into two just before the first p-satisfying element of the list.

Some care is needed: if there are singletons in all of the cells of the matrix, this function will be undefined. So

	expand = concat . map expand . expand1

can only hold if there is at least one non-singleton cell.

Say that a matrix of freedoms is complete if all cells are singletons, and safe if the singletons in the matrix are not contradictory. Unsafe matrices cannot lead to a solution. A complete and safe matrix corresponds to a unique legal grid.

```
> complete :: Matrix Freedoms -> Bool
> complete = all (all singleton)

> safe :: Matrix Freedoms -> Bool
> safe fss = all (nodups.once) (rows fss) &&
>            all (nodups.once) (cols fss) &&
>            all (nodups.once) (boxs fss)       
```


Assuming that it is applied to a matrix that is safe but incomplete,

```
filter legal · expand
≈ { construction of expand1 }
filter legal · concat · map expand · expand1
= { filter p · concat = concat · map (filter p) }
concat · map (filter legal ) · map expand · expand1
= { map f · map g = map (f · g) }
concat · map (filter legal · expand ) · expand1
= { construction of prune }
concat · map (filter legal · expand · prune) · expand1
= { let search = filter legal · expand · prune }
concat · map search · expand1
```

This suggests we can write a solver

> `solve :: Solver`
> `solve = search . freedoms`

where search uses this derivation to solve an incomplete problem and in the case of a completed puzzle, either to return it or not according to whether or not it is safe (and so legal).

	search fss | complete pss       = [ map (map head) pss | safe pss ]
	           | not (complete pss) = (concat . map search . expand1) pss
	           where pss = prune fss

Since, once the committed squares become unsafe, there is no way of filling in the other squares in a safe way, it would be more efficient to abandon the search as soon as the problem becomes unsafe.

> `search :: Matrix Freedoms -> [ Grid ]`
> `search fss | not (safe pss) = []`
> `           | complete pss   = [ map (map head) pss ]`
> `           | otherwise      = (concat . map search . expand1) pss`

and this solves problems much faster than a human.

