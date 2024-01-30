
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
 
 ![[Figure 3.2.png]]

We could go further with polynomial approximation, for example to a fourth-order polynomial that matches $f$ at $x=a$,$\frac{3a+b}{4}$, $b$. This is called Boole's rule and it turns out to be $$B_4[f,a,b] = \frac{b-a}{90}(7f(a)+32f\left(\frac{3a+b}{4}\right)+12f(m)+32f\left(\frac{a+3b}{4}\right)+7f(b)),$$but it is no longer widely used.


#### 3.3 [[Error Analysis]]

The second part of designing a numerical method is to analyse its error. We will write the error of the Midpoint, Trapezium, and Simpson's rules for $f$ on $[a,b]$ as $$err(M_1)[f,a,b] = M_1[f,a,b] - I$$and so on, and we want to find upper and lower bounds on these errors. Only the proof for the Midpoint rule is within our syllabus, as well as the statements of the bounds for the other methods. The error bounds come from Taylor's theorem, although sometimes applied in surprising ways. In research, finding the expansion that gives the tightest error bound is something of a dark art.

In this section, we will assume that $f$ can be differentiated at least five times. If not, some of the results may not hold.

**The Midpoint Rule**: $f$ is approximated by $\tilde{f}(x)=f(m)$, but Taylor's theorem with second order remainder says that $$f(x)=\tilde f(x)+(x-m)\frac{df}{dx}(m)+\frac{(x-m)^2}{2}\frac{d^2f}{dx^2}(\xi)$$for some $\xi\in(a,b)$. Rearranging and bounding the second derivative, $$\frac{(x-m)^2}{2}\underline{D_2}\leq f(x)-\tilde f(x)-(x-m)\frac{df}{dx}(m)\leq \frac{(x-m)^2}{2}\overline{D_2}$$where $\underline{D_k}=\min _{x \in(a, b)} \frac{\mathrm{d}^k f}{\mathrm{~d} x^k}$ and $\overline{D_k}=\max _{x \in(a, b)} \frac{\mathrm{d}^k f}{\mathrm{~d} x^k}$. Since $\int_a^b(x-m) \mathrm{d} x=0$ and $\int_a^b \frac{(x-m)^2}{2} \mathrm{~d} x=\frac{(b-a)^3}{24}$, rearranging the above gives $$
-\frac{1}{24}(b-a)^3 \overline{D_2} \leq \operatorname{err}\left(M_1\right)[f, a, b]=\int_a^b \hat{f}(x)-f(x) \mathrm{d} x \leq-\frac{1}{24}(b-a)^3 \underline{D_2} .
$$As we would expect, the Midpoint rule is generally more accurate for small strips. The error is no worse than $O((b-a)^3)$, and the hidden constant depends on the second derivative of the integrand. If the case when $D_2 \geq 0$, which happens when $f$ is convex, we can be certain that the error is negative, and conversely the error is positive if $D_2 \leq 0$.

The trapezium rule: Error analysis of the single-strip Trapezium rule is not in the scope for this module. The classic way to analyse this error is to use the interpolation error bound for polynomial interpolation. The result, however, is $$\frac{1}{2}(b-a)^3\underline{D_2}\leq\text{err}(T_1)[f,a,b]\leq\frac{1}{12}(b-a)^3\overline{D_2}$$Like the midpoint rule, the error of the Trapezium rule is cubic and its bounds depend on the second derivative of the integrand, but the sign is opposite that of the Midpoint rule: for convex functions it will overestimate and for concave functions underestimate. Also note that the error bound is a factor of 2 larger than for the Midpoint rule. That is not to say that the Trapezium rule is always twice as inaccurate as the Midpoint rule, but it often is.

Simpson's rule: Error analysis of single-strip Simpson's rule is notoriously fiddly and also not in the scope of the syllabus. However, it is listed below $$\frac{1}{2880}(b-a)^5\underline{D_4}\leq \text{err}(S_2)[f,a,b]\leq \frac{1}{2880}(b-a)^5\overline{D_4}$$Simpson's rule has an error that is quintic: impressive for such a simple formula. The hidden constant depends on the fourth derivative of the integrand. Here are the following displayed with their errors:

![[Figure 3.3.png]]

The true integral here is 0.27468, however these are the computed values respectively: 0.6795, 0.2374, 0.1500, −0.6241.

Here are the approximations and their errors listed in tabular form:

