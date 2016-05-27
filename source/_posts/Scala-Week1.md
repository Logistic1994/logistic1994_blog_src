title: Scala Week1
category: scala
tags:
  - scala
date: 2016-04-05 13:36:22
---
#### [Scala][1]
集面向对象和函数式编程于一体的语言。

#### [Martin_Odersky][2]
Scala语言的实现者。

#### [Functional Programming Principles in Scala][3]
Coursera上的相关课程。

<!-- more -->

### Evaluation Strategies and Termination
Evaluation策略有两种，Call-by-value按值访问，Call-by-name按名称访问。
Scala默认按照CBV的方式，如果要按CBN的方式，则需要加上`=>`标志
``` scala
def add(x:Int, y:=>Int) = 1
// 其中x按值访问，y按名称访问
```
如果用CBV方式能够终结(terminate)的程序，那么按照CBN的方式肯定也能终结；反之不一定。

### Conditionals and Value Definitions
`val`和`def`的区别是，`val`在定义的过程将立即进行求值，`def`则会按名访问
比如
``` scala
def loop: Boolean = loop
def x = loop // 成功的定义
val x = loop // 错误的定义，将会进行一次non-terminated的求值过程
```

### Tail Recursion
If a function calls itself as its last action, the function's stack frame can be reused. This is called **tail recursion**


  [1]: https://zh.wikipedia.org/wiki/Scala "Scala维基百科"
  [2]: https://en.wikipedia.org/wiki/Martin_Odersky "Martin_Odersky"
  [3]: https://class.coursera.org/progfun-005 "Functional Programming Principles in Scala"