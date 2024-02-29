
Say we want to transfer a file between computers. How can we do so? 

We have electrical signals at our disposal. We also want to limit the number of pins in order to make the microprocessor as efficient as possible when it comes to resources. And, we want to communicate bits sequentially over a single wire.

We can actually delve into the serial port ourselves and see what the micro-bit is sending us. 

![[Oscilloscope - Serial.png]]

The idle time is high, and the start but indicates data transmission. The data is read at regular time intervals, which is something we will come back to.

The stop bit is a high bit to show the end of serialisation. The configured baud rate for the micro-bit chip is at 9600 symbols/s. This could also be implemented with the GPIO as spoken about in the previous lecture.


###### UART Interface

On the Nordic chip, hardware handles most conversations, with a convenient memory-mapped interface. 

The Universal Asynchronous Receive Transmit interface (UART) on the Nordic chip provides the following:

- Full-duplex operation: Separate receive/transmit wires, for simultaneous receive/transmit.
- It's universal so it can operate at different speeds.
- It's asynchronous which means that the communicating computers need not be at the same clock speed.

Here is a basic UART driver setup:

```c
void serial_init(void) {
	UART_ENABLE = 0;
	// Select 9600 Baud, relative to 16 MHz crystal
	UART_BAUDRATE = UART_BAUD_9600;
	UART_CONFIG = UART_CONFIG_8N1; // 8N1 (8 data,1 stop)
	UART_PSELTXD = USB_TX; // choose pins
	UART_PSELRXD = USB_RX;
	UART_ENABLE = UART_Enabled;
	UART_RXDRDY = 0; UART_TXDRDY = 0;
	UART_STARTTX = 1; UART_STARTRX = 1;
	txinit = 1;
}
```

And here is the transmission initialisation from the receiving computer:

```c
void serial_putc(char ch) {
	while (! UART_TXDRDY) { /* idle */ } UART_TXDRDY = 0;
	UART_TXD = ch;
}
```

The problem here is, the micro-bit chip runs way faster than the transmission speed of the serial connection, so the program has to poll in order to transmit data properly. Fun fact: the function `printf()` is a wrapper around this.

Here is the print out sequence of primes via the serial:

```c
start_timer();
while (count < 500) {
	if (prime(n)) {
		count++;
		printf(“prime(%d) = %d\r\n”, count, n);
	}
	n++;
}
stop_timer();
```

We can confirm this hunch of ours (that the micro-bit chip ends up polling) by looking at the signals from the pins. We do this with the use of the Logic Analyser, which works like an oscilloscope, but it only stores the digital state. There are more channels and it can record for a longer time.

Here is what the visualised channels look like. Early on, most time is spent waiting for UART to send.

![[UART Logic Analyser.png]]

Later on, longer periods are spent calculating

![[Calculating Logic Analyser.png]]

This begs the question however. What is the point of having hardware to handle receive transmission if the CPU will just wait around until it's done anyway?

This is a question we will aim to answer in the upcoming lectures.