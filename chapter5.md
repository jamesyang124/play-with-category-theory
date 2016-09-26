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

# derivation, let z is a generic of m b
f : a -> m b
fmap: (a -> z) -> m a -> m z
fmap f x = m(x) = m(m(b))
join (fmap f x) = join m(m(b)) = m(b) == x >> f
```

We can found that `join (fmap f x)` is very similar as `map` then `flatten` in Scala as `flatmap` function.

The first function that the `Monad` type class defines is `return`. It's the same as `pure`. We use **bind** (`>>=`) to replace actual `join` implementation, `Monad` type class definition:

```hasekll
class Monad m where  
    return :: a -> m a  
  
    (>>=) :: m a -> (a -> m b) -> m b  
  
    (>>) :: m a -> m b -> m b  
    x >> y = x >>= \_ -> y  
  
    fail :: String -> m a  
    fail msg = error msg  
```

By above definition, a monad holds these **categorical laws**:

1. `join . M(join) = join . join`
2. `join . M(unit) = join . id = id`
3. `unit . f = M(f) . unit`
4. `join . M(M(f)) = M(f) . join`

<br>
#### First law

Translate `M(join)` as `fmap join`:

```haskell
join . M(join) = join . join

# right hand side
join (join [[[x]]]) = join [[x]] = [x]

# left hand side
# join: [[x]] -> [b]
# fmap: (a -> b) -> f(a) -> f(b)
# fmap: ([[x]] -> [b]) -> [[[x]]] -> [[b]]
join fmap join [[[x]]] = join [[x]] = [x]

# take Maybe as example
return :: a -> Maybe a
return x = Just x

join :: Maybe (Maybe a) -> Maybe a
join Nothing         = Nothing
join (Just Nothing)  = Nothing
join (Just (Just x)) = Just x
```

<br>
#### Second Law

```haskell
join . M(unit) = join . id = id

join . fmap return = join . return = id
```

<br>
#### Third Law

```haskell
unit . f = M(f) . unit

return . f = fmap f return
# left side
return . f x = M(f(x))
# right side
fmap f (return x) = M(f(x))
```

<br>
#### Forth Law

```haskell
join . M(M(f)) = M(f) . join

join . fmap (fmap f) = fmap f . join
# left sideu
join . fmap (fmap f) x = join M(M(f(x))) = M(f(x))
# right side
fmap f . join M(M(x)) = fmap f . M(x) = M(f(x)) 
```

<br>
#### Conclude Categorical Laws to functional Laws

```haskell
-- Categorical:
join . fmap join = join . join
join . fmap return = join . return = id
return . f = fmap f . return
join . fmap (fmap f) = fmap f . join

-- Functional:
# Left identity, f: a -> M b
return m >>= f = f m

return a >>= f 
= join (fmap f (return a))
= join (fmap f M(a)) 
= join M(f(a)) 
= join M(M(b)) 
= M(b) 
== f(a)

# derive with categorical laws:
return a >>= f 
= join (fmap f (return x))
= join (return (f x))      -- By law 3
= (join . return) (f x)
= id (f x)                 -- By law 2
= f x


# Right identity, m: M a
m >>= return = m
= join (fmap return m)
= join (return(M(a)))
= join (M(M(a)))
= M(a) 
== m

# derive with categorical laws:
m >>= return
= join (fmap return m)    -- By the definition of (>>=)
= (join . fmap return) m
= id m                    -- By law 2
= m


# Associativity
(m >>= f) >>= g = m >>= (\x -> f x >>= g)
= (join (fmap f m)) >> = g
= join (fmap g (join (fmap f m)))

m >>= (\x -> f x >>= g)

# derive with categorical laws:
(m >>= f) >>= g
= (join (fmap f m)) >>= g                          -- By the definition of (>>=)
= join (fmap g (join (fmap f m)))                  -- By the definition of (>>=)
= (join . fmap g) (join (fmap f m))
= (join . fmap g . join) (fmap f m)
= (join . join . fmap (fmap g)) (fmap f m)         -- By law 4
= (join . join . fmap (fmap g) . fmap f) m
= (join . join . fmap (fmap g . f)) m              -- By the distributive law of functors
                                                   -- F(f . g) = F(f) . F(g)
= (join . join . fmap (\x -> fmap g (f x))) m
= (join . fmap join . fmap (\x -> fmap g (f x))) m -- By law 1
= (join . fmap (join . (\x -> fmap g (f x)))) m    -- By the distributive law of functors
= (join . fmap (\x -> join (fmap g (f x)))) m
= (join . fmap (\x -> f x >>= g)) m                -- By the definition of (>>=)
= join (fmap (\x -> f x >>= g) m)
= m >>= (\x -> f x >>= g)                          -- By the definition of (>>=)
```