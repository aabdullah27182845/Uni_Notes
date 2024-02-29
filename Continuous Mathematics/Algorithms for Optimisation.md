A major application of numerical methods is in finding the minimum of a function. In these notes, we will only consider unconstrained optimisation, although numerical methods for constrained optimisation are equally important. In [[Optimisation]] we saw that a local minimum of $f\in\mathbb{R}^d\to\mathbb{R}$ occurs only when $\frac{df}{dx}=0$, but that more is needed to rule out a saddle point or local maximum: $f$ having a positive definite Hessian at the stationary point, or being convex, are sufficient conditions.

In applications, however, finding solutions to $\frac{df}{dx} = 0$ can be impossible analytically. Such applications include most of machine learning, image and signal processing, and resource allocation. In each case a loss function, in some fields called energy, is to be minimised, but the minimum has no closed form. So we turn to iterative methods.

We must be modest in our ambitions. A local minimum is all we expect to achieve. For convex functions, a local minimum is the global minimum and all is well, but for non-convex functions it can be extremely difficult to find the global minimum. We will assume that the objective function $f$ is bounded below, and many methods will assume that $f$ has a continuous derivative and perhaps Hessian too.

The astute reader may ask: why not simply add the d-dimensional root-finding methods of [[Algorithms for Root-Finding]] to the simultaneous equations $\frac{df}{dx}=0$? We will come to this idea later.

#### 6.1 [[When to Stop]]

Once again, we will be iterating, in the case of d-dimensional optimisation giving a sequence of vectors $(x_0,x_1,...)$ that hopefully converges to a local minimum $x^*$. We need to give termination criteria, and things are not quite the same as for root-finding. First, because we are not trying to find a particular value of $f$, we cannot give a backward error criterion in terms of $f(x_n)$. Instead, we might have conditions on the amount that $x_n$ changed, the amount that $f(x_n)$ changed, or the gradient of $f(x_n)$. Picking good termination criterion is tricky, but some common conditions include:

1. $||x_n-x_{n-1}|| < \text{tol}(1 + ||x_n||)$, the location of the approximate minimum did not change much on the last iteration.
2. $|f(x_n) - f(x_{n-1})|<\text{tol}(1+|f(x_n)|)$, the value of the approximate minimum did not change much on the last iteration.
3. $||\frac{df}{dx}(x_n)||<\text{tol}$, the gradient of the function, for methods that compute it was sufficiently close to zero. Under some conditions this is also an estimate of forward error.
4. $n = N$, we have run out of time
5. An iteration step was ill-defined.

Whether to pick 1,2, 3, or a combination, depends on the requirements of the application and may depend on the properties of $f$. Condition 2 is considered rather risky, as some functions can decrease ever more slowly even as they diverge to $-\infty$. Condition 1 is good for Newton-like methods, but in other methods it can simply be an indication that the step size is too small. When the gradient is available, condition 3 can be a good choice. We should always include conditions 4 and 5.

Another difference from root-finding is the tolerance. How precisely can we approximate the location of the minimum? Less than you might imagine actually. Consider the one-dimensional case: whenever $$\frac{|f(x)-f(x^*)|}{|f(x^*)|}<\epsilon$$the values of $f(x)$ and $f(x^*)$ are, or might be, indistinguishable as floating-point numbers. Since $\frac{df}{dx}(x^*)=0$, the left-hand size is $$\frac{\frac{1}{2}(x-x^*)^2|\frac{d^2f}{dx^2}(\xi)|}{|f(x^*)|}$$for some $\xi\in(x,x^*)$. So $f(x)$ and $f(x^*)$ may be indistinguishable when $$\frac{|x-x^*|}{|x^*|}<\sqrt{\frac{2|f(x^*)|}{|x^*|^2|\frac{d^2f}{dx^2}(\xi)|}}$$The second square root is practically a constant, since $\frac{d^2f}{dx^2}(\xi)$ will be almost exactly $\frac{d^2f}{dx^2}(x^*)$, which depends on the problem we are solving. But apart from esoteric cases it will not be many ordered of magnitude different from 1. Thus, we cannot distinguish $f(x)$ and $f(x^*)$ better than machine roundoff if $|x-x^*|$ is of the order of $\sqrt{\epsilon}$. The same applies, with more involved linear algebra, in d dimensions.

