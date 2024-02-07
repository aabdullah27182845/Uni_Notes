Once we start using algorithms that return an approximation - be it Taylor's theorem to approximate the value of a function, Simpson's rule to approximate an integrand and hence the integral, or Monte Carlo integration that returns a random number that should be close to the true answer - we encounter error. These notes is about how we measure error and some of the sources of error. We should have some idea of what levels of accuracy are feasible for 'normal' implementations of numbers on computers. And for iterative algorithms, that refine the accuracy of an answer step by step, we need to measure the rate at which the answer improves.


#### 4.1 [[Error and Relative Error]]

Let $u \in \mathbb{R}$ be the number that we want to approximate, and $\tilde{u} \in\mathbb{R}$ its approximation. There are three ways to measure the error $$\begin{aligned}\tilde{u}-u \;\;\;\text{is the error}, \\ |\tilde{u}-u|\;\;\;\text{is the absolute error},\\\frac{|\tilde{u}-u|}{|u|}\;\;\;\text{is the relative error.}\end{aligned}$$If we approximate a vector $\mathbf{u}\in\mathbb{R}^n$ by $\mathbf{\tilde{u}}\in\mathbb{R}^n$, then we would replace the absolute value $|\cdot|$ by the Euclidean norm $||\cdot||$. Absolute and relative error are always scalars.

For an example, suppose that we wish to approximate the value of $\sin(\frac{355}{113})$ using the Taylor polynomial we derived earlier, but for even more accuracy, we decide to use the 14-th order Taylor polynomial $$\sin(x)\approx x-\frac{x^3}{3!}+\frac{x^5}{5!}-\frac{x^7}{7!}+\frac{x^9}{9!}-\frac{x^{11}}{11!}+\frac{x^{13}}{13!}.$$Before starting, we computed an upper bound on the error, which in our earlier examples, we found was $\frac{x^{15}}{15!}\cos(\xi)$ for some $\xi$, which is less than $(\frac{355}{113})^{15}/15!\approx 0.0000219$. The absolute error of our equation is going to be small.

We feel less good about the error when we compute the equation earlier to 12 decimal places. In fact, $$\sin(\frac{355}{113})=-0.000000266764189...$$and the relative error was approximately $-79.26$. We did not even get the sign correct.

This is a case where the absolute error is small, but the relative error is large. Furthermore, we might try to reduce the relative error by increasing the order of the Taylor polynomial. We can do this to some extent, reducing the relative error to around $10^{-9}$ with a 26th order polynomial. But, after that, additional terms do not reduce the absolute or the relative error at all. This is because of how real numbers are stored with limited precision.

Whether relative or absolute error is appropriate depends on the application. We might think that an error of $\underline{+}1$ is significant if the true value is $2$, and insignificant if the true value is $2\cdot 10^9$: this suggests that relative error is a better quantity, and more advanced books on numerical analysis tend to focus on the relative error. On the other hand, in this introductory course we will often only be able to deal with absolute error. It is generally simpler to find bounds on absolute error; you might notice that the error bounds for numerical integration, in [[Algorithms for Numerical Integration]], were all in absolute terms.

Something that advanced books also discuss is the distinction between forward error and backward error. Loosely speaking, when we want to approximate the value of $f(x)$ by a numerical algorithm that computes $\tilde{f}(x)$, the forward error is $\tilde{f}(x) - f(x)$ and the backward error is $\tilde{x}-x$ when $\tilde{f}(x) = f(\tilde{x})$. The forward error is how much the approximate answer is wrong, and the backward error is how much you would have to change the input to make the approximate answer right. Don't worry about this stuff, it's not in the scope of the syllabus.


#### 4.2 [[Floating-point Numbers]]

Computers rarely store a real number precisely. Although it is conceivable to store arbitrary many decimal places, it is impossible to store infinitely many. And to keep memory requirements small and predictable, almost every language and processor store a discrete approximation to a real number called a floating-point number.

