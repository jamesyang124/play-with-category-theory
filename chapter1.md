# Category

**Category theory** is a concept in terms of a collection of objects and morphisms.

A category should have three basic components:

1. **objects** 
2. **morphisms** 
3. **morphism composition**

#### Objects

Object describe a `algebraic data type`, it might the same as the terms such as `shape`, or `context`.

#### Morphisms

Morphisms map a relation from one type to another type regardless whether the types are in same category.

#### Morphsim Composition


Morphisms must be composable such as a composition `f . g` is from the morphism `f` and `g`. This is also a **binary operation** of the **prodcut** in Category theory. 

We can denote that composition as:

`(f . g)(x) == f(g(x))`

It can also be visualized as:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ef/Commutative_diagram_for_morphism.svg/200px-Commutative_diagram_for_morphism.svg.png)


#### Category Laws

If a category holds above three components, then it should have the **category laws** as below:

1. The composition of morphisms is **associative**.

  > `(f . g) . h == f . (g . h)`
  
2. The composition operation must be **closed**. 

  > It means if the relationship from `f` to `g` is applicable, then there **must have `f . g` which apply from right to left**. So we say that `f` and `g` is **closed** by `f . g`.

3. For that category `C`, it must have an identity function `id` which is `id: A -> A`.

The definition of `id` should be:

*** For every `g: A -> B` in category `C`, `id(B) . g == g . id(A) == g`. ***

The `id(B)` describe the `id` function accepts type `B` as an input and returns same type `B` as output. 

In `Haskell`, the main category is `hask`, which treat `Haskell types` as objects and `Haskell functions` as morphisms and uses `(.)` for composition:

`id . f == f . id == f`

As we know that `(.)` is a associative function, so it must apply from right to left. 

In `Haskell`, the `id` is **polymorphic** ** - it can take many different types for its domain and range, or, in category-speak, can have many different source and target objects.**

But morphisms in category theory are by definition **monomorphic** — **each morphism has one specific source object and one specific target object**.

A polymorphic `Haskell` function can be made **monomorphic** by specifying its type.