So, a sensible default value of $tol$ for optimisation is $\sqrt{\epsilon}$, not $O(\epsilon)$. If working in double-precision arithmetic, do not expect to find the location of a minimum with relative error better than about $10^{-8}$. Knowing this limitation can save a lot of pointless iterations.


#### 6.2 [[Golden Section Search (1 dimension)]]

In one-dimensional root-finding, we had the powerful concept of a bracket, an interval in which a root must lie. The same exists for minimisation. Here, a bracket is a triple where the value of $f$ is lower in the middle than at either end: $$(a,b,c) \text{ with } a<b<c.f(b)<f(a),f(b)<f(c)$$If $(a,b,c)$ is a bracket then at least one local minimum must occur somewhere between $a$ and $c$. 

We can refine a bracket using a method similar to interval bisection. If $z\in(a,b)$ and $f(z)<f(b)$ then $(a,z,b)$ is a smaller bracket; conversely if $f(z)>f(b)$ then $(z,b,c)$ is a smaller bracket. A symmetrical argument holds if we pick $z\in(b,c)$ instead. Then we iterate to produce smaller and smaller brackets.

How best to pick $z$? We want to subdivide the largest part of the original bracket, so $z\in(a,b)$ if $b-a>c-b$, otherwise $z\in(b,c)$. Suppose the latter, and let $$c-z=\phi(c-b)$$so that $z$ divides the interval $(b,c)$ in the ratio $1-\phi:\phi$. The length of the new bracket will either be $z-a$ or $c-b$, but we cannot know which until we pick $z$ and evaluate $f(z)$. So that back luck does not penalize us, we ensure that these lengths are equal: $$z-a=c-b$$This is enough to determine $z$, but it can lead to brackets that do not shrink as fast as they might. Instead, we make the further assumption that the previous bracket $(a,b,c)$ has the same proportions as $(b,z,c)$, i.e. $$c-b=\phi(c-a)$$Combining the last three equations, we end up with $$c-b=\phi(c-a)=\phi(c-z+z-a)=(\phi^2+\phi)(c-b)$$and $$\phi = \frac{\sqrt{5}-1}{2}\approx0.618$$This says that we should divide the bigger interval in the golden ratio, in order to find the location of $z$. This is why this search is called the golden section search.

For starting an iteration using the above bracket-shrinking process, the starting bracket $(a_0,b_0,c_0)$ would ideally also have $b_0-a_0:c_0-b_0$ in the same ratio. But even if it does not, choosing subsequent $z$ using our equations will still give a sequence of brackets whose ratio rapidly approaches the optimal.

A careful implementation of golden search needs only one evaluation of $f$ at each step. The value of $f(b)$ can be remembered from the previous step. No derivatives are evaluated, and $f$ need not even be differentiable. The method has linear convergence because the size of the new bracket $c_{n+1}-a_{n+1}=c_n-b_n=\phi(c_n-a_n)$. Convergence is not quite as fast as interval bisection for a root, where the bracket shrinks by a factor of 0.5 on each step; here it only shrinks by a factor of approximately 0.618. Golden section search normally terminates when the total width of the interval is less than $tol$ times the middle point $b_n$. Remember again: $tol$ should generally not be less than $\sqrt{\epsilon}$.

![[Figure 6.1.png]]

Just like interval bisection, the method does need the user to find a starting bracket. Sometimes this is easy to find algebraically. Otherwise we might try an expanding search for a bracket: for example, if we can prove the the minimum must be at some positive $x$, we can test brackets of the form $(0,(\phi+1)^n),(\phi+1)^{n+1}$, or $((\phi+1)^{n-1},(\phi+1)^n,(\phi+1)^{n+1})$. These have the golden ratio property, and testing a new bracket requires only one further evaluation of $f$ - until we find one. There are many other possible approaches to finding an initial bracket.

