title: Haskell-3
date: 2015-09-26 16:20:14
category: Haskell
tags: [Haskell]
---
# Type Classes and Overload
## parametric polymorphism or ad hoc polymorphism or overloading
``` haskell
x `elem` [] = False
x `elem` (y: ys) = x == y || (x `elem` ys)
```
如果要求上面 **elem** 的类型是 **a->[a]->Bool**，那么我们就必须要求 **==** 的类型是 **a->a->Bool**, 但是为什么 **==**的类型会是这样呢？更进一步，如果是两个list进行比较，比较的过程会是什么样子呢？
We expect **==** to be overloaded to carry on these various tasks.

<!-- more -->

*Type classes* allows us to declare which types are *instances* of which class, and to provide definitions of the overloaded *operations* associated with a class.
``` haskell
class Eq_ a where
    (==) :: a -> a -> Bool
```
这里我定义了一个class **Eq_**, 它有一个operation **==**, 其中a是一个type. 应该这样读, "a type **a** is an instance of the class **Eq** if there is an (overloaded) operation **==**, of the appropriate type, defined on it"
其中 **Eq_ a**表达了对于 **a**的一个限制，被称为 **context**
对照上面定义的elem函数, **:type elem** is **elem :: (Eq a) => a -> [a] -> Bool**, 读作"For every type **a** that is an instance of the class **Eq**, **elem** has type **a->[a]->Bool**"
``` haskell
data Tree a = 
    Leaf a | Branch (Tree a) (Tree a)

instance (Eq a) => Eq (Tree a) where
    Leaf a == Leaf b                    = a == b
    (Branch l1 r1) == (Branch l2 r2)    = (l1 == l2) && (r1 == r2)
    _ == _                              = False  
```
其中(Eq a)是必须的，因为比较Leaf时就是直接使用的Leaf的值来进行比较的。

``` haskell
class Eq_ a where
    (==), (/=) :: a -> a -> Bool
    x /= y = not (x == y)
-- 那么在继承Eq_的instance中，只要定义了==，那么/=也就定义好了
```

*class extension*, define a class which inherits an existed class
``` haskell
class (Eq_ a) => Ord_ a where
    (<), (<=), (>=), (>) :: a -> a -> Bool
    max, min :: a -> a -> a
```
We say that Eq_ is a *superclass* of Ord_, Ord_ is a *subclass* of Eq_, and any type which is an instance of Ord_ must also be an instance of Eq_.

Class methods共享同一个命名空间，也就是说class methods不能重复。
当然，class methods的参数也可以定义除此class的其他参数，比如
``` haskell
class C a where 
    m :: Show b => a -> b
```

## Functor函子
第一个例子
``` haskell
class Tofu t where
    tofu:: j a -> t a j
-- a is *, so j is * -> *, so t is (* -> (* -> *))

data Frank a b = Frank {frankField :: b a} deriving (Show)
-- b is *, so a is * -> *
-- the first Frank is data construct 
-- the second Frank is type construct
-- the frankField need type *, so a is *, while b is * -> *

instance Tofu Frank where
    tofu x = Frank x
-- tofu need 2 params, the first type is *, while the second is * -> *
-- Frank type just satisfies

data Barry t k p = Barry {yabba:: p, dabba:: t k} deriving (Show)
-- p is *
-- k is *, t is *->*
-- so :k Barry is (*->*) -> * -> * -> *
-- class Functor f where
--      fmap :: (a->b) -> f a -> f b
-- a is * , so f needs to be *->*
-- so while applying Functor onto Barry, the Functor needs (*->*)
-- we need to fill the first 2 param of Barry
instance Functor (Barry a b) where
    fmap f (Barry {yabba = x, dabba = y}) = Barry {yabba = f x, dabba = y}
-- 意思说fmap过来的函数始终只作用在第一个参数yabba上面
```
总的意思就是类型的函数，将类型类比为原先的函数中的数值就可以了
