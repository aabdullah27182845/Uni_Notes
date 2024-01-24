
In this chapter, we survey the ideas of continuous and differentiable **univariate** functions $(f : \mathbb{R}\rightarrow\mathbb{R})$ or $(f : D\rightarrow\mathbb{R}$ where $D \subseteq \mathbb{R})$ and Taylor's theorem. Then we move on to **multivariate** functions $(f : \mathbb{R}^n\rightarrow\mathbb{R}$, or $f : D\rightarrow\mathbb{R}$ where $D\subseteq\mathbb{R})$, which in fact has a lot in common with the univariate case as long as one uses vector notation. Taylor's theorem, in both univariate and multivariate versions, will be the basis of much of the material in this course. We also take a brief look at the calculus of **vector-valued function f:** $\mathbb{R}^n\rightarrow\mathbb{R}^m$.


#### 1.1 [[Continuity]]

Continuous maths is about continuous functions. Continuous functions are 'smooth', or 'have no jumps'. We will not dwell on the technical definition, but some mathematical analysis is needed if we want to make it formal:

**Definition**   A function f **is continuous** at $x$ if

		$\lim_{h \to 0}\;f(x+h)=f(x)$

$f$ is called continuous if it is continuous at every point of its domain.

This definition makes sense whether x is a scalar $(\in\mathbb{R})$ or a vector $(\in\mathbb{R})$, and also whether the output of the function is a scalar or a vector. Indeed, continuity also makes sense for functions on many other sets, even some that are not numerical at all. But we will leave this to the mathematicians. We can also leave them the formal proofs that continuous function satisfy certain properties, instead taking such properties for granted. 

Not all functions are continuous. A non-continuous function we are likely to encounter, apart from dividing by or taking logs at zero, is the **sign function**: it usually means one of the follow three functions $sgn : \mathbb{R}\to\mathbb{R}$,

![[Pasted image 20240112194026.png]]

Common sense tells us the following.

---
**[Lemma 1.1]**: If $f$ and $g$ are continuous functions on some domain that is a subset of the reals, then so are

		$f + g,$
		$c f,$
		$f^n$
		$f g$
		$f \circ g$
		$max(f,g)$
		$|f|$
		$exp \;f$
		$f^{\alpha}$
		$\frac{f}{g}$ 
		$\log f$ 

Similar rules apply to functions that return vectors, when the operations are well-defined.

---

Note that it is possible that, for example, $f / g$ to be continuous where $g$ is zero: this sometimes happens when f is also zero at that point, but is not guaranteed. We will leave these difficult cases to mathematicians.

It follows that the class of continuous functions on $\mathbb{R}$ includes polynomials and combinations of exponentials and polynomials. It also includes square roots, quotients, and logarithms of polynomials, as long as we restrict the domain to an interval which ensures that the function is properly defined. The trigonometric functions $\sin$ and $\cos$ are also continuous.


Here is **Example 1.2**  The function $f : \mathbb{R}\to\mathbb{R}$ is given by

		$f(x) = \ln(\frac{1}{1+e^{-x}})$

The function $l:\mathbb{R}\to\mathbb{R}$ is given by

		$l(\textbf{w})=\sum_{i=1}^{m}-y_{i}f(\textbf{w}^{T}\textbf{x}_i) - (1-y_i)(f(-\textbf{w}^{T}\textbf{x}_i))$,