Finally, we should mention that it does not matter if the ratio $\phi$ used is not exactly $\frac{\sqrt{5}-1}{2}$. It changes the rate of convergence only slightly if we use an approximation such as 0.618.

***
Here is the algorithm in pseudocode for the Golden Section Search:

```
procedure GoldenSectionSearch(f, a_0, b_0, c_0, N, tol)
	require a_0 < b_0 < c_0 && f(a_0) > f(b_0) < f(c_0)
	f_b = f(b_0)
	n = 0
	phi = (sqrt(5) - 1)/2 
	repeat
		if c_n - b_n > b_n - a_n then
			z = c_n - phi*(c_n - b_n)
			f_z = f(z)
			if f_b < f_b then
				(a_{n+1}, b_{n+1}, c_{n+1}, f_b) = (a_n, b_n, z, f_b)
			else
				(a_{n+1}, b_{n+1}, c_{n+1}, f_b) = (b_n, z, c_n, f_z)
			endif
		else
			z = a_n + phi*(b_n - a_n)
			f_z = f(z)
			if f_b < f_b then
				(a_{n+1}, b_{n+1}, c_{n+1}, f_b) = (z, b_n, c_n, f_b)
			else
				(a_{n+1}, b_{n+1}, c_{n+1}, f_b) = (a_n, z, b_n, f_z)
			endif
		endif
	n += 1
	until n = N || abs(c_n - a_n) < tol(abs(b_n))
	return b_n
endprocedure
```

***

Here is an example:  6.1 - Golden section search is ideal for finding the minimum of a non-differentiable function like $$f(x)=\max(\frac{10}{1+10\sin x},2-(x-2)^4,(x-1)^3).$$
***

There are super-linear improvements to golden section search, which are also derivative-free one-dimensional minimisation algorithms. One is successive parabolic interpolation, which uses a quadratic function to interpolate $f$ at $a,b$ and $c$, setting $z$ as the minimum of the quadratic. It has some drawbacks. There is a version of Brent's method for minimisation that uses a combination of parabolic interpolation and golden section search, selecting the latter when the former does not provide much improvement. Methods that make use of derivatives of $f$ are also available.

It would be remiss not to mention that a d-dimensional minimisation algorithm, that uses no derivates, also exists. It is called the Nelder-Mead method and it walks a $(d+1)$-dimensional polyhedron downhill across a d-dimensional function's surface. But there is no way to 'bracket' a local minimum in more than 1 dimension, and the Nelder-Mead method is not guaranteed to find a local minimum.


#### 6.3 [[Gradient Descent Methods (d dimensions)]]

In more than 1 dimension, bracketing a local minimum is not feasible. Optimisation of functions in higher dimensions is a vast discipline, and we can only give some introductory methods.

The major class of numerical optimisation algorithms are line search methods. Broadly, they work as follows: given $x_n$, and perhaps some information about the gradient and Hessian of $f$ at or near $x_n$, choose a direction $d_n$. Then search for a step length $\alpha_n>0$. The next iteration is $$x_{n+1}=x_n+\alpha_nd_n.$$Since we will refer to the gradients of $f$ at $x_n$ often, for the rest of the notes, we will write $$g_n=\frac{df}{dx}(x_n).$$Desirable properties of $d_n$ and $\alpha_n$ include:

1. $d_n$ should be a descent function, in which $f$ initially decreases $\frac{df(x_n+\alpha d_n)}{d\alpha}(0)<0$, which is equivalent to $$g_n^Td_n < 0;$$
2. the directions $d_n,d_{n-1},...$ should not veer wildly, with each undoing some of the work of the previous steps;
3. $\alpha_n$ should 'loosely' minimise $f(x_n+\alpha_nd_n)$;
4. the infinite travel condition to prevent the method slowing to a complete stop $$\sum_{n=1}^{\infty}=\infty$$

