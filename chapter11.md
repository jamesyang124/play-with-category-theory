# Cats Data Types

## Semigroup
----

A semigroup is a set $$S$$ together with a binary operation "$${\displaystyle \cdot}$$" (that is, a function $${\displaystyle \cdot :S\times S\rightarrow S}$$) that satisfies the associative property:

For all $${\displaystyle a,b,c\in S}$$, the equation $${\displaystyle (a\cdot b)\cdot c=a\cdot (b\cdot c)}$$ holds.

#### Semigroup law

```scala
// use infix |+| as combine method.

// 1. associativity
(x |+| y) |+| z = x |+| (y |+| z) 
```
  
       
## Monoid
----


Monoid add `empty` method to define the empty result. And it applied associativity:

```scala
(combine(x, empty) == combine(empty, x) == x)
```

Then you can call `combineAll` with empty defined item:

```scala
Monoid[String].empty
// res0: String = ""

Monoid[String].combineAll(List("a", "b", "c"))
// res1: String = abc

Monoid[String].combineAll(List())
// res2: String = ""
```

#### Monoid law

```scala
// 1. associativity
(x combine y) combine z = x combine (y combine z)

// 2. left identity
mempty combine x = x               

// 3. right identity
x combine mempty = x               
```

Haskell defines an extra `mconcat` method, which iterate a `list`(it is general term as _iterable_ in Haskell) with `mappend empty`. It call fold right to traverse values.

https://wiki.haskell.org/Fold

```haskell
class Monoid a where
    mempty  :: a
    mappend :: a -> a -> a

    mconcat :: [a] -> a
    mconcat = foldr mappend mempty
```

## Functor
----

By previous definition, a functor should define `map` method in scala, `fmap` in Haskell.

```scala
def map[A, B](fa: F[A])(f: A => B): F[B]
```

Then you can implement it by _ad-hoc_ polymorphism as:

```scala
import cats._

implicit val optionFunctor: Functor[Option] = new Functor[Option] {
  def map[A,B](fa: Option[A])(f: A => B) = fa map f
}

implicit val listFunctor: Functor[List] = new Functor[List] {
  def map[A,B](fa: List[A])(f: A => B) = fa map f
}
```

#### Functor laws

It states that `id` function should reflect the same type by `fmap` or call it self. And the composed function should be able to `fmap` first, then composed them in latter.

```haskell
fmap id = id
-- abbrev. as fmap id x = id x

fmap (g . f) = fmap g . fmap f
```

#### Lift

Besides calling `map`, you can call `lift` to lift a function to its functor type:

```scala
val len: String => Int = _.length

// Call lift
// A => B to F[A] => F[B]

val lenOption: Option[String] => Option[Int] = Functor[Option].lift(len)
// lenOption: Option[String] => Option[Int] = cats.Functor$$Lambda$3838/109914554@70211317

lenOption(Some("abcd"))
// res8: Option[Int] = Some(4)
```

In Haskell, this define as:

```haskell
lifting :: Functor f => (a -> b) -> f (a -> b)
```

#### Fproduct

`fproduct` use to create key-value tuples:

```scala
val source = List("a", "aa", "b", "ccccc")
// source: List[String] = List(a, aa, b, ccccc)

Functor[List].fproduct(source)(len).toMap
// res9: scala.collection.immutable.Map[String,Int] = Map(a -> 1, aa -> 2, b -> 1, ccccc -> 5)
```

#### Composed functor 

Or the most important one - `compose`, given two functors `F[_]` and `G[_]` then compose to `F[G[_]]` with `Nested` data type:

```scala
import cats.data.Nested

val listOpt = [Nested[List, Option, Int]]List(Some(5), None, Some(8))

Functor[Nested[List, Option, ?]].map(listOpt)(_ + 1)
```

**Functor allowed composition, while monad is not.**

To get this conclusion in depth:

http://stackoverflow.com/questions/7040844/applicatives-compose-monads-dont

```haskell
(<*>) :: Applicative a => a (s -> t) -> a s -> a t
(>>=) :: Monad m =>       m s -> (s -> m t) -> m t
```

That `(s -> m t)` shows that a **value** in `s` can **determine the behavior of a computation** in `m t`. Monads allow interference between the _value_ and _computation layers(1-arity type)_. 

The `(<*>)` operator allows no such interference: **the _function_ and _argument_ computations don't depend on values**.

Consider example and a unfinished derivation:

```haskell
(>>>>==) :: (Monad m, Monad n) => m (n s) -> (s -> m (n t)) -> m (n t)

mns >>>>== f = mns >>-{-m-} \ ns -> let nmnt = ns >>= (return . f) in ???
```

> We have an `n (m (n t))`, so we need to get rid of the outer `n`

This is hard.

The weaker `double apply` composition is easy to define:

```haskell
(<<**>>) :: (Applicative a, Applicative b) => a (b (s -> t)) -> a (b s) -> a (b t)

abf <<**>> abs = pure (<*>) <*> abf <*> abs
```

#### Subtyping 

In scala, we can borrow OO's subtype polymorphism, which is simulated by `absurd` in Haskell.

```scala
class A
class B extends A

val b = new B
val a: A = b  // upcasting

val listB = List(new B)

val liftA1 = listB.map(b => b: A)
val listA2 = listB.map(b => identity[A])
val listA3 = Functor[List].widen[B, A](listB)
```

**Subtyping relationships are _lifted_ by functors, such that if `F` is a lawful functor and `A <: B then F[A] <: F[B]`**. 

This is because functor laws and `identity` guarantee that `identity : a => b` so `a map identity == b` which make the subtyping is natural relationship in here.

> Note that `identity` in scala may not be the same as `Id` in Haskell.


Apply
----

Cats split the same methods which is defined by `applicative functor` in Haskell, so `Apply` is actually add an transformer function `ap` as follows:

```scala
def ap(fa: A)(f: A => B)
```

Like functors, `Apply` can composed:

```scala
import cats.data.Nested
// import cats.data.Nested

val listOpt = Nested[List, Option, Int](List(Some(1), None, Some(3)))
// listOpt: cats.data.Nested[List,Option,Int] = Nested(List(Some(1), None, Some(3)))

val plusOne = (x:Int) => x + 1
// plusOne: Int => Int

val f = Nested[List, Option, Int => Int](List(Some(plusOne)))
// f: cats.data.Nested[List,Option,Int => Int]

Apply[Nested[List, Option, ?]].ap(f)(listOpt)
// res6: cats.data.Nested[[+A]List[A],Option,Int] = Nested(List(Some(2), None, Some(4)))
```




