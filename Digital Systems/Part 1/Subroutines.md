
Previously, we wrote a single subroutine, but with limitations. It couldn't call any other subroutines, and it could only used `r0 - r3`, while leaving `r4` and above unchanged.

These kinds of subroutines are very useful, because they are very efficient.

In these notes, we will aim to answer the following questions:

- How can we create subroutines which can call others?
- How can we use `r4` and above?
- How can we accept more than 4 arguments?
- What if we need more local memory than the registers?

Essentially, subroutines should be black-boxes. They should work no matter who calls them. Instructions inside are fixed. We need conventions that allow the same instructions to perform the same operation, regardless of the state of the calling subroutines.

However, we cannot use absolute memory addresses since some subroutines may be calling themselves implicitly.

Here, the useful property of stacks come in to play. We can let subroutines use memory relative to the stack pointer (register `sp`). Memory below the stack is used by calling subroutines, and should remain untouched. Memory above the stack is free to be used by the subroutine.

This is the only possible solution, but is the one chosen by design in ARM. Special instruction are created to facilitate using the stack.

Here is the following stack:

![[Pasted image 20240206172828.png]]

Each time a subroutine is called, `sp` points to a usable region of memory, that won't interfere with other subroutines. When a subroutine returns, the caller finds its stack frame as it left it. The convention here is that the stack starts at the top of address space, and grows downwards. So, the top of the stack actually has the lower addresses.


###### Subroutine Contract

Calling function:

- Places first arguments 1 to 4 in `r0 - r3`.
- The rest of the arguments are placed on the stack.

Called function:

- Leave values of `r4` and up unchanged on return.
- Leave stack pointer `sp` unchanged on return.
- Leave stack below current location unchanged.
- Leave return value in `r0`.

Subroutine can use the stack in whatever way it wants, assuming there is space. However, ARM provides instructions for:

![[Pasted image 20240206173256.png]]

ARM provides two assembly instructions to make restoring stare following the subroutines easy:

- `push {REGISTER_LIST}`: Place registers in list on stack in order, and decrease `sp` accordingly.
- `pop {REGISTER_LIST}`: Take values from stack, and place into the registers in list order, and increase `sp` accordingly.

Here is the assembly code for it:

```assembly
push {r4, r5, lr}  @ Save registers
@ Use r4 and r5, call other routines
pop {r4, r5, pc} @ Restore and return
```

`push` and `pop` can store or restore lower registers. `push` can store `lr` as well, and `pop` can restore to `pc`.

Previously, we saw we could return from a subroutine using `bx lr`. Using `pop` to restore to `pc` gives the same effect, but without first restoring to `lr`.

So, putting it all together, we can do the following. Take the factorial function:

```c
int fac(int n) {
	int k = n, f = 1;
	while (k != 0) {
		f = mult(f, k);
		k = k - 1;
	}
	return f;
}
```

Subroutine calls another subroutine, so we will use what we discuss.

As usual, let's start by converting code to assembly, and not worrying about efficiency. Let's assign `k` to `r4`, and `f` to `r5`. And then, we shall use push.

```assembly
push {r4, r5, lr}
movs r4, r0       @ Initialise k to n
movs r5, #1       @ Set f to 1
```

And here is the factorial loop:

```assembly
fac:
	push {r4, r5, lr}
	movs r4, r0       @ Initialise k to n
	movs r5, #1       @ Set f to 1
again:
	cmp r4, #0        @ Is k = 0?
	beq finish        @ If so, finished
```

We can add the branching to this subroutine too:

```assembly
again:
	cmp r4, #0        @ Is k = 0?
	beq finish        @ If so, finished

	movs r0, r5       @ Set f to f * k movs r1, r4
	bl mult           @ branch link instruction!
	movs r5, r0

	subs r4, r4, #1   @ decrement k
	b again           @ and repeat
```

You may have spotted that `f` can live in `r0` constantly, since the return value of `mult()` will also be the first argument for the next call.

Optimising compliers can spot this.

