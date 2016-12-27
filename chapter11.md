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

Haskell defines an extra `mconcat` method, which iterate a `list` or `traversable` types with `mappend empty`. It call fold right to traverse values.

https://wiki.haskell.org/Fold

```haskell
class Monoid a where
    mempty  :: a
    mappend :: a -> a -> a

    mconcat :: [a] -> a
    mconcat = foldr mappend mempty
```

## Functor

