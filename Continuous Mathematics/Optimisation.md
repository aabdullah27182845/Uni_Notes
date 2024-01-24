Many of the computer science applications of continuous maths are in optimisation: finding the minimum of a function. In these notes, we find them exactly, using derivatives. We also examine the related concept of convexity, a property which ensures that the minimum is unique - and therefore easier to find. As in [[Derivatives and Taylor's theorem]], we do these first for univariate functions and then for multivariate scalar functions.

If the function is not differentiable, then we cannot apply the techniques of this chapter. Neither can we if the minimum is the root of an equation that we cannot solve. For an alternative, we will look at numerical algorithms for finding approximate minima in [[Algorithms for Optimisation]].


#### 2.1 [[Optimisation and Constraints]]

The general form of the optimisation problem is as follows: find
$$\min_{x\in F}f(x).$$
The above expression denotes the value of the minimum; in practice we often care more about the location, which is written
$$\arg\min_{x\in F}f(x)$$
This is the $x$ that attains the minimum value: $f(x) \leq f(y)$ for  any $y\in F$. If there is more than one such $x$ then $\arg\min$ is usually considered to return the set of points that attain the minimum. Symmetrically, $\arg\max f$ is the location or set of locations of the maximum value of $f$.

Instances of this problem are pervasive in computer science. We might want to minimise numerical error or running time of an algorithm. Minimising the **loss function** is the keystone of machine learning. Minimising each participant's cost arises in game theory and operations research. And maximising entropy is often an aim of privacy-preserving technologies. There are countless more examples.

Some optimisation terminology: $f$ is called the objective function. $F$ is called the feasible set. If $f$ takes values in $\mathbb{R}^n$ and $F$ is the whole of $\mathbb{R}^n$ then the optimisation problem is called unconstrained, otherwise it is called constrained. In the case of constrained optimisation, $F$ is typically given in standard form:
$$F = \{x\in\mathbb{R}^n | g_1(x)=0,...,g_l(x)=0,h_1(X)\geq0,...,h_m(X)\geq0\}$$
where $g_i(x)=0$ are $l$ equality constraints and $h_i(x)\geq0$ are $m$ inequality constraints. Sometimes $l$ and $m$ are both zero: the techniques for solving optimisation constrained only by equalities are simpler than those for inequalities - or a mixture of both. Whatever the origin of the optimisation problem we wish to solve, we will rewrite its constraints into a standard form as shown above.

Optimisation problems can fail to have a solution. If the constraints actually force $F=\emptyset$ then the problem is called infeasible or inconsistent. If there is no minimum because the objective function can take arbitrarily low values then the problem is called unbounded. Here, I'll give a quick example
***
Suppose you have $n$ lightbulbs and you want to light up an area of farmland with those. Given that the intensity of light of each bulb at any point of the farmland can be modelled as
$$I(x,y) = \frac{C}{x^2+y^2}*\cos^2(\theta)$$
where $x$, $y$, $C$, and $\theta$ are parameters for the distance as well as the angle of the light, finding the lowest amount of wattage required to power each of the light bulbs in order to maximise the light intensity across the entire farmland would require the wattage of the light bulbs to at least be bounded, with each $W \geq 0$ for $W$ as watts.
***

There is nothing special about minimisation: we can convert maximisation to it via $\max f(x) = -\min - f(x).$ For historic reasons, methods and algorithms tend to focus on minimisation rather than maximisation. Optimisation is also sometimes known as programming, in the old-fashioned military sense of allocating resources. There are some particular classes of optimisation problems known as linear programming, convex programming, and so on.

Finally, a local minimum for a function $f$ is a point $x \in F$ such that
$$f(x) \leq f(x+h)$$
for vectors $h$ that are sufficiently small. A local maximum is defined symmetrically. $\arg\min_xf(x)$ can be referred to as a/the global minimum if we want to make the distinction clear. It tends to be easier to find local minima, exactly or approximately, than global minima. In applications however, we usually want the latter.

Note: this is essentially computer science lore right here...


#### 2.2 [[Optimisation in 1 dimension]]

Let $f:\mathbb{R}\to\mathbb{R}$. We want to find
$${\arg\min}_{x\in F} f(x)$$
for $F$ is a closed interval. In one dimension there aren't any interesting equality constraints, and the useful inequality constraints set a maximum or minimum on $x$. In this subsection, we assume that $f$ is at least twice differentiable on $F$, and that those derivatives are continuous.

It would be rare to see an optimisation problem on an open interval like $(a,b)$, because if the minimum value occurs at an endpoint then it is not technically a minimum at all but an infimum.

You already know a method to find the minimum. It begins with finding the stationary points of $f$, solving $\frac{df}{dx}=0$.

This is called a first-order condition because it depends on the first derivative of $f$. In some books, these may be called the critical points. As you know, this only gets up part of the way, because not every stationary point is necessarily a local minimum, since it could be either a local maximum or a point of inflection. We can remember the following set relationships

$$global\;minima \subseteq local\;minima\subseteq stationary\;points$$
and for constrained optimisation, it is always good to check the endpoints too.

A mathematical proof of why every local minimum/maximum has zero derivative is a bit fiddly (known as Fermat's theorem) but is also common sense, so we leave the formality to mathematicians.

![[Pasted image 20240113164747.png]]

Different cases of the first-order condition. As you can see, $a$ is the global maxima, and at 1, we have our local minima. At 2, we have a stationary point but it is a point of inflection. At 3, we have a local maxima since it's smaller than the global maxima. At 4, we have a local minima which is bigger than the global minima. All of these are on stationary points except for $a$ since it's on the boundary of our closed set.

Having found the set of stationary points, we need to classify them: determine which are local minima, local maxima, and stationary points of inflection. Naively, we might simply test some values of the function: if the first-order conditions tell us that $x_0$ is a stationary point, evaluate $f(x_0+\epsilon)$ and $f(x_0-\epsilon)$ for one or more small $\epsilon$, and compare them with $f(x_0)$. Unfortunately, this cannot classify the stationary point, because we cannot be certain that we picked a small enough values of $\epsilon$ in order to avoid any other turning points. Or rather, we cannot be sure unless we do more mathematics to rule out such behaviour.

Instead, we can apply Taylor's theorem, which is an ideal tool to examine the local behaviour of a function. Suppose that $x_0$ is a stationary point. Consider the first-order Taylor polynomial for $f$ at $x_0$, with second-order remainder:
$$f(x) = f(x_0) + (x-x_0)\frac{df}{dx}(x_0)+\frac{(x-x_0)^2}{2!}\frac{d^2f}{dx^2}(\xi)$$
for some $\xi\in(x_0,x)$. Since $x_0$ is a stationary point, we know that the first derivative is zero, and so the second term vanishes.

- If $\frac{d^2f}{dx^2}(x_0)>0$ then it follows that $\frac{d^2f}{dx^2}(\xi)>0$ for all $\xi$ close to $x_0$, by continuity of $\frac{d^2f}{dx^2}$. The third term of our expression above is non-negative, which means that $f(x) \geq f(x_0)$ for $x$ near $x_0$: a local minimum.
- If $\frac{d^2f}{dx^2}(x_0)<0$ then it follows that $\frac{d^2f}{dx^2}(\xi)<0$ for all $\xi$ close to $x_0$, by continuity of $\frac{d^2f}{dx^2}$. The third term of our expression above is non-negative, which means that $f(x) \leq f(x_0)$ for $x$ near $x_0$: a local maximum.

Sometimes, we write this as a second-order sufficient condition: $\frac{d^2f}{dx^2}(x_0)>0$.

It is common to find second-order conditions that are sufficient to classify $x_0$ as a local minimum, but not necessary and that is the case here too. Some local minima do not satisfy this condition.

On the other hand, $\frac{d^2f}{dx^2}(x_0)\geq0$ is a necessary condition, but not a sufficient one.

If we want to classify all stationary points, we may have to work harder, because of the possibility that $\frac{d^2f}{dx^2}(x_0)=0.$ In that case, we should look at the higher-order Taylor polynomial:
$$f(x)=f\left(x_0\right)+\overbrace{\left(x-x_0\right) \frac{\mathrm{d} f}{\mathrm{~d} x}\left(x_0\right)}^{=0}+\overbrace{\frac{\left(x-x_0\right)^2}{2 !} \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}\left(x_0\right)}^{=0}+\frac{\left(x-x_0\right)^3}{3 !} \frac{\mathrm{d}^3 f}{\mathrm{~d} x^3}(\xi)$$
for some $\xi\in(x_0,x)$. If $\frac{d^3f}{dx^3}(x_0)\neq0,$ the third term changes sign when $(x-x_0)$ changes sign. meaning that this is neither a local minimum, nor local maximum; it must be a stationary point of inflection. If $\frac{d^3f}{dx^3}(x_0)=0,$
$$f(x)=f\left(x_0\right)+\overbrace{\left(x-x_0\right) \frac{\mathrm{d} f}{\mathrm{~d} x}\left(x_0\right)}^{=0}+\overbrace{\frac{\left(x-x_0\right)^2}{2 !} \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}\left(x_0\right)}^{=0}+\frac{\left(x-x_0\right)^3}{3 !} \frac{\mathrm{d}^3 f}{\mathrm{~d} x^3}(\xi) + \frac{(x-x_0)^4}{4!}\frac{d^4f}{dx^4}(\xi)$$
and we are back where we started.

This shows that classifying stationary points can be complicated. In one dimension, if the first nonzero derivative is of odd order than we have a stationary point of inflection, and if it is of even order than we have a local minimum/maximum according to whether that nonzero derivative is positive/negative.

That is not even the end of the complications. For non-analytic functions it is possible that every derivative is zero at $x_0$, in which case Taylor's theorem fails to help classify the stationary point. In higher dimensions, as we will see, it can be even more tricky and we will be happy to use sufficient second-order conditions to classify some of the stationary points, and hope that no difficult cases arise.

To summarise, the one-dimensional case, we find $\arg\min_{x\in F}f(x)$ by the following process: differentiate $f$ and, if you can solve the first-order condition, find its stationary points. Differentiate again to try to classify the stationary points. The global minimum is attained at one of the local minima, or on a boundary of the feasible interval if there is one.

***

Here is a practical example. Let $$f(x)=(x-1)^3(x-2)^2.$$Find $\arg\min_{x\in[1,3]}f(x)$ and $\arg\max_{x\in[1,3]}f(x)$.

Solution: We will use product rule to differentiate:
$$\frac{df}{dx}(x) = 3\cdot(x-1)^2(x-2)^2+2\cdot(x-1)^3(x-2)$$
which we can clean up the right hand side to $(x-1)^2(x-2)(5x-7)$ and further expand the last two terms into $(x-1)^2(5x^2-17x+14)$. Now, 
$$\frac{d^2f}{dx}(x) = 2\cdot(x-1)(5x^2-17x+14) + (x-1)^2(10x-17)$$
We can clean this equation too, but that isn't useful. Now, by looking carefully, we know that the stationary points are $x=1$, $x=2$, and $x=7/5$ from our first-order conditions. By finding the value of our second order differential at this, we can see that for each value, $\frac{d^2f}{dx}(1)=0$, $\frac{d^2f}{dx}(2)=3$, $\frac{d^2f}{dx}(7/5)= \frac{12}{25}$, meaning that we have a point of inflection for $x=1$, a local minima for both $x=2$ and $x=7/5$.    

***

Finally, notice that optimisation is a lot easier when the second order derivative is positive for all $x$: in that case there can be at most one stationary point, and if there is one it is the global minimum. 


#### 2.3 [[Unconstrained Optimisation (n dimensions)]]

Let us now return to multivariate functions, $f:\mathbb{R}^n\to\mathbb{R}$, and the same problem of optimisation. For now, let us assume no constraints, so the feasible set is the whole of $\mathbb{R}^n$. We use much of the same method as in 1 dimension: find stationary points, classify them, and select the global minimum if there is more than one local minimum.

The stationary points are defined the same as earlier, $\frac{df}{dx}=0.$

Note that this is a vector equation. It is not always easy to solve such a set of equations and there might be zero, one, or many solutions. For now, let us assume that we can solve the first-order condition to get a set of stationary points.

![[Pasted image 20240113174046.png]]
Fig 2.2: Stationary points in $n$ dimensions (here $n = 2$). From left to right: a local minimum, a local maximum, a saddle point.

In multiple dimensions, a stationary point can be a local minimum, a local maximum, or a saddle point, where it is a maximum for one of the dimensions, but minimum for the other. 

As before, let us use Taylor's theorem to examine the local behaviour of $f$ near a stationary point $x_0$. Using Lemma 1.14,
$$f(x)=f\left(x_0\right)+\overbrace{\left(x-x_0\right)^T \frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}}\left(x_{\mathbf{0}}\right)}^{=\mathbf{0}}+\frac{1}{2}\left(x-x_{\mathbf{0}}\right)^T \mathbf{H}(f)\left(x^*\right)\left(x-x_0\right)$$
for some $\mathbf{x^*}$ between $x_0$ and $x$.