Having made good progress at reducing $f$ in the direction $d_n$, we move on to the next iterative step in a new direction.

First, let us discuss ways to choose $d_n$. The simplest is called co-ordinate gradient descent. Here $d_n$ repeatedly cycles through unit vectors $e_i$. We should allow $\alpha<0$, as some of these vectors will point in the wrong direction and fail to satisfy our earlier equation $g_n^Td_n < 0$. We are minimising $f$ with respect to each of its arguments one at a time, holding the others constant. Co-ordinate gradient descent is naïve, because $d_n$ is independent of the function $f$; this also means that the iterative step can be fast, and derivatives are not needed.

A more sophisticated direction is to choose $$d_n = -g_n$$then, $g_n^Td_n < 0$ is automatically satisfied. This is called gradient descent or sometimes steepest descent, because it is the solution to $$\text{minimize}_{d_n}\frac{df}{dx}(x_n)^Td_n \quad\text{ subject to } ||d_n||\leq 1,$$i.e. it is the direction in which $f$ decreases the most rapidly.

We will see another possibility for direction, which is informed by both the gradient and the Hessian of $f$, in the next section.

After deciding $d_n$, in principle, we could find $a_n$ by a one-dimensional minimisation algorithm. This can lead to convergence in relatively few iterative steps and, furthermore, it is possible to prove that convergence to a local minimum is certain for any choice of $x_0$ except one directly on a stationary point. But in practice then each iterative step, which itself involves solving a one-dimensional optimisation problem, is too slow. It is better to find $\alpha_n$ that is only very roughly optimal, and move on to the next iteration. After all, even the $\alpha_n$ that truly minimises $f(x_n+\alpha_nd_n)$ is only a step on the way to solving the original optimisation problem.

The simplest gradient descent algorithms use a predetermined $\alpha_n$, either a constant, or decaying as $n\to\infty$. But even when $d_n$ is a descent direction, we are only guaranteed that $f(x_n+\alpha_nd) < f(x_n)$ for sufficiently small $\alpha_n$, so we are on the horns of a dilemma: choose $\alpha_n$ too small and the iteration makes little progress, choose $\alpha_n$ too large and the next iteration might be a worse solution.

It is probably fair to say that entire textbooks can be written on the subject of choosing a good step length. We will discuss a simple method called backtracking: choose an initial step length $\alpha_n=\alpha'$, and then repeatedly multiply it by a factor of $0<p<1$ until we have reduced it to a 'good enough' $\alpha_n$.

![[Figure 6.2.png]]

![[Figure 6.3.png]]

We have three things to choose. The constant $p$ is often $\frac12$. The very first step size $\alpha'$ is notoriously tricky, as the right magnitude of the steps depends on the function being optimised. It should be large enough that iterations make progress. Note that it does not cost a great deal if $\alpha'$ is too large, as few repetitions of $\alpha_0 \to \rho\alpha_0$ will soon find the right magnitude. After the first step, a reasonable guess for $\alpha'$ is such that the first-order approximation to the amount of descent is the same as it was at the last step, so $$\alpha'=\alpha_{n-1}\frac{g_{n-1}^Td_{n-1}}{g_n^Td_n}$$This still leaves the very first $\alpha'$ to be supplied by the user.

Finally, we need to define what constitutes a 'good enough' step length. We could simply say that $f(x_n + \alpha_nd_n) < f(x_n)$ is good enough, but this allows for steps that makes infinitesimal progress. Instead, we could adopt the Armijo rule, which is $$f(x_n+\alpha_nd_n)<f(x_n)+\sigma\alpha_ng_n^Td_n$$where $\sigma$ is a small constant, often in the range $10^{-4} \_\_\; 10^{-1}$. This ensures that we make at least a proportion of the progress that the first-order Taylor approximation would expect. It is a consequence of the second-order remainder that the Armijo rule is satisfied for sufficiently small $\alpha_n$ whenever $d_n$ is a descent direction, so the backtracking is guaranteed to terminate.

