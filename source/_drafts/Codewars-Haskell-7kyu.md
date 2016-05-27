title: Codewars上Haskell练习题7kyu
category: OJ
tags: [Haskell, Haskell练习题]
---
## Remove the minimum
>Given an array of integers, remove the smallest value. If there are multiple elements with the same value, remove the one with a lower index. If you get an empty array/list, return an empty array/list.
Don't change the order of the elements that are left.

与一般非函数式语言不同，Haskell中数据是不可变的（Immutable），这就意味着不能像python或者c++中直接remove这个最小值所在的iterator。
<!-- more -->

### My solution
``` Haskell
module Codewars.Kata.RemoveSmallest where
getSmallestIndex :: [Int] -> Int -> Int -> Int
getSmallestIndex (x:xs) smallest index =  if x == smallest then index else getSmallestIndex xs smallest (index+1)

removeIndex :: Int -> [Int] -> [Int]
removeIndex sindex = map snd . filter (\(x,_) -> x /= sindex) . zip [1..]

removeSmallest :: [Int] -> [Int]
removeSmallest xs = removeIndex (getSmallestIndex xs (minimum xs) 1) xs
```
先找到最小值所在的index，然后生成一个新List时过滤掉这个index所在的元素。

### Best solution
``` Haskell
module Codewars.Kata.RemoveSmallest where
import Data.List (delete)

removeSmallest :: Ord a => [a] -> [a]
removeSmallest xs = delete (minimum xs) xs
```
我的方法是基于index过滤，这边是直接基于内容过滤。
``` Haskell
-- It is a special case of 'deleteBy', which allows the programmer to
-- supply their own equality test.

delete                  :: (Eq a) => a -> [a] -> [a]
delete                  =  deleteBy (==)

-- | The 'deleteBy' function behaves like 'delete', but takes a
-- user-supplied equality predicate.
deleteBy                :: (a -> a -> Bool) -> a -> [a] -> [a]
deleteBy _  _ []        = []
deleteBy eq x (y:ys)    = if x `eq` y then ys else y : deleteBy eq x ys
```
Data.List.delete的源码