This brings us to a property of matrices which is extremely important for optimisation:

**Definition** A symmetric matrix A is ___ if ___ 

$positive\;definite \to x^TAx > 0$
$positive\;semidefinite \to x^TAx \geq 0$
$negative\;definite \to x^TAx < 0$
$negative\;semidefinite \to x^TAx \leq 0$

for all nonzero $x$.

A symmetric matrix that satisfies none of these above is called indefinite. Positive semidefinite is also sometimes called non-negative definite and negative semidefinite non-positive definite.

This allows us to classify stationary points in many, but not all cases:

- If $\textbf{H}(f)(x_0)$ is positive definite, then so is $\textbf{H}(f)(x^*)$ for $x^*$ close enough to $x$. It follows that $f(x)\geq f(x_0)$ for $x$ close to $x_0$, so that $x_0$ is a local minimum.
- If $\textbf{H}(f)(x_0)$ is negative definite, then for similar reasons, $x_0$ is a local maximum,
- If $\textbf{H}(f)(x_0)$ is indefinite, $x_0$ is a saddle point.

Note that this does not cover the semidefinite cases, and we might have to go to a higher-order Taylor expansion, or use some other analytical tricks, to classify such stationary points.

But at least we have the following second-order condition, which is sufficient to guarantee that $x_0$ is a local minimum: A second-order sufficient condition condition for a minimum is $\textbf{H}(f)(x_0)$ is positive definite.

