
We will begin by talking about the nature of the datasheets. If we want to do assembly programming, we will need to understand the effect of each instruction. Unfortunately, there is no way to know this, except to look this up.

Looking up the specification of machine code is a skill itself, and will be learned throughout the course. Let's recap from our previous lecture and revisit the micro-bit chip design.

![[Pasted image 20240124085406.png]]

The datasheet for this micro-bit chip is kindly provided by Mike Spivey from Oriel College: https://spivey.oriel.ox.ac.uk/digisys/The_BBC_micro:bit.

We will use an example to see how to use the datasheet in order to make programs. The example in this case is pushing a button. To do this, we will need to figure out what pin is physically connected to the button, and we will figure this out on the electronic manual of the micro-bit specification.

---

This lecture is good to revisit on if you ever want to get a guide to use Datasheets for the BBC micro-bit chip. Here is the following link for this lecture: https://ox.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=ff58cb68-83c3-4153-ba80-b0ff009ab974&query=digisys

---

Here's the second link that will be useful to us. This manual details the specification of the sending data through UART. Here is the link: https://spivey.oriel.ox.ac.uk/dswiki/images-digisys/3/34/NRF51822-ref-manual.pdf

If you were wondering how to do this in the exam, we only have constrained questions with the Rainbow Chart and the List of common instructions provided.


###### The ADDS instruction

There are various flavours of the adds instruction encoded in the BBC micro-bit chip. Some of these add immediate 3-bit integers only, some only add immediate 8-bit integers. 

What we need to do is to optimise the common case. This is a design principle, making the common case fast, while leaving uncommon case possible. **Amdahl's Law**: The maximum achievable speed-up of an optimisation is limited by how often the optimised part is used.


This lecture is more hands on, and the rest of the lecture from here is completely practical.