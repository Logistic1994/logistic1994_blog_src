title: Scala Week2
category: scala
tags:
  - scala
date: 2016-04-05 13:52:01
---

### Scala Syntax Summary
Below, we give the context-free syntax in Extended Backus-Naurform(EBNF,扩展巴科斯范式), where
    `I` denotes an alternative
    `[...]` an option (0 or 1)
    `{...}` a repetition (0 or more)

#### Types
```
Type            = SimpleType | FunctionType
FunctionType    = SimpleType '=>' Type | '(' [Types] ')' '=>' Type
SimpleType      = Ident
Types           = Type {',' Type}
```
每种类型可以是以下几种：
1. A numeric type
2. A boolean type
3. String type
4. A function type

<!-- more -->

#### Expressions
```
Expr            = InfixExpr | FunctionExpr | if '(' Expr ')' Expr else Expr
InfixExpr       = PrefixExpr | InfixExpr Operator InfixExpr
Operator        = ident
PrefixExpr      = ['+' | '-' | '!' | '~'] SimpleExpr
SimpleExpr      = ident | literal | SimpleExpr '.' ident | Block
FunctionExpr    = Bindings '=>' Expr
Bindings        = ident [':' SimpleType] | '(' [Binding {',' Binding}] ')'
Binging         = ident [':' Type]
Block           = '{' {Def ';'} Expr '}'
```

#### Definitions
```
Def             = FunDef | ValDef
FunDef          = def ident {'(' [Parameters] ')'} [':' Type] '=' Expr
ValDef          = val ident [':' Type] '=' Expr
Parameter       = ident ':' ['=>'] Type
Parameters      = Parameter {',' Parameter}
```

### More Fun With Rationals
#### Preconditions
```
class Rational(x: Int, y: Int) {
    require(y>0, "denominator must be positive")
    ...
}
```
#### Assertions
Preconditions和Assertions的区别在于, Assert会抛出AssertionError错误，而Precondition会抛出IllgalArgumentException异常

### Classes and Substitutions
The expression $new C(v_1,\dots,v_m).f(w_1,\dots,w_n)$ is rewritten to $[w_1/y_1,\dots,w_n/y_n][v_1/x_1,\dots,v_m/x_m][new C(v_1,\dots,v_m)/this]b$
1. 替换函数f的参数
2. 替换new C的参数
3. 替换f的owner为this

### Evaluation and Operation
二元运算中，所有的中缀表达式都可以进行简写，比如`a.max(b) <=> a max b`
一元运算，比如取负，则必须强调为`prefix operator`：`def unary_- : `