Just as scientists often write numbers as some multiple of a power of ten, e.g. $c=2.99792\cdot10^8$, the most common computer representations of real numbers are powers of two, of the form $$\underline{+}\left(1+\frac{m}{2^p}\right)\cdot2^e$$where $0\leq m<2^p$ and $e\in\mathbb{Z}$ is called the mantissa, and $e$ is called the exponent. In the IEEE 754 technical standard for floating-point, there is a particular format for storing this representation, which need not concern us. Sufficient to know that $m$ is stored in binary using $p$ bits, $e$ (limited to $e_{min}<e<e_{max}$) using $[\log_2(e_{max}-e_{min})]$ bits, and the plus-minus sign using 1 bit. The number of bits in the mantissa, $p$ is called the precision. The standard also specifies the representations of error, and some special cases for numbers very close to zero.

Single precision - takes 4 bytes of storage: the sign bit, 23 bits of precision, and 8 bits for the exponent with $e_{min}=-126$, $e_{max}=127$. The type associated with single precision numbers in commonly called `float` 

Double precision - takes 8 bytes of storage, the sign bit, 52 bits of precision, and 11 bits for the exponent with $e_{min}=-1022$, $e_{max}=1023.$ The type associated with double-precision numbers is commonly called `double`.

Some processors also use registers that have extended precision, taking 10 bytes of memory and supplying 63 bits of precision. There is also a less-widely implemented quadruple-precision format (big decimal).

It is probably true that the default for computing today is double precision; the exception would be GPUs, where single precision is still common, as 23-bits of mantissa is sufficient resolution for storing colours of textures and pixels.

What does this mean for numerical computing? First, that (unlike integers) there is almost no chance of overflow. The largest positive number that can be stored in double precision is just under $2\cdot 2^{1023}$, which is more than $10^{308}$. Instead, the danger is of rounding error, because only approximately 15 significant decimal digits are stored. Note that the rounding errors of floating point numbers are relative, not absolute. Understanding when floating-point rounding is going to cause significant errors could be a lecture course in its own right, but here are some piecemeal advice. 

First of though, consider the Gram-Schmidt Orthogonalization. Since the algorithm recursively finds orthogonal vectors by using division, it is a very unstable algorithm since its relative error grows with each iteration of the algorithm. Back to the advice:

1. Because few floating-point numbers are stored exactly, and will be subject to rounding at intermediate stages of computation, it is dangerous to test them for equality. In Scala, `var x=0.0 ; while(x != 1.0) x += 0.1` fails to terminate because `x` just misses the exact value $1.0$. Instead of that, do the following `if (abs(a-b) < SMALL)` for some small constant `SMALL`. Choose this constant with thought.
2. The accuracy of floating point numbers is usually measured by machine epsilon. the quantity $\epsilon$ such that every number $x\in\mathbb{R}$ can be stored as $\tilde{x}$ with relative error no more than $\epsilon$. For single precision numbers, our machine epsilon is $2^{-24}\approx 6 \cdot 10^{-8}$; meanwhile the precision error for double-precision numbers is $2^{-53}\approx 1.1 \cdot 10^{-16}$. You should never rely on any floating-point computation having a relative error less than $\epsilon$. Given that most computations involve a sequence of operations, each of which can introduce or compound previous errors, relative errors a few multiples of $\epsilon$ are more common. For example, Brent's version of the famous `zeroin` one-dimensional root-finder guarantees relative error no more than $4\epsilon$![[Figure 4.1.png]]
3. Particular peril lurks where the result of a computation is orders of magnitude smaller than the inputs, because then small relative errors on the inputs can lead to large relative errors on the outputs. A notoriously nasty operation is subtraction: if $a\approx b$ then $a-b\approx 0$, so small relative errors in $a$ and/or $b$ can cause large relative errors in $a-b$. When this happens it is called catastrophic cancellation. Sometimes, this can be avoided simply by replacing one formula by another, equivalent formal that avoids subtracting close numbers. For example, in [[Figure 4.1.png]], we compute $1-\cos x$ and the equivalent $2\sin^2(\frac{x}{2})$, which avoids subtracting close numbers. For small $x$, the latter is much more accurate. On the other hand, if this occurs in a context like $\sqrt{2}+1-\cos x$ then there is probably no need to worry about the cancellation, as the error will remain small relative to the overall answer.