Furthermore, $\textbf{H}(f)(x_0)$ being positive semidefinite is a necessary condition: if not, there is some direction - an eigenvector corresponding to a negative eigenvalue - in which moving from $x_0$ decreases $f$.

As you can see, the positive/negative definiteness condition on $\textbf{H}(f)$ works similarly to the positivity/negativity of $\frac{d^2f}{dx^2}$ in one dimension (after all, a 1-by-1 matrix is the hessian), but there are additional cases. Furthermore, these results are only useful if you have techniques to establish whether a matrix is positive or negative (semi)definite. In two dimensions, this is trivial.
***
**Lemma 2.2** The symmetric 2-by-2 matrix $A = \begin{bmatrix} a & b \\ c & d \\\end{bmatrix}$ is
__ if and only if __

Where the spaces consist of 
$positive\;definite\to|A|>0\;and\;a>0$
$positive\;semidefinite\;,not\;definite\to|A|=0\;and\;a+c\geq0$
$negative\;definite\to|A|>0\;and\;a<0$
$negative\;semidefinite\;,not\;definite\to|A|=0\;and\;a+c\leq0$
$indefinite\to|A|<0$
***

Note that if $|A|\geq0$ then necessarily $a$ and $c$ have the same sign, or one is zero. For higher dimensions there are various methods for deciding whether a matrix is positive/negative semidefinite/definite. Here are some that can be helpful

