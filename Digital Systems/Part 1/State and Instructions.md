In these notes, we aim to formally answer the following questions : 
- How do instructions alter the CPU state?
- How are instructions encoded electronically?
- How does the CPU decode machine code?
- How can we make parts of the program usable?

First, it will be wise for us to understand how operations are done on CPUs. Here is a figure to help
![[Pasted image 20240119091539.png]]

The controller fetches an instruction from memory, at location in the Program Counter (PC). The instruction is then decoded bitwise since this is a RISC processor. The instruction is then executed, which follows with the PC incrementing by 1, and repeating this whole process again. This is known as the Fetch-Decode-Execute cycle.


###### Implementation

All state is representation by an electrical voltage. Voltage is either "high" or "low". This voltage represents binary encoding of numbers. 

This is physically real, since if you were to open up a chip and attach LED circuit to the wires, these LEDs will light up according to the binary stored in the chip.

For most programming purposes, the state of the registers is the state that we manipulate with instructions. 

All registers are 32 bits "wide". Here are some classifications of such registers:
- `r0 - r7` : General purpose registers. Can be used for any value used in any calculations.
- `r0 - r3` : Handled differently during subroutine calls.
- `r8 - r12`: Can be used for any value, but the instruction set makes it hard to access them.
- `pc` : Program counter. Stores memory address of next instruction.
- `lr` : Link register. Stores address where current subroutine returns.
- `psr` : Processor Status Register. Different bits have very specific meanings. Some instructions write bits, some read bits.

Let's consider our first instruction: 

![[Pasted image 20240119092858.png]]

We are using hex to denote the bit-value since each hex digit corresponds to 4 digits. If you observe the figure above, you see a program that adds two values in registers together.

The status bits N, Z, C, V, are in `psr`. The instructions `adds` is does "add"ition, and sets the "s"tatus bits.
- N: Set if the result result is negative.
- Z: Set if the result is zero

###### Program Counter

There are multiple encodings for ARM instructions. Big chips can choose.
- Native ARM instructions (32 bits)
- "Thumb" instructions (16 bits)
- Thumb-2 extensions (mixture of 16 and 32 bits)


###### Assembly Language

Assembly language is the first level of abstraction up from machine code. It has very close correspondence between machine code 
- It is basically machine code in human-readable form, with tiny bits of "syntactic sugar".
- The program that translates from assembly to machine code is known as an Assembler.
- Given close correspondence, assemblers are simple programs, particularly when compared to compilers.

Consider the following instructions 
- **✅** adds r0, r1, #3
- **✅** adds r0, r0, #10
- ❌ adds r0, r1, #10

This seems out of the ordinary. We will understand why this occurs.

We may want to learn how to take machine code, and determine what assembly instruction it maps to. As an assembly level programmer, there isn't a need to know this, unless we want to study the nature of our programs.

![[Pasted image 20240119094356.png]]

Observing this carefully, you can notice that only the lower 8 registers can be accessed by most instructions. Allocation 3 bits to indicate the register only gives 8 options. Therefore, feeding it the input #10 is illegal.

Now, let's consider the next instruction given that we ran a legal instruction beforehand. 

The `pc` points to 194 in memory, and contains the instructions `0x4770 = 0b01000111 0 1110 000`, which decodes to an instruction we can refer to in the rainbow chart. In this case, we have a `branch` instruction that we are referring to. 

Essentially, we are free to use ARM documentation in order to decode what assembly instruction we are running from our non-abstracted machine code. 

![[Pasted image 20240119100304.png]]

Notice how the program counter takes the value of the link register in order to branch to another subroutine of our program.


###### Subroutines

We can make parts of a program reusable by storing them at a known memory location. We then run the program
- Our instruction first should be to store the location of the next instruction in `lr`
- We then change the `pc` to the known subroutine location.
- We run multiple instructions in the subroutine, and then change the `pc` back to the value in `lr`.

We can enter a subroutine too. However this can get a bit tricky. When jumping into a subroutine, the registers may be in use by the caller. Subroutines needs to use registers as well, so we need to make sure data is not overwritten by our subroutine call.