The backtracking method that was depicted in [[Figure 6.3.png]], as well as the progress of an example of gradient descent that uses it. We should stress that this method of backtracking is far from the best: more sophisticated optimisation methods called the Wolfe conditions which include the Armijo rule and another condition on the curvature of $f(x_n+\alpha_nd_n)$. 

We have computed the gradient at each step, so we can terminate when it is close to zero. Since the scale of $g_n$ will depend on the problem, it makes sense to set a relative threshold on $||g_n||$, and one simple possibility is to make it relative to $g_0$. This method is shown in the later algorithm, which includes backtracking and choosing the initial step size based on the previous step by $\alpha'=\alpha_{n-1}\frac{g_{n-1}^Td_{n-1}}{g_n^Td_n}$. The user supplies the very first initial step size of $\alpha_0'$, along with the backtracking parameters $\rho$ and $\sigma$.

***

Here is the algorithm for Gradient descent with backtracking:

```
procedure GradientDescent(f, x_0, a_0', p, sigma, N, tol)
	g_0 = diff(f, x_0)
	d_0 = -g_0
	a_0 = a_0'
	n = 0
	repeat
		x_{n+1} = x_n + a_n*d_n
		f_{n+1} = f(x_{n+1})
		while f_{n+1} >= f_n + simga*a_n*transpose(g_n)*d_n do
			a_n = p*a_n
			x_{n+1} = x_n + a_n*d_n
			f_{n+1} = f(x_{n+1})
		endwhile
		n += 1
		g_n = diff(f, x_n)
		d_n = -g_n
		a_n = a_{n-1} * (transpose(g_{n-1})*d_{n-1}) / (transpose(g_n)*d_n)
	until n = N or abs(g_n) < tol(1 + abs(g_0))
	return x_n
endprocedure
```

***
Here is an example of gradient descent used on a multivariate function: $$f(x,y)=4(x-2y)^2+(x+2y)^2+\frac1{50}(4(x-2y)^4+(x+2y)^4).$$Algebra shows that its minimum is at $(0,0)$. Various gradient descent methods will use it as a running example in the lectures.

A harder example will be $$g(x,y)=\log(1+f(x,y))$$which, unlike $f$, is non-convex.
***

There are many variants of gradient descent, that are more robust to difficult optimisation problems. One is conjugate gradient descent, which aims to prevent the sharp 'turns' that are evident in our initial algorithm. This is a phenomenon where each step partly 'undoes' the work of the last. This does not happen with conjugate directions, that satisfy $$d_n^THd_m=0, \quad\text{for}\quad m < n$$where $H$ is a matrix, in our case the Hessian of $f$ at $x_n$. A set of conjugate directions forms an orthonormal basis for the space $\mathbb{R}^d$, but a remarkable fact is that an approximate conjugate direction can be chosen without remembering all the previous directions, or even knowing $H$. The Polak-Ribière version of conjugate gradient descent uses $$d_n = -g_n + d_{n-1}\frac{(g_n - g_{n-1})^Tg_n}{g_{n-1}^Tg_{n-1}}$$but is beyond the syllabus. Search it at your own free time g :). In one sense, this method is akin to Newton's method in that the Hessian of $f$ informs a sometimes more direct route towards the local minimum.

Another variant can be applied when the objective function is in the form $l(w)=\frac1N\sum_{i=1}^Nl_i(w)$, e.g. arising from $N$ times of data in machine learning where the total loss function is the average loss on each item. If the complete data are too large to fit into memory, or simply to make each iteration faster, we can calculate a gradient with respect to part of the data only. For example, $$d_n=-\frac1{M-L}\sum_{i=L}^M\frac{dl_i}{dw}(w_n)$$which is called the minibatch gradient descent when the selection of data moves through the full set, in batches, as the iterative steps of gradient descent proceed. The smaller the size of the minibatch, the more 'noisy' the gradient, and it sometimes chooses directions that are bad for the full data. When performance is paramount, good minibatch sizes are dictated by memory cache sizes.

