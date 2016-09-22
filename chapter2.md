# Functor

A `Functor` is **a type which relates categories together**. A functor is essentially a transformation between categories. *** - Haskell Wikipedia ***

Given categories `C` and `D`, a functor `F: C -> D` describe:

1. Maps any object `A` in **C**, to `F(A)` in **D**.
2. Maps morphisms `f: A -> B` in **C**, to `F(f): F(A) -> F(B)` in **D**.

Then there has two axioms that functors must obey:



1. Given identity morphism `id(A)` on object `A`, `F(id(A))` must be the identity morphism on `F(A)`:

  > *** `F(id(A)) = id(F(A)) <=> F[(id: A -> A)] = (id: F[A] -> F[A])` ***

2. Functors must distributed over morphism composition:

  > *** `F(f . g) == F(f) . F(g)` ***

Now consider `Lst` subcategory in `Hask` category, it contains **only list types**. That is, `[T]` for any type of `T`, the morphisms in `Lst` are functions defined on list types:

*** `[T] -> [U]` for types `T`, `U` ***

In `Haskell`, functor definition:

```haskell
class Functor (f :: * -> *) where
  fmap :: (a -> b) -> f a -> f b
```

And `Maybe` functor:

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




