

Many recent innovations rely on: fast computers and efficient algorithms. The real question we should be asking as computer scientists is which one is more important?

#### [[The importance of of efficient algorithms]]

The cost of an algorithm can be quantified by the number of steps $T(n)$ in which the algorithm solves a problem of size n.

Imagine that a certain problem can be solved by four different algorithms, with $T(n)=n,n^2,n^3$ and $2^n$ respectively. What is the maximum problem size that the algorithm can solve in a given time?

We will assume that a computer is capable of $10^{10}$ steps per second.

| Cost $T(n)$ (Complexity) | 1 second | 1 hour | 1 year |
| ---- | ---- | ---- | ---- |
| $n$ | $10^{10}$ | $3.6\times10^{13}$          | $3\times10^{17}$                  |
| $n^2$ | $10^5$ | $6\times10^{6}$ | $5\times10^{8}$ |
| $n^3$ | $2154$ | $33000$ | $680000$ |
| $2^n$ | $33$ | $45$ | $58$ |

Suppose a faster computer is capable of $10^16$ steps per second

| Cost | Max. size before | Max size now |
| ---- | ---- | ---- |
| $n$ | $s_1$ | $10^6\times s_1$ |
| $n^2$ | $s_2$ | $1000\times s_2$ |
| $n^3$ | $s_3$ | $100\times s_3$ |
| $2^n$ | $s_4$ | $s_4+20$ |

A $10^6$ times increase in speed in only a factor-of-100 improvement if cost is $n^3$, and only an additive increase of 20 if cost is $2^n$.

So, now we conclude that as computer speed increases...
- it is algorithmic efficiency that determines the increase in problem size that can be achieved.
- so does the size of problems we wish to solve. Thus, designing efficient algorithms becomes even more important.


#### [[From Algorism to Algorithms]]

Invented in India around AD 600, the decimal system was a revolution in quantitative reasoning. Arabic mathematicians helped develop arithmetic methods using the Indian decimals.

A 9th-century Arabic textbook by the Persian Al Khwarizmi was the key to the spread of the Indian-Arabic decimal arithmetic. He gave methods for basic arithmetic, even the calculation of square roots and digits of $\pi$.

Derived from 'Al Khwarizmi', algorism means rules for performing arithmetic computations using the Indian-Arabic decimal system.

The word "algorism" devolved into algorithm, with a generalisation of the meaning to: **a finite set of well-defined instructions for accomplishing some task.**

Before we start though, two questions we ask about an algorithm:
- Is it correct?
- Is it efficient?

Correctness is of utmost importance. It is easy to design a highly efficient but incorrect algorithm. Efficiency is respect to the running time, the space, the network traffic, or any other myriad of variables set for efficiency.

On an actual computer, the running time of a program depends on many factors:
- The running time of an algorithm
- The input of the system
- The quality of the implementation
- The machine running the program

In these notes, we will focus solely on the first point for now.


#### [[Sorting]]

The sorting problem is defined with an input and an output. The input is a sequence of $n$ integers, $a_1,...,a_n$. The output of such a problem is $a_{\sigma(1)},...,a_{\sigma(n)}$ such that $a_{\sigma(1)}\leq a_{\sigma(2)}\leq\;...\leq a_{\sigma(n)}$. The sequences are typically stored in arrays,   

Here is a pseudocode of such a correct algorithm:

```
Input: An integer array A
Output: Array A sorted in non-decreasing order
> for j = 1 to A.length -1
>   key = A[j+1]
>   i = j
>   while i > 0 and A[i] > key
>       A[i+1] = A[i]
>       i = i -1
>   A[i+1] = key 
```

Just to note, this type of pseudocode is called CLRS, and has similarities to Pascal, C, and Java. Pseudocode is for communicating algorithms to humans, so many programming issues like modularity and error handling are ignored.


#### [[Loop-Invariant approach to correctness proof]]

There are three key components of a loop-invariant argument:
- Initialisation: Prove that invariant (I) holds prior to the first iteration
- Maintenance: Prove that if (I) holds just before an iteration, then it holds just before the next iteration.
- Termination: Prove that when the loop terminates, the invariant (I), and the reason that the loop terminates, imply the correctness of the loop-construct.

The approach is quite similar, if you see it already, to mathematical induction, where Initialisation is correspondent to the base case, Maintenance is correspondent to the inductive case, with the Termination is the complete establishment of the proof.

So, let's examine to correctness of our earlier algorithm - Insertion Sort.