An extreme example is stochastic gradient descent, which uses a single random item on each step. It calculates the gradient only with respect to that part of the loss function: $$i \text{ chosen randomly from } \{1,...,N\}, d_n=\frac{dl_i}{dw}(w_n).$$We should expect to need many more iterations, but each step is much faster. Sometimes, the choice of $i$ is not random, but repeats through the entire data set in a shuffled order, re-shuffled for each pass. This ensures that every item of data is visited the same number of times.


#### 6.4 [[Newton Methods (d dimensions)]]

Returning to the theme of other numerical algorithms we have seen, let us approximate $f$ by a local quadratic function near $x_0$: $$\hat{f}(x)=f(x_0)+(x-x_0)^Tg_0+\frac12(x-x_0)^TH(f)(x_0)(x-x_0)$$The minimum of the approximation is when $$\frac{d\hat{f}}{dx}=g_0+H(f)(x_0)(x-x_0)=0 \;\leftrightarrow\; x=x_0-(H(f)(x_0))^{-1}g_0,$$Iterating this gives rise to Newton's method $$x_{n+1}=x_n - (H(f)(x_0))^{-1}g_n.$$Observe that this is a line search method like gradient descent, bit with $d_n = -(H(f)(x_0))^{-1}g_n$ and $\alpha_n=1$.

A great advantage of Newton-like methods is that there is a natural step size which is independent of the scale of the function $f$. It is still wise to have the option of backtracking, in case the Newton iteration overshoots the minimum, but it negates the need for the user to supply an initial step size $a_0'$, and means that each step length can be independent of the last.

There are two disadvantages. Clearly, we need to compute the Hessian, $d^2$ derivatives, and perform $O(d^3)$ work to solve a linear system, on each iterative step. This is in contrast to gradient descent, which needs $d$ derivatives for the direction and $O(d)$ worm to determine the step length.

The second disadvantage is more subtle: the Newton direction is not always a descent direction. According to $g_n^Td_n < 0,$ that requires $$g_n^T(H(f)(x_n))^{-1}g_n < 0,$$which is guaranteed if $f$ is convex, but not guaranteed otherwise. See the diagram below.

![[Figure 6.4.png]]

Near a local maximum, Newton's method will in fact do the opposite of our intention and rapidly converge to that local maximum. In other situations it may diverge. We can mitigate this by including backtracking, which will refuse to take a step that increases $f$, but in that case Newton's method would come to a halt as $\alpha_n$ never meets the Armijo rule when $d_n$ is not a descent direction, and the loop that multiplies by $\rho$ will not terminate. Newton's iteration is also ill-defined if $H(f)(x_n)$ is singular.

If $f$ has a well-defined local minimum $x^*$, $H(f)(x_n)$ should be positive definite near the $x^*$, and in such circumstances Newton's method will have a quadratic convergence close to the root. Thus, it can polish up an approximate local minimum into a highly accurate local minimum in few iterations. But even when $f$ is convex, quadratic convergence is not guaranteed if $H(f)$ is only semidefinite at $x^*$. This is the optimisation analogue of the double root when root-finding.

In an attempt to mitigate the risks of Newton's method, there are methods to modify the Hessian. A simple trick is to add $\lambda I$ to $H(f)$, where $\lambda$ is just big enough to make the Hessian positive definite. Ideally $\lambda$ should be slightly greater than the magnitude of the most negative eigenvalue of $H(f)$, but finding eigenvalues is expensive, so some methods simply try bigger and bigger $\lambda$ until the Cholesky algorithm succeeds.

###### 6.4.1 Connections between Optimisation and Root-finding

The astute reader, me lmao, will have drawn a connection between Newton's method for optimisation and Newton's method for root-finding. The former is precisely the latter applied to $\frac{df}{dx}$, where the latter is the former precisely applied to $\frac{d}{dx}J(f)$. This makes sense, as zero derivative is a necessary condition for a local optimum.