***
**Lemma 2.4** 
(i) $\mathbf{A}$ is $\left\{\begin{array}{c}\text { positive definite } \\ \text { positive semidefinite } \\ \text { negative definite } \\ \text { negative semidefinite }\end{array}\right\}$ if and only if all its eigenvalues are $\left\{\begin{array}{c}> \\ \geq \\ < \\ \leq\end{array}\right\} 0$.
(ii) $\mathbf{A}$ is $\left\{\begin{array}{c}\text { positive definite } \\ \text { positive semidefinite } \\ \text { negative definite } \\ \text { negative semidefinite }\end{array}\right\}$ if and only if all its pivots are $\left\{\begin{array}{c}> \\ \geq \\ < \\ \leq\end{array}\right\} 0$.

(The pivots are the leading entries in the unreduced row echelon form: where row additions have been used to convert $\mathbf{A}$ to upper triangle, but rows have not been divided to make their leading entry equal to one. If the echelon form contains a row of all zeros, we call that a zero pivot.)

(iii) $\mathbf{A}$ is positive (semi)definite if and only if $-\mathbf{A}$ is negative (semi)definite.

(iv) The sum of positive semidefinite matrices is positive semidefinite. The sum of a positive definite and a positive (semi)definite matrix is positive definite. Similarly this exists for negative.