where $x_1, x_2, ..., x_m$ are are fixed vectors and $y_1, ..., y_m$ are fixed scalars. The function $g:\mathbb{R}\to\mathbb{R}$ is given by

		$g(\mu) = \sqrt{exp(-\sum_{i=1}^{m}(x_i - \mu)^{T}\textbf{A}(x_i - \mu)}$

where $x_1, x_2, ..., x_m$ are fixed vectors and $\textbf{A}$ is a fixed $n$-by-$n$ matrix.

They will be used in examples throughout this chapter. All of $f$, $l$, and $g$ are continuous.


#### 1.2 [[Differentiation in One-Dimension]]

Let us limit our attention to continuous univariate functions $l:\mathbb{R}\to\mathbb{R}$.

![[Pasted image 20240112200319.png]]

As $h\to0$, the secant connecting $(x,f(x))$ with $(x+h,f(x+h))$ approaches the tangent to the curve at $x$ which has gradient $\frac{df}{dx}$.

Most of this course is about making approximations to continuous functions, and then using them in algorithms. Sadly, not all continuous functions have good approximations. Our main mathematical tool, Taylor's theorem, applies to functions that have derivatives. Again, a technical definition:

**Definition**   A continuous function $f:\mathbb{R}\to\mathbb{R}$ is called differentiable at x if

		$\lim_{h\to0}\frac{f(x+h)-f(x)}{h}=d$

exists for some real $d$. The value of $d$ is called the derivative at x. $f$ is called differentiable if it is differentiable at every point of D.

We write $\frac{df}{dx}$ for the derivative function. If $f$ is differentiable at some, but not all, points of $D$ then this will be a partial function.

The derivative, if it exists, is the gradient of the function at $x$. 

Since $\frac{df}{dx}$ is a function, we can apply it to an argument. We will write $\frac{df}{dx}(a)$ to mean the value of the derivative function at $a$. Do not, however, confuse $\frac{df(a)}{dx}$ with $\frac{df}{dx}(a)$: the first one is the constant zero function, because $f(a)$ is a constant; the second is the one we need.

Not all functions are differentiable. Non-continuous functions cannot be, but even some common continuous functions fail to be differentiable, for example $x \to |x|$ is not differentiable at zero, nor is $x \to \min(0,x)$. As with continuity, we leave to mathematicians the formal proofs that differentiable functions have the following properties that we learned at school.
***
**Lemma 1.3**   The same facts about continuity in [Lemma 1.1] also apply to the derivatives, except for $|f|$, which is not necessarily differentiable where $f$ changes sign, and $max(f,g)$, which is not necessarily differentiable where $f - g$ changes sign. Some rules for differentiation include:

		$\frac{d}{dx}(f+g) = \frac{df}{dx}+\frac{dg}{dx}$
		$\frac{d}{dx}(cf)=c\frac{df}{dx}$
		$\frac{d}{dx}(f\cdot g) = f\frac{dg}{dx}+g\frac{df}{dx}$,
		$\frac{d}{dx}(\frac{f}{g}) = \frac{g\frac{df}{dx} - f\frac{dg}{dx}}{g^2}$ 
		$\frac{d}{dx}(g\circ f) = (\frac{dg}{dx}\cdot f)\frac{df}{dx}$

assuming all the functions are differentiable at the points concerned. Some standard derivates are

$\frac{d}{dx}x^{\alpha} = \alpha x^{\alpha - 1}$ 
$\frac{d}{dx}e^x = e^x$
$\frac{d}{dx}\ln x = \frac{1}{x}$
$\frac{d}{dx}\sin x = \cos x$
$\frac{d}{dx}\cos x = -\sin x$
$\frac{d}{dx}\log_{2} x = \frac{1}{x\cdot \ln2}$ 

***

A sometimes-useful rule that can be derived from the chain and quotient rules is; if all the derivatives exist and $k \in \mathbb{N}$,

$\frac{d}{dx}(\frac{f}{g^k}) = \frac{g\frac{df}{dx} - kf\frac{dg}{dx}}{g^{k+1}}$

If you think of $\frac{d}{dx}$ as an operator that works on functions, it makes sense to apply it twice. The derivative of the derivative of $f$ if it exists is called the second derivative, and more generally, we can attempt to compute a $k$-$th$ derivative

		$\frac{d^k}{dx^k}$

***
**Example 1.4**

$\frac{df}{dx}=\frac{1}{1+e^x}$, $\frac{d^2f}{dx^2}=\frac{e-^x}{(1+e^x)^2}$, …

***

We have used Leibniz's notation for derivatives; you may already know other various alternatives, such as Newton's notation which uses a dot for a derivative. This is mostly used in physics and won't be used frequently in computer science. Lagrange's notation uses a prime symbol $'$, so $f'(x)$ instead of $\frac{df}{dx}$. For the $k$-$th$ derivative we can write $f^{(k)}(x)$; and the parenthesis are required in order to avoid confusion with the $k$-$th$ power of $f$. 

Lagrange's notation is nicer and easier to write - which is why your dumbass used it in A-Level, but it isn't so easy to use when there are more than one variable we are differentiating with respect to.


#### 1.3 [[Taylor's Theorem (1 dimension)]]

A common and useful way to approximate a function is through Taylor's theorem. The theorem itself is exact, but it is often used to make an approximation to $f(x)$ if we know the value of a function $f$ and some of its derivatives at a single point $x_0$. Typically, but not necessarily, $x_0$ will be close to $x$.

***

**Theorem 1.5 (Taylor's Theorem)**   Let $n \geq 0$ be any integer. As long as $f:D\to\mathbb{R}$ satisfies some so-called smoothness conditions,

$f(x) \approx f(c) + f'(c)(x - c) + \frac{f''(c)}{2!}(x - c)^2 + \frac{f'''(c)}{3!}(x - c)^3 + \ldots + \frac{f^{(n)}(c)}{n!}(x - c)^n$,

***

The theorem holds for all $x_0, x$ and $n$. We will want to rewrite Taylor's theorem in some different forms. If we take $\frac{d^0f}{dx^0}$ to mean the original function $f$, we can write 

$$f(x) = \hat{f}_k(x) + e_{k+1}(x,x_0)$$
where
$$\hat{f}_k(x)=\sum_{i=0}^{k}\frac{(x-x_0)^i}{i!}\cdot\frac{d^if}{dx^i}(x_0)$$

is called the order-k Taylor polynomial or sometimes the truncated Taylor series. Note that $\hat{f}_k$ is a polynomial of degree $k$, and furthermore the unique polynomial of degree k that agrees with $f$ as to its first $k$ derivatives at $x_0$. We use it as an approximation for $f$ near $x_0$, with $e_{k+1}(x,x_0)$ telling us the error of the approximation.

$e_{k+1}(x,x_0)$ is called the error term, also called the Lagrange remainder term.

$$e_{k+1}(x,x_0) = \frac{(x-x_0)^{k+1}}{(k+1)!}\cdot\frac{d^{k+1}f}{dx^{k+1}}(\xi)$$

Mathematicians know the equivalent formulae for the remainder, but will not be mentioned here, since the Lagrange version will work best for us. If $e_{k+1}(x,x_0)$ is the error in the approximation

$$f(x) \approx f_k(x)$$

then we might hope that this error is small. If $|x-x_0| < 1$ then the term $(x-x_0)^{k+1}$ helps to make it small for large $k$, as does by dividing it by the factorial term $(k+1)!$, but it is possible that the $(k+1)$-$st$ derivative of $f$ gets larger as $k$ does. $e_{k+1}$ is guaranteed to be 'small' in the following sense: $x\to x_0, e_{k+1}(x,x_0)/(x-x_0)^k \to0$. However, this doesn't mean that it is actually small for a particular combination of $f$, $x_0$ and $x$, and indeed this error might be much bigger than the value of the function itself. When we apply Taylor's theorem, we will have to examine the remainder and see.

Typically, we want to find the upper and lower bounds on the error, which often come from upper and lower bounds of the $k+1$-st derivative: if $\underline{C} \leq \frac{d^{k+1}f}{dx^{k+1}} \leq \bar{C}$ on the interval $(x_0, x)$, then

$$\bar{f}_k(x) + C (x - x_0)k+1 \leq f(x) \leq \hat{f}_k(x) + \bar{C}\frac{(x - x_0)^{k+1}}{(k + 1)!}$$

with the inequalities reversed if $k$ is even and $x < x_0$. This is the essence of Taylor's theorem as a numerical approximation: the $k$-$th$ order Taylor polynomial has an error that depends of the $k+1$-st derivative of $f$.

Don't confuse Taylor's theorem with the concept of a Taylor series. The latter attempts to express the function $f$ as an infinite sum with no remainder term

$$f(x) \stackrel{?}{=}\sum_{n=0}^{\infty} \frac{\frac{d^n}{dx^n}(f(x_0))}{n!}(x - x_0)^n$$ 

The reason for the question mark - for you mathematicians and your analysis of convergence and divergence - is that this series may not converge onto the correct answer.  A function where the error term in Taylor's theorem satisfies $e_k(x,x_0)\to0$ as $k\to\infty$, for all $x$ and small enough $x-x_0$, will always have a correct Taylor series and is called analytic. But as computer scientists we will find it more useful to use a finite sum (since these are computable) than an infinite one.

![[Pasted image 20240113121453.png]]


#### 1.4 [[Partial Differentiation]]

Now let us move to functions of more than one variable, $f:D\to\mathbb{R}$ for $D\subseteq\mathbb{R}^n$. Depending on the context these are called multivariate functions or scalar fields. When we differentiate $f$ with respect to one variable $x$, holding all others constant, this is called a partial derivative, and is written $\frac{\partial f}{\partial x}$. There is nothing otherwise special about partial differentiation: it is the same operation, and satisfies the same properties, as differentiation of univariate functions.

***
**Example 1.7**  (a) If $c(x,y) = 2x^2 + xy + y^2$ then $\frac{\partial c}{\partial x}=4x+y$ and $\frac{\partial c}{\partial y}=x+2y$
b) If $d(x_1,...,x_n)=\sum(a_ix_i)$ then $\frac{\partial d}{\partial x_k} = a_k$
c)If $f(x_1,...,x_n)=\sum_{i}\sum_{j}(a_{ij}x_ix_j)$ then $\frac{\partial f}{\partial x_k} = \sum_i(a_{ik}+a_{ki})x_i$  
***