| Rule | Approximation | Error bounded by |
| :--- | :---: | ---: |
| Midpoint | $(b-a) f(m)$ | $-\frac{1}{24}(b-a)^3 \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}$ |
| Trapezium | $\frac{b-a}{2}(f(a)+f(b))$ | $\frac{1}{12}(b-a)^3 \frac{\mathrm{d}^2 f}{\mathrm{~d} x^2}$ |
| Simpson's | $\frac{b-a}{6}(f(a)+4 f(m)+f(b))$ | $\frac{1}{2880}(b-a)^5 \frac{\mathrm{d}^4 f}{\mathrm{~d} x^4}$ |


#### 3.4 [[Composite Methods]]

How can we improve accuracy? Following the discussion in the earlier section, we might try higher-order Taylor expansions for $f$, or interpolating polynomials of higher degree. These are complicated, much more so than the simple rules we derived earlier, and sometimes they offer no improvement. In the case of higher-order Taylor expansions, it may not improve accuracy if the Taylor series for $f$ fails to converge or converges to the wrong answer. Or $f$ may fail to be differentiable enough times for the error analysis to be correct. In the case of higher-order polynomial interpolation, there are examples of perfectly sensible functions where polynomial interpolation goes haywire as the order increases. A classic example is the Runge function, one version of which is $$f(x)=\frac{1}{1+25(2x-1)^2}$$
![[Figure 3.4.png]]

In the figure before this one, [[Figure 3.2.png]], we show polynomial interpolation, on the interval $[0,1]$, for the Runge function using order-2, order-4, order-8, and order-16 polynomials. Although they become more exact on the centre of the interval, they become less accurate towards the edges, and the integral of the interpolating polynomial does not approach the integral of $f$.

Higher-order interpolation is certainly not useless, either for integration or other purposes. The Runge phenomenon is avoidable, if we use interpolation points that are not equally spaced across the interval. It is also commonly believed that polynomial interpolation is unstable: that rounding errors in the calculations can cause large errors in the answer. This is also avoidable, with carefully chosen algorithms. You may want to read the article *Six Myths of Polynomial Interpolation and Quadrature* by Nick Trefethen.

An alternative, though, is to divide the interval of integral into small strips, and approximate each strip separately. See [[Figure 3.4.png]] in how that's done with the trapezium rule. By making the individual strips very small, we only need the approximations to be valid in a small range each. As well as avoiding the worst of the problems with higher-order Taylor expansions and polynomial interpolation, it allows us to use the extremely simple methods to achieve high accuracy. Since for a strip of width $w$ the error of the Midpoint and Trapezium rule is $O(w^3)$, and of Simpson's rule is $O(w^5)$, accuracy should increase rapidly as the strips become small. 

So we divide the integral $I = \int_a^bf(x)dx$ into $n$ strips: $$I = \int_a^bf(x)dx= \int_{x_0}^{x_1}f(x)dx+\int_{x_1}^{x_2}f(x)dx\;+\;...\;+\int_{x_{n-1}}^{x_n}f(x)dx$$Using this with n-equal sized strips so that $x_i = a + i(\frac{b-a}{n})$, we approximate our integral by the Composite Midpoint rule: $$M_n[f,a,b]=\sum_{i=1}^{n}M_1[f,x_{i-1},x_i]=\frac{b-a}{n}(f\left(\frac{x_0+x_1}{2}\right)+f\left(\frac{x_1+x_2}{2}\right)+\;...\;+f\left(\frac{x_{n-1}+x_n}{2}\right))$$We can also bound the error, since $\max_{x\in(x_{i-1},x_i)}\frac{d^2f}{dx^2} \leq \overline{D_2}$ where $\overline{D_2}=max_{x\in(a,b)}\frac{d^2f}{dx^2}$, we have $$-\frac{(b-a)^3}{24n^2}\overline{D_2}\leq\text{err}(M_n)[f,a,b]=\sum_{i=1}^n\text{err}(M_1)[f,x_{i-1},x_i]\leq-\frac{(b-a)^3}{24n^2}\underline{D_2}$$The same calculations apply for the Trapezium rule. The composite Trapezium rule with $n$ strips is:
$$
T_n[f, a, b]=\sum_{i=1}^n T_1\left[f, x_{i-1}, x_i\right]=\frac{b-a}{2 n}\left(f\left(x_0\right)+2 f\left(x_1\right)+2 f\left(x_2\right)+\cdots+2 f\left(x_{n-1}\right)+f\left(x_n\right)\right)
$$
and the error bound is
$$
\frac{(b-a)^3}{12 n^2} \underline{D_2} \leq \operatorname{err}\left(T_n\right)[f, a, b]=\sum_{i=1}^n \operatorname{err}\left(T_1\right)\left[f, x_{i-1}, x_i\right] \leq \frac{(b-a)^3}{12 n^2} \overline{D_2} .
$$

