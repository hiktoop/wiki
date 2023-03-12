# pepresenting code

see post-order traversal:

![Evaluating the tree from the bottom up.](https://craftinginterpreters.com/image/representing-code/tree-evaluate.png)

- scanner: characters --> tokens (*regular language*.)

- **context-free grammar** (**CFG**)

## Rules for grammars

```c
breakfast  → protein "with" breakfast "on the side" ;
breakfast  → protein ;
breakfast  → bread ;

protein    → crispiness "crispy" "bacon" ;
protein    → "sausage" ;
protein    → cooked "eggs" ;

crispiness → "really" ;
crispiness → "really" crispiness ;

cooked     → "scrambled" ;
cooked     → "poached" ;
cooked     → "fried" ;

bread      → "toast" ;
bread      → "biscuits" ;
bread      → "English muffin" ;
```

!["Playing" the grammar to generate a string.](https://craftinginterpreters.com/image/representing-code/breakfast.png)

enhance the notation:

```c
breakfast → protein ( "with" breakfast "on the side" )?
          | bread ;

protein   → "really"+ "crispy" "bacon"
          | "sausage"
          | ( "scrambled" | "poached" | "fried" ) "eggs" ;

bread     → "toast" | "biscuits" | "English muffin" ;
```

## A Grammar for Lox expressions

- **Literals.** Numbers, strings, Booleans, and `nil`.

- **Unary expressions.** A prefix `!` to perform a logical not, and `-` to
  negate a number.

- **Binary expressions.** The infix arithmetic (`+`, `-`, `*`, `/`) and logic
  operators (`==`, `!=`, `<`, `<=`, `>`, `>=`) we know and love.

- **Parentheses.** A pair of `(` and `)` wrapped around an expression.

```c
expression     → literal
               | unary
               | binary
               | grouping ;

literal        → NUMBER | STRING | "true" | "false" | "nil" ;
grouping       → "(" expression ")" ;
unary          → ( "-" | "!" ) expression ;
binary         → expression operator expression ;
operator       → "==" | "!=" | "<" | "<=" | ">" | ">="
               | "+"  | "-"  | "*" | "/" ;
```

## expression problem

![A table where rows are labeled with expression classes, and columns are function names.](https://craftinginterpreters.com/image/representing-code/table.png)

object-oriented:

![The table split into rows for each class.](https://craftinginterpreters.com/image/representing-code/rows.png)

functional

![The table split into columns for each function.](https://craftinginterpreters.com/image/representing-code/columns.png)

## visiter pattern

functional style within an OOP language
