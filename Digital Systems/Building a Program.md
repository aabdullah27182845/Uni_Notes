
To recap, we understood instructions and how they affected the CPU. We introduced assembly instructions, and observed an example of a small subroutine.

![[Pasted image 20240124085406.png]]

Here is the computer of the BBC micro-bit chip, and we will mostly be performing our practical's on this. 

The micro-vit behaves like a USB storage device when connected to a computer. To upload a program:
- You drag a file, containing the program, to the storage device.
- 2nd processor receives the file over USB.
- The chip doesn't store our program, it will look as if the file disappeared just after it was copied. 
- What actually happens under-the-hood is that the 2nd processor's program control pins on the `nordic` chip and transmits the program.
- The chip then goes into a mode that allows programming the flash, and the chip stores the program into Flash memory.


###### Memory Layout

Every byte of memory is given a 32-bit address. Different types of memory are just given addresses.
- The peripherals are given memory locations too.
- Single address space makes it easy to access all of the memory locations.


Our starting point for building and running the program starts as the following:

```
	.syntax unified     @ Use modern 'unified' syntax
	.global fun         @ Allow calling func from main
	.text               @ Text segment -- goes into ROM

	.thumb_func
func:                   @ Entry point for function func
@ ------------
@ Two parameters are in registers r0 and r1
	adds r0, r0, r1     @ One crucial instruction

@ Result is now in register r0
@ ------------
	bx lr               @ Return to the caller
```

The subroutine here performs the following function : it takes two values in the registers r0 and r1 are added and then stored in r0. This is done by the `bx lr` instructions. The annotations are helpful in knowing what the program is doing.

To call a subroutine inside a subroutine, we need to consider how to properly store the returned value in the link register. This will be done later.

