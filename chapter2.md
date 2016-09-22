# Functor

A `Functor` is **a type which relates categories together**. A functor is essentially a transformation between categories. *** - Haskell Wikipedia ***

<br/>
#### Definition

Given categories `C` and `D`, a functor `F: C -> D` describe:

1. Maps any object `A` in **C**, to `F(A)` in **D**.
2. Maps morphisms `f: A -> B` in **C**, to `F(f): F(A) -> F(B)` in **D**.

<br/>
#### Axioms

Then there has two axioms that functors must obey:

1. Given identity morphism `id(A)` on object `A`, `F(id(A))` must be the identity morphism on `F(A)`:

  > *** `F(id(A)) = id(F(A)) <=> F[(id: A -> A)] = (id: F[A] -> F[A])` ***

2. Functors must distributed over morphism composition:

  > *** `F(f . g) == F(f) . F(g)` ***

<br/>
#### Example

Now consider `Lst` subcategory in `Hask` category, it contains **only list types**. That is, `[T]` for any type of `T`, the morphisms in `Lst` are functions defined on list types:

*** `[T] -> [U]` for types `T`, `U` ***

In `Haskell`, functor definition:

***A `Functor` is any type that defines how `fmap` applies to it.***

```haskell
class Functor (f :: * -> *) where
  fmap :: (a -> b) -> f a -> f b
```

`fmap` is the same as `(a -> b) -> (f a -> f b)` and can be read as: 

> Take a function `f: a -> b` and returns another function `g: f a -> f b` which takes a functor `f a` then returns another functor `f b`. 

Definition of `Maybe` functor:

```haskell
instance Functor Maybe where
  fmap f Just(x) = Just(f x)
  fmap _ Nothing = Nothing
```

The **type constructor** and **Functor** - `Maybe` takes any type `T` to a new type `Maybe T`. Also `fmap` takes a function `a -> b` to a function `Maybe a -> Maybe b`. `fmap` is also a morphism in `Maybe` category. 

So, we defined two parts:

1. Something that takes object in `Hask` to object in another category `Maybe`.
2. Something that takes morphisms in `Hask` to morphisms in another category `Maybe`.

So `Maybe` should be a functor if `Maybe` also obey functor axioms.

`Haskell` functors is that **they represent types that can be mapped over**. So a function that does some mapping could be written using `fmap`.

Lets check the `id` function in `Maybe`:

```haskell
fmap id == id

fmap id Just(t) = Just(id(t)) = Just(t)
fmap id Nothing = Nothing
```

Then review the composition:

```haskell
fmap (f . g) == fmap f . fmap g

f :: x -> y
g :: t -> x
f . g :: t -> y 

# apply Just(t) from right to left
fmap (f . g) Just(t) = fmap f . fmap g Just(t)
= fmap f . Just(g(t))
= fmap f . Just(x)
= Just(f(x))
= Just(y)
```

<br/>
#### Fusion

Picturing the functor as a list or similar container, the right-hand side is a two-pass algorithm: we map over the structure, performing `g`, then `map` over it again, performing `f`. **The functor axioms guarantee we can transform this into a single-pass algorithm that performs `f . g`**. This is a process known as **fusion**.

<br>
####Type Constructor

Functors transform a type to another type, so they are **typeclass** as well.

If a type is a part of a typeclass, that means that it supports and implements the behavior the typeclass describes.

http://learnyouahaskell.com/types-and-typeclasses#typeclasses-101

<br/>
#### Value Construcotor

In `Haskell` a data type is defined by `data`:

```haskell
# create another data type Bool
data Bool = False | True
```

The parts after the `=` are value constructors. They specify the different values that this type can have. 

Now consider `Int` type:

```haskell
data Int = -2147483648 | -2147483647 | ... | -1 | 0 | 1 | 2 | ... | 2147483647  
```

The **first** and **last** value constructors are the minimum and maximum possible values of `Int`.

Value constructors are actually functions that **ultimately return a value of a data type**.

http://learnyouahaskell.com/making-our-own-types-and-typeclasses



<br/>
#### Translating Categorical concepts into Haskell

1. We work in the category `Hask` and its subcategories.
2. Objects are **types**.
4. Morphisms are **functions**.
5. Morphisms take a type and return another type are **type constructors**.
6. Morphisms take a function and return another function are **higher-order functions**.
7. **Typeclasses**, along with the polymorphism they provide, make a nice way of capturing the fact that in category theory **things are often defined over a number of objects at once**.

We can think `typeclasses` as `trait`, `interface` which is extensible in `Scala` world.

In `Haskell` internally, all functions are `curried`. It means it only takes one input source and return a **partial function**. When all inputs are supplied, then it returns the target object finally.