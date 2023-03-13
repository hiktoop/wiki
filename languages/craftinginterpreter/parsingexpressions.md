# parsing expressions

## grammer & precedence

| Name       | Operators         | Associates |
| ---------- | ----------------- | ---------- |
| Equality   | `==` `!=`         | Left       |
| Comparison | `>` `>=` `<` `<=` | Left       |
| Term       | `-` `+`           | Left       |
| Factor     | `/` `*`           | Left       |
| Unary      | `!` `-`           | Right      |

```c
expression     → equality ;
equality       → comparison ( ( "!=" | "==" ) comparison )* ;
comparison     → term ( ( ">" | ">=" | "<" | "<=" ) term )* ;
term           → factor ( ( "-" | "+" ) factor )* ;
factor         → unary ( ( "/" | "*" ) unary )* ;
unary          → ( "!" | "-" ) unary
               | primary ;
call           → primary ( "(" arguments? ")" )* ;
arguments      → expression ( "," expression )* ;
primary        → NUMBER | STRING | "true" | "false" | "nil"
               | "(" expression ")" ;
```

![Top-down grammar rules in order of increasing precedence.](https://craftinginterpreters.com/image/parsing-expressions/direction.png)

## parser's jobs

A parser really has two jobs:

1. Given a valid sequence of tokens, produce a corresponding syntax tree.

2. Given an *invalid* sequence of tokens, detect any errors and tell the
   user about their mistakes.

A parser must:

- **Detect and report the error.**

- **Avoid crashing or hanging.** 

and some thing also need for real parser:

- **Be fast.**

- **Report as many distinct errors as there are.**

- **Minimize *cascaded* errors.**

**error recovery** : responds to an error and keeps going to look for later errors.