#### 4.3 [[Sources of Error]]

What are the sources of error in numerical computing? There are many, and different authors categorize them in different ways. We will mention two main types of error:

- Truncation error :: is the error introduced when approximating an infinite procedure by a finite procedure. For example, the use of a finite Taylor polynomial to approximate a function, we have truncated a power series and the remainder term is the truncation error. Or when we perform numerical integration we have truncated a hypothetical infinite sum by the finite sum over $n$ strips.
- Roundoff error :: is error introduced by approximating a real number. for example in a floating-point formal. A common mistake is to think that roundoff error is called truncation error because it involves truncating the binary representation of a floating-point number. Roundoff error is not truncation error.

We could also identify other sources of error: model error, if we are computing the output of a model but the model itself is an approximation; input error; programmer error. Some texts also mention propagation error, which refers to errors arising in an early part of a numerical method being amplified by a later part. This phenomenon is called instability and some classic problems are subject to it. For some $\mathbf{A}$, solving $\mathbf{Ax=b}$ can be very unstable.

To illustrate a problem with two sources of error, let us approximate $\frac{df}{dx}$ by the numerical derivative $$\tilde{d}(x,h)=\frac{f(x+h)-f(x)}{h}$$By definition, $\tilde{d}(x,h)$ tends to $\frac{df}{dx}$ as $h\to 0$. So we approximate $\frac{df}{dx}$ by $\tilde{d}(x,h)$ with small, nonzero, $h$. Let us apply this method to the function $f(x)=e^x$, at $x=0$. Of course, we know the true answer to be 1, so the relative error equals the absolute error in this instance. Implementing $\frac{f(x+h)-f(x)}{h}$ on a computer, we show this error for values of $h$ between $10^{-16}$ and $1$. Reading the graph from right-to-left, observe that the error reduces as $h$ reduces, until $h$ gets to a certain level; then it increases. 

![[Figure 4.2.png]]

The error for large $h$ is mainly due to truncation error, approximating a limit by a small finite value. The error for small $h$ is due to roundoff error in calculating $f(x+h)-f(x)$, which is an example of catastrophic cancellation: a small relative error in $f(x+h)$ leads to a large relative error in $f(x+h)-f(x)$ because $f(x+h)-f(x)$ is close to zero. For example, although $f(10^{-16})$ is actually just over $1+10^{-16}$, its double-precision floating point representation is 1, so $f(10^{-16})-f(0)$ comes out to zero.

Truncation error is usually something that we can analyse, and pick suitable parameters to keep it small. In the above example, we could use the remainder of Taylor's theorem to show that $h < 10^{-6}$ should give relative truncation error no more than $5 \cdot 10^{-7}$, for example. But, as we said earlier, roundoff is more difficult to analyse. Scientists using numerical computing have to spot the types of computation where roundoff error is significant, such as catastrophic cancellation.


#### 4.4 [[Rates of Convergence]]

Suppose that we have designed iterative numerical algorithm: a procedure that attempts to approximate the true value $x$ by starting with an initial guess, then repeatedly refining an answer, making it more and more accurate. We will see many such algorithm in the later notes. Starting with $x_0$, let $$x_{n+1}=f(x_n)\;\;\;\;\text{or}\;\;\;\;\mathbf{x}_{n+1}=f(\mathbf{x}_n)$$We identify the absolute error at the n-th step as $$c_n=x_n-x^*$$The first goal of our algorithm is that it should converge to the correct answer: $|\epsilon_n|\to 0$ as $n\to\infty$. But how to compare two methods, both of which achieve this goal? In numerical algorithms, just as in exact algorithms, we want to look at the time complexity: in our case, the time complexity of computing $x_{n+1}$ from $x_n$. But we also need to look at how quickly does our limit for epsilon converge. The speed of convergence is measured in the following way,

