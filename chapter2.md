# Functor

A `Functor` is **a type which relates categories together**. A functor is essentially a transformation between categories. *** - Haskell Wikipedia ***

Given categories `C` and `D`, a functor `F: C -> D` describe:

1. Maps any object `A` in **C**, to `F(A)` in **D**.
2. Maps morphisms `f: A -> B` in **C**, to `F(f): F(A) -> F(B)` in **D**.

Then there has two axioms that functors must obey:

1. `F(id(A)) = id(F(A))`