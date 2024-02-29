Finding the roots or zeros of a continuous function is an ancient art: methods for solving quadratic equations go back to the Babylonians and mathematical history charts progress in finding roots of wider classes of functions. But some equations cannot be solved, and methods for locating approximate roots were developed as early as the 17th century. They are still useful today.

Following the usual pattern, we will begin with root-finding methods for univariate functions $f:\mathbb{R}\to\mathbb{R}$. They will all be iterative, in the sense of the previous notes in [[Accuracy]]: given some starting point they generate either a sequence $(x_0,x_1,x_2,...)$ which aims to converge at some $x^*$ where $f(x^*)=0$, or a sequence of smaller-and-smaller intervals in which $x^*$ is located. They can be compared by the simplicity of the iterative step, their order of convergence, and the range of starting points for which they do converge and the smoothness conditions for the error analysis to hold. Some trade-offs are inevitable here.

Then we consider root-finding in $d$ dimensions. This solves $\mathbf{f}(x)=\mathbf{0}$, usually when $\mathbf{f}:\mathbb{R}^d\to\mathbb{R}^m$ with $m > d$. This is called an overdetermined system, but there is rarely a zero because there are more equations than variables (unless some of the simultaneous equations are redundant). Instead, overdetermined systems are solved by finding $$\arg\min_{x\in\mathbb{R}^d}||\mathbf{f(x)}||$$This is a problem of optimisation rather than root-finding, but root-finding methods can be adapted to it. We will come to it in the next chapter.


#### 5.1 [[When to Stop]]

We will be running iterative algorithms generating a sequence $(x_0,x_1,...)$ that, hopefully, converges to a root $x^*$. We are most unlikely to find an exact solution to $f(x)=0$, so at some point we stop and take $x_n$, as our approximate solution. Termination conditions are usually phrased in terms of a tolerance parameter $\text{tol}$, a small positive number, and can include:

