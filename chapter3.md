# Applicative Functor

#### Type Signature

A type signature in `Haskell` represents the behavior of the morphism, ex: `readFile` function's type signature:

```haskell
readFile :: FilePath -> IO String
```

It means that it take a `FilePath` type then return a `IO String` type.

We can also find that fat arrow `=>` in another example:

```haskell
:t (==)  
(==) :: (Eq a) => a -> a -> Bool  
```

Everything before the `=>` symbol is called a **class constraint**, We can read the previous type declaration like this:

> The equality function takes any two values that are of the same type and returns a `Bool`. The type of those two values must be a member of the `Eq` class (this was the class constraint).


#### Why we need applicative functor?

The functor only address a single input function `(f: a -> b)`. But what if we need a multi-parameters function as the input of functor? Also, in `Haskell`, functions are **curried** which means it only take a single input and return a partial function. The final result will be produced only when all input are curried.

To address **multi-paramters** issue, **Applicative** type class defined as follow:

#### Definition

```haskell
class (Functor f) => Applicative f where  
    pure :: a -> f a  
    (<*>) :: f (a -> b) -> f a -> f b  
```

It starts the definition of the `Applicative` class and **it also introduces a class constraint**. It says that if we want to make a type constructor part of the `Applicative` typeclass, **it has to be in `Functor` first**. That's why if we know that if a type constructor is part of the `Applicative` typeclass, it's also in `Functor`, so we can use `fmap` on it.