(v) For any symmetric matrix $\mathbf{C}$, $\mathbf{C}-\lambda\mathbf{I}$ is positive semidefinite as long as $\lambda\leq$ the smallest eigenvalue of $\mathbf{C}$. It is positive definite if the inequality is strict.

(vi) If $\mathbf{A}$ and $\mathbf{B}$ are positive (semi)definite matrices of the same size, then so are:
- $c\mathbf{A}$
- $\mathbf{A}^{-1}$
- any upper-left submatrix of $\mathbf{A}$
- $\mathbf{A}\mathbf{B}\mathbf{A}$
- $\mathbf{C}^T\mathbf{A}\mathbf{C}$
- $\mathbf{A}-\nu\nu^T$ 

Sometimes all this complexity can be avoided, in the helpful case where the Hessian matrix is positive definite everywhere: this is going to be talked more in [[Convexity]].

![[Pasted image 20240113213810.png]]


#### 2.4 [[Convexity]]

There is a class of optimisation problems where classifying the stationary points is much easier. We have already seen that when $\frac{d^2f}{dx^2}>0$ everywhere, or $\mathbf{H}(f)$ is positive definite everywhere, there can only be at most one stationary point, and if there is one then it is the global minimum. These are examples of functions that are convex, something that will be frequently encountered.

Intuitively, a convex function is 'bowl shaped', whether in 1 or more dimensions and whether differentiable or not. Only functions whose domains are convex sets can be convex:

![[Pasted image 20240113214208.png]]- Convex functions in 1 and 2 dimensions. The secant connecting $(x_1,f(x_1))$ with $(x_2,f(x_2))$ lies above the curve or surface of $f$.

**Definition**   A set $D\subseteq\mathbb{R}^n$ is called convex if, for all $x_1,x_2\in D$ and $\alpha\in(0,1),$ $$(1-\alpha)x_1+\alpha x_2\in D$$A function $f:D\to\mathbb{R}$, for some convex set $D\subseteq\mathbb{R}^n$, is convex if for all $x_1,x_2\in D$ and $\alpha\in(0,1),$ $$f((1-\alpha)x_1+\alpha x_2)\leq(1-\alpha)f(x_1)+\alpha f(x_2).$$$f$ is strictly convex if the inequality above, is strict. It is called concave if  the inequality is reverse, and strictly concave analogously.

Thus convexity of $f$ ensures that any secant joining $(x_1,f(x_1))$ to $(x_2,f(x_2))$ lies above graph, and strict convexity that it lies strictly above the graph. For concavity, the secants lie below the graph.

The reason convexity matters to optimisation is the following: in constrained optimisation of convex $f$.

If furthermore, $f$ is strictly convex, there can be at most one stationary point. If there are none, there is no global minimum. To find the global minimum of a convex objective function, therefore, we only need to solve the first-order condition.

In the case of constrained optimisation, it is still possible that the global minimum occurs on the boundary of $F$. When $f$ is strictly convex, this can only happen if there are no stationary points.

