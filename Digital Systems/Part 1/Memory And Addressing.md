
So, to recap, what have we still yet to answer? Here are some possible ideas we may be unclear about:

- How can we access memory?
- How can we place values in subroutine in memory?
- How can please values in memory that can be accessed in all subroutines?
- How can we handle variable amounts of data?


#### How can we access memory?

Instructions for storing and loading locations in memory: `str` and `ldr`. Remember that memory is accessed by providing an address. `ldr` and `str` have different ways of calculating ways of calculating the address. These are called addressing modes/ 

We can use the `ldr` and `str` instructions in two different syntaxes, to use the different addressing modes:

```
xxr r0, [r1, #20] @ Load/store r0 in r1 + 20
xxr r0, [r1, r2] @ Load/store r0 in r1 + r2
```

Typical encoding only works on the lower registers, only `r0 - r7` can be used. There is an alternative coding for the encodings shown earlier, they can also be written as `xxr r0, [sp, #20] ; xxr r0, [pc, #40]`. There are different constant ranges that are allowed.

Addressed must be aligned, based on their size, so for example, a 4-byte quantity would require only `addr % 4 == 0`. In native ARM code, we also allow 

```
ldr r0, [r1, r2, LSL #x]      @ r1 + r2 << x
```

to make this easy to enforce. In Thumb, we need more instructions.


#### How can we store local variables?

Local variables are allocated on the stack, if you recall from before. Instead of restoring `r4, r5`, let's keep variables in memory.

```c++
int fac(int n) {
	int k = n, f = 1;//<==
	while (k != 0) {
		f = mult(f, k);
		k = k-1;
	}
	return f;
}
```

where the program will look like the following in assembly

```
fac:
push {lr} @ Save return addr
sub sp, sp, #8 @ Allocate 8 bytes
str r0, [sp,#4] @ Save n as k
movs r0, #1 @ Set f to 1
str r0, [sp,#0]
...
```

So, if you look carefully in the C++ code, the subroutine contract is to return the value in `r0`, and also leave the `sp` unchanged, so that the stack is in the same state as we found it).


###### Global Variables

Global variables are stored at a fixed absolute location. Addresses are 32-bit quantities. How can we load the address into a register? 

We may decide to use `movs rx, #255` to load 8 bits into a register. Or, we may use a 32-bit constant in memory, and load that.

In practice though, it is annoying to manually manage addresses and offsets. `ldr` using `pc` as the base is again peculiar. `pc` is always 4 ahead of instructions (try remembering why?) currently being executed, which makes calculating offsets error-prone. Addresses need to aligned so `addr % 4 == 0`. Thumb instructions are 16-bit, so `pc` can be un-aligned.

`ldr r2, [pc, #d]` rounds `pc` down to a multiple of 4 before adding offset.


###### Relying on the Assembler

Can we get the assembler to

- select an address in RAM for the global variable?
- store the address in ROM?
- Load the address from ROM without us needing to worry about the address of the address?

We can answer these questions by visiting an example. Here is some code in C as well as its assembly counterpart:

```c
int count = 0;
void increment(int n) {
	count = count + n;
}
```

```
ldr r1, =count       @ Neat shorthand for ldr r2, [pc, #d]!
ldr r2, [r1, #0]     @ r2: store value of count addr
adds r0, r2, r0      @ r0: store n
str r0, [r1, #0]
```

Now, we must tell the assembler what `count` refers to.

```
	.bss           @ Place the following in RAM
	.balign 4      @ Align to a multiple of 4 bytes
count:
	.word 0        @ Allocate a 4-byte word of memory
```

And here is the assembler input:

```
	.text        @ I.e. ROM
	.thumb_func
func:
	ldr r1, =count
	ldr r2, [r1]
	adds r0, r2, r0
	str r0, [r1]
	bx lr
	.pool        @ Const pool


	.bss         @ I.e. RAM
	.align 4
count:
	.word 0
```


#### How can we store (in memory) arrays?

Arrays are essentially just a collection of contiguous memory locations. We can allocate arrays of fixed size in memory with the use of `(.bss)`, or of variable size on stack.

```c
static int account[10];
int deposit(int i, int a) {
	int x = account[i] + a;
	account[i] = x;
	return x;
}
```

This is how it would look when programmed in assembly:

```
deposit:
	ldr r3, =account     @ r3 is base of array
	lsls r2, r0, #2      @ r2 is 4*index
	ldr r0, [r3, r2]     @ Fetch balance
	adds r0, r0, r1      @ Add deposit
	str r0, [r3, r2]     @ Store back in array
	bx lr

	.bss
	.balign 4
account:
	.space 40            @ 40 bytes for 10 ints
```