Simpson's rule with what is conventionally called $n$ strips requires dividing into $n / 2$ intervals, and applying $S_2$ to each ( $n$ must be even to apply Simpson's rule in this way). It approximates $\int_a^b f(x) \mathrm{d} x$ by the composite Simpson's rule:
$$
\begin{aligned}
S_n[f, a, b] & =\sum_{i=1}^{n / 2} S_2\left[f, x_{2 i-2}, x_{2 i}\right] \\
& =\frac{b-a}{3 n}\left(f\left(x_0\right)+4 f\left(x_1\right)+2 f\left(x_2\right)+4 f\left(x_3\right)+\cdots+4 f\left(x_{n-1}\right)+f\left(x_n\right)\right) .
\end{aligned}
$$

The error bound is
$$
\frac{(b-a)^5}{180 n^4} \underline{D_4} \leq \operatorname{err}\left(S_n\right)[f, a, b]=\sum_{i=1}^{n / 2} \operatorname{err}\left(S_2\right)\left[f, x_{2 i-2}, x_{2 i}\right] \leq \frac{(b-a)^5}{180 n^4} \overline{D_4} .
$$

 ![[Example 3.1.png]]

Observe that all of the composite rules are of the form $$\frac{(b-a)}{\sum w_i}(w_0f(x_0)+w_1f(x_1)+\;...\,+w_nf(x_n))$$These are collectively known as Newton-Cotes formulae, and there are many such. The more complex Newton-Cotes formulae used to be more popular than they are now, but with modern computing power it is easier to apply the simplest rules (Midpoint or Simpson's) with more strips, rather than to make the interpolation more complex.

What do the error bounds mean? First, let us measure the amount of 'work' required to compute $M_n[f,a,b]$, $T_n[f,a,b]$, and $S_n[f,a,b]$. In the first case, $O(n)$ arithmetic operations plus $n$ evaluations of the function, which is practically the same thing. We assume that the expensive part of this operation is evaluating $f$ (Since if $f$ is quicker to evaluate than some scalar additions, and divisions by constants, it can be probably be integrated exactly). Thus, all composite methods do approximately the same amount of work, $n$ evaluations of $f$. This is why the composite Simpson's rule conventionally counts $n/2$ intervals as $n$ 'strips'.

Now how does the approximation error depend on $n$? In the case of the Midpoint or Trapezium rule, $O(n^{-2})$. That is, to reduce the error by a factor of about 10, we should increase $n$ by a factor of $\sqrt{10}$. In the case of Simpson's rule, the error decreases rapidly, $O(n^{-4})$, and it even has a small hidden constant. To reduce the error by a factor of about 10 only requires about 1.8 times as much work. Considering its extreme simplicity, Simpson's rule is tremendously good value.

As with the single-strip case, the error bound also depends on the second or fourth derivation of $f$. This forms part of the hidden constant in the $O(n^{-2})$ or $O(n^{-4})$. Remember that these are bounds on the error, and sometimes the error will be a lot smaller. For example, of the second or fourth derivative is only large in one of the strips, and very small elsewhere, the error bound will be very loose.

Indeed, this suggests one possible improvement to the composite rules: unequal-width strips. There are adaptive methods that start with one strip, then recursively subdivide until an error tolerance is achieved: much more sophisticated than the methods we have described, but a bit advanced for us yet.

So, summary of the composite integration rules, and their error bounds, for approximating $\int_a^bf(x)dx,$ with $x_i = a + i\frac{b-a}{n}$:
$$\begin{array}{l l l}
\textbf{Rule} & \textbf{Approximation} & \textbf{Error bounded by} \\
\hline
Midpoint & \frac{b-a}{n} \left( f\left(x_0+\frac{1}{2}\right) + f\left(x_1+\frac{1}{2}\right) + \cdots + f\left(x_{n-1}+\frac{1}{2}\right) \right) & \frac{(b-a)^3}{24n^2} \max \left| \frac{d^2f}{dx^2} \right| \\

Trapezium & \frac{b-a}{2n} \left( f(x_0) + 2f(x_1) + \cdots + 2f(x_{n-1}) + f(x_n) \right) & \frac{(b-a)^3}{12n^2} \max \left| \frac{d^2f}{dx^2} \right| \\
\
Simpson’s & \frac{b-a}{3n} \left( f(x_0) + 4f(x_1) + 2f(x_2) + \cdots + 4f(x_{n-1}) + f(x_n) \right) & \frac{(b-a)^5}{180n^4} \max \left| \frac{d^4f}{dx^4} \right| \\
\end{array}$$


#### 3.5 [[Integration in d Dimensions]]

Consider a function $f:\mathbb{R}^2\to\mathbb{R}$. Integration of $f$ calculates the volume underneath the surface, as depicted in [[Figure 3.5.png]]. The notation for this is $$\int_Rf(x,y)d(x,y)$$
![[Figure 3.5.png]]

Such integrals can sometimes be evaluated exactly by converting them into nested integrals over univariate functions: $$\int_Rf(x,y)d(x,y)=\int_{x_0}^{x_1}\int_{y_0}^{y_1}f(x,y)dydx$$providing that all the integrals are finite, which is a result called Fubini's Theorem. Note that the inner integrals treats $x$ as a constant, a bit like partial derivatives do. However, one has to identify the limits $(x_0,x_1)$, and $(y_0,y_1)$ such that the region $R \subset \mathbb{R}^2$ is covered: easy in the case of rectangles, triangles, and circles; difficult for more general shapes. The order of integrations does not matter and you can choose whether to integrate first with respect to $y$, then $x$, or the other way around. Sometimes it is more convenient to describe the limits one way than the other.

The same concept generalizes to multivariate functions in $d$ dimensions, $$\int_Rf(\mathbf{x})d\mathbf{x},$$for $R \subseteq \mathbb{R}^d$, which can sometimes be computed by converting to $d$ nested integrals, each over one dimension of R. The one-dimensional integration trick you might know as 'integration by substitution' applies in higher dimensions (and uses the Jacobian of the map), but there are many examples of functions where the antiderivatives simply cannot be found. Then we need numerical methods.


#### 3.6 [[Numerical Methods for Integration (d dimensions)]]

The methods we saw earlier for one dimension can be adapted for higher dimensions. 

For simplicity's sake, consider just the composite Midpoint rule, and suppose we wish to evaluate $$I=\int_Rf(x,y)d(x,y)$$where $R$ is the rectangle $[0,1]\times[0,1]$. Fubini's theorem and then the midpoint rule, with n strips, on the outer integral gives $$I=\int_0^1\int_0^1f(x,y)dxdy\approx\frac{1}{n}\sum_{i=1}^n\int_0^1f\left(\frac{2i-1}{2n},y\right)dy$$Then we can apply the Midpoint rule again to each inner integral, perhaps again with $n$ strips, giving us the following $$I\approx\frac{1}{n^2}\sum_{i=1}^n\sum_{j=1}^nf\left(\frac{2i-1}{2n},\frac{2j-1}{2n}\right)$$Notice that this is just evaluating $f$ on the midpoint of the squares of a $n\times n$ grid over the region $R=[0,1]\times[0,1]$, and taking the average value.

One can extend the same method to higher dimensions in the same way. There are also versions of the Trapezium rule and Simpson's rule. In the case of 2 dimensions, the latter evaluates $f$ on a $(n+1)\times(n+1)$ grid over $R$, and then multiplies the results by constants given by the $(n+1)\times(n+1)$ matrix $$\frac{1}{9n^2}
\begin{pmatrix}
1 & 4 & 2 & 4 & 2 & \cdots & 2 & 4 & 1 \\
4 & 16 & 8 & 16 & 8 & \cdots & 8 & 16 & 4 \\
2 & 8 & 4 & 8 & 4 & \cdots & 4 & 8 & 2 \\
4 & 16 & 8 & 16 & 8 & \cdots & 8 & 16 & 4 \\
2 & 8 & 4 & 8 & 4 & \cdots & 4 & 8 & 2 \\
\vdots & \vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots & \vdots \\
2 & 8 & 4 & 8 & 4 & \cdots & 4 & 8 & 2 \\
4 & 16 & 8 & 16 & 8 & \cdots & 8 & 16 & 4 \\
1 & 4 & 2 & 4 & 2 & \cdots & 2 & 4 & 1
\end{pmatrix}
$$Which is a seemingly a pretty simple calculation, even generalised to d dimensions, but there is a hitch.

Formal error analysis of the d-dimensional versions of Midpoint, Trapezium, and Simpson's rules is tricky, but an interesting thought experiment is to imagine that all the inner integrals are done with zero error, then consider just the effect of the relevant rule on the outer integral. Whatever answer this gives, it can only be an underestimate for the error when the inner integrals are also subject to approximation.

There will be some unpleasant dependence on partial derivatives of $f$, but that only contributes a constant factor. Whatever else happens, from our analysis of the one-dimensional cases we can say that the error decreases as best as $O(n^{-2})$ in the case of the Midpoint and Trapezium rules, and $O(n^{-4})$ for Simpson's rule. In fact, it can be shown that the error of the d-dimensional Midpoint and Trapezium rules are $O(dn^{-2})$ and Simpson's rule is $O(dn^{-4})$. This feels like fast convergence.

But consider the amount o work required, to apply the numerical methods. In d dimensions we need to evaluate the function in $N=n^d$ locations, because this is the number of vertices in the d-dimensional grid. This is a phenomenon called the curse of dimensionality, which occurs in numerical algorithms and machine learning in high dimensions. The amount of 'work' increases exponentially with the dimension.

It therefore means that the error of the algorithm is only $O(N^{-2/d})$ or $O(N^{-4/d})$, if $N$ represents the amount of 'work' performed. So even Simpson's rule, which is wonderful in 1 dimension, converges as $O(N^{-1/3})$ when attempting a 12-dimensional integral: to reduce the error by a factor of 10 we need around 1000 times as many function evaluations. For a lot of decimal places of accuracy, the work becomes infeasible. And when we come to integration over hundreds of dimensions, which is quite commonplace in machine learning and Bayesian inference, we will need an alternative method.


#### 3.7 [[Monte Carlo Integration]]

A completely different approach to numerical integration is found in Monte Carlo methods. These are randomised algorithms that give random answers but where the random answer is likely close to the correct answer.

The random part of Monte Carlo integration comes from where to evaluate thee integrand $f(x)$. Instead of calculating $f(x)$ over points in a regular d-dimensional grid, and then returning a weighted or an unweighted average of the answers, we calculate $f(x_i)$ for randomly generated points $x_i$, and then return a weighted or unweighted average of the answers. What is surprising is how powerful this method is, in higher dimensions.

Consider first a one-dimensional integration problem $\int_b^af(x)dx$. Suppose that we pick a random number $X$, uniformly distributed on $[a,b]$. Its probability density function is $p(x)=\frac{1}{b-a}$ for $x\in[a,b]$and zero otherwise. Then, using a property of expectation of a function of a random variable, $$\mathbb{E}[f(X)]=\int f(x)p(x)dx=\frac{1}{b-a}\int_a^bf(x)dx.$$This suggests a way to approximate $\int_a^bf(x)dx$ ; pick a large number of continuous random variables $X_1, ..., X_N$, all identically and independently uniformly distributed on $[a,b]$; the Monte Carlo estimate for $\int_a^bf(x)dx$ is $$MC_N[f,a,b]=(b-a)\frac{1}{N}\sum_if(X_i).$$(This is different to [[Las Vegas methods]], which are randomised algorithms that give non-random answers, but have random running time. An example of such is Quicksort with the use of Hoare partition)

This has a natural intuition: the area under $f$ in an interval $[a,b]$ is the average value of $f$ times the width of the interval.

Note that $MC_N[f,a,b]$ is a random variable: it will give a different answer every time, if the $X_i$ are regenerated randomly. As with all Monte Carlo algorithms, we seek a proof that is likely to be close to the true answer.

The method applies equally well in higher dimensions. For a multivariate function $f : \mathbb{R}^d \to \mathbb{R}$, we can approximate $\int_Rf(x)dx$ by generating random vectors $X_1,...,X_N$ uniformly on the region $R$ and then $$MC_N[f,R]=A(R)\frac{1}{N}\sum_if(X_i)$$where $A(R)$ denotes the area or volume of the region $R$. 

Is this method any useful? Here is a sequence of increasingly precise things that we can say about Monte Carlo integration.

---

**Lemma 3.2** Assuming that $f$ is well-behaved,

(i) $E[MC_N[f,R]] = \int_Rf(x)dx$       (So the estimate is on average correct, or unbiased)

(ii) $Var[MC_N[f,R]]=\frac{V}{N}$, for a constant $V$. Furthermore, $V$ can be estimated by $$\hat{V}=\frac{A(R)^2}{N}\sum_if^2(X_i)-\left(\frac{A(R)}{N}\sum_if(X_i)\right)^2.$$*(The square root of the estimated variance is called the standard error and sometimes used to measure accuracy of Monte Carlo methods)*

Now denote the error of the Monte Carlo estimate $$err(MC_N)[f,R]=A(R)\frac{1}{n}\sum_if(X_i)-\int_Rf(x)dx$$
(iii) $P\left[|err(MC_N)[f,R]| \geq \epsilon \right]\to0$ as $N\to\infty$ for any $\epsilon > 0$.    *(Which means that the estimate gets arbitrarily close to the right answer, for large enough N; it is consistent.)*

(iv) For large $N$, $err(MC_N)[f,R]$ approaches the Normal distribution $N(0,\frac{V}{N})$. The same holds if $V$ is replaced with $\hat{V}$.     *(So the estimate is within +SE about 68% of the time and within +-2SE about 95% of the time.)*

---

This lemma tells us that Monte Carlo integration works. Furthermore its error decreases as $O(N^{-1/2})$ regardless of the dimension. This is vastly inferior to Trapezium or Simpson's rule for one-dimensional integration, but superior in higher dimensions, and when the number of dimensions is dozens or hundreds, it is the only game in town. Of course, we might expect $f$ to be slower to evaluate when the dimensionality is large, but this need not and should not be the exponential relationship that manifests in the curse of dimensionality.

On the other hand, we no longer have any guarantees about the error of the approximate integral. We only have a probabilistic statement that its standard error is a certain quantity, or that it is about $X\%$ likely to within a certain range.

It is a common feature of randomized algorithms that they can sometimes avoid the curse of dimensionality, at the cost of certainty on the error bounds.

###### Proof of Lemma 3.2

(i) is simple: by linearity of expectation, $$E\left[A(R)\frac{1}{n}f(X_i)\right] = \frac{1}{N}\sum_iA(R)E[f(X_i)]=\frac{1}{N}\sum_i\int_R\frac{A(R)}{A(R)}f(x)dx$$
(ii) Using the additive property of variance, because $X_1,...,X_n$ are independent and identically distributed, $$Var[MC_N[f,R]]=Var\left[\frac{A(R)}{N}\sum_if(X_i)\right]=\frac{A(R)^2}{N^2}\sum_iVar[f(X_i)]=\frac{A(R)^2Var[f(X_i)]}{N}.$$So $$V=A(R)^2Var[f(X_i)].$$Furthermore, the formula for variance is $$Var[f(X_i)]=E[f^2(X_i)]-(E[f(X_i)])^2=\frac{1}{A(R)}\int_Rf^2(x)dx-\left(\frac{1}{A(R)}\int_Rf(x)dx\right)^2$$for the same reasons that Monte Carlo integration is useful, the first term can be estimated by $\frac{1}{N}\sum_if^2(X_i)$ and the second by the square of $\frac{1}{N}\sum_if(X_i)$. With the factor $A(R)^2$ we get (ii) in lemma 3.2

(iii) Let us quote Chebyshev's inequality from probability: if $Y$ is any random scalar with finite mean $\mu$ and finite, non-zero variance $\sigma^2$ then $$P(|Y-\mu|\geq \epsilon) \leq \frac{\sigma^2}{\epsilon^2}$$In our case setting $Y = MC_N[f,R]$, (i) proves that $\mu = \int_Rf(x)dx$, so the LHS is $P[|err(MC_N)[f,R]|\geq \epsilon].$ The RHS is $\frac{V}{Ne^2}$, which certainly tends to zero as $N \to \infty$.

(iv) Let us quote, slightly loosely, the central limit theorem from probability: if $Y_1, ... Y_N$ are independent, identically distributed random scalars with finite mean $\mu$ and finite, non zero variance $\sigma^2$ then, for large $N$, $\frac{1}{N}\sum_iY_i$ approaches the Normal distribution $N(\mu,\sigma^2/N)$. In our case, use $Y_i=A(R)f(X_i)-\int_Rf(x)dx$. By (i), $\mu = 0$. By (ii), $\sigma^2=V$.

![[Figure 3.6.png]]
- Monte Carlo integration over a region that we can sample uniformly, and a region that we cannot. In the latter case, red dots indicate samples that were rejected for being outside $R$. The integral is estimated to be the average 'height' of the blue dots, multiplied by the area of $R$.


#### 3.8 [[A Monte Carlo Algorithm]]

How could we use Monte Carlo integration in practice? Generate the random $X_i$, then compute the estimate $$MC_N[f,R]=A(R)\frac{1}{N}\sum_if(X_i)$$and the standard error $SE=\sqrt{\hat{V}/N}$ using our Lemma. Simple?

The difficult part is generating a uniformly distributed random vector $X_i$ from the region $R$. If $R$ is a rectangle, it is as simple as calling your random number generator for each dimension, and scaling the answer. For circles and other simple geometrical objects there are also easy methods. But generating random vectors for complex regions is a topic in its own right. known as simulation.

The simplest way to deal with a complex region $R$ is to find a simple region $S$ with $R \subseteq S$. For example $S$, could be an n-dimensional rectangle that surrounds $R$. Then generate uniform random vectors from $S$ but reject any that do not come from $R$. Only the accepted vectors contribute to the Monte Carlo integration. If $A(R)$ is a lot smaller than $A(S)$ then it means drawing a lot more samples. [[Figure 3.6.png]] displays the procedure of Algorithm 1, which is outlined below. An alternative is to stop, rather than after a fixed number of samples $N$, only when the standard error reduces to a desired level.

*******
**Algorithm 1** Simple Monte Carlo integration, and calculation of standard error.
******
M, Sum, SumSq ← 0
for i ← 1, . . . , N do
	Xi ← GenerateUniform(S) ▷ Generates random vector uniformly from S
	if Xi ∈ R then
		Sum ← Sum + f (Xi)
		SumSq ← SumSq + f (Xi)2
	M ← M + 1
	end if
end for
$MC_N[f, R] ← A(R) \frac{Sum}{M}$                                           ▷ Estimate of R
$SE ← \sqrt{\frac{1}{M}(A(R)^2\frac{SumSq}{M}-(A(R)\frac{Sum}{M})^2)}$                ▷ Standard error

---

If $R$ is so complex that we do not even know its area/volume, we can estimate that too: $A(S)$ times the proportion of accepted samples from $S$ is a good estimate for $A(R)$; indeed, once you see that $$A(R) = \int_R\cases{1, x \in R \\ 0, x \notin R}\;dx,$$this just is another Monte Carlo integral.

There are many ways to improve Monte Carlo integration, which aim to reduce the standard error. That would increases the accuracy of the estimate or, equivalently, reduce the number of samples needed to obtain a desired accuracy.

One method is stratified sampling. This divides the region R into sub-regions and samples each region separately, allocating more samples to the regions that would have higher variance, and fewer samples to the 'easier' regions. We will explore a similar in one of the tutorial exercises. In practice, stratified sampling is often performed by dividing regions recursively, which leads to a more complex algorithm.

Another variance-reduction method is importance sampling. Here, the random vectors $X_i$ are not generated uniformly. Instead, they are generated according to some p.d.f. $g(x)$, and the Monte Carlo estimator is altered to $$MC_N[f,R]=\frac{1}{N}\sum_i\frac{f(X_i)}{g(X_i)}$$It is a one-line proof that this is an unbiased estimator, and when $g$ is chosen well it will have a lower $V$ and hence lower $SE$. A good $g$ is higher when $f$ is higher, and lower when $f$ is lower: this captures the intuitive idea that parts of $R$ with higher values of $f$ contribute more to the integral, and so should be examined more closely, than parts of $R$ with lower $f$.

Lastly, there are control variates. A control variate is another function $\hat{f}$ that is close to $f$, but easy to integrate. Then use $$\int_Rf(x)dx=\int_Rf(x)-\hat{f}(x)dx+\int_R\hat{f}(x)dx.$$The second integral can be done exactly, and if $\hat{f}$ is well-chosen and close to $f$ then the error in estimating the first integral should be smaller than $\int_Rf(x)dx.$ You could see this as a hybrid between pure Monte Carlo integration and methods such as the Trapezium rule or Simpson's rule that approximate the integrand.

