# Applicative Functor


#### Ver 1.0 - Application in functors

We have know about the application `$` operator and functor `fmap`, as useful as it is, `fmap` isn't much help if we want to apply a function of two arguments to functorial values. 

How could we sum `Just 2` and `Just 3`? The brute force approach would be extracting the values from the `Maybe` wrapper. That, however, would mean having to do tedious checks for `Nothing`. Even worse: in a different `Functor` extracting the value might not even be an option (just think about `IO`).

We could use `fmap` to partially apply `+` to the first argument:

```haskell
Prelude> :t (+) <$> Just 2
-- a partial applied function wrapped in Maybe
(+) <$> Just 2 :: Num a => Maybe (a -> a)
```

But now we are stuck: **we have a function and a value both wrapped in `Maybe`, and no way of applying one to the other**.

What we would like to have is an operator with a type akin to `f (a -> b) -> f a -> f b` to apply functions in the context of a functor - `<*>`:

```haskell
(+) <$> Just 2 <*> Just 3
Just 5

Prelude> :t (<*>)
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```

`<*>` is one of the methods of `Applicative`, the type class of applicative functors - functors that **support function application within their contexts**. 

Ordianry `Functors` help us related two categories' types, but have limit ability to related function applications in two different categories. We need to define applicative functors for higher abstraction.

Before we jump to next section to convey the intention of applicative functor, lets see an example which use pattern matching to do the same thing as applicative functor did:

```haskell
interactiveSumming = do
    putStrLn "Choose two numbers:"
    sx <- getLine
    sy <- getLine
    let mx = readMaybe sx :: Maybe Double
        my = readMaybe sy
    case mx of
        -- not an applicative functor, so have to apply by specific rules
        Just x -> case my of
            Just y -> putStrLn ("The sum of your numbers is " ++ show (x+y))
            Nothing -> retry
        Nothing -> retry
    where
    retry = do
        putStrLn "Invalid number. Retrying..."
        interactiveSumming
```

`interactiveSumming` works, but it is somewhat annoying to write. If only there was a way of summing the numbers before unwrapping them, analogously to what we did with `fmap` in the second version of `interactiveDoubling`, we would be able to get away with just one case:

```haskell
-- Wishful thinking...
    case somehowSumMaybes mx my of
        Just z -> putStrLn ("The sum of your numbers is " ++ show (x+y))
        Nothing -> do
            putStrLn "Invalid number. Retrying..."
            interactiveSumming
```

But what should we put in place of `somehowSumMaybes`? `fmap`, for one, is not enough. While `fmap (+)` works just fine to partially apply `+` to the value wrapped by `Maybe`:

```haskell
:t fmap (+) (Just 3)
fmap (+) (Just 3) :: Num a => Maybe (a -> a)
```

We don't know how to apply a function wrapped in `Maybe` to the second value. For that, we would need a function with a signature like this:

```haskell
<*> :: Maybe (a -> b) -> Maybe(a) -> Maybe(b)
```

Which would then be used like this:

```haskell
fmap (+) (Just 3) <*> Just 4
Just 7
```

We can then use `<*>` which is the infix synonym of `fmap` 

```haskell
(+) <$> Just 3 <*> Just 4
Just 7

-- infix version of fmap
(\y -> y + 5) <$> pure 6
11

fmap (\y -> y + 5) (pure 6)
11

:t (<$>)
(<$>) :: Functor f => (a -> b) -> f a -> f b

:t fmap
fmap :: Functor f => (a -> b) -> f a -> f b
```

Finally, we can rewrite the whole program with higher abstraction, without overuse of pattern matching:

```haskell
instance Applicative Maybe where
    pure                  = Just
    (Just f) <*> (Just x) = Just (f x)
    _        <*> _        = Nothing


interactiveSumming = do
    putStrLn "Choose two numbers:"
    sx <- getLine
    sy <- getLine
    let mx = readMaybe sx :: Maybe Double
        my = readMaybe sy
    case (+) <$> mx <*> my of
        Just z -> putStrLn ("The sum of your numbers is " ++ show z)
        Nothing -> do
            putStrLn "Invalid number. Retrying..."
            interactiveSumming
```

<br>
#### Ver 2.0 - Why we need applicative functor?

