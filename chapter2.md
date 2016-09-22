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
