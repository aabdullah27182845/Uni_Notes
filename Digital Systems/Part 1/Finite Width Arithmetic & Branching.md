
In these notes, we will precisely define arithmetic. As you have seen earlier, we have not done this. Registers have limited width, and can only represent $2^{32}$ different values. Regular arithmetic is specified on a larger set of numbers.

So far, we have implicitly assumed that operations work on unsigned integers, i.e. numbers from 0 to $2^{31}-1$. Let's precisely define how numbers are represented, as well as how arithmetic operations work.

We will first begin by illustrating this with 8-bit binary numbers. We aim to answer the following questions:

- What happens when we produce a result larger than the max?
- How do we represent negative numbers?
- How can we implement subtraction?
- How do branching operations actually work?

So, let's be precise about how bitstrings represent numbers: $$\text{bin}:\{0,1\}\to\mathbb{N}$$$$\begin{aligned}
\text{bin}(a)&=\text{bin}(a_7a_6a_5a_4a_3a_2a_1a_0) \\
   &= \sum_{i=0}^{n-1}2^ia_i
\end{aligned}$$Can prove that $$0\leq \text{bin}(a)\leq 2^n-1$$$a$ in this case is a bit-string that is being turned into a number.


###### Representing Addition

Representing the `add` operation on bitstrings as $\oplus$, we want: $$\text{bin}(a\oplus b)=\text{bin}(a)+\text{bin}(b)$$Unfortunately, we cannot find such an operation that satisfies this. Since, 254+3 = 257, we cannot represent 257 with 8 bits.

We need to change our definition slightly. Here is the operation: $$\text{bin}(a\oplus b) \equiv (\text{bin}(a) + \text{bin}(b)) \mod 2^n$$This gives us the same result, but our result "wraps" around to zero if $\geq 2^n$. This can be implemented as normal binary addition, but ignoring bits $\geq n$.

What can go wrong with this definition? Fortunately, the ARM chip does provide us some tool to deal with this. In this case, the status bits tell us when we deviate from normal arithmetic.

The status bits N, Z, C, V are in `psr`. The instruction `adds` perform "add"ition, and sets the 's'tatus bits. In this case, the C status bit for carry is used.


###### Representing Negative Numbers

We can introduce a different mapping from binary representations to numbers, to represent negative numbers. Here's the 2's complement representation $$\begin{aligned}
\text{twoc} &: \{0,1\}^n\to\mathbb{N} \\
twoc(a) &= \sum_{i=0}^{n-2}2^ia_i-a_{n-1}2^{n-1}
\end{aligned}$$It's easy to see that it can represent negative numbers (the most significant bit can be 1 and the whole number is negative), but it has many other nice properties. All modern computers use twoc for representing negative numbers.

Here's an example of a two's complement number. In the last notes, we encountered a jump instruction with an offset of `0xFA`. We decoded that into binary to obtain `0xFA = 0b1111 1010`, which in two's complement is `twoc(1111 1010) = -128 + 122 = -6`.

Two's complement has the following bounds $$-2^{n-1} \leq \text{twoc}_n(a)\leq2^{n-2}-1$$Twoc and bin are equal, or they differ by $2^n$, $$\text{twoc}(a)\equiv\text{bin}(a)\mod2^n$$This means that addition is the same for both signed and unsigned numbers.


###### Representing subtraction

How can be implement subtraction? Well, since $b-a=b+(-a)$, let's first consider how to negate a number.

Take $\overline{a}$ to be the binary complement of $a$, i.e. $\overline{a}_i=1-a_i$. Therefore: $$\begin{aligned}
\text{twoc}(\overline{a})&=\sum_{i=0}^{n-2}(1-a_i)2^i-(1-a_{n-1})2^{i-1} \\
   &= -\text{twoc}(a) + \sum_{i=0}^{n-2}2^i-2^{i-1} \\
   &= -\text{twoc}(a) - 1
\end{aligned}$$ So, for all bit strings a, we have $$\text{twoc}(\overline{a}\oplus1)=-\text{twoc}(a)  \mod 2^n$$
Here are a few things to note: The overflow of the operation makes the negation of 0 correct. The negation of `-128 = 0b1000 0000`, remains itself, which is correct$\mod 2^n$.

Let's use this to create a subtraction operation.

Since $b-a=b+(-a)$, we can define $b\ominus a=b\oplus \overline{a} \oplus 1$. And therefore, we have defined the following: $$\text{twoc}(b\ominus \overline{a})=\text{twoc}(b)-\text{twoc}(a) \mod 2^n$$Overflows can still go wrong since $\text{twoc}(127 \oplus 1) = -128$. Overflow is impossible if the signs are opposite. If the numbers have the same signs, but the result has an opposite sign, then a signed overflow has occurred. 

We can also use the status bit, but in this case, the V bit is used when signed operations give an overflow bit.


###### Branching & Comparisons

If/else and loops all require branching if a particular condition is true. ARM provides many branching instructions to make this convenient. The condition of a branch is always a property of arithmetic instructions.

Properties of arithmetic outcomes are stored in `psr` status bits. In addition to checks if arithmetic goes wrong, `psr` contains bits helpful for branching.

So, here are the complete status bit definition:

- N: Negative bit. If the result is negative, i.e. MSB is one.
- Z: Zero bit. If the result is zero, i.e. all bits in arithmetic result are zero.
- C: Carry bit. Set if the unsigned addition overflowed. I.e. a carry was produced which could not be stored in the register.
- V: Overflow bit. Set if signed addition overflowed.

 Branching instructions interpret the `psr` bits as though a subtraction was performed. Compare instruction `cmp` performs subtraction, sets `psr` status bits, and does not write result to register.

Here are some examples of equality branches:

- `beq`: Branch if equal. Branches if `Z`
- `bne`: Branch is not equal. Branches if `!Z`

Here are some inequality branches. `blt`: branch if less than (signed). Can we figure out which condition we need to hold the status bits to?

If $\text{twoc}(a\ominus b) < 0$ then either we really have the inequality, or the subtraction overflowed. For this, we need `N` and `¬V`.

If $\text{twoc}(a\ominus b) \geq 0$ then either we really have the inequality, or the subtraction overflowed. For this, we will need `¬N` and `V`.

So overall, we need (`N` and `¬V`) or (`¬N` and `V`), which we can summarise as just needing `N != V`.


Here is another inequality branch: `blo`, branch if lower. Can we figure out the condition? The same operation performs subtraction on unsigned numbers modulo $2^n$. 

We can do the same deduction and find out that we get an underflow if the subtracting number is larger than the subtracted number. All we need in this case is `¬C`.