Take `*` multiplier as an example, it takes two inputs over a functor, when we do `fmap (*) (Just 3)` it turns out `Just(* 3)` partial applied function. What if we want to map `Just(* 3)` functor over `Just(5)` to get the result? 

```haskell
let a = fmap (*) [1,2,3,4]  
:t a 
#a :: Num a => [a -> a]
```

But we **can't map a function that's inside a functor over another functor** with what `fmap` defined for us.

We could pattern-match against the `Just` constructor to get the function out of it and then map it over `Just 5`, but we're looking for a more general and abstract way of doing that, which works across functors.

To refine above definition:

As useful as it is, `fmap` isn't much help if we want to apply a function of two or more arguments to functorial values.

If we start by using `fmap` then we will get a partial applied function inside that `Functor` type:

```haskell
(+) <$> Just 2
# (+) <$> Just 2 :: Num a => Maybe (a -> a)
# result a f(b -> c) 
```

So we need a morphism which define `f(a -> b) -> f(a) -> f(b)`:

```haskell
(<*>) :: Applicative f => f (a -> b) -> f a -> f b
```

By this menas, we can curry our result until all input are curried, then final result delivered. To wrap function into a `Functor`, there has a `pure` morphism which wrap it:

```haskell
class (Functor f) => Applicative f where
    pure  :: a -> f a
    (<*>) :: f (a -> b) -> f a -> f b
```

Then we left those applicative functor laws:

```haskell
# -- Identity
pure id <*> v = v

# -- Homomorphism
pure f <*> pure x = pure (f x)               

# -- Interchange
u <*> pure y = pure ($ y) <*> u

# -- Composition
pure (.) <*> u <*> v <*> w = u <*> (v <*> w) 

# -- fmap
fmap f x = pure f <*> x                      
```

`($ y)` is the function that supplies (**return**) `y` as argument to another function.

<br>
#### Homomorphism 

A function `f :: a -> b` between two monoids `a` and `b` is called a monoid homomorphism if it preserves the monoid structure, so that: 

```haskell
f mempty          = mempty
f (x `mappend` y) = f x `mappend` f y
```

The functor only address a single input function `(f: a -> b)`. But what if we need a multi-parameters function inside a functor and map over to final output in a general way?

To address above **multi-paramters** issue and provide a general definition for **mapping a functor which have a function over another functor**, **Applicative** type class defined as follow:

<br>
#### Definition

```haskell
class (Functor f) => Applicative f where  
    pure :: a -> f a  
    (<*>) :: f (a -> b) -> f a -> f b  
```

Beyond `<*>`, the class has a second method, `pure`, which brings arbitrary values(maybe a function) into the functor. 

It starts the definition of the `Applicative` class and **it also introduces a class constraint `(Fucntor f)`**. 

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

`Control.Applicative` defines a function that's called `liftA2`. It's defined like this:

```haskell
liftA2 :: (Applicative f) => (a -> b -> c) -> f a -> f b -> f c  
liftA2 f a b = f <$> a <*> b 
```

Applicative functors are more powerful than just ordinary functors. With ordinary functors, we can just map functions over **one functor**. But with applicative functors, **we can apply a function between several functors**.

<br>
#### Applicative functor laws

```haskell
pure id <*> v = v                            -- Identity
pure f <*> pure x = pure (f x)               -- Homomorphism

-- ($ y) u == (u $ y) apply y to function u in low.
u <*> pure y = pure ($ y) <*> u              -- Interchange
pure (.) <*> u <*> v <*> w = u <*> (v <*> w) -- Composition
```

The interchange law says that applying a morphism to a **pure** value `pure y` is the same as applying `pure ($ y)` to the morphism. No surprises there - as we have seen in the higher order functions chapter, **`($ y)` is a partial applied function that supplies `y` as argument to  another function from next input argument**.

```haskell
-- call it as prefix operator
($) (\y -> y + 5) 6
11

-- call it as section, apply left side first
((\y -> y + 5) $) 6
11

-- call it as section, apply right side first
($ 6) (\y -> y + 5)
11

:t ($ 6)
($ 6) :: Num a => (a -> b) -> b

(\y -> y + 5) $ 6
11

6 $ (\y -> y + 5)
<interactive>:70:1: error:
    • Non type-variable argument in the constraint: Num ((a -> a) -> t)
```