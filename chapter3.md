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

Take `*` as an example, it takes two inputs over a functor, when we do `fmap (*) (Just 3)` it turns out `Just(* 3)` partial applied function. What if we want to map `Just(* 3)` functor over `Just(5)` to get the result? 

Even when we mapped `\f -> f 9` over a functor that contained functions inside it, we were **just mapping a normal function `g: a -> b`** over it though the output result will be what we expect for:

```haskell
let a = fmap (*) [1,2,3,4]  
:t a 
#a :: Num a => [a -> a]

fmap (\f -> f 9) a
# [9,18,27,36]  
```

But we **can't map a function that's inside a functor over another functor** with what `fmap` defined for us.

We could pattern-match against the `Just` constructor to get the function out of it and then map it over `Just 5`, but we're looking for a more general and abstract way of doing that, which works across functors.

#### Currying

In `Haskell`, functions are **curried** which means it only take a single input and return a partial function. The final result will be produced only when all input are curried.

The functor only address a single input function `(f: a -> b)`. But what if we need a multi-parameters function inside a functor and map over to final output in a general way?

To address above **multi-paramters** issue and provide a general definition for **mapping a functor which have a function over another functor**, **Applicative** type class defined as follow:

#### Definition

```haskell
class (Functor f) => Applicative f where  
    pure :: a -> f a  
    (<*>) :: f (a -> b) -> f a -> f b  
```

It starts the definition of the `Applicative` class and **it also introduces a class constraint**. 

It says that if we want to make a type constructor part of the `Applicative` typeclass, **it has to be in `Functor` first**. That's why if we know that if a type constructor is part of the `Applicative` typeclass, it's also in `Functor`, so we can use `fmap` on it.

A better way of thinking about `pure` would be to say that it takes a value and puts it in some sort of default (or `pure`) context — a minimal context that still yields that value. 

Now we can map a normal function `g: a -> b` over a functor which have that function inside it:

```haskell
# M is the context
pure g == M g
```

Let's check `Maybe`'s applicative implementation, remember we have `fmap` since `Maybe` is aslo a functor:

```haskell
instance Applicative Maybe where  
    pure = Just  
    Nothing <*> _ = Nothing  
    (Just f) <*> something = fmap f something  
```

The `(Just f) <*> something = fmap f something` obey the definition as it takes a functor which have a function and another functor in left side of `=`, then map them over to another functor at the right side of `=`.

We observe that `(Just f)` could be rewritten as `pure f`, then above `applicative` definition can supply a law as:

```haskell
pure f <*> something = fmap f something
```

It could be translated as: 

> when we take a function `f` with another functor `something` as input for `<*>`, then the output is equivalent to `fmap f something`.

Now, if `f` is a multi-paramter function, we could gracefully yield the result from `pure f <*> x <*> y ...` because `<*>` is **left associative**.

In `haskell` it also export a function `<$>` to reduce redundant code:

```haskell
(<$>) :: (Functor f) => (a -> b) -> f a -> f b
# f <$> x = pure f <*> x = fmap f something
f <$> x = fmap f something

(++) <$> Just "johntra" <*> Just "volta"
```

Now we basically solve multi-paramter function issue. We don't have to handly do the pattern match over and over again to unboxing the context and apply with the value.