- At the start of the j-th iteration, the subarray `A[1..j]` consists of the elements originally in `A[1..j]` but in sorted order.
- When `j=1`, the subarray `A[1..j]` is a singleton and trivially sorted.
- The outer `for` loop terminates when `j=n:=A.length` with `A[1..n]` in sorted order now.
- For maintenance, we need to prove that - if at the start of the j-th iteration `A[1..j]` consists of $a_1,...,a_j$ in sorted order, then at the start of the $(j+1)$-th iteration, we will get a sequence $a_1,...,a_{j+1}$ in sorted order. We need to examine the inner loop for that.
	- if `A[1..j]` consists of $a_1, · · · , a_j$  in sorted order, then, at termination of the while loop, the sequence `A[1..i]` key `A[i+2..j+1]` consists of $a1, · · · , a_{j+1}$ in sorted order. We will use a loop invariant to solve this.
	- `A[1..i]A[i+2..j+1]` is $a_1, · · · , a_j$ in sorted order, and all elements in `A[i+2..j+1]` are strictly greater than key. See source for full proof.


#### [[Running Time Analysis]]

The running time of an algorithm is $$\sum_{all\;statements}\;\;(cost\;of\;statement)\cdot(numbeer\;of\;time\;statement\;executed)$$CLRS assume the following model: for a given pseudocode:
- Line i takes constant time $c_i$.
- When a for and while loop exists normally, the test is executed one more time than the loop body.

This model is well justified when each line of the pseudocode contains:
- a constant number of basic arithmetic operations
- a constant number of data movement instructions
- a constant number of control instructions.

Recall the pseudocode:

```
1 for j = 1 to A.length − 1
2    key = A[j + 1]
3    // Insert A[j + 1] into the sorted sequence A[1 . . j].
4    i = j
5    while i > 0 and A[i] > key
6       A[i + 1] = A[i]
7       i = i − 1
8    A[i + 1] = key
```

Setting $n:=A.length$, the running time is $$\begin{aligned}
T(n) = c_1n+c_2(n-1)+c_3(n-1)+c_4(n-1)+c_5\sum_{j=1}^{n-1}t_j + \\ c_6\sum_{j=1}^{n-1}(t_j-1)+c_7\sum_{j=1}^{n-1}(t_j-1)+c_8(n-1),
\end{aligned}
$$ where $t_j$ is the number of times the test of the while loop is executed for a given value of $j$.

We may analyse the worst case scenario
- The input array contains distinct elements in reverse sorted order i.e. is strictly decreasing.
- Why? Because we have to compare $key=a_{j+1}$ with every element to left of the (j+1)-th element, and so compare with j elements in total. 
- With some extra calculation, we may find $T(n)$ to be a quadratic function.

Best case, we can do the same analysis and find $T(n)$ to be a linear function.

We can also perform an average-case analysis.
- Randomly choose n numbers as input.
- On average, the key in `A[j]` is less than half the elements in `A[1..j]` and greater than the other half, and so, on average the while loop has to look halfway through the sorted subarray `A[1..j]` to decide where to drop the key.
- Hence $t_i=j/2$.
- Although average-case is running time approximately half that of the worst-case, it is still quadratic.

So, Average-case complexity can be asymptotically as bad as the worst-case. It is also not straightforward, since the definition of an "average input" depends on the application. Mathematics on this "average input" may also be difficult for more complex algorithms.

#### [[Asymptotic Notation]]

Let $f,g:\mathbb{R}\to\mathbb{R}^+$ be functions. Define the set $$\begin{aligned}
O(g(n)):=\;\{f:\mathbb{N}\to\mathbb{R}^+:\exists n_0\in\mathbb{N}+.\exists c\in\mathbb{R}^+.\forall n. \\ n\geq n_0\to f(n)\leq c\cdot g(n)\}
\end{aligned}
$$In words, $f\in O(g)$ if there exists a positive integer $n_0$ and a positive real $c$ such that $f(n)\leq c\cdot g(n)$
for all $n\geq n_0$.

Informally, $0(g)$ is the set of functions that are bounded above by g, ignoring constant factors, and ignoring a finite number of exceptions.

If $f\in O(g)$, then we say that "g is an asymptotic upper bound for $f$"