The failure modes of Newton's method for optimisation demonstrate why. Zero derivative is a necessary condition for a local minimum, but not a sufficient condition, and Newton's method without backtracking will just as happily converge to a saddle point or a maximum as to a minimum. For a strongly convex function with continuous second derivatives, there is only one global minimum stationary point and Newton's method is globally quadratically convergent. But even so, it is not making full use of the structure of a minimisation problem. Minimisation ought to be easier than root-finding for a stationary point, because we have an unambiguous definition of the next iterative step being 'better' than the last, and because a Hessian has structure that an arbitrary Jacobian does not. For one thing, it is symmetric assuming, as we have throughout this section, that the second derivatives are all continuous.

We could also ask the converse question: since all $x$ with $f(x)=0$ are solutions to $$\arg\min_{x}||f(x)||$$why not use minimisation algorithms to solve root-finding problems? It is not a terrible idea to use a few steps of a minimisation algorithm to find a starting point for the methods from [[Algorithms for Root-Finding]]. But $||f(x)||^2$ is rarely a convex function, and optimisation algorithms applied to it may struggle to avoid local minima that are not zeros of $f$. A case where $||f(x)||^2$ is convex arises when $f$ is linear, and indeed iterative optimisation methods can usefully be applied to the problem $Ax=b$. Gradient descent and conjugate gradient descent are commonly used for large linear systems where Gaussian elimination is infeasible, or when the system is sparse.

***
Here is Newton's method for optimisation, with backtracking.

```
procedure NewtonOptimisation(f, x_0, p, sigma, N, tol)
	g_0 = diff(f, x_0)
	solve for d_0 : (H(f)(x_0))*d_0 = -g_0
	f_0 = f(x_0)
	n = 0
	repeat
		a_n = 1
		x_{n+1} = x_n + a_n*d_n
		f_{n+1} = f(x_{n+1})
		while (f_{n+1} >= f_n + sigma * a_n * transpose(g_n) * d_n) do
			a_n = p*a_n
			x_{n+1} = x_n + a_n*d_n
			f_{n+1} = f(x_{n+1})
		endwhile
		n += 1
		g_n = diff(f, x_n)
		solve for d_n : (H(f)(x_0))*d_0 = -g_n
	until n = N or abs(g_n) < tol(1 + abs(g_0))		
	return x_n
endprocedure
```

***
#### 6.5 [[Quasi-Newton Methods (d dimensions)]]

As with root-finding, there are secant-style methods for optimisation that do not compute derivatives. We can take the opportunity to include three advantages over Newton's method: the replacement of derivatives by an approximation that builds through the iterations; the removal of $O(d^3)$ work in solving a linear system; remedying the problems caused by Hessians that are not positive definite.

There are many analogues of Broyden's method, and a full explanation of their derivation is beyond the syllabus. But we can present the most widely-used method. It was discovered independently by Broyden, Fletcher, Goldfarb, and Shanno, and hence known as BFGS.

First, recall the Jacobian update for Broyden's method: $$\hat{J}_n = \hat{J}_{n-1}+\frac{\Delta y - \hat{J}_{n-1}\Delta x}{||\Delta x||^2}\Delta x^T.$$This should not be used to update a Hessian, because it does not give a symmetric matrix. Instead, BFGS uses a rank-2 update that preserves symmetry, while satisfying a secant equation and minimising a suitably-measured change in the inverse Hessian from the previous step. The approximate Hessian at step $n$ is given by: $$\hat{H}_n = \hat{H}_{n-1}+\frac{\Delta g\Delta g^T}{\Delta g^T\Delta x} - \frac{\hat{H}_{n-1}\Delta x\Delta x^T \hat{H}_{n-1}}{\Delta x^T \hat{H}_{n-1} \Delta x}.$$Furthermore, $\hat{H}_{n}$ will be positive definite as long as $\hat{H}_{n-1}$ is positive definite and the so-called curvature condition is satisfied $$\Delta x^T \Delta g > 0.$$Unfortunately, although this condition is satisfied for convex $f$, for non-convex $f$ it can fail for small step lengths. There are a number of ways to proceed: 

