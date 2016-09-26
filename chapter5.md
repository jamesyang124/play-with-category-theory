# Monad


Monads, which are just beefed up applicative functors, much like applicative functors are only beefed up functors.

Monads are a natural extension of applicative functors and with them we're concerned with this: **if you have a value with a context, `m a`, how do you apply to it a function that takes a normal a and returns a value with a context?** That is, **how do you apply a function of type `a -> m b` to a value of type `m a`**?

```haskell
(>>=) :: (Monad m) => m a -> (a -> m b) -> m b  
```

We will take monad `Maybe` as an example. Instead of calling it `>>=`, let's call it `applyMaybe` for now. It takes a `Maybe` a and a function that returns a `Maybe b` and manages to apply that function to the `Maybe a`:

```haskell
applyMaybe :: Maybe a -> (a -> Maybe b) -> Maybe b  
applyMaybe Nothing f  = Nothing  
applyMaybe (Just x) f = f x  
```

<br>
####Definition

In mathematical way, we will using scala-like synatx to define a Monad as:

```scala
def unit[M <: Monad](x: Any) = M[x]
def join[M <: Monad](mx: M[M[x]]) = M[x]
```

It means it must have two functions, one is the unit function which very similar to `pure` function in functor. Another `join` function transform a double-wrapped context value `M(M(x))` to one shape `M(x)` instead.


`Monad` type class definition:

```hasekll
class Monad m where  
    return :: a -> m a  
  
    (>>=) :: m a -> (a -> m b) -> m b  
  
    (>>) :: m a -> m b -> m b  
    x >> y = x >>= \_ -> y  
  
    fail :: String -> m a  
    fail msg = error msg  
```