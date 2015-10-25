title: Haskell 1
date: 2015-09-24 16:53:00
category: Haskell
tags: [Learn, Note]
---
*From "A Gentle Introduction to Haskell 98"*
 
**学习一门函数式编程，以作为我大学的纪念。**

<!-- more -->
# Values, Types and Other Goodies
Evaluation of expressions(syntactic terms) to yield values.
Every value has an associated type.
Just as expressions denote values, type expression are syntactic terms that denote type values(or just types).
All Haskell values are **"first-class"**.
Haskell's static type system defines the formal relationship between types and values, which ensures *type safe*.

## Polymorphic types
`[Char]` can be derived from `[a]`，其中`a`的意思是任意一种型别。 但是对操作数组的函数来说，比如`length`，不应该考虑这个数组是装什么类型的数组。

list `[1, 2, 3]` is actually `1:(2:(3:[]))` => `1:2:3:[]`, for `:` is right associative.

**pattern matching**
``` haskell
length      :: [a] -> Integer -- 输入任意type的数组，输出一个整数
length []   = 0
length (x: xs)  = 1 + length xs
```
其中，匹配empty list以及non-empty list的过程叫pattern matching

**Hindley-Milner type system**
Haskell的类型系统，是基于$\lambda$演算与参数多态的经典类型系统。

## User-Defined Types
``` hs
data Color = Red | Green | Blue | Indigo | Violet
data Point a = Pt a a
data Tree a = Leaf a | 
            Branch (Tree a) (Tree a)
data Shape = Circle Float | Rectangle Float Float deriving (Show)
```
其中`Red`被称为一个 *data constructor*, `Color`被称为一个 *type constructor*
`Point`被称为`tuple type`
`Tree`是`recursive type`
It is important to distinguish between applying a data constructor to yield a value, and applying a type constructor to yield a type.

另一种自定义类型的方法
``` haskell
data Person = Person {
        firstName:: String, 
        lastName:: String,
        age:: Int,
        height:: Float,
        phoneNumber:: String,
        flavor:: String
    } deriving (Show)
```

``` haskell
data Tree a = Leaf a | Branch (Tree a) (Tree a)
fringe      :: Tree a -> [a]
fringe (Leaf x) = [x]
fringe (Branch left right) = (fringe left) ++ (fringe right)
```

## Type Synonyms
``` haskell
type String     = [Char]
type Name       = String
data Address    = None | Addr String
type Person     = (Name, Address)
```

# Functions
## List Comprehensions and Arithmetic Sequences
`x <- xs`, is called `generator`, meaning for every x in xs

``` haskell
quicksort []    = []
quicksort (x: xs) = quicksort [y | y <- xs, y<x] ++ [x] ++ quicksort [y | y <- xs, y >= x]
```

`arithmetic sequences`
[1..10] => [1,2,3,4,5,6,7,8,9,10]
[1,3..10] => [1,3,6,9]

默认Strings定义为  type String = [Char]
所以
`:type "hello" => [Char]`
``` haskell
"hello" ++ " world" => "hello world"
```

## Functions
``` haskell
add :: Integer -> Integer -> Integer
add x y = x + y
```
`:type add => Integer -> Integer -> Integer`
`:type add 1 => Integer -> Integer`
`:type add 1 2 => Integer`
add is **curried function**.
`add x y` equals `(add x) y`
That means applying `add` to one argument yields a new function which is then applied to the second argument.
`inc = add 1`
> The name *curry* derives from the person who popularized the idea: Haskell Curry. To get the effect of an uncurried function, we could use a tuple, as in `add (x, y) = x + y`.But then we will see that this version of add is really just a functioin of **one** argument!

pass functions as argument
``` haskell
map :: (a->b) -> [a] -> [b]
map f [] = []
map f (x: xs) = f x : map f xs
```
`map` receives a functioni and a list as its arguments.PS: function application has higher precedence than any infix operator.

## Lambda Abstractions
`add x y = x + y` equals `add = \x y -> x + y`

## Infix Operators 中缀操作符
``` haskell
-- 数组连接，当然了，这里用到了已定义的(:)操作符
(++) :: [a] -> [a] -> [a]
[] ++ ys = ys
(x: xs) ++ ys = x : (xs ++ ys)
```

``` haskell 
-- 复合函数
(.) :: (b->c) -> (a->b) -> (a->c)
f . g = \ x -> f (g x)
```

## Sections 切片
中缀表达式本质上只是函数，所以可以这样用
`(x+)` equals `\y -> x + y`
`(+y)` equals `\x -> x + y`
其中,
`(+)` equals `\x y -> x + y`

## Functions are Non-strict
define `bot = bot`
`:type bot` is `bot:: t`

> A function **f** is said to be *strict* if, when applied to a nonterminating expression, it also fails to terminate.
define `const1 x = 1`
`:type const1` is `const1:: Num a => t -> a`
`const1 bot` is `1`
but `inc bot` shows nothing
so `const1` is not a **strict** function, while `inc` is.

PS: we denote the *value* of a non-terminating expression as $\perp$

## Infinite Data Structures
`bot` is a infinite data structure.
`ones = 1: ones` is infinite
`numsFrom n = n: numsFrom (n+1)` is infinite
`ones` and `numsFrom` 肯定是list，可以推断出来
`squares = map (^2) (numsFrom 0)` is infinite

可以定义一个Fibonacci数列
``` haskell
fib = 1: 1: [a+b | (a,b) <- zip fib (tail fib)]
```
我去，实在是想象不出来fib到底是怎么计算出来的

## The error function
`error` is built-in function.
`:type error` is `::[Char] -> a`, 返回一个任意类型的值，据作者将这个值可以用$\perp$来表示
