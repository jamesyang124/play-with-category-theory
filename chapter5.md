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

A monad is a functor `M : C -> C`, along with two morphisms for every object `X` in `C`:

In mathematical way, we use Scala-liked synatx to define a Monad as:

```scala
// M is a monad
def unit: a => M(a)
def join: M(M(x)) => M(x)
```

It means it must have two functions, one is the unit function which is very similar to `pure` function in functor. Another `join` function transform a double-wrapped value `M(M(x))` to `M(x)` instead.

```haskell
return :: a -> m a
join :: Monad m => m (m a) -> m a
join x = x >>= id

(>>=) :: Monad m => m a -> (a -> m b) -> m b
x >>= f = join (fmap f x)
```

In haskell, we use **bind** (`>>=`) to replace actual `join` implementation, `Monad` type class definition:

```hasekll
class Monad m where  
    return :: a -> m a  
  
    (>>=) :: m a -> (a -> m b) -> m b  
  
    (>>) :: m a -> m b -> m b  
    x >> y = x >>= \_ -> y  
  
    fail :: String -> m a  
    fail msg = error msg  
```