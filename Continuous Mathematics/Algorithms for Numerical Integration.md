
Many mathematicians are first introduced to numerical algorithms through integration. Finding the exact area under a curve can often be extremely hard or impossible, but it is a problem that is amenable to approximation. We first look at the problem in one dimension and then move onto integration over regions of a multivariate scalar function. This will illustrate a problem known as the curse of dimensionality.

Numerical algorithms tend to come in two parts. First, make some approximations to the problem, and solve the approximate problem exactly. We will use various approximation methods including Taylor's theorem from [[Derivatives and Taylor's theorem]]. Second, use mathematics to perform error analysis: an upper bound on the difference between our approximation and the true answer. Again, [[Taylor's Theorem (n dimensions, scalar function)]] will be key here. We can compare different numerical algorithms by the amount of calculation they need to do, and the guarantees on the maximum error.

In high dimensions, it is randomised algorithms that tend to work best. In such a case, there are no guarantees, but we can make probabilistic statements about the error.


#### 3.1 [[Integration]]

The integral of $f$ from $a$ to $b$, $$\int_a^bf(x)dx$$is defined (too mathematically rigorous for our sake now) to be the area bounded by the x-axis, the lines $x=a$ and $x=b$, and the graph $y=f(x)$, with the interpretation of area below the x-axis as negative and above as positive. In such an integral, the function $f(x)$ is known as the **integrand**.

From school, the elementary way to compute this area is to find an antiderivative, a function $F:\mathbb{R}\to\mathbb{R}$ with the property that $$\frac{dF}{dx}=f(x)$$An antiderivative is also called the indefinite integral, and written without limits. The conventional constant '$+c$' reflects the fact that any constant added to our antiderivative still satisfies our equation. If there exists an antiderivative, then $$\int_a^bf(x)dx=[F(x)]_a^b=F(b)-F(a)$$
Now, how the antiderivative happens to be the area under the curve isn't our matter but the mathematicians. The proof to that is known as the fundamental theorem of calculus.

As a simple example, the area under $y=\cos x$ between $x=1$ and $x=3$ is $\sin 3 - \sin 1 \approx -0.700$, a negative answer because most of the curve lies below the x-axis.

In this course, we will only consider integration on finite intervals and none of that infinity bs. Those require more advanced techniques.

Unfortunately, finding an antiderivative is not nearly as simple as finding a derivative. You may have learned some tricks such as integration by substitution or integration by parts, but in general there is no 'product rule' or 'chain rule' for integration.

Worse than simply difficult, in some cases, it is known to be impossible: although every continuous function has an antiderivative, some cannot ever be expressed in terms of elementary functions (these are polynomials, trigonometric functions, exponentials, logarithms, and roots of polynomials). For example, none of $e^{-x^2}$, $\frac{e^x}{x}$, $\ln\ln x$, $\frac{\sin x}{x}$ have an elementary antiderivative.


#### 3.2 [[Numerical Methods for Integration]]

If we wish to find the area under a curve, but cannot find an antiderivative that we can evaluate, we must use an approximation. We approximate the function that we cannot integrate with a function that we can, and then bound how much error this introduces. In the rest of this section, we suppose that we want to approximate $$I = \int_a^bf(x)dx$$for some function $f : [a,b] \to \mathbb{R}$. The region $[a,b]$ is called a strip, and later we will combine many strips to integrate a function over a larger range. Throughout this section we will also write $$m = \frac{a+b}{2}$$for the midpoint of the strip.

A natural idea is to approximate $f(x)$ by a Taylor polynomial around $x=m$. The zero-order approximation is simply $\tilde{f}(x)=f(m)$, and its integral is called the Midpoint rule since it approximates $f$ by its value at the midpoint of the strip $[a,b]$. Its integral is $$M_1[f,a,b] = \int_a^bf(m)dx=[xf(m)]_a^b=(b-a)f(m)$$You can check for yourself that the first-order Taylor polynomial leads to exactly the same estimate: the linear term does not contribute to the integral.

Higher-order Taylor expansions are possible: all the odd powers cancel, as did the linear term, but the even terms persists. One can also perform the Taylor approximation around a different point: $x=a$ or $x=b$, perhaps. These are sometimes called the Left-hand rule and the Right-hand rule, respectively, but they are only useful in special circumstances.

More popular methods for numerical integration are based on interpolating polynomials. That is, approximating $f$ not by its Taylor polynomial value and first few derivatives that agree with $f$ at the single point $x=m$, but by a polynomial that agrees with the value of $f$ at some set of points across the strip.

The simplest is a linear approximation that agrees with $f$ at the endpoints of the interval: a linear $\tilde{f}$ such that $\tilde{f}(a) = f(a)$ and $\tilde{f}(b) = f(b)$. It is easy to see that this is $\tilde{f}(x) = \frac{(b-x)f(a)+(x-a)f(b)}{b-a}$. The approximation to $I$ is therefore $$\begin{aligned}T_1[f,a,b] &= \int_a^b\frac{(b-x)f(a)+(x-a)f(b)}{b-a}dx \\ &= \frac{1}{b-a}\left[-\frac{(b-x)^2}{2}f(a) + \frac{(x-a)^2}{2}f(b)\right]_a^b \\ &= \frac{b-a}{2}(f(a)+f(b))\end{aligned}$$This is the Trapezium or Trapezoid rule which you may have seen in school. Despite its frequent occurrence in school mathematics, it is rarely a good choice as its error is generally worse than the Midpoint rule.

Instead, we can try a quadratic approximation that agrees with $f$ at the endpoints and midpoints of the interval. Finding such a quadratic function can be messy; perhaps easiest is to write $$\tilde{f}(x) = \alpha(x-m)(x-b) + \beta(x-a)(x-b) + \gamma(x-a)(x-m)$$and then:
$$
\begin{aligned}
& \hat{f}(a)=f(a) \text { implies } \alpha=\frac{2}{(b-a)^2} f(a) \text {, } \\
& \hat{f}(m)=f(m) \text { implies } \beta=-\frac{4}{(b-a)^2} f(m) \text {, } \\
& \hat{f}(b)=f(b) \text { implies } \quad \gamma=\frac{2}{(b-a)^2} f(b) . \\
&
\end{aligned}
$$

The approximation to $I$, using $\int_a^b(x-a)(x-b) \mathrm{d} x=(a-b)^3 / 6$, is
$$
\begin{aligned}
S_2[f, a, b] & =\int_a^b \frac{2 f(a)(x-m)(x-b)-4 f(m)(x-a)(x-b)+2 f(b)(x-a)(x-m)}{(b-a)^2} \mathrm{~d} x \\
& =\frac{b-a}{6}(f(a)+4 f(m)+f(b)) .
\end{aligned}
$$

This is called Simpson's rule and we have written it $S_2$ to comply with convention, which regards our 'strip' of length $b-a$ as if they were two strips of length $\frac{b-a}{2}$. This makes sense, as we shall see later, since we had to evaluate the integrand at every multiple of $\frac{b-a}{2}$. 

The approximation made by the Midpoint rule, the Trapezium rule, and Simpson's rule are all displayed in the following:
 
 ![[Pasted image 20240123155832.png]]

We could go further with polynomial approximation, for example to a fourth-order polynomial that matches $f$ at $x=a$,$\frac{3a+b}{4}$, $b$. This is called Boole's rule and it turns out to be $$B_4[f,a,b] = \frac{b-a}{90}(7f(a)+32f\left(\frac{3a+b}{4}\right)+12f(m)+32f\left(\frac{a+3b}{4}\right)+7f(b)),$$but it is no longer widely used.


#### 3.3 [[Error Analysis]]

The second part of designing a numerical method is to analyse its error. We will write the error of the Midpoint, Trapezium, and Simpson's rules for $f$ on $[a,b]$ as $$err(M_1)[f,a,b] = M_1[f,a,b] - I$$and so on, and we want to find upper and lower bounds on these errors. Only the proof for the Midpoint rule is within our syllabus, as well as the statements of the bounds for the other methods. The error bounds come from Taylor's theorem, although sometimes applied in surprising ways. In research, finding the expansion that gives the tightest error bound is something of a dark art.

In this section, we will assume that $f$ can be differentiated at least five times. If not, some of the results may not hold.

**The Midpoint Rule**: $f$ is approximated by $\tilde{f}(x)=f(m)$, but Taylor's theorem with second order remainder says that $$f(x)=\tilde f(x)+(x-m)\frac{df}{dx}(m)+\frac{(x-m)^2}{2}\frac{d^2f}{dx^2}(\xi)$$for some $\xi\in(a,b)$. Rearranging and bounding the second derivative, $$\frac{(x-m)^2}{2}\underline{D_2}\leq f(x)-\tilde f(x)-(x-m)\frac{df}{dx}(m)\leq \frac{(x-m)^2}{2}\overline{D_2}$$where $\underline{D_k}=\min _{x \in(a, b)} \frac{\mathrm{d}^k f}{\mathrm{~d} x^k}$ and $\overline{D_k}=\max _{x \in(a, b)} \frac{\mathrm{d}^k f}{\mathrm{~d} x^k}$. Since $\int_a^b(x-m) \mathrm{d} x=0$ and $\int_a^b \frac{(x-m)^2}{2} \mathrm{~d} x=\frac{(b-a)^3}{24}$, rearranging the above gives $$
-\frac{1}{24}(b-a)^3 \overline{D_2} \leq \operatorname{err}\left(M_1\right)[f, a, b]=\int_a^b \hat{f}(x)-f(x) \mathrm{d} x \leq-\frac{1}{24}(b-a)^3 \underline{D_2} .
$$As we would expect, the Midpoint rule is generally more accurate for small strips. The error is no worse than $O((b-a)^3)$, and the hidden constant depends on the second derivative of the integrand. If the case when $D_2 \geq 0$, which happens when $f$ is convex, we can be certain that the error is negative, and conversely the error is positive if $D_2 \leq 0$.

The trapezium rule: Error analysis of the single-strip Trapezium rule is not in the scope for this module. The classic way to analyse this error is to use the interpolation error bound for polynomial interpolation. The result, however, is $$\frac{1}{2}(b-a)^3\underline{D_2}\leq\text{err}(T_1)[f,a,b]\leq\frac{1}{12}(b-a)^3\overline{D_2}$$Like the midpoint rule, the error of the Trapezium rule is cubic and its bounds depend on the second derivative of the integrand, but the sign is opposite that of the Midpoint rule: for convex functions it will overestimate and for concave functions underestimate. Also note that the error bound is a factor of 2 larger than for the Midpoint rule. That is not to say that the Trapezium rule is always twice as inaccurate as the Midpoint rule, but it often is.

Simpson's rule: Error analysis of single-strip Simpson's rule is notoriously fiddly and also not in the scope of the syllabus. However, it is listed below $$\frac{1}{2880}(b-a)^5\underline{D_4}\leq \text{err}(S_2)[f,a,b]\leq \frac{1}{2880}(b-a)^5\overline{D_4}$$Simpson's rule has an error that is quintic: impressive for such a simple formula. The hidden constant depends on the fourth derivative of the integrand. Here are the following displayed with their errors:

![[Pasted image 20240123164835.png]]

The true integral here is 0.27468, however these are the computed values respectively: 0.6795, 0.2374, 0.1500, −0.6241.

Here are the approximations and their errors listed in tabular form:

| Rule | Approximation | Error bounded by |
| :--- | :---: | ---: |
| Midpoint | $(b-a) f(m)$ | $-\frac{1}{24}(b-a)^3 \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}$ |
| Trapezium | $\frac{b-a}{2}(f(a)+f(b))$ | $\frac{1}{12}(b-a)^3 \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}$ |
| Simpson's | $\frac{b-a}{6}(f(a)+4 f(m)+f(b))$ | $\frac{1}{2880}(b-a)^5 \frac{\mathrm{d}^4 f}{\mathrm{~d} x^4}$ |


#### 3.4 [[Composite Methods]]

