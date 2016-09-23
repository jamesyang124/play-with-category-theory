# Applicative Functor


#### Type Signature

A type signature in `Haskell` represents the behavior of the morphism, consider `readFile` function's type signature:

```haskell
readFile :: FilePath -> IO String
```

It means it take a `FilePath` type then return a `IO String` type.

We can also find that fat arrow `=>`. Everything before the `=>` symbol is called a class constraint:

```haskell
sort :: Ord a => [a] -> [a]
```

In here, `Ord a => [a] -> [a]`, which basically says **given a type `a` for which ordering is defined, then we can sort a list of such `a`s to provide another list of the same element type**.


Let us recap that that if two morphisms

#### Definition

```haskell
class (Functor f) => Applicative f where  
    pure :: a -> f a  
    (<*>) :: f (a -> b) -> f a -> f b  
```

It starts the definition of the `Applicative` class and **it also introduces a class constraint**. It says that if we want to make a type constructor part of the `Applicative` typeclass, **it has to be in `Functor` first**. That's why if we know that if a type constructor is part of the `Applicative` typeclass, it's also in `Functor`, so we can use `fmap` on it.

