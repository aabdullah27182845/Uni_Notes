
**Formal Definition**: A regular expression specifies a set of strings over some alphabet $\Sigma$ (a language) such that the language contains:

- $\epsilon$ - the empty string
- $a$ - a literal character
- $E_1E_2$ - concatenation
- $E_1 \cup E_2$ - union
- $E_1^*$ closure. Refer to models of computation to find the definition.

If a regular expression is defined under those closures, then these are also regular expression:

- $[abc] = a \,|\, b \,|\, c$
- $[a - z] = a \,|\, b \,|\,...\,|\, z$.
- $E_1? = \epsilon \,|\, E_1$ (either $E_1$ is or isn't)
- $E_1+ = E_1E_1^*$ (at least one of the string)  
- `[^abc]` = every character in $\Sigma$ except these ones.
- $.$ = any character.

**Note:** Brackets are used to group terms, e.g. $b?(an)+s?$. 

##### Examples

Here is a regular expression for an identifier in Pascal: `[A-Za-z][A-Za-z0-9]*` 

Here is a regular expression for an integer constant for C: `-[0-9]+|0x[0-9a-f]+`


---

##### Applications of Regex in Lexers

This is a very Haskell-like way of defining these types, but it helps us understand more concretely how a compiler would go about producing tokens. Here is some code for this:

```ocaml
type Token =
	IDENT of string
  | NUMBER of int
  | IF | THEN | ELSE | ...
  | COMMA | ASSIGN | ...
  | ADDOP of op | MULOP of op | ...
  | BADTOK | EOF
```

Notice that the type `Token` is defined as many separate data constructors, all of which will have their own respective definitions, **given by a regular expression**.

###### Notes

- `BADTOK` is a constructor that refers to any badly referenced code, so spelling `while` as `wihle` will incur a creation of this constructor. Usually this will then be caught by the lexer and then returned as a compile-time error.
- For our cases, this will most likely be written in `lexer.mll` which specifies that association. 




