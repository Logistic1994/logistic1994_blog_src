title: Scala Week3
category: scala
tags:
  - scala
date: 2016-04-05 13:56:26
---

### Types and Evaluation
Type parameters will not affect evaluation in Scala.
All specific type will be removed when compiling. For example, `Array[Int]` is the same class as `Array[Double]`. But in C++, C#, they are not the same. Removing the specific type is called `Type Erasure`. C++, C# will make compiled code larger, beacause for every Gerenic Type, a specail class/type will be generated.
