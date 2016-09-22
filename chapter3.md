# Applicative Functor


#### Why we need applicative functor?

The functor only address a single input function. But what if we needs a multi-parameters function as the input of functor? Also, in `Haskell`, function are **curried** which means it only take a single input and return a partial function. The final result will be produced only when all input are curried.

To address **multi-paramters** issue, **Applicative** typeclass defind as follow:

#### Definition

```haskell
class (Functor f) => Applicative f where  
    pure :: a -> f a  
    (<*>) :: f (a -> b) -> f a -> f b  
```

It starts the definition of the `Applicative` class and **it also introduces a class constraint**. It says that if we want to make a type constructor part of the `Applicative` typeclass, **it has to be in `Functor` first**. That's why if we know that if a type constructor is part of the `Applicative` typeclass, it's also in `Functor`, so we can use `fmap` on it.

