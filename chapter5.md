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

Academically, a monad is a functor `M : C -> C`, along with two morphisms for every object `X` in `C`:

- $${\mathit  {unit}}_{X}^{M}:X\to M(X)$$
- $${\displaystyle {\mathit {join}}_{X}^{M}:M(M(X))\to M(X)}$$

It means it must have two functions, one is the unit function which is very similar to `pure` function in functor. Another `join` function transform a double-wrapped value `M(M(x))` to `M(x)`, **which flatten inner `M`context**.

**Any time you have some kind of structure $$M$$ and a natural way of taking any object $$X$$ into $${\displaystyle M(X)}$$, as well as a way of taking $${\displaystyle M(M(X))} $$ into $${\displaystyle M(X)}$$, you probably have a monad.**

We can define `Monad` in Haskell as:

1. a type constructor `m`
2. a function `return`
3. an operator `(>>=)` which is pronounced **bind**.

```haskell
return :: a -> m a
(>>=)  :: m a -> (a -> m b) -> m b
```

Aside from return and bind, there are two additional methods, `(>>)` spelled "then" and `fail`.

```haskell
m >> n = m >>= \_ -> n
```

The function `fail` handles pattern match failures in `do` notation. You are advised not to call `fail` directly in your code.

`(>>)` sequences two monadic actions when the second action does not involve the result of the first, which is a common scenario for monads such as `IO`.

```haskell
printSomethingTwice :: String -> IO ()
printSomethingTwice str = putStrLn str >> putStrLn str
```

For a concrete example, take the `Maybe` monad. The type constructor is `m = Maybe`, while `return` and `(>>=)` are defined like this:

```haskell
-- return type signature:
-- return :: a -> Maybe a
return x  = Just x

-- (>>=) type signature:
-- (>>=)  :: Maybe a -> (a -> Maybe b) -> Maybe b
m >>= g = case m of
            Nothing -> Nothing
            Just x  -> g x
```

`(>>=)` helps us pass **non-monadic values** to functions without actually leaving a monad. In the case of the `Maybe` monad, the monadic aspect is the qualifier that we don't know with certainty whether the value will be found: `Nothing` or `Just`.

`Control.Monad` defines `liftM`:

```hasekll
liftM :: (Monad m) => (a1 -> r) -> m a1 -> m r
```

As you might suspect, `liftM` is merely `fmap` implemented with `(>>=)` and `return`, `liftM` and `fmap` are therefore interchangeable.

Another function is `ap`:

```haskell
ap :: Monad m => m (a -> b) -> m a -> m b
```

Analogously to the other cases, `ap` is a monad-only version of `(<*>)`.

<br>
#### Three Monad laws

In Haskell, every instance of the `Monad` type class (and thus all implementations of bind `(>>=)` and `return`) must obey the following three laws:

```haskell
m >>= return     =  m                        -- right unit
return x >>= f   =  f x                      -- left unit

(m >>= f) >>= g  =  m >>= (\x -> f x >>= g)  -- associativity
```

The associativity of the **then** operator `(>>)` is a special case:

```haskell
(m >> n) >> o  =  m >> (n >> o)
```

Monadic composition:

```haskell
(f >=> g) >=> h  =  f >=> (g >=> h)

(>=>) :: Monad m => (a -> m b) -> (b -> m c) -> a -> m c
f >=> g = \x -> f x >>= g
```

<br>
#### Extended Definition

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

The `bind` is actually as a `map` then `flatten` operation as `flatMap` in Scala.

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

The left-hand side will **flatten the inner two layers into a new layer**, then flatten this with the outermost layer. The right-hand side will **flatten the outer two layers, then flatten this with the innermost layer**. 

Translate `M(join)` as `fmap join`:

```haskell
join . M(join) = join . join

# right hand side
join (join [[[x]]]) = join [[x]] = [x]

# left hand side
# join: [[x]] -> [b]
# fmap: (a -> b) -> f(a) -> f(b)
# fmap: (join: [[x]] -> [b]) -> [[[x]]] -> [[b]]
join fmap join [[[x1, x2], []], [[y1, y2], []]] 
= join [[x1, x2], [y1, y2]] 
= [x1, x2, y1, y2]

# famp join
fmap (\x -> x >>= id)  [[[1,2,3],[],[4]],[[5,6],[7]],[[8],[9]]]
[[1,2,3,4],[5,6,7],[8,9]]
```

