
We begin by asking the question: "How should we actually build computers?"

And to answer this question, we might ask what a computer actually is. We can have a rudimentary definition:

**Computer**: A device that produces the outcome of a computation, given a problem specification.

A computation may be some procedure following well-defined steps such as calculations or resolution of any algebraic expression. This means that a computer must be built, which means it it made out of matter, and therefore governed by physics.

Physics is a mathematical description oh how a description of a system behaves through time. An example is Newton's Law of motion, which is defined as $$m_i\frac{d^2x_i}{dt^2}=\sum_kF_k$$which describes a system's movement over time.

What does this entail then? Essentially, If calculations describe Physics, then Physics can do calculations. We will begin from this step.


#### [[History of Computers]]

We can use shapes for computation. A simple example of this is using the physical property of length by a number. We can put objects end-to-end adds their lengths. Rulers were often used with logarithmic scales in order to multiply numbers.

The first computers - around 205 BC - were built using gears in order to compute digits. They were discovered in an ancient shipwreck off Antikythera coast. It is assumed to have been used to calculate astronomical events, specifically orbits.

There is another way of computing integration with electricity. By using the following circuit, the total voltage inputted into this circuit is used to compute the integral of a given expression. Here is the circuit:

![[Pasted image 20240117133332.png]]

And the corresponding algebraic expression that is solved by this system is $$V_0(t)=-\frac{1}{R_1C_F}\int_0^tV_{in}(\tau)d\tau$$and the integration naturally occurs over the change of the input voltage.

There are various other examples of physical devices that were used to calculate expressions, and ones which were highly sought after were computers that would work with digits. Digital computers try their best to remove the analogue nature of the physical laws.


So, after reviewing the history of computers, what have we learnt? Essentially, we desire to use a **General-Purpose Computer**. 
- We now know that physics allows us to build some machines that compute some things.
- Would it not be better to only have one machine, that can be configured to compute everything?

For this, we need to define a computation. We need to refer to Alan Turing's work in order to answer this, specifically a Turing machine. 

A Turing machine is a state machine with an "infinitely" long tape of memory. The choice of the state transitions is the program, and we are to argue that a well-defined computation is whatever can be encoded into a set of state transitions.

**Church-Turing Thesis:** There exists a well-defined method for computing something if and only if there exists a Turing Machine that produces the result.

The initial question regarding this thesis is that the thesis is circular in its definition. Using A to define B and B to define A hasn't led you to define any of them formally. However, the Church-Turing Thesis is an assertion that defines both of them simultaneously. This assertion is justified since "It is a good definition, because there is no way of defining a larger set of computations."


#### [[Turing Machine]]

We will take a closer look at the Turing Machine. Two components define the Turing Machine:
- The infinite set of tape of memory.
- The state machine (CPU) which has a state at each time.

The CPU sits at a location on the tape. The CPU can write a symbol in that location. It can move the tape by a single step. We can define the algorithm by which the CPU is governed by: $$\begin{aligned}tape(L_t)=lookup\_table_1(S_t,tape(L_t))\\S_{t+1}=lookup\_table_2(S_t,tape(L_t))\\L_{t+1}=lookup\_table_3(S_t,tape(L_t))\end{aligned}$$In which the state and lookup tables are implemented mechanically. Unfortunately, programming is a pain, since the lookup tables are selected manually.

Turing Machine technically can do any computation, but it isn't practical. So, how can we build a machine that can perform any computation as well as being practical?

From here on, formalities are dropped and we have to work in constraints and bounds in order to optimise both for completeness and efficiency. Usually though, there is a clean and good reason for doing things in a particular way.