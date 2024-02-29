So, starting from our previous lecture notes, we have the following to recap. 

`ldr` and `str` deal in 32-bit values, the size of a register. But there are also

- `ldrb` and `strb` for 8-bit values.
- `ldrh` and `strh` for 16-bit values.
- `ldrsb` and `ldrsh` to load 8- or 16-bit values with sign extension.

This isn't part of our course this year, but it is good to mention them. There are such attacks called buffer overrun attacks - which exploit how programs that interact with the outside world may be exploited to run arbitrary code.

Now, to move on to our main objective of these notes. How can we control LEDs on the BBC `micro:bit`? 

Before we answer this question, we need to know a bit about how LEDs work, electronically.

![[LED Circuit.png]]

Here is the design. The resistance is around $222\;\Omega$, and the current is $\approx4.5mA$. The general purpose In/Out pins on chip act as voltage source.

We could connect one LED per GPIO pin, but the `nRF51822` has 31 GPIO pins. There is a better solution to this problem.


###### LED Multiplexing

![[LED Multiplexing.png]]

If you observe the diagram, you can see that A and B are connected to the anode. X, Y, and Z are connected to the cathode. Setting the cathode-controlling GPIO to high ends up switching off all the LEDs connected to it. LEDs are no longer independently controllable, and can still make arbitrary patterns by switching on/off quickly.

The BBC `micro:bit` has 25 multiplexed LEDs, in 5 physical rows and columns. They are electronically arranged in 3 groups of 9, and can display patterns by controlling each group in sequence:

- Row 1: 5, 6, 7, 9
- Row 2: 1, 2, 3, 4, 5
- Row 3: 1, 4, 5, 6, 7, 8, 9

This begs the following questions. How many LEDs can you control in total? And, how many LEDs can be independently controlled?

GPIO controls voltage as well as the fact that peripherals are memory mapped (controlled by writing to a memory location).

```
GPIO_DIR 0x50000514           Controls in/out direction
GPIO_OUT 0x50000504           High or low for each pin
```

For example:

```
ldr r0, =0x50000504
ldr r1, =0x5fb0
str r1, [r0]
```

or in C:

```c
#include ”hardware.h”
...
GPIO_OUT = 0x5fb0
```

LEDs are connected as 

```
	...|R3 R2 R1 C9|C8 C7 C6 C5|C4 C3 C2 C1|0000
bits 15----13 12-----------------------4
```

So, lighting LED 5 in row 2 is done by running `GPIO_OUT = 0x5fb0`.

This can also be represented in C, as done in the code snipped below:

```c
while (1) {
GPIO_OUT = 0x28f0;
delay(JIFFY);
GPIO_OUT = 0x5e00;
delay(JIFFY);
GPIO_OUT = 0x8060;
delay(JIFFY);
}
```

`JIFFY` in this case is an arbitrary wait time, but let's say its 5000 for 67 updates per second.

Here is the frame function as defined in C:

```c
static const unsigned heart[] = { 0x28f0, 0x5e00, 0x8060 }
/* frame -- show three rows n times */
void frame(const unsigned *img, int n) {
	while (n > 0) {
		for (int p = 0; p < 3; p++) {
			GPIO_OUT = img[p];
			delay(JIFFY);
		}
		n--;
	}
}

void delay(unsigned usec) {
	unsigned n = 2 * usec;
	while (n > 0) {
		nop(); nop(); nop();
		n--;
	}
}
```

Experiments shows that each iteration takes 8 cycles, which is half a microsecond at 16MHz. Different numbers are needed if the clock speed is faster, and that's why they are left only as variables.

We have been using `GPIO_OUT` so conveniently, except we don't understand how it's implemented. We aim to answer this question. So,  we want to make C write to a specific memory location. This is where pointers will come to help.

```c
(* (volatile unsigned *) 0x50000504) = ...

#define _REG(ty, addr) (* (ty volatile *) addr)
#define GPIO_OUT _REG(unsigned, 0x50000504)
```

