# Category

A category should have three basic components:

1. **objects** 
2. **morphisms** 
3. **morphism composition**

<br>
#### Objects

Object describe an `algebraic data type`, it might be the same as the terms such as `shape`, or `context`.

<br>
#### Morphisms

**Morphisms** maps a relation from one type to another type regardless whether the types are in the same category. The term **arrows** have as the same meaning as **morphisms**. We will use **morphism** through out the book.

<br>
#### Morphsim Composition


Morphisms must be composable such as a composition `f . g` is from the morphism `f` and `g`. This is also a **binary operation** of the **prodcut** in Category theory. 

It defines that, if there has a morphism `f: A -> B` and there has another morphism `g` which takes `f`'s output object `B` as input and denote as `g: B -> C`, then they must be **closed** by a composition `f . g`.

We can describe that composition as:

`(f . g)(x) == f(g(x))`

It can also be visualized as:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ef/Commutative_diagram_for_morphism.svg/200px-Commutative_diagram_for_morphism.svg.png)

To learn more about off-topic **product**, the graph illustrate as:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/89/CategoricalProduct-03.svg/280px-CategoricalProduct-03.svg.png)

It defines that: if `X` denote as `X1 x X2` and `f1`, `pi_1`, `f2`, and `pi_2` are already learned, then it must exist a morphism `f` to make `f1` composition closed the relation from `Y` to `X` then to `X1`.

https://en.wikipedia.org/wiki/Product_(category_theory)

<br>
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

<br>
#### Monomorphic vs Polymorphic

In `Haskell`, the main category is `hask`, which treat `Haskell types` as objects and `Haskell functions` as morphisms and uses `(.)` for composition:

`id . f == f . id == f`

As we know that `(.)` is a associative function, so it must apply from right to left. 

For `Haskell`, the `id` is **polymorphic** ** - it can take many different types for its domain and range, or, in category-speak, can have many different source and target objects.** 

But morphisms in category theory are by definition **monomorphic** — **each morphism has one specific source object and one specific target object**.

A polymorphic `Haskell` function can be made **monomorphic** by specifying its type for **source** and **target** object:

`(id :: B -> B) . f = f . (id :: A -> A) = f` 

We denote the first `id` as `id(B)` and second as `id(A)` to make it **monomorphic**.