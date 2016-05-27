title: Haskell-2
date: 2015-09-25 19:32:14
category: Haskell
tags: [Haskell]
---
# Case Expressions and Pattern Matching
`As-patterns`
语法糖
``` haskell
f s@(x:xs) = x:s -- where s means (x:xs)
```

`Wild-cards 通配符`
`head (x: _) = x`, actually we don't care where _ is, but in fact, we all know _ must a list, for `:` operator has list as its second argument.
`tail (_: x) = x`

<!-- more -->

## Pattern-Matching Semantics
匹配时严格按照由左到右，由上而下的顺序进行
匹配一般有三种结果 *fail, succedd, diverge*,比如`[1, 2]`和`[2, bot]`进行匹配，则是匹配失败，而`[2, 1]`和`[bot, 2]`进行匹配，则是diverge

`Guard`
``` haskell
sign x | x > 0   = 1
       | x == 0  = 0
       | x < 0   = -1
```
其中对于x的条件选择称为`Guard`

Guard的顺序不同可能会引起逻辑顺序不同
``` haskell
f x | x > 0 = 1
    | x > -1 = 2
    | x <= -1 = 3
f2 x | x > -1 = 2
     | x > 0 = 1
     | x <= -1 = 3
-- f 1 结果为 1
-- f2 1 结果为 2
```
困难一点的，对于$\perp$
``` haskell
bot = bot

take 0 _ = []
take _ [] = []
take n (x: xs) = x : take (n-1) xs

take2 _ [] = []
take2 0 _ = []
take2 n (x: xs) = x: take2 (n-1) xs

-- take 0 bot = [], bot 匹配上 _， 不需要求值
-- take2 0 bot = $\prep$ 0先匹配上_， bot匹配上[]则发生diverge
-- take bot [] = $\prep$ bot 匹配上0， 发生diverge
-- take2 bot [] = [], bot匹配上_, [] 匹配上[]
```

## Case Expressions
``` haskell
take2 m ys = case (m, ys) of
            (0, _) -> []
            (_, []) -> []
            (n, x:xs) -> x: take2 (n-1) xs
```

`if e1 then e2 else e3`也可以看做`case e1 of True -> e2 False -> e3`

## Lazy Patterns 看上去有点吊
Lazy Patterns are *irrefutable*: matching a value *v* against *~pat* always succeeds, regardless of *pat*.简单点说就是你参数进来的时候我不看你正不正确，但是如果在等号右边被用到了，那么此时才会去进行match操作，成功或者$\perp$。
举个例子
``` haskell
reqs = client init_client resps
resps = server reqs
client init_client (resp: resps) = init_client: client (next resp) resps
server (req: reqs) = process req: server reqs
init_client = 1
next resp = resp * 2
process req = req + 10
```
这个程序的意思是，client先发出初始request, `init_client`，然后server接受request, process它返回response, 接着client使用next对resp进行处理，并发出下一条request，如此循环
从逻辑上看，对于`resps`，在`client init_client (resp: resps)`的时候，`server (req: reqs)`并没有知晓，所以`resp:resps`究竟能否不为空也并不知晓，因此这样的程序存在过早匹配，就是说我们可以将`resp:resps`先缓一缓在进行匹配

当然传统的改进是这样
``` haskell
reqs = client init_client resps
resps = server reqs
client init_client resps = init_client: client (next (head resps)) (tail resps)
server (req: reqs) = process req: server reqs
init_client = 1
next resp = resp * 2
process req = req + 10
```
这样改进，程序肯定能够运行，并且语意上也能懂，就是讲resps只要是个list就肯定能匹配上，至于运行会不会出错，那不关我的事了

lazy pattern的改进是这样的
``` haskell
reqs = client init_client resps
resps = server reqs
client init_client ~(resp: resps) = init_client: client (next resp) resps
server (req: reqs) = process req: server reqs
init_client = 1
next resp = resp * 2
process req = req + 10
```
先对`resp: resps`的非空list的匹配先缓一缓，日后如果匹配上就好，匹配不上就直接为$\perp$值

这个有点绕，需要好好理解一下。

另一个例子是Fibonacci数列。
``` haskell
fib@(1: tfib) = 1: 1: [a+b | (a, b) <- zip fib tfib]
```
$fib$ is $[1, 1, 2, 3, 5, 8, 13, \ldots]$
$tfib$ is $[1, 2, 3, 5, 8, 13, \ldots]$
$[a+b | (a, b) <- zip fib tfib]$ is $[2, 3, 5, 8, 13, \ldots]$
$1: 1: [a+b|(a, b) <- zip fib tfib]$ is $[1, 1, 2, 3, 5, 8, 13, \ldots]$ 正好就是$fib$
正确性可以得到验证，但是至于它是怎么计算出来的，转换成什么样的汇编就不清楚了
从另一个角度来看，这种写法的好处是结尾少一个tail函数。这样的写法叫做**pattern binding**.另外，按照原先的看法是在fib还不知道是什么样子的情况下，莫名地匹配上$1: tfib$的格式，理论上是不会有输出的，但事实上**pattern binding**会自动在fib前面加上`~`。

## Lexical Scoping and Nested Forms
``` haskell
-- let 用法
f x = let y = x * x
    in y
```
``` haskell
-- where 用法
f x y | y > z = 1
      | y == z = 2
      | y < z = 3
      where z = x * x
```

函数在`where`绑定中定义的名字只对本函数可见。`where`绑定不会在多个模式中共享。`where`绑定可以使用模式匹配。
``` haskell
initials :: String -> String -> String
initials firstname lastname = [f] ++ "." ++ [l] ++ "."
  where (f:_) = firstname
        (l:_) = lastname
```

## 有$的函数调用
$的优先级最低并且是右结合的，所以可以减少括号的写作
`sum (map sqrt[1..130])` => `sum $ map sqrt [1..130]`