Here's a definition: Let $\epsilon_n$ be the error of some iterative numerical algorithm $A_n$ after $n$ steps. If $|\epsilon_n|\to0$ we say that 
1. $A_n$ converges linearly if, for some $0<a<1,\;\frac{\epsilon_{n+1}}{\epsilon_n}\to a.$
2. $A_n$ converges sub-linearly, if $\frac{|e_{n+1}|}{|e_n|}\to1,$ a special case, logarithmic convergence, if also $\frac{|e_{n+2}-e_{n+1}|}{|e_{n+1}-e_n|}\to1$.
3. $A_n$ converges super-linearly if $\frac{|e_{n+1}|}{|e_n|}\to 0$, a special case, order-q convergence, if $\frac{|e_{n+1}|}{|e_n|^q}\to 0$ for $a > 0, q > 1$.

All limits are viable as $n\to\infty$. Note that $q$ need not be an integer.

We might measure the performance of a numerical algorithm in terms of its actual error, or alternatively in terms of an upper bound on its error. The latter is more appropriate when the algorithm approaches the true answer non-monotonically, in which case the error might go up before it goes down again, all while tending to zero. In some literature the convergence of the actual error is called Q-linear convergence, Q-quadratic convergence, etc., while the convergence of an error bound is called R-linear convergence, R-Quadratic convergence, etc. We will not need such a distinction.

We can also use slightly relaxed definitions that do not require the ratio of successive errors to converge to a limit, merely to have an upper bound. For example, if $$\frac{|\epsilon_{n+1}|}{|\epsilon_n|^q}<a, \text{for} \;a < 1$$then we have at least linear convergence, and if $$\frac{|\epsilon_{n+1}|}{|\epsilon_n|^q}<a, \text{for} \;a < 1,q>1$$then we have at least order-q convergence. They are at least because the limit of the ration might be zero.

---
Example 4.1  For $c>1$
1. $x_n=\frac{1}{n}$ converges logarithmically to zero.
2. $x_n = c^n$ converges linearly to zero.
3. $x_n = c^{n^2}$ converges super-linearly, but not with order $q$ for any $q>1$, to zero.
4. $x_n = c^{2^n}$ converges quadratically to zero.
---

The words quadratic and linear do not mean that the error itself is a quadratic or linear function of $n$. Instead, it is best to understand a linearly convergent algorithm as one that increases the number of correct decimal places $(-\log_{10}|e_n|)$ by a constant at each step, and a quadratically convergent algorithm as one that approximately doubles the number of correct decimal places at each step, provided that the error is fairly small to begin with. Quadratic algorithms are highly prized because they need so few steps to achieve accuracy that reaches the limit of rounding. However, it is a common property of quadratically convergent numerical algorithms that they fail to converge unless the starting point is chosen carefully.

When computing a slower algorithm with a fast-convergence against a fast algorithm with a slow convergence, you should bear the following lemma in mind:

******
Lemma 4.2   If $A_n$ converges with order $q$, then $A_{2n}$ converges with order $q^2$.
******

Proof : $$\left| \epsilon_{2n+1} \right| =\epsilon_{2n+2} \cdot \left( \frac{|\epsilon_{2n+1}|}{|\epsilon_{2n}|^q} \right)^q
\rightarrow a^{q+1} \text{ if } \frac{|\epsilon_{2n+1}|}{|\epsilon_{2n}|^q} \rightarrow a.$$So, multiplicative constants aside, given an order-1.5 numerical procedure where each step runs twice as fast as a quadratic procedure, the former is probably preferable. It can achieve order-2.25 in equivalent time simply by taking twice as many steps. Of course, our decision could be different if we take into account to numerical stability of the methods, or whether one method is guaranteed to converge starting from a wider range of starting points.

******
Example 4.3   The iteration $$x_{n+1}=\frac{x_n}{2}+\frac{1}{x_n},$$starting with $x_0 = 1$, converges quadratically to $\sqrt{2}$.
******

