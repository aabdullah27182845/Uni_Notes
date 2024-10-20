
A compiler is a computer program that translate a high level program into a semantically equivalent program into a specific target language (most likely a lower level program).

Levels referenced here are typically relating to how close the program is semantically to being understood by humans, or being understood by machines.


### Examples

Here is a model that you can use to understand what compilation is and how it occurs in relation to the source language and the target language.

![[Pasted image 20241019190213.png]]
As you can see from this diagram, this is the **Clang Compiler**, written in C++, which compiles C code into ARM64 assembly language.

---

A compiler need not have a target language be something machine readable. For example:
![[Pasted image 20241019190355.png]]
This is the **Scalac** compiler, written in Scala, which compiles Scala code into JVM Bytecode, which isn't a machine interpretable language.   