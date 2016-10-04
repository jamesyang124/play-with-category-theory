# Free Monoids

#### Endofunctor

http://stackoverflow.com/questions/10342876/differences-between-functors-and-endofunctors

A functor may go from one category to a different one, an endofunctor is a functor for **which start and target category are the same**.

The key point why a monad has to be an endofunctor, is that join, as it is called in Haskell, or `µ`, as it is usually called in category theory, is part of the definition of a monad:

```haskell
:t join
join :: Monad m => m (m a) -> m a
```

Also, identity function support a functor to be an endofunctor.