1. Use a more complicated step-length selection algorithm that expands as well as contracts that step size so as to enforce the Wolfe conditions, which ensure $\Delta x^T \Delta g > 0.$
2. If $\Delta x^T \Delta g > 0$ fails, simply skip the Hessian update and set it as the last.
3. If $\Delta x^T \Delta g > 0$ fails, damp the Hessian update with some weighted average of the previous hessian.
4. If $\Delta x^T \Delta g > 0$ fails, recognise that the approximate Hessian is not looking good, and we may be in a non-convex region of $f$. Reset to $\hat{H}_n=I$, so that we use the ultra-safe gradient descent for the next step, and then start re-approximating the Hessian when we are hopefully in a convex region of $f$.

For simplicity and safety we choose the last option.

After computing the approximate Hessian, BFGS proceeds to use a quasi-Newton direction $$d_n=-\hat{H}_n^{-1}g_n$$and the rest of the algorithm works as Newton's method. The termination can also be similar, for example, stopping when the magnitude of $g_n$ is small relative to the initial gradient $g_0$, or some other condition.

To get BFGS started, we need to supply an initial Hessian $\hat{H}_0$. The termination can also be similar, for example stopping when the magnitude of $g_n$ is small relative to the initial gradient $g_0$, or some other condition.

To get BFGS started we need to supply an initial Hessian $\hat{H}_0$. The identity matrix is an easy-to-justify choice, because then the first of BFGS is simply gradient descent. As the iterations proceed, $\hat{H}_n$ should approach the true Hessian if $f$ is well behaved. And since $\hat{H}_n$ is constructed to be positive definite. BFGS should not be attracted by local maxima or saddle points, nor should the system $\hat{H}_nd_n=-g_n$ be singular.

A simple version of BFGS is displayed in the algorithm below, but a further improvement is to track the inverse Hessian directly using the Sherman-Morrison formula, $$\hat{H}_n^{-1}=\hat{H}_{n-1}^{-1}+\frac{(\Delta x^T\Delta g+\Delta g^T\hat{H}_{n-1}^{-1}\Delta g)\Delta x \Delta x^T}{(\Delta x^T\Delta g)^2}-\frac{\hat{H}_{n-1}^{-1}\Delta g\Delta x^T + \Delta x \Delta g^T\hat{H}_{n-1}^{-1}}{\Delta x^T \Delta g},$$thus avoiding any $O(d^3)$ computations. This is completely analogous to the case of Broyden's method.

Error analysis of BFGS is well beyond our level. Under certain conditions on $f$ is has been globally convergent. It can be shown that convergence is super-linear but not in general quadratic. In practice, it outperforms Newton's method both for reliability and speed. The limited-memory version of BFGS known as L-BFGS, which avoids storing any matrices in full. is popular in machine learning applications of high dimensions, but the simpler and quicker methods like gradient descent are also useful in practice.

***
Here is the final algorithm: A simple version of BFGS method.

```
procedure BFGS(f, x_0, p, sigma, N, tol)
	H_0 = I
	g_0 = diff(f, x_0)
	d_0 = -g_0
	f_0 = f(x_0)
	n = 0
	repeat
		a_n = 1
		x_{n+1} = x_n + a_n*d_n
		f_{n+1} = f(x_{n+1})
		while (f_{n+1} >= f_n + sigma*a_n*tranpose(g_n)*d_n) do
			a_n = p*a_n
			x_{n+1} = x_n + a_n*d_n
			f_{n+1} = f(x_{n+1})
		endwhile
		n += 1
		g_n = diff(f,x_n)
		delta_x = x_n - x_{n-1}
		delta_g = g_n - g_{n-1}
		if (transpose(delta_x)*delta_g > 0) then
			H_n = H_{n-1} + ... // look at lecture notes
		else
			H_n = matrix.identity
		endif
	until n = N or abs(g_n) < tol(1 + abs(g_0)) 
	return x_n
endprocedure
```

***
