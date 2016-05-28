title: Scala-Week4
category: Scala
tags:
  - Scala
date: 2016-05-27 16:40:52
---

## Functions as Objects
#### Expansion of Function Values
``` scala
(x: Int) => x * x
```
is expanded to 
``` scala
new Function1[Int, Int] {
    def apply(x: Int) = x * x
}
```
This is called `Eta-Exp`.

<!-- more -->

## Boolean Object
```scala
abstract class Boolean {
  def ifThenElse[T](t: => T, e: => T): T

  def &&(x: => Boolean) : Boolean = ifThenElse(True, x)
  def ||(x: => Boolean) : Boolean = ifThenElse(x, False)
  def unary_! : Boolean = ifThenElse(False, True)
  // false < true
  def ==(x: Boolean): Boolean = ifThenElse(x, x.unary_!)
  def !=(x: Boolean): Boolean = ifThenElse(x.unary_!, x)
  def <(x: => Boolean) : Boolean = ifThenElse(ifThenElse(False, False), ifThenElse(True, False))
}

object True extends Boolean {
  override def ifThenElse[T](t: => T, e: => T): T = t

  override def toString: String = "True"
}

object False extends Boolean {
  override def ifThenElse[T](t: => T, e: => T): T = e

  override def toString: String = "False"
}
```

## Non-negative Numbers
``` scala
// Peano numbers
abstract class Nat {
  def isZero: com.good.Boolean
  def predecessor: Nat
  def successor = new Succ(this)
  def + (that: Nat): Nat
  def - (that: Nat): Nat
}

object Zero extends Nat {
  override def isZero = True
  override def predecessor = throw new Error("0.predecessor")
  override def + (that: Nat) = that
  override def - (that: Nat) = if(that.isZero.equals(True)) this else throw new Error("0.minus Nat")
}

class Succ(n: Nat) extends Nat {
  override def isZero = False
  override def predecessor = n
  override def + (that: Nat) = new Succ(n + that)
  override def - (that: Nat) = if(that.isZero.equals(True)) this else n - that.predecessor
}
```

## Generic and Subtyping
#### Type Bounds
`S <: T` means S is a subtype of T
`S >: T` means S is a supertype of T, or T is a subtype of S

```scala
def assertAllPos[S >: NonEmpty <: IntSet](s: S) // restrict S any type on the interval between NonEmpty and IntSet
```

#### Covariance
Given: 
`NonEmpty <: IntSet` 
So
`List[NonEmpty] <: List[IntSet]`

This is called `covariant` because the subtyping relationship varies with the type parameter.

#### The Liskov Substitution Principle
> Let q(x) be a property provable about objects x of type. THEN q(y) should be provable for objects y of type A where A <: B

可替换原则：当A是B的子类型时，对于B所有能进行的操作也应该在A上能被操作。

####  Variance
> A type that accepts mutations of its elements should not be covariant.
> But immutable types can be convariant, if some conditions on methods are met.

如果有C[T], A, B, 并且A <: B
那么对于C[A]和C[B]，有三种情况
1. C[A] <: C[B] => C is covariant => class C[+A] {...}
2. C[A] >: C[B] => C is contravariant => class C[-A] {...}
3. otherwise => C is nonvariant => class C[A] {...}

``` scala
// NonEmpty <: IntSet
type A = IntSet => NonEmpty
type B = NonEmpty => IntSet
```
因为所有给B函数的参数都能给A用，所以A <: B

if A2 <: A1 and B1 <: B2, then
    A1 => B1 <: A2 => B2
意思就是对于一个函数f = A => B来说，输入A的类型要<:A, 输出B的类型要<:B，才能满足函数要求。

``` scala
trait Function1[-T, +U] {
    def apply(x: T): U
}
```

## Pattern Matching
需要解决的问题：
1. Which subclass was used
2. What were the arguments of the constructor?

具体语法
``` scala
var = xs match {
  case CONSTRUCTORS(arg1, arg2, ...) => VALUE
  case VARIABLES => VALUE
  case _ => 
  case CONSTANTS => VALUE
}
```

可以避免使用cast，防止不安全的情况出现