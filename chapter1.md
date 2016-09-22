## What is Category?

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

`(f.g)(x) == f(g(x))`

It can also be visualized as:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ef/Commutative_diagram_for_morphism.svg/200px-Commutative_diagram_for_morphism.svg.png)


If a category holds above three components, then it should have the **category laws** as below:

1. The composition of morphisms is **associative**.

  > `(f . g) . h == f . (g . h)`
2. The composition operation must be **closed**. 

  > It means the relationship from `f` to `g` must have `f.g` which apply from **right to left**.
  