![](https://upload.wikimedia.org/wikibooks/en/2/2f/Monad-law-1-lists.png)


Maybe is also a `monad`, with

```haskell
return :: a -> Maybe a
return x = Just x

join :: Maybe (Maybe a) -> Maybe a
join Nothing         = Nothing
join (Just Nothing)  = Nothing
join (Just (Just x)) = Just x
```

So if we had a **three-layered** `Maybe` (i.e., **it could be `Nothing`, `Just Nothing`, `Just (Just Nothing)` or `Just (Just (Just x))`**). The first law says that collapsing the inner two layers first, then that with the outer layer is exactly the same as collapsing the outer layers first, then that with the innermost layer.

Scala Example:

```scala
// fmap join
List(List(List(1), List(2), List(3, 4, 6), List(), List(4), List()), List(List(5), List())).map(_.flatten)
// res41: List[List[Int]] = List(List(1, 2, 3, 4, 6, 4), List(5))

// join
List(List(List(1), List(2), List(3, 4, 6), List(), List(4), List()), List(List(5), List())).flatten
// res39: List[List[Int]] = List(List(1), List(2), List(3, 4, 6), List(), List(4), List(), List(5), List())

// flatten as Haskell join, unwrap from inner context first
List(1, 2).flatten
// <console>:12: error: No implicit view available from Int => scala.collection.GenTraversableOnce[B].
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

# rethink about it
\x -> return (f x) = \x -> fmap f (return x)
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


# rethink about it
\x -> join (fmap (fmap f) x) = \x -> fmap f (join x)
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

<br>
#### Alternative

`Alternative` is a subclass of `Applicative` whose instances must define, at a minimum, the following two methods:

```haskell
class Applicative f => Alternative f where
  empty :: f a
  (<|>) :: f a -> f a -> f a
```

`empty` is an applicative computation with zero results, while `(<|>)` is a binary function which combines two computations.

```haskell
instance Alternative Maybe where
  empty               = Nothing
  -- Note that this could have been written more compactly.
  Nothing <|> Nothing = Nothing -- 0 results + 0 results = 0 results
  Just x  <|> Nothing = Just x  -- 1 result  + 0 results = 1 result
  Nothing <|> Just x  = Just x  -- 0 results + 1 result  = 1 result
  Just x  <|> Just y  = Just x  -- 1 result  + 1 result  = 1 result:
                                -- Maybe can only hold up to one result,
                                -- so we discard the second one.

instance Alternative [] where
  empty = []
  (<|>) = (++) -- length xs + length ys = length (xs ++ ys)
```

<br>
#### Example: parallel parsing

Traditional input parsing involves functions which consume an input one character at a time. That is, a parsing function takes an input string and chops off (i.e. "consumes") characters from the front if they satisfy certain criteria.

If the characters on the front of the string don't satisfy the given criteria, the parser has **failed**.

```haskell
digit :: Int -> String -> Maybe Int
digit i s | i > 9 || i < 0 = Nothing
          | otherwise      = do
  let (c:_) = s
  if [c] == show i then Just i else Nothing
```

Now, `(<|>)` can be used to run two parsers in parallel. 

That is, we use the result of the first one if it succeeds, and otherwise, we use the result of the second. **If both fail, then the combined parser returns `Nothing`**. We can use digit with `(<|>)` to, for instance, parse strings of binary digits:

```hasekll
binChar :: String -> Maybe Int
binChar s = digit 0 s <|> digit 1 s
```

<br>
#### MonadPlus

`MonadPlus` is a class which is closely related to `Alternative`:

```haskell
class Monad m => MonadPlus m where
  mzero :: m a
  mplus :: m a -> m a -> m a
```

**This definition is exactly like that of `Alternative`, only with different method names and the `Applicative` constraint being changed into `Monad`**. 

For types that have instances of both `Alternative` and `MonadPlus`, `mzero` and `mplus` should be equivalent to `empty` and `(<|>)` respectively.

One might legitimately wonder why the seemingly redundant `MonadPlus` class exists. Part of the reason is historical: just like `Monad` existed in Haskell long before `Applicative` was introduced, `MonadPlus` is much older than Alternative. 

<br>
####

`Alternative` and `MonadPlus` laws:

```haskell
-- empty is a neutral element
empty <|> u  =  u
u <|> empty  =  u
-- (<|>) is associative
u <|> (v <|> w)  =  (u <|> v) <|> w

-- exampl:
mzero `mplus` m  =  m
m `mplus` mzero  =  m
m `mplus` (n `mplus` o)  =  (m `mplus` n) `mplus` o
```

Plus the additional two laws, quoted by the `Control.Monad` documentation:

```haskell
mzero >>= f  =  mzero -- left zero
m >> mzero   =  mzero -- right zero
```

<br>
####Relationship with monoids

```haskell
class Monoid m where 
  mempty  :: m
  mappend :: m -> m -> m
  
instance Monoid [a] where
  mempty  = []
  mappend = (++)
```

Note the use of `[a]` instead of `[]` in the instance declaration. Monoids are **not necessarily "wrappers" of anything**, or parametrically polymorphic. 

`Alternative` is a separate type class because it captures a specific sort of monoid with distinctive properties − for instance, a binary operation.