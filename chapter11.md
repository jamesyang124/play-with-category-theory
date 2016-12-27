# Cats Data Types

## Semigroup

A semigroup is a set $$S$$ together with a binary operation "$${\displaystyle \cdot}$$" (that is, a function $${\displaystyle \cdot :S\times S\rightarrow S}$$) that satisfies the associative property:

For all $${\displaystyle a,b,c\in S}$$, the equation $${\displaystyle (a\cdot b)\cdot c=a\cdot (b\cdot c)}$$ holds.

#### Semigroup law

```scala
// use infix |+| as combine method.

// 1. associativity
(x |+| y) |+| z = x |+| (y |+| z) 
```

## Monoid

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

It states that `id` function should reflect the same type by `fmap` or call it self. And the composed function should be able to `fmap` first, then composed in latter no matter execution order.

```haskell
fmap id = id

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

`fproduct` use to create key-value tuples:

```scala
val source = List("a", "aa", "b", "ccccc")
// source: List[String] = List(a, aa, b, ccccc)

Functor[List].fproduct(source)(len).toMap
// res9: scala.collection.immutable.Map[String,Int] = Map(a -> 1, aa -> 2, b -> 1, ccccc -> 5)
```

Or the most important one - `compose`, given two functors `F[_]` and `G[_]` then compose to `F[G[_]]` with `Nested` data type:

```scala
import cats.data.Nested

val listOpt = [Nested[List, Option, Int]]List(Some(5), None, Some(8))

Functor[Nested[List, Option, ?]].map(listOpt)(_ + 1)
```