- $|x_n-x_{n-1}|<\text{tol}$
- $|x_n-x{n-1}|<\text{tol}|x_n|$
- $|f(x_n)|<\text{tol}$
- $n=N$ (we have run out of time and should warn the user that the specified tolerance wasn't met.)
- an iteration step was ill-defined.

It is common to combine the first two into a single condition:

- $|x_n-x_{n-1}|<\text{tol}(1+|x_n|)$, the iteration either made a small relative change or a small absolute change.

Whether to prefer conditions one, two, or three depends on the application. If $f$ is particularly shallow near the root, and $\text{tol}$ is small, there might not exist any floating point number where the third condition holds. And a word of warning about relative forward error: seeking a root very close to zero is prone to roundoff error problems.

We should always include condition 4 to prevent nontermination if, for example, an iteration gets stuck in a loop. We must, of course, always include the last condition. 

How do we choose the tolerance parameter $\text{tol}$? It depends on the application and may be specified by the user. It often depends to $2\epsilon$ or $4\epsilon$, where $\epsilon$ is machine epsilon, but this can be ambitious if the iterative step suffers from roundoff error.


#### 5.2 [[Interval Bisection (1 dimension)]]

The most reliable way to locate a root in one dimension is to start with a bracket, an interval $(a_0,b_0)$ where $f(a_0)$ and $f(b_0)$ have different signs. We can be sure that $f$ has at least one root in this interval, as long as $f$ is continuous (this is called the intermediate value theorem). A method that finds and refines a bracket is called an inclusion method; most of the other root-finding algorithms we will see are not inclusion methods.

Given a bracket $(a_n,b_n)$, we can refine it to a bracket exactly half as big, by testing the sign of $f(x_n)$ where $x_n=\frac{a_n+b_n}{2}$. Either $(a_n,x_n)$ or $(x_n,b_n)$ becomes the new bracket $(a_{n+1},b_{n+1})$, like divide-and-conquer. That is not to say that there is no root in the other half - $f$ might have lots of roots - but that there is guaranteed to be at least one in the smaller bracket. 

Just as binary search does in discrete algorithms, this halves the problem, hence the name interval bisection. Unlike binary search, it is unlikely to terminate by finding an exact root. Instead, we should terminate when the answer is accurate enough for our purposes, or when any further subdivision is impossible because of floating-point accuracy. Because bisection has a precise error bound, it is common simply to specify the number of iterations in advance.

As an aside: when practising bisection, or indeed any of the methods in these notes, do not be tempted by the simplicity of a recursive implementation. The overhead of the recursive call is often significant, and avoided by using iteration instead.

Interval bisection has two drawbacks: it is slow, and it does not generalise to higher dimensions. Here is a pseudocode of the interval bisection algorithm:

```
procedure BISECTION(f, a_0, b_0, N, tol)
	require f(a_0)f(b_0) < 0
	n = 0
	x_0 = (a_0 + b_0) / 2
	repeat 
		if f(a_n)f(x_n) then
			(a_{n+1}, b_{n+1}) = (a_n, x_n)
		else
			(a_{n+1}, b_{n+1}) = (x_n, b_n)
		endif
		n = n + 1
		x_n = (a_n + b_n) / 2
	until n = N or abs(b_n - a_n) < tol
	return x_n
endprocedure
```

![[Figure 5.1.png]]

[[Figure 5.1.png]] shows Interval bisection in a graphical form.


###### 5.2.1 Error Analysis

There is almost nothing to analyse: at each step the bisection method exactly halves the interval. If we take the midpoint of the interval as the $n$-th approximation, $x_n = \frac{a_n+b_n}{2}$, where the error is not guaranteed to reduce at every step. But since the error is always half of the previous, we can say for certain that this is linear convergence.

We need $\lceil \log_2\frac{b_0-a_0}{2\text{tol}}\rceil$ to guarantee absolute error less than $\text{tol}$. Alternatively, we can set a relative error bound, or stop iterating when we achieve $|f(x_n)|<\text{tol}$.


###### 5.2.2 Finding a Starting Bracket

Sometimes there is no obvious bracket $(a_0,b_0)$. There are simple algorithm to help find one, but they require some information about the function. This is necessary because an arbitrary function might be positive everywhere, ducking below the $x$-axis for a brief time only. Without any information as to whether this could happen, or a minimum on the interval that is below the $x$-axis, there is no algorithm that guarantees to find a bracket.

But, for example, if we know that the function is increasing, then we could test $(-1,1)$ as a bracket, and if not then $(-2,2)$, and so on. This guarantees to find a starting bracket in a number of steps logarithmic in the size of the root. In other situations, we might search for a bracket in the form $(0,2^k)$ or $(0,2^{-k})$. Failing that, we will need to do some mathematics to bound $f(x)$ and derive a bracket using algebra.

******
Example 5.1  Let us return to an example from [[Optimisation]]. After solving the Lagrange system, we found that $$\pi_i(\lambda)=\frac{1}{1+\exp(\lambda c_i)}$$where $c_i \geq 0$ are known constants, but $\lambda$ is unknown. We determine $\lambda$ from the payload constraint $$f(\lambda)=\sum_{i=1}^nH(\pi_i(\lambda))-m=0,$$where $m < n$ is a known constant. Observe that $\pi_i(0)=\frac{1}{2}$, so $f(0)>0$. Also, all $\pi_i$ and $f$ are decreasing in $\lambda$. Therefore, we seek a starting bracket of the form $(0,2^k)$, and having found it use interval bisection to locate $\lambda$. In this application, we are not concerned about setting a relative or absolute error bound on $\lambda$, but are instead content to stop iterating when $\sum_{i=1}^nH(\pi_i(x_i))\in(m,m+1)$.

******

#### 5.3 [[Newton's Method (1 Dimension)]]

A powerful, sometimes temperamental, root-finding method is to approximate a function $f(x)$ by its first order Taylor polynomial, and then solve the approximation $$\hat{f}(x)=f(x_0)+(x-x_0)\frac{df}{dx}(x_0)=0 \to x=x_0-\frac{f(x_0)}{\frac{df}{dx}(x_0)}$$![[Figure 5.2.png]]
The figure above shows the visual representation of newton's method.

Using the right-hand side as $x_1$, then iterating the same step at $x_1$ to derive $x_2$, and so on, gives Newton's method: $$x_{n+1}=x_n-\frac{f(x_0)}{\frac{df}{dx}(x_0)}$$This is known as the Newton-Raphson method, because newton only used it on polynomials, while Joseph Raphson refined it for non-polynomial equations.

As we shall see, Newton's method can converge rapidly, but it does require evaluation of the derivative of $f$. This can be significantly slower than the evaluation of $f$, and is some cases it is simply unknown. 

To make this method practical, we should state termination criteria, including a maximum number of iterations. We must also handle an error if $\frac{df}{dx}(X_0)=0$, where the iteration is undefined. Unfortunately, there are other ways Newton's method can fail: the iteration can in fact diverge: it can converge to a difficult root than we hoped, or it can get stuck in an oscillation. We might hope that these cannot happen if we start with $x_0$ that is 'reasonably close' to our root. For 'well-behaved' functions, we will need to prove this property.

A useful observation about Newton's method concerns the termination criteria. By the same Taylor expansion derived $$f(x^*)=f(x_n)+(x^*-x_n)\frac{df}{dx}(x_n)+e_2$$where $e_2$ is a remainder term of order $(x^*-x_n)^2$. Neglecting the error term, and using $f(x^*)=0$, this rearranges to $$x^*-x_n\approx-\frac{f(x_n)}{\frac{df}{dx}(x_n)}=x_{n+1}-x_n$$This is an example of a posteriori error estimate, and it justifies the use of $|x_n-x_{n-1}| < \text{tol}$ as a termination condition: this approximates a bound on the forward error, which is not normally known. The cost is that we must perform one further iteration in order to learn that the previous answer was sufficient. On the other hand, a termination condition $|f(x_n)|<\text{tol}$ is a bound on the backward error.

****
Here's another algorithm, for the Newton Raphson's method

```
procedure Newton1D(f, x_0, N, tol)
	n = 0
	repeat
		x_{n+1} = x_n - f(x_n) / diff(f,x_n)
		n += 1
	until n = N or abs(x_n - x_{n+1}) < tol(1 + abs(x_n))
	return x_n
endprocedure
```
****
![[Figure 5.3.png]]

The figure above shows how the Newton Raphson method fails in different scenarios.

----
Here is an example: The equation $$x^3-2x+2$$has one root. Bisection shows that the root lies between -2 and -1. Newton's iteration is $$x_{n+1}=x_n-\frac{x_n^3-2x_n+2}{3x_n^2-2}$$As we will see later, this iteration does not always, but is extremely fast when it does converge.
****
###### 5.3.1 Error Analysis

Newton's method is analysed using Taylor's theorem. We want to find conditions on $f$ to make it 'well-behaved', and how 'reasonably close' we must be to the root to guarantee quadratic convergence. We cannot find necessary conditions for the latter (in fact the regions of convergence for Newton's method are often fractal) but we can find useful sufficient conditions. A good combination is to start root-finding using interval bisection, then switch to Newton's method when the bracket is small enough to guarantee quadratic convergence from then on: we need to sufficient conditions for such a bracket.

We first establish a bound between the error at step $n$ and step $n+1$, then give an interval $(x^*-c, x^*+c)$ for which convergence is guaranteed and of quadratic order. Since $x^*$ is not usually known, we convert this into a sufficient condition when we only know a bracketing interval for $x^*$.

****
**Lemma 5.3**  Let $f$ have two continuous derivatives, and $f(x^*)=0$. Write $\epsilon_n=x_n-x^*$. Therefore

1. If $x_n\in I=(x^*-c,x^*+c)$ and $$A(c)=\frac{\max_{\beta\in I}|\frac{d^2f}{dx^2}(\beta)|}{\min_{\alpha\in I}|\frac{d^2f}{dx^2}(\alpha)|}$$then $|\epsilon_{n+1}|\leq\frac{A(c)}{2}\epsilon_n^2$.
2. If $x_0\in I$, and further $\frac{cA(c)}{2}<1$, then Newton's method converges at least quadratically to $x^*$.
3. If $x^*\in(m-c,m+C)$ and for $I=(m-2c,m+2c)$, $$B(c)=\frac{\max_{\beta\in I}|\frac{d^2f}{dx^2}(\beta)|}{\min_{\alpha\in I}|\frac{d^2f}{dx^2}(\alpha)|}$$and further that $\frac{cB(c)}{2}<1$, then Newton's method starting from $x_0=m$ converges at least quadratically to $x^*$.
4. Such an interval $I$ exists if $\frac{df}{dx}(x^*)\neq 0$.

Here is the proof for this:

$$\begin{aligned}x_{n+1} - x^* &= \frac{x_n - \frac{f(x_n)}{df/dx(x_n)}}{df/dx(x_n)} - x^* \\
               &= \frac{df/dx(x_n)(x_n - x^*) - f(x_n)}{df/dx(x_n)} \\
               &= \frac{f(x^*) - (f(x_n) + (x^* - x_n)df/dx(x_n))}{df/dx(x_n)} \\
               &= \frac{1}{2}(x^* - x_n)^2 \frac{d^2f/dx^2(\xi)}{df/dx(x_n)}, \quad \xi \in (x_n, x^*)
\end{aligned}$$

This is the proof for the first statement. You can find the rest of the proofs on page 114 of Andrew Ker's notes.

****

Here is another example: **Example 5.4**  In applied probability, a branching process is a sequence of integer-valued random variables $(Z_0,Z_1,...)$ representing the size of some population at discrete time intervals (generations) $t=0,t=1,...$. Branching processes can be used to model disease, the spread of malware, and genetics. The population evolves as follows: at first there is one individual $Z_0 =1$. At each time interval every individual of the existing population generates some random number of successors and dies. Thus $Z_{n+1}$ is the sum of $Z_t$ i.i.d. random variables.

One of they key questions to ask about a branching process is the probability of ultimate extinction: the probability that $Z_t=0$ at any point $t$. Using only elementary probability techniques, it can be shown that this probability is a solution of the equation $$G(x)=x$$where $G$ is the probability generating function for each individual's number of successors. Note that $x=1$ is always a solution of our equation earlier, but when ultimate extinction is not certain there is also a smaller root: that is the probability we seek.

For example, if each member of the population generates a Poisson number of successors, with parameters $\lambda >1$, the the probability of ultimate extinction is the lower root of $$f(x)=e^{\lambda(x-1)}-x=0$$We must apply Newton's method carefully, to avoid converging to the trivial root of $x=1$. Also note that $\frac{df}{dx}=0$ for some value of $x$, which we need to avoid.

----

#### 5.4 [[The Secant Method (1 dimension)]]

![[Figure 5.4.png]]

Computing derivatives can be expensive, or impossible in some cases. An alternative to approximating $f$ by a linear Taylor polynomial is to use linear interpolation. Given two points on the curve $(x_0,f(x_0))$ and $(x_1,f(x_1))$, we approximate and solve: $$\hat{f}(x) = \frac{(x - x_0)f(x_1) - (x - x_1)f(x_0)}{x_1 - x_0} = 0 \Leftrightarrow x = x_1 - \frac{f(x_1)(x_1 - x_0)}{f(x_1) - f(x_0)}.
$$Using the right-hand side as $x_2$, and iterating this method, gives us the Secant method: $$x_{n+1}=x_n-\frac{f(x_n)(x_n-x_{n-1})}{f(x_n)-f(x_{n-1})}$$An advantage of this method is that $f(x_{n-1})$ was already computed at the previous iteration, so it only requires evaluating $f$ once per step. And of course it does not use derivatives at all. The Secant method predates Newton's method, some say by thousands of years.

Another way to understand the Secant method is to think of $\frac{f(x_n)-f(x_{n-1})}{(x_n-x_{n-1})}$ as an approximation of $\frac{df}{dx}(x_n)$: it is the truncation of the infinite process as $(x_n-x_{n-1})\to0$, which should happen when $x_n$ converges. The class of Newton-like methods that approximate the derivative/gradient are called quasi-Newton methods.

Just as with Newton's method, the Secant method can fail. If we hit a point where $f(x_n)=f(x_{n-1})$, the quotient is undefined. Sometimes we will wish to terminate when $|f(x_n)-f(x_{n-1})|<\text{tol}|f(x_n)|$, otherwise the next step might go far from the root. The iteration can diverge, oscillate, or converge to a root other than the intended. To know when this cannot happen, we need to analyse the Secant method's error.

****
Here's an algorithm for the Secant method:

```
procedure SECANT(f, x_0, x_1, N, tol) 
	y_0 = f(x_0)
	n = 1
	repeat
		y_n = f(x_n)
		x_{n+1} = x_n - (y_n * (x_n - x_{n-1})) / (y_n - y_{n-1})
		n = n + 1
	until n = N or abs(x_n - x_{n-1}) < tol(1 + abs(x_n))
	return x_n
endprocedure
```

***

We can always apply the secant method in the same cases where we would apply Newton's method, but in the case where the derivative is harder to compute.


###### 5.4.1 Error Analysis

Error analysis of the Secant method is similar to newton's method, and is actually an exercise on the problem sheet.

***
**Lemma 5.6** If $f:\mathbb{R}\to\mathbb{R}$ has at least two continuous derivatives, and $f(x)=0$, then for any $a,b\neq 0$, $$\frac{f(x+a)}{a}-\frac{f(x+b)}{b}=\frac{(a-b)^2}{2}\frac{d^2f}{dx^2}(\xi)$$for some $\xi$ in an interval containing $x$, $x+a$, and $x+b$.
***

We will show that the Secant method has order-$\phi$ convergence, for some $\phi$ between 1.5 and 1.7, and that conditions of Lemma 5.3 are also sufficient to ensure this case, as long as both $x_0$ and $x_1$ lie in the interval in question. Given a well-behaved function, the Secant method is super-linear but not as high order as Newton's method. But do note the following though, since $\phi^2>2$, this means that if we can perform two secant steps in the time it takes one step of Newton's method, we are probably winning.


#### 5.5 [[Other Methods (1 dimension)]]

There is a connection between Newton's method for root-finding and the Midpoint rule for numerical integration: both approximate the function by its first-order Taylor polynomial. There is a similar connection between the secant method and the Trapezium rule: both approximate the function by a linear interpolation of two points on the curve.

It is natural to ask whether we can do better with higher-order Taylor approximations or higher-order interpolation. Using the solution of the second-order Taylor approximation as an iteration gives rise to Halley's method, which has cubic convergence as long as certain conditions are met. However a second-order polynomial might have zero roots, or two roots, and solving them requires the square-root function. Halley's method avoids these problems by making a further approximation.

The quadratic equivalent of the Secant method is called Muller's method. It keeps track of three points on the function, interpolation them by a parabola and following the parabola down to its intersection with the $x$-axis. Similar problems arise as with the quadratic Taylor approximation. When the parabola has no real roots, one way to progress is to allow complex roots. Thus Muller's method may find a sequence of complex numbers that converge to a root. Unfortunately, it will sometimes be a complex root, which may not be what we want. Muller's method has order of convergence approximately 1.84.

There is a better option than either of these higher-order methods. Instead of finding a function $y=ax^2+bx+c$ that interpolates three known values of $f(x)$, instead fit $x=ay^2+bY+c$. This brilliant idea is called the Inverse Quadratic Interpolation (IQI) and it is no more difficult that quadratic interpolation, yet it always has exactly one real solution to $y=0$, and it is algebraically simple to find: $x=c$. Like Muller's method it has order of convergence approximately 1.84.

The gold standard in one-dimensional root-finding is a combination of bisection, the Secant method, and IQI called Brent's method. (It is Brent's modification to an idea originally by Decker.) It is an inclusion method,  so it maintains a bracket that contains a root. Given an initial bracket, it always converges: linearly if the function is badly behaved, and with order approximately 1.84 if the function is well-behaved or near the root. It does not need to evaluate the derivative at all. Brent's method works by keeping track of three values of the functions, using IQI where possible but falling back to the Secant method if IQI tries to go outside the known bracket, and to bisection if IQI/Secant step does not provide sufficient reduction in the bracket size. Requiring about 30 lines of code, it is at the core of one-dimensional root-finding libraries.


#### 5.6 [[Newton's Method (d dimensions)]]

Root finding in d dimensions means solving $$f(\mathbf{x})=\mathbf{0}$$This is more difficult than the one-dimensional case, and not just because dealing with vectors is more difficult than scalars. In more than one dimension there is almost no way to bracket a root, hence nothing is safe as interval bisection won't help us find a good starting point for other iterative methods, to fall back on if they fail to converge, or even to prove that a root exists. For now, we will accept this uncertainty.

To derive a Newton method, break down the initial equation into $d$ simultaneous equations $$f_1(x)=0,...,f_d(x)=0,$$where $f_i:\mathbb{R}\to\mathbb{R}$ are the components of $\mathbf{f}$. Given an initial point $x_0$, we approximate each component using its 1st-order Taylor polynomial at $x_0$, then solve for all approximate components simultaneously equal to zero: $$
\begin{aligned}
\hat{f}_1(x) &= f_1(x_0) + \left( \frac{df_1}{dx}(x_0) \right)^T (x - x_0) = 0 \Leftrightarrow \left( \frac{df_1}{dx}(x_0) \right)^T (x - x_0) = - f_1(x_0), \\
&\vdots \hspace{10cm} \vdots \\
\hat{f}_d(x) &= f_d(x_0) + \left( \frac{df_d}{dx}(x_0) \right)^T (x - x_0) = 0 \Leftrightarrow \left( \frac{df_d}{dx}(x_0) \right)^T (x - x_0) = - f_d(x_0).
\end{aligned}
$$These $d$ equations can be gathered into one $d\times d$ matrix equation $$(J(f)(x_0))(x-x_0)=-f(x_0)$$This will give rise to an iteration that is often written $$x_{n+1}=x_{n}-(J(f)(x_n))^{-1}f(x_n).$$This shows how the one-dimensional Newton's method is simply a special case of the d-dimensional case, since $J(f)^{-1}$ is the d-dimensional analogue of $1/\frac{df}{dx}$. However, in practice we should not invert the Jacobian, because finding a solution $\Delta x$ to $$(J(f)(x_n))\Delta x=-f(x_n)$$by Gaussian elimination is typically about three times faster than inverting. Instead solve this for $\Delta x$, then set $x_{n+1}=x_n+\Delta x$.

If we reach a point $x_n$ where $J(f)(x_n)$ is singular, then we may fail to have a solution. This is equivalent of the derivative being zero in the one-dimensional case. In that case, which is the d-dimensional analogue of $\frac{df}{dx}(x_n)=0$, we must first terminate with an error. Otherwise, we terminate the iteration under the usual conditions: either we reach a maximum number of steps, or we have converged sufficiently close to a root. This might be specified in terms of the forward error or the backward error.

The computational cost of Newton's method is in evaluating the Jacobian ($O(d^2)$ entries which might be slow to compute) and solving the system, which takes $O(d^3)$. These are required at each step of the iteration.

***
Here is the algorithm of Newton's method in d dimensions:

```
procedure NEWTON(f, x_0, N, tol)
	n = 0
	repeat
		Solve for detla(x) 
		x_{n+1} = x_n + delta(x)
		n = n + 1
	until n = N or abs(delta(x)) < tol(1 + abs(x))
	return x_n
endprocedure
```

***

Error analysis of the d-dimensional Newton's method is beyond the syllabus. Under smoothness condition on $f$, and assuming that its Jacobian is non-singular $x^*$, there is a region around $x^*$ in which quadratic convergence is guaranteed.

Here is an example of this in action: An interesting two-dimensional root-finding problem arises if we modify our earlier example from probability. Let us suppose that the population consists of two types of individuals, say type A and B. At each generation, each individual of type A independently and identically gives rise to some number of type-A offspring plus some number of type-B offspring, with the P.G.F being $G_{AB}$, before dying. Similarly, each individual of type B gives rise to type-A offspring and type-B offspring. What is the probability that the entire population becomes extinct, if we start with one individual?

Using only elementary probability, it can be shown that the probabilities of extinction after starting with one individual of type A, x, and extinction after starting with one individual of type B, y, satisfy the simultaneous non-linear equations: $$\begin{aligned}x&=G_{AA}(x)G_{AB}(y)\\ y&=G_{BA}(x)G_{BB}(y)\end{aligned}$$For specified generating functions, Newton's method (applied carefully) is a fast way to solve this system.

***

###### 5.6.1 Globalising Newton's Method

We briefly mention a way to make Newton's method less fragile. At each step we compute $\Delta x$, but we reject it if $x_{n+1}$ is not a 'better' solution than $x_n$. A sensible way to define 'better' is to insist that $$||f(x_{n+1})||<||f(x_{n})||$$When this does not hold, we set $x_{n+1}=x_n + \lambda\Delta x$ for some $\lambda\in(0,1)$. We will see more of this idea in the next set of notes, which is called backtracking, because we take a step back towards $x_n$ from $x_{n+1}$.

This is a so-called damped Newton's method. It can be shown that, for sufficiently small $\lambda$, the damped iteration does satisfy the earlier equation, but damping every step leads only to linear convergence. There are algorithms which choose suitable $\lambda$ at each step, which do achieve quadratic convergence near a root, and which terminate after a finite number of steps instead of oscillating indefinitely. That is to say: they do not guarantee convergence, but they do guarantee that the user knows whether convergence was going to happen or not. Even if it happens, convergence may still be slow if $x_0$ was not close to a root.


#### 5.7 [[Quasi-Newton Methods (d dimensions)]]

Newton's method requires a lot of derivatives: all $d^2$ elements of the Jacobian, which need to be derived algebraically and then evaluated at each step. Naturally we might consider quasi-Newton methods which are analogous to the Secant method. Recall that the Secant method can be understood as approximating $\frac{df}{dx}(x_n)$ by $\frac{f(x_n)-f(x_{n-1})}{(x_n - x_{n-1})}$. How do we do this in d dimensions? We need to approximate the entire Jacobian $J(f)(x_n)$ which, note, depends on $n$. Thus the approximated Jacobian will evolve through the iterations, preferably without doing too much work at each step.

Let us derive an approximate Jacobian for the n-th step, which we will call $\hat{J}_n$. If $y_{n-1}=f(x_{n-1})$ and $y_n=f(x_n)$, then we want $\hat{J}_n$ to satisfy the secant equation $$y_n=y_{n-1}+\hat{J}_n(x_n-x_{n-1})$$but in more than one dimension this does not determine $\hat{J}_n$: it is a system of d equations, but $\hat{J}_n$ has $d^2$ entries to find. We need to add further constraints on $\hat{J}_n$.

One sensible way to constrain $\hat{J}_n$ is to note that the earlier equation gives no information about $\hat{J}_nz$ for $z$ orthogonal to $x_n-x_{n-1}$. We could therefore specify that $\hat{J}_n$ has no reason to change from $\hat{J}_{n-1}$ when applied to such vectors: $$\hat{J}_nz=\hat{J}_{n-1}z, \quad\text{whenever}\quad z^T(x_n-x_{n-1})=0.$$This supplies enough constraints to determine $\hat{J}_n$. We would need to couple this with a choice of initial $\hat{J}_0$.

An alternative way to constrain $\hat{J}_n$ is to say that we want it to be close to $\hat{J}_{n-1}$. The reasoning is that, once we get close to the root, $x_n$ should not be very different from $x_{n-1}$, and hence the true Jacobians will not be very different when evaluated there. A principle we could apply is $$\text{minimize}||\hat{J}_n-\hat{J}_{n-1}||$$where $||\cdot||$ is a matrix norm. There are many matrix norms, and in the case we choose the Frobenius norm $||A||=\sqrt{\sum_{i,j}a_{ij}^2}$.

Happily, the solutions to the second equation above combined with the two other equations above become identical. Write $\Delta y=y_n-y_{n-1}$ and $\Delta x = x_n - x_{n-1}$, then $$\hat{J}_n=\hat{J}_{n-1} + \frac{\Delta y-\hat{J}_{n-1}\Delta x}{||\Delta x||^2}\Delta x^T.$$Updating the Jacobian in this way, then using the quasi-Newton step: $$x_{n+1}=x_n+\Delta x, \quad\text{where}\quad \hat{J}_n\Delta x=-f(x_n)$$is known as Broyden's method, and it is one of a family of quasi-Newton methods invented by Charles Broyden in the 1960s. At each step of the iteration we need only evaluate $f$ once, update $\Delta x$, $\Delta y$ and compute $\hat{J}_n$.

As with Newton's method, we need a starting point $x_0$, but we also need a starting Jacobian $\hat{J}_n$. Absent other information, this might be $J(f)(x_0)$, which unfortunately does require us to compute the Jacobian of $f$. We cannot use a second point $x_1$ to determine $J(f)(x_0)$, as the Secant method did, because $J(f)$ is underdetermined as described above. To avoid computing the Jacobian of $f$ at all, in some cases it is safe to use a generic starting Jacobian $\hat{J}_0=-I$, or a suitably-scaled version of this. $\hat{J}_n$ will still become a good approximation to $J(f)(x_n)$ after $O(d)$ iterations. That is beyond our scope.

As described in the algorithm below, each step of Broyden's method requires $O(d^3)$ work in the dominant step, which is solving the system. Compared with Newton's method, we avoided evaluating the Jacobian at each stage, at the cost of more complex formulae, but did not change the complexity of the matrix operations. There are some efficiency improvements that can be made to Broyden's method, but perhaps the most important is to avoid this dominant step of solving $d\times d$ system. Instead of updating $\hat{J}_n$ at each iteration, equivalent formulae can be found that keep on track of $\hat{J}_n^{-1}$.

Like the secant method in one-dimension, Broyden's method converges if started suitably close to an isolated root $x^*$, but not as fast as Newton's method. Sufficient conditions for super-linear convergence have been found, but the order of convergence depends on the nature of the problem. If $d$ is large, and the efficient version of the algorithm that tracks $\hat{J}_n^{-1}$ is used, the need for more iteration steps is easily outweighed by the much faster speed of computing each step. Termination conditions can be similar to other d-dimensional methods, in terms of the norm of the change at each step, or the backward error.

As with Newton's method in d dimensions, convergence is not guaranteed unless $x_0$ is sufficiently close to a root, and it is difficult to determine in advance whether a given $x_0$ is 'sufficiently close'.

***
Here is the algorithm for Broyden's method 

```
procedure BROYDEN(f, x_0, N, tol)
	y_0 = f(X_0)
	J_hat_0 = J_f(x_0)
	Solve for delta(x)
	x_1 = x_0 + delta(x)
	n = 1
	repeat
		y_n = f(x_n)
		delta(y) = y_n - y_{n-1}
		delta(J) = (delta(y) - J_hat_{n-1}(delta(x))) / abs(delta(x))^2 * delta(x).tranpose
		J_hat_n = J_hat_{n-1} + delta(J)
		Solve for delta(x)
		x_{n+1} = x_n + delta(x)
		n += 1
	until n = N or abs(delta(x)) < tol(1 + abs(x_n))
	return x_n
return x_n
```

***

There are backtracking techniques to make Broyden's method more robust to bad starting points. They generally do not guarantee convergence, but make failure less likely. There are also limited-memory methods which avoid having to store the Jacobian $\hat{J}_n$: bear in mind that these matrices are $O(d^2)$ in size, which might be impractical to store if the dimension is, say, one million. Limited-memory methods store only some recent vectors which are sufficient to perform calculations with an approximate Jacobian, reducing the space requirement to $O(d)$.