Higher partial derivates can also be computed: $\frac{\partial}{\partial y}\frac{\partial}{\partial x}f$ means to differentiate first with respect to $x$, holding other variables constant, and then y. It is written $\frac{\partial^2f}{\partial y \partial x}$. Note that the order of differentiation is the reverse of what is written in the denominator. The good news is that, in most circumstances, we do not need to distinguish between the so-called mixed partials. **Clairaut's Theorem** says that they will be equal at least as long as they are both continuous. 

The second partial derivatives can be collected together into a matrix called the **Hessian**. 

$$\textbf{H}(f) = \begin{bmatrix}
  \frac{\partial^2 f}{\partial x_1^2} & \frac{\partial^2 f}{\partial x_1 \partial x_2} & \cdots & \frac{\partial^2 f}{\partial x_1 \partial x_n} \\
  \frac{\partial^2 f}{\partial x_2 \partial x_1} & \frac{\partial^2 f}{\partial x_2^2} & \cdots & \frac{\partial^2 f}{\partial x_2 \partial x_n} \\
  \vdots & \vdots & \ddots & \vdots \\
  \frac{\partial^2 f}{\partial x_n \partial x_1} & \frac{\partial^2 f}{\partial x_n \partial x_2} & \cdots & \frac{\partial^2 f}{\partial x_n^2}