However, the definition of convexity is usually not easy to check. In practice, we might establish it using the following lemma, which will be no surprise given the discussion in the preceding sections:
***
**Lemma 2.7**  Suppose that $f:D\to\mathbb{R}$ for some convex set $D\subseteq\mathbb{R}^n$, and that its second-order partial derivatives are all continuous.
- $f$ is convex if and only if $\mathbf{H}(f)$ is positive semidefinite.
- $f$ is strictly convex if, but not only if, $\mathbf{H}(f)$ is positive definite.
***

The proof of this lemma is quite fiddly, so we will yet again leave it to the mathematicians to prove it formally. There are characterisations of convexity in terms of derivatives, but we will not use them.

Often it is better to demonstrate that a function $f$ is convex by expressing it as a suitable combination of functions we already know to be convex, saving a lot of calculation. 

***
**Lemma 2.8**   
- Linear functions and only linear functions are both convex and concave.
- $\exp(x)$ is strictly convex, $\log x$ is strictly concave on $(0,\infty)$, and $|x|^p$ is strictly convex for $p>1$ and only convex for $p=1$
- $x\mapsto x^T\mathbf{A}x$ is convex/concave when $\mathbf{A}$ is positive/negative semidefinite, and strictly convex/concave when $\mathbf{A}$ is positive/negative definite.
- If $f$ and $g$ are (strictly) convex functions on some convex domain $D\subseteq\mathbb{R}^n$ then so are
	- $f+g$
	- $c\cdot f$
	- $x\mapsto f(Ax+b)$
	- $\max(f,g)$
	- $\exp(f)$
- Let $f:\mathbb{R}^n\to\mathbb{R}$, $g_1,...,g_n$: $f:\mathbb{R}^m\to\mathbb{R}$ . If $f$ is (strictly) convex, and for each $i=1,...,n$, then either: $f$ is (strictly) increasing in its $i$-th argument and $g_i$ is (strictly) convex, or $f$ is (strictly) decreasing in its $i$-th argument and $g_i$ is (strictly) concave, then $f\circ g$ is (strictly) convex.
***
![[Pasted image 20240114140941.png]]

