# Monoid


https://wiki.haskell.org/Monoid

A monoid is when you have **an associative binary function and a value which acts as an identity with respect to that function**. 

When something acts as an identity with respect to a function, it means that when called with that function and **some other value, the result is always equal to that other value**. 

Let's see how `Monoid` typeclass defined:

```haskell
class Monoid m where  
    mempty :: m  
    mappend :: m -> m -> m  
    mconcat :: [m] -> m  
    mconcat = foldr mappend mempty 
```

Clearly we can have a monoid should have following properties:

```haskell
mempty `mappend` x = x
x `mappend` mempty = x
(x `mappend` y) `mappend` z = x `mappend` (y `mappend` z)
```

`List` is a monoid:

```haskell
instance Monoid [a] where  
    mempty = []  
    mappend = (++)
    
mconcat [[1,2],[3,6],[9]]  
[1,2,3,6,9]

# similar as: join fmap join
fmap mconcat [[[1,2],[],[4,7]],[[3,4]],[]]
[[1,2,4,7],[3,4],[]]

mconcat [[[1,2],[],[4,7]],[[3,4]],[]]
[[1,2],[],[4,7],[3,4]]
```

Others such as `Product`, `Sum`, `Maybe`. Let's check out `foldr` for `mconcat`: 

```haskell
:t foldr  
foldr :: (a -> b -> b) -> b -> [a] -> b 

foldr (*) 1 [1,2,3]  
6
```

And `foldMap`:

```haskell
foldMap :: (Monoid m, Foldable t) => (a -> m) -> t a -> m 

data Tree a = Empty | Node a (Tree a) (Tree a) deriving (Show, Read, Eq)

instance F.Foldable Tree where  
    foldMap f Empty = mempty  
    foldMap f (Node x l r) = F.foldMap f l `mappend`  
                             f x           `mappend`  
                             F.foldMap f r
```