\end{bmatrix}
$$
i.e. the $(i,j)$ entry is $\frac{\partial^2f}{\partial x_i \partial x_j}$. When everything is continuous, Clairaut's theorem says that $\textbf{H}(f)$ is symmetric. The role of this matrix is similar to $\frac{d^2f}{dx^2}$ in the case of the univariate functions, and we will see much more of it soon. 

Note that the Hessian is a function of $x$ and will normally take different values at different points. When we want to evaluate the Hessian at a particular $x^*$, we write $\textbf{H}(f)(x^*)$, which can also be written as $\textbf{H}_f(x^*)$  in order to avoid the extra parentheses.

![[Pasted image 20240113124801.png]]


#### 1.5 [[Differentiation (n dimensions, scalar function)]]

If collecting the second partial derivatives into a matrix makes sense, so does collecting the first partial derivatives in a vector. For a function $f : D\to\mathbb{R}$ for $D\subseteq\mathbb{R}$ it is very convenient to write

$$\nabla \mathbf{v} = \begin{bmatrix} \frac{\partial v_1}{\partial x_1} \\ \frac{\partial v_2}{\partial x_1} \\ \vdots \\ \frac{\partial v_n}{\partial x_1} \end{bmatrix}$$
Some books may write it as $D(f)$; applied maths and physics texts tend to write it $\nabla f$, the gradient function of f. Note that, unlike $f$ which returns a scalar,  $\nabla f$ is a vector-valued function. A few sources write it as a row vector rather than a column vector, but don't worry, we will write it as a column vector.

