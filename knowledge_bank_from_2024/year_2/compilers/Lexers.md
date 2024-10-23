**Formal Definition:** A lexer is a part of a compiler, usually run first that takes the human-written code as input, and generates a sequence of tokens as output as a stream. Here is a visual representation of what Lexers could do in a given context:

![[Pasted image 20241020011026.png]]

---

##### Motivation Behind Lexers

As of 2024, most compilers for most, if not all languages incorporate some kind of lexer that generates a **token stream** which will then be used later for the **parser** #TO-DO (add link to parser). Alongside that, it helps get rid of a class of symbols that are necessary for a human to understand, but is unnecessary for code compilation, e.g. white-space, line separators, comments, and etc.

Generally, the parser is required to build a syntax tree from the program, and so having a token stream to do is far more helpful than trying to generate that syntax tree from the input code itself.

Compilers usually requires loads of steps, meaning that introducing a lexer does modularise the process of compiling. However, there are certain compilers that don't require the use of a lexer, only because the source language that is being worked on is low-level enough.


#### Desiderata

This is just a guideline, but here is some of the key elements of a Lexer.

- Keywords such as *if*, or *while*, or *for* are assigned designated tokens.
- Operations such as +,\*,/,- are also assigned specific tokens too.
- Reasonably, due to the fact that a given source code could have a large amount of variables, these variables are all assigned arbitrary names under a similar type of token.

---

In code, Lexers are essentially DFA machines that accept or reject a sequence of strings. Taking this idea from models of computation, you can see how an expression that accepts a subset of all possible strings is in fact a DFA, specifically with how we have defined them.

It's perhaps easier to think of them first as NFA's, and from there, constructing the DFA through [[Subset Construction Rule]], we can see how these Lexers would then be programmed in.