Although we will not use it much this course, it would remiss not to mention perhaps the most useful property that convex functions possess:
***
**Lemma 2.10 (Jensen's Inequality)**  If $f$ is convex and $X$ any random variable taking values in its domain, then $$f(\mathbb{E}[X])\leq\mathbb{E}[f(X)]$$
The inequality is reversed for concave functions. It is strict if $f$ is strictly convex/concave under the further condition that $X$ is not constant.
***


#### 2.5 [[Optimisation Tricks]]

Before we proceed to constrained optimisation, we should mention a few methods that can make optimisation easier.
***
**Lemma 2.11**  If $g:\mathbb{R}\to\mathbb{R}$ is a strictly increasing function, then $$\arg\min_{x\in F}f(x)=\arg\min_{x\in F}g(f(x)).$$
***

We can generalise to functions $g$ that are defined and strictly increasing on at least the image of $F$ under $f$.

For example, if you are asked to minimise $(x+e^{-x2x})^2$ (whether constrained or not), it it probably easier to minimise $x+e^{-2x}$ instead. A commonly-used example is to minimise $\log f$ in place of $f$: this is particularly helpful if $f$ is a product, because then $\log f$ becomes a sum, and hopefully, easier to differentiate.
***
**Lemma 2.12** If $g:\mathbb{R}\to\mathbb{R}$ is an injection (1 to 1 function), then  $$\arg\min_{x\in F}f(x)=g(\arg\min_{x\in F}f(x)).$$
***

For example, the maxima and minima of $\cos(\exp(x))$ will be found when $\exp(x)=n\pi$. We can generalise to functions $g$ that are defined and 1-1 on $F$.


#### 2.6 [[Optimisation with Equality Constraints]]

Now, let us turn to optimisation in $n$ dimensions where there are equality constraints. First, imagine just one equality constraint. In standard form, $$minimise\;f(x)\;subject\;to\;g(x)=0$$
Sometimes, we might be able to re-parametrize so that the constraint is always satisfied, reducing the problem to unconstrained optimisation of one fewer variable. (Example: minimise $f(x,y)$ subject to $x^2+y^2=1$ can sometimes be solved by minimising $f(\cos\theta,\sin\theta)$.) This is rare. Sometimes, $g(x)=0$ has no solutions, in which case, the problem is infeasible, or sometimes it is clear that $f$ is unbounded even in the feasible set.

Most of the time, we need another trick. There is one that allows us to solve the problem using unconstrained optimisation methods, which in two dimensions shows the contours of a function $f$ and the constraint $g(x)=0$. The key observation is that, when $f$ is maximised or minimised on the constraint, the gradient $f$ and $g$ must be parallel. That is, $\frac{df}{dx}=-\lambda\frac{dg}{dx}$ for some scalar $\lambda$. If this were not so, imagine 'moving' around the constraint, following the line $g(x) = 0$; if the contours of $f$ are not in the same direction that we are travelling $(\frac{dg}{dx})$ then we can increase or decrease $f$ by going a small distance forward or back, so $f$ is not at a stationary point here on the constraint.

This leads to the method of Lagrange multipliers. To solve our earlier equation, we form the Lagrangian $$\Lambda(\lambda,x)-f(x)-\lambda g(x)$$
and we use the fact that the stationary points of $f$ on the constraint are all stationary points of $\Lambda$. You should be careful about using the method of Lagrange multipliers mindlessly, because it will not tell you if the problem is unbounded, but it is a powerful method when used carefully. A formal proof of the correctness of this method is difficult, which we won't cover here lmao.

![[Pasted image 20240114185345.png]]The stationary points of $f(x)$, subject to the constraints $g(x)=0$, must occur when the gradients of $f$ and $g$ are parallel.

If $f$ is a function of $n$ variables then the Lagrangian is a function of $n+1$ variables. $\lambda$ is the Lagrange multiplier. We apply the first-order condition which gives the system of $n+1$ equations $\frac{d\Lambda}{d(\lambda,x)}=0$, i.e.

- $g(x)=0$
- $\frac{\partial f}{\partial x_1} = \lambda\cdot\frac{\partial g}{\partial x_1}$
- $\vdots$
- $\frac{\partial f}{\partial x_n} = \lambda\cdot\frac{\partial g}{\partial x_n}$

There is nothing to say that this system of equations must be easy to solve! $\lambda$ is a 'dummy' variable that we do not normally care about (though it does have an economic interpretation as a shadow price) so it can be convenient to eliminate it by manipulating the system, but take care not to divide by zero while doing this. Also, do not forget that square roots have positive and negative values!

The same method works for multiple constraints. For $$minimise\;f(x)\;subject\;to\;g_1(x)=0,...,g_l(x)=0,$$form the Lagrangian with one Lagrange multiplier for each constraint, $$\Lambda(\lambda_1,...,\lambda_lx)=f(x)-\lambda_1g_1(x)-...-\lambda_lg_l(x)$$The stationary points of the original problem will all be stationary points of the Lagrangian.

The method of Lagrange multipliers is good for finding the stationary points of $f$ subject to the constraints. But how do we know whether they are local/global minima or maxima, or merely stationary points of inflection? This is rather difficult: it is not as simple as requiring that the Hessian of $\Lambda$ is positive definite at the stationary point. Indeed, it can never be, because $\frac{\partial^2\Lambda}{\partial\lambda^2}=0$, so that the $\mathbf{H}(\Lambda)$ has a zero in the upper-left entry, and so $x^T\mathbf{H}(\Lambda)x=0$ when $x$ is the first unit vector.

It is the case that $x$, assuming it solves the first-order condition, is a local minimum of $f$ subject to $g(x)=0$ as long as $\upsilon^T\mathbf{H}(\Lambda)>0$ for all $\upsilon$ where $\frac{dg}{dx}^T=\vec{0}^2$ but this is not easy to check.

It is also not sufficient to establish that $f$ is convex, since equality constraints typically describe a non-convex set, unless they are all linear. In practice, it it typically easiest either to check by hand that $f$ is not unbounded, and then evaluate it at the stationary points to find which are the global extrema.


#### 2.7 [[Optimisation with Inequality Constrains]]

Consider optimisation subject to inequality constraints: $$minimize\;f(x)\;subject\;to\;h_1(x)\geq0,...,h_m(x)\geq0.$$These are harder than optimisation problems with equality constraints. First, some terminology. If, at the minimum, $h_i(x) >0$ we say that constraint $i$ is slack. If, the the minimum, $h_i(x)=0$ we say that the constraint is tight.

An elementary observation that can help solve such problems is simply that each constraint is either slack, in which case it can be converted into an equality constraint. Once all constraints are either removed or converted to equalities we can apply the Lagrange multiplier method as before. Unfortunately, we do not know in advance which constraints will be tight and which will be slack: in the worst case we might have to try all $2^m$ options. We should certainly look for reasons why some constraints must be tight or intuition that some must be slack.

After guessing which constraints are tight and which are slack, we can solve the equality-constrained optimisation problem that arises, using the methods of the previous section. If the solution satisfies the slack inequalities, we have found the minimum for this combination of tight and slack constraints. That does not necessarily mean that we guessed correctly and we do need to try all possibilities for tight and slack constraints that we have not ruled out by other means. Although laborious, this technique should be sufficient for now.

The analysis can be made a bit more precise if we return to that figure in [[Optimisation with Equality Constraints]]. Imagine one inequality constraint $h(x)\geq0$. Observe that, if $f(x)$ is to find a minimum on the boundary of the feasible region when $h(x)=0$, the gradients of $f$ and $h$ have to be parallel and in the same direction. More reason as to why in the source material.

This leads to a mathematical formulation similar in style to Lagrange multipliers: find stationary points of $$\Lambda(\mu,x)=f(x)-\mu h(x)$$ satisfying the constraint plus $$\mu\geq0,\mu h(x)=0.$$We can combine $m$ inequality constraints with $l$ equality constraints into what are known as the **Karush-Kuhn-Tucker conditions:** to solve $$
\begin{aligned}
minimise\;f(x)\;subject\;to\;g_i(x)=0\;\;i=1,...,l, \newline
h_i(x)\geq0\;\;i=1,...,m.
\end{aligned}
$$some necessary (but not sufficient) conditions for $x$ to be the minimum are: 
$$\begin{aligned}
\frac{d}{dx}[f(x)-\sum_i\lambda_ig_i(x)-\sum_i\mu_ih_i(x)] = 0 \\
g_i(x) = 0,\;i=1,...,l \\
h_i(x) \geq 0,\;i=1,...,l \\
\mu_i \geq 0,\;i=1,...,l \\
\mu_ih_i(x) = 0,\;i=1,...,l
\end{aligned}$$We will not use this method now, but it is helpful in many applications including support vector machines and other kinds of machine learning. There must be further conditions to ensure that $x$ locates a global minimum, for example a convexity condition on $f$ and the feasible region. That takes us well beyond the scope of this module.
***
**Example 2.14** In steganography, we make changes to an apparently-innocent cover in order to conceal a secret message within it. In one formulation of the steganography problem, we enumerate all possible changes and assign them costs $c_1,...,c_n$. Each change will be made with corresponding probability $\pi_i$, which conveys up to $H(\pi_i)$ bits of secret information. Here, $H$ is the binary entropy function, $$H(x)=-x\log_2x-(1-x)\log_2(1-x),\;\;\;\;\;H(0)=H(1)=0.$$The best hiding is one that minimises the total average cost, while having enough entropy to hide a secret message of some fixed length $m$ bits. That is, find $\pi_1,...,\pi_n$ to $$minimise\;\sum_{i=1}^n\pi_ic_i\;\;subject\;to\;\sum_{i=1}^nH(\pi_i)\geq m,\;\;0\leq\pi_i\leq1$$We can argue that the first constraint must be tight, and test the intuition that the others are loose. We find the solution $$\pi_i=\frac{1}{1+\exp(\lambda c_i)}$$for some constant $\lambda>0$. This is the unique stationary point, the objective is a convex function and the constraint describes a convex set, so this solution must be the global minimum.
 