Our interest in collecting partial derivatives into a vector is because it allows us to use familiar identities. All but one of the following are quite easy to prove from the corresponding rules for univariate functions, but we will leave it to the mathematicians.

***
**Lemma 1.9** As long as all the derivatives concerned exist,
$$
\begin{aligned}
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}(f+g) & =\frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}}+\frac{\mathrm{d} g}{\mathrm{~d} \boldsymbol{x}}, \\
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}(c f) & =c \frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}}, \\
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}(f g) & =f \frac{\mathrm{d} g}{\mathrm{~d} \boldsymbol{x}}+g \frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}}, \\
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}\left(\frac{f}{g}\right) & =\frac{g \frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}}-f \frac{\mathrm{d} g}{\mathrm{~d} \boldsymbol{x}}}{g^2},
\end{aligned}
$$

If $f: \mathbb{R}^n \rightarrow \mathbb{R}$ and $g: \mathbb{R} \rightarrow \mathbb{R}$,
$$
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}(g \circ f)=\left(\frac{\mathrm{d} g}{\mathrm{~d} x} \circ f\right) \frac{\mathrm{d} f}{\mathrm{~d} \boldsymbol{x}},
$$
If $f: \mathbb{R}^m \rightarrow \mathbb{R}^n, g: \mathbb{R}^n \rightarrow \mathbb{R}$
$$
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}}(g \circ \boldsymbol{f})=\mathbf{J}(\boldsymbol{f})^T\left(\frac{\mathrm{d} g}{\mathrm{~d} \boldsymbol{x}} \circ \boldsymbol{f}\right),
$$
Some standard derivatives are:
$$
\begin{array}{rlrlrl}
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}} \boldsymbol{a}^T \boldsymbol{x} & =\boldsymbol{a}, & \frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}} \boldsymbol{x}^T \boldsymbol{a} & =\boldsymbol{a}, & \text { (where } \boldsymbol{a} \text { is a column vector) } \\
\frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}} \boldsymbol{x}^T \boldsymbol{x} & =2 \boldsymbol{x}, & \frac{\mathrm{d}}{\mathrm{d} \boldsymbol{x}} \boldsymbol{x}^T \mathbf{A} \boldsymbol{x} & =\left(\mathbf{A}+\mathbf{A}^T\right) \boldsymbol{x}, & &
\end{array}
$$
***

Computing derivatives in this way can simplify notation and save a lot of time: it is a good idea to practice it.

![[Pasted image 20240113131318.png]]


#### 1.6 [[Vector Fields and the Jacobian]]

Briefly, let us consider vector-valued functions $f:\mathbb{R}^n\to\mathbb{R}^m$, sometimes called a vector field. These are not our primary interest in this course but they allow us techniques for calculating the Hessian of a scalar function conveniently: notice that the derivative of a scalar-valued multivariate function is a vector-valued function.

It is best to consider a vector-valued function to be a vector of scalar-valued functions, each giving one of it's 'co-ordinates'. So $\textbf{f(x)}$ can be broken into the vector
$$\mathbf{v} = \begin{bmatrix} f_1(x) \\ f_2(x) \\ \vdots \\ f_m(x) \end{bmatrix}$$
The object that acts like the derivative of $f$ is called the Jacobian, which is the matrix

$$J(\mathbf{f}) = \begin{bmatrix}
  \frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} & \cdots & \frac{\partial f_1}{\partial x_n} \\
  \frac{\partial f_2}{\partial x_1} & \frac{\partial f_2}{\partial x_2} & \cdots & \frac{\partial f_2}{\partial x_n} \\
  \vdots & \vdots & \ddots & \vdots \\
  \frac{\partial f_m}{\partial x_1} & \frac{\partial f_m}{\partial x_2} & \cdots & \frac{\partial f_m}{\partial x_n}
\end{bmatrix}
$$

This contains the gradient of each $f_i$ as one of its rows. This leads to the rather unfortunate fact that the Jacobian of a scalar functions is the transpose of its gradient, the row vector $(\frac{\partial f_1}{\partial x_1}\;\frac{\partial f_1}{\partial x_2}...\frac{\partial f_1}{\partial x_n})$. Indeed some books prefer to define the Jacobian to be the transpose of what we used above, so take care: our version is probably more common.

Do not confuse the Hessian matrix with the Jacobian. Both are matrices which are functions of $x$, but the Hessian is always square and contains second partial derivatives of a multivariate scalar-valued function, whereas the Jacobian is a $m$-by-$n$ and contains first partial derivatives of a vector-valued function. 

You could also say that the Jacobian works a bit like a vector derivative since if we take apply it in the first principles of differentiation, we see that it does behave like a derivative.

The reason we will use a Jacobian is the following observation, which comes directly from the definitions. For $f:\mathbb{R}^n \to\mathbb{R}$,
$$\textbf{H}(f)=\textbf{J}(\frac{df}{dx})^T$$
Furthermore, as long as everything in sight is continuous, the Hessian is symmetric so the transpose changes nothing.

Like derivatives, Jacobians have rules for applying them to sums, products, and compositions, but not the quotient rule since you cannot divide vectors with vectors. Most of these are easy to prove from the definitions and the rules for univariate scalar functions, but the partial chain rule is more difficult. We will leave the proofs to mathematicians.

You may not need all these rules for prelims, but they are included as a reference for future thy (lol).

***
**Lemma 1.11**  As long as all the derivatives concerned exist, 

$$
\begin{aligned}
\mathbf{J}(\boldsymbol{f}+\boldsymbol{g}) & =\mathbf{J}(\boldsymbol{f})+\mathbf{J}(\boldsymbol{g}), \\
\mathbf{J}(c \boldsymbol{f}) & =c \mathbf{J}(\boldsymbol{f}), \\
\mathbf{J}(\mathbf{A} \boldsymbol{f}) & =\mathbf{A} \mathbf{J}(\boldsymbol{f}), \\
\mathbf{J}\left(\boldsymbol{f}^T \boldsymbol{g}\right) & =\boldsymbol{g}^T \mathbf{J}(\boldsymbol{f})+\boldsymbol{f}^T \mathbf{J}(\boldsymbol{g}), \\
\mathbf{J}(f \boldsymbol{g}) & =\boldsymbol{g} \frac{\mathrm{d} f^T}{\mathrm{~d} \boldsymbol{x}}+f \mathbf{J}(\boldsymbol{g}), \\
\mathbf{J}(\boldsymbol{g} \circ \boldsymbol{f}) & =(\mathbf{J}(\boldsymbol{g}) \circ \boldsymbol{f}) \mathbf{J}(\boldsymbol{f}),
\end{aligned}
$$

Some standard Jacobians are:
$$
\mathbf{J}(\boldsymbol{x})=\mathbf{I}, \quad \mathbf{J}(\mathbf{A} \boldsymbol{x})=\mathbf{A}, \quad \text { (where } \mathbf{A} \text { is a matrix) }
$$
***

A special case of the product of the scalar and vector rule is when the vector is constant: if $c$ does not depend on $x$ then

$$\mathbf{J}(f(x)\cdot c) = c\frac{df}{dx}^T$$
![[Pasted image 20240113135531.png]]


#### 1.7 [[Taylor's Theorem (n dimensions, scalar function)]]

Taylor's theorem has a version for multivariate functions, which has similar uses: we can approximate a function near a point by a multivariate polynomial, whose coefficients depend on its (partial) derivatives at that point. And we can write a formula for the error in this approximation. At first sight it is a mess of notation, but we will find ways of making it more comprehensible.

***
**Theorem 1.13 (Taylor's theorem)** Let $D \subseteq\mathbb{R}^n$ and $f:D\to\mathbb{R}$. Given vectors $x,x_0\in\mathbb{R}^d$, write their difference as $x-x_0=h=(h_1,...,h_n)^T$. Let $k\geq0$ be an integer. As long as $f$ satisfies some smoothness condition, then
$$\begin{aligned} f(\boldsymbol{x})= & f\left(\boldsymbol{x}_{\mathbf{0}}\right) \\ & +\left[\left(h_1 \frac{\partial}{\partial x_1}+\ldots+h_n \frac{\partial}{\partial x_n}\right) f\right]\left(\boldsymbol{x}_0\right) \\ & +\frac{1}{2 !}\left[\left(h_1 \frac{\partial}{\partial x_1}+\ldots+h_n \frac{\partial}{\partial x_n}\right)^2 f\right]\left(\boldsymbol{x}_0\right) \\ & +\cdots \\ & +\frac{1}{k !}\left[\left(h_1 \frac{\partial}{\partial x_1}+\ldots+h_n \frac{\partial}{\partial x_n}\right)^k f\right]\left(\boldsymbol{x}_0\right) \\ & +\frac{1}{(k+1) !}\left[\left(h_1 \frac{\partial}{\partial x_1}+\ldots+h_n \frac{\partial}{\partial x_n}\right)^{k+1} f\right]\left(\boldsymbol{x}_0+\xi \boldsymbol{h}\right)\end{aligned}$$
for some $\xi\in(0,1)$. The final term is the error term, denoted $e_{k+1}(x,x_0)$.
***

A sketch of how to derive this from the univariate version is provided in other notes.

This looks intimidating, but it has similarities to the one-dimensional version of Taylor's theorem: derivatives of order up to $k$ evaluated at $x_0$, and a remainder term that involves $k$+1-st order derivatives evaluated somewhere on the straight line between $x_0$ and $x$. Let us simplify it gradually. 

First, what do we mean by a term like $[(h_1\frac{\partial}{\partial x_1}+...+h_n\frac{\partial}{\partial x_n})^2f]$? It is a shorthand: if we multiply out the square, we see that  

$$\begin{align*}
& (h_1\frac{\partial}{\partial x_1} + \ldots + h_n\frac{\partial}{\partial x_n})^2f \\
&= (h_1\frac{\partial}{\partial x_1} + \ldots + h_n\frac{\partial}{\partial x_n})(h_1\frac{\partial}{\partial x_1} + \ldots + h_n\frac{\partial}{\partial x_n})f \\
&= \;\;\;\;\;... \\
&= \sum_ih_i^2\frac{\partial^2f}{\partial x_i^2}f + 2\sum \sum_{i<j} h_ih_j\frac{\partial^2f}{\partial x_i \partial x_j}
\end{align*}
$$
The same applies to the other terms. 

Let's take the case of a function of two variables $f(x,y)$, up to third order with a fourth-order term error term. So $n=2, k=3$. We will use $(x_0,y_0)$ for the vector $x_0$, similarly $(x,y)$ for $x$. Then, $e_{k+1}(x,x_0)$ becomes

$$\begin{aligned} & f(x, y)= \\ & \quad f\left(x_0, y_0\right) \\ & \quad+\left(x-x_0\right) \frac{\partial f}{\partial x}\left(x_0\right)+\left(y-y_0\right) \frac{\partial f}{\partial y}\left(x_0\right) \\ & +\frac{1}{2 !}\left(\left(x-x_0\right)^2 \frac{\partial^2 f}{\partial x^2}\left(x_0\right)+2\left(x-x_0\right)\left(y-y_0\right) \frac{\partial^2 f}{\partial x \partial y}\left(x_0\right)+\left(y-y_0\right)^2 \frac{\partial^2 f}{\partial y^2}\left(x_0\right)\right) \\ & \quad+\frac{1}{3 !}\left(\left(x-x_0\right)^3 \frac{\partial^3 f}{\partial x^3}\left(x_0\right)+3\left(x-x_0\right)^2\left(y-y_0\right) \frac{\partial^3 f}{\partial x^2 \partial y}\left(x_0\right)+3\left(x-x_0\right)\left(y-y_0\right)^2 \frac{\partial^3 f}{\partial x \partial y^2}\left(x_0\right)\right. \\ & \left.\quad+\left(y-y_0\right)^3 \frac{\partial^3 f}{\partial y^3}\left(x_0\right)\right) \\ & +\frac{1}{4 !}\left(\left(x-x_0\right)^4 \frac{\partial^4 f}{\partial x^4}\left(x^*\right)+4\left(x-x_0\right)^3\left(y-y_0\right) \frac{\partial^4 f}{\partial x^3 \partial y}\left(x^*\right)+6\left(x-x_0\right)^2\left(y-y_0\right)^2 \frac{\partial^4 f}{\partial x^2 \partial y^2}\left(x^*\right)\right. \\ & \left.\quad+4\left(x-x_0\right)\left(y-y_0\right)^3 \frac{\partial^4 f}{\partial x \partial y^3}\left(x^*\right)+\left(y-y_0\right)^4 \frac{\partial^4 f}{\partial y^4}\left(x^*\right)\right)\end{aligned}$$
where $x^* = x_0+\xi(x-x_0)$ with $\xi\in(0,1)$.

In Taylor's theorem the remainder can be shown to be 'small' in the following sense: $e_{k+1}(x,x_0)/||x-x_0||^k\to0$ as $||x-x_0||\to0$. As in the one-dimensional case, whether it is actually small in relation to $f(x)$ depends on our function. The good news is that we will rarely need to examine a remainder term above second order.

Recall that Taylor's univariate theorem approximates $f(x)$ as a polynomial of order $k$ in $x$, whose coefficients depend on $f$ and its derivatives at $x_0$, and is the only such polynomial that agrees wit the first $k$ derivatives of $f$ at $x_0$. The multivariate version approximates $f(x)$ as a multivariate polynomial of order $k$ in $x$, whose coefficients depend on $f$ and its partial derivatives all at $x_0$, and is the unique polynomial such that it agrees with those derivatives.

Finally, we can make Taylor's theorem look a lot less messy if we use vector notation: notice that
$$(h_1\frac{\partial}{\partial x_1} + ... + h_n\frac{\partial}{\partial x_n})f = h^T\frac{df}{dx}$$
and
$$(h_1\frac{\partial}{\partial x_1} + ... + h_n\frac{\partial}{\partial x_n})^2f = h^T\textbf{H}(f)\frac{df}{dx}$$
We can only do this up to the second term. The third term will involve a 'three-dimensional matrix' of all mixed third-order partials: such a thing does exist (called a rank-3 tensor) but it is way beyond the scope of this course. That's when you want $\frac{\partial^3}{\partial x^3}$ .The good news is that we almost always only use Taylor's theorem at order 0,1, or 2. Putting everything together, we have the following simplified cases:

***
**Lemma 1.14 (The multivariate Taylor's theorem that you will want to learn)**

$f(x)=f(x_0) + (x-x_0)^T\frac{df}{dx}(x^*)$

$f(x)=f(x_0) + (x-x_0)^T\frac{df}{dx}(x_0) + \frac{1}{2}(x-x_0)^T\textbf{H}(f)(x^*)(x-x_0)$

$f(x)=f(x_0) + (x-x_0)^T\frac{df}{dx}(x_0) + \frac{1}{2}(x-x_0)^T\textbf{H}(f)(x^*)(x-x_0) + e_3$

where in each case $x^*$ lies on the line between $x_0$ and $x$. The remainder term $e_3$ is complicated to write out, bit at least it is 'small' in the sense that it approaches zero as the difference of $x$ and $x_0$ approaches zero.
***

![[Pasted image 20240113143008.png]]

![[Pasted image 20240113143034.png]]