$$\begin{aligned}
O(g(n)):=\;\{f:\mathbb{N}\to\mathbb{R}^+:\exists n_0\in\mathbb{N}+.\exists c\in\mathbb{R}^+.\forall n. \\ n\geq n_0\to f(n)\leq c\cdot g(n)\}
\end{aligned}$$ 
- $3^{98}\in O(1)$. Take $n_0 = 1$ and $c=3^{98}$.
- $5n^2+9\in O(n^2).$ Tale $n_0=3$ and $c=6$.
- Some more functions in $O(n^2)$: $1000n^2$, $n$, $n^{1.9999}$, $\frac{n^2}{\log\log\ n}$ and $6$.

Now, onto a **Lemma** : Let $f,g,h:\mathbb{N}\to\mathbb{R}^+$. Then
1. For every constant $c > 0$, if $f ∈ O(g)$ then $c f ∈ O(g)$.
2. For every constant $c > 0$, if $f ∈ O(g)$ then$ f ∈ O(c g)$.
3. If $f_1 ∈ O(g1)$ and $f_2 ∈ O(g2)$ then $f_1 + f_2 ∈ O(g_1 + g_2)$.
4. If $f_1 ∈ O(g_1)$ and $f_2 ∈ O(g_2)$ then $f_1 + f_2 ∈ O(max(g_1, g_2))$.
5. If $f_1 ∈ O(g_1$) and $f_2 ∈ O(g_2)$ then $f_1 · f_2 ∈ O(g_1 · g_2)$.
6. If $f ∈ O(g)$ and $g ∈ O(h)$ then $f ∈ O(h)$.
7. Every polynomial of degree $l ≥ 0$ is in $O(n^l)$.
8. For any $c > 0$ in $\mathbb{R}$, we have $\log(n^c) ∈ O(\log(n))$.
9. For every constant $c, d > 0$, we have $\log^c(n) ∈ O(n^d)$.
10. For every constant $c > 0$ and $d > 1$, we have $n^c ∈ O(d^n)$.
11. For every constant $0 ≤ c ≤ d$, we have $n^c ∈ O(n^d)$.

There is a shorthand for this. Instead of writing $f\in O(g)$, we often write $$f(n)=O(g(n))$$(read "$f$ is Big-O of $g$").

It is also convenient to write $$f_1(n)=f_2(n)+O(g(n))$$meaning that $$f_1(n)=f_2(n)+h(n)$$where $h(n)$ is a generic function in our asymptotic set.

This is why I don't like the shorthand, since it implies an equality. Truly, there is nothing equal about a function with a set, but it is definitely a member of the set. Two elements of the same set are not necessarily the same element. Imagine that $n^2$ and $n^3$ both equal $O(n^3)$. Clearly, those two functions are equal, and so we can say, by transitivity of equality that this is indeed not an equality.

So why do we use it? Simply put, convenience. We already abuse the equality sign in declaration, so abusing it here doesn't really impact us, just don't let a mathematician look at it.

The Big-O notation is useful for upper bounds. There is an analogous notation for lower bounds, called Big-Omega. We write $$f(n)=\Omega(g(n))$$to mean, "there exists a positive integer $n_0$ and a positive real $c$ such that for all $n\geq n_0$, we have $f(n)\geq c\cdot g(n)$". We call this the asymptotic lower bound of $f$.

Here's a proof: $f(n) = \Omega(g(n))$ if and only if $g(n)=O(f(n))$. 
- From the definition of Big-O, we know that for a positive integer $n_0$ that is $n>n_0$, we have $g(n)\leq c\cdot f(n)$. Now, we can define another $c'=1/c$ where  $f(n)\geq c'\cdot g(n)$ for some $n_1$ and this holds true for all $n>n_1$. This $n_1$ must exist since the condition where we worked off previously is unbounded. 
- From the definition of Big-Omega, we know that for a positive integer $n_0$, that is $n>n_0$, we have $f(n)\geq c\cdot g(n)$. Now, we can rearrange this to get $\frac{1}{c}\cdot f(n)\geq g(n)$.  Now, this is back to the definition of the right hand side, with $c'=\frac{1}{c}$, and with the same reasoning as our first case, an $n_1$ must exist due to the unbounded nature of our initial condition.
QED.

We also have a Big-Theta notation, where both $f(n) = \Omega(g(n))$ and $g(n)=O(f(n))$ are true. We write this notation as such $$f(n)=\Theta(g(n))$$Equivalently, this means that there exist positive reals $c_1$ and $c_2$ and a positive integer $n_0$ such that for all $n\geq n_0$, we have $$c_1g(n)<f(n)<c_2g(n).$$We can think of $f$ and $g$ as having the same order of magnitude.