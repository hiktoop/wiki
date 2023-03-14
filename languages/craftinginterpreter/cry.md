## Token:

- type 类型

- lexeme 源文本字符串

- literal 值(源文本的存储)

- line 行号



## Expr:

- 声明抽象接口visitXXXExpr(expr)



每个子类都有accept函数：以自己作为参数调用visit当前表达式

accept(visitor) : visitor.visitorXXXExpr(this)



- Assigin:
  
  - Token name
  
  - Expr value

- Unary:
  
  - Token operator
  
  - Expr right

- Binary:
  
  - Expr left
  
  - Token operator
  
  - Expr right

- Call:
  
  - Epxr callee
  
  - Token paren
  
  - List<Expr> arguments

- Get:
  
  - Expr object
  
  - Token name

- Grouping:
  
  - Expr expression

- Literal:
  
  - Object value

- Logical:
  
  - Epxr left
  
  - Token operator
  
  - Expr right

- Set:
  
  - Expr object
  
  - Token name
  
  - Expr value

- Super
  
  - Token keyword
  
  - Token method

- This:
  
  - Token keyword

- Variable:
  
  - Token name





## Stmt

- visit接口同上



- Block:
  
  - List<Stmt> statements

- Class:
  
  - Token name
  
  - Expr.Variable superclass
  
  - List<Stmt.Function> methods

- Expression:
  
  - Expr expression

- Function:
  
  - Token name
  
  - List<Token> params
  
  - List<Stmt> body

- If:
  
  - Expr condition
  
  - Stmt thenBranch
  
  - Stmt elseBranch

- Print:
  
  - Expr expression

- Return:
  
  - Token keyword
  
  - Expr value

- Var:
  
  - Token name
  
  - Expr initializer

- While:
  
  - Expr condition
  
  - Stmt body


