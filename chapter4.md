# Monoid


#### Semigroup

A algebraic structure which have a associative binary operation. The binary operation of a semigroup is most often denoted **multiplicatively**: `x . y` 

Then `(x . y) . c === x . (y . c)` holds.

Monoid is a semigroup since its `identity` or `empty` function holds this rule.

- A left identity of a Semigroup is `s` an element $${\displaystyle}$$ e such that for all $${\displaystyle x}$$ in $${\displaystyle S}$$, $${\displaystyle ex=x}$$

- A right identity is an element $${\displaystyle f}$$ such that for all $${\displaystyle x}$$ in $${\displaystyle S}$$, $${\displaystyle xf=x}$$.

Left and right identities are both called **one-sided identities**.

A **two-sided identity** (or just identity) is an element which is both a left and right identity. 

**Semigroups with a two-sided identity are called monoids**.

**A semigroup may have at most one two-sided identity**. If a semigroup has a two-sided identity, then the two-sided identity is the only one-sided identity in the semigroup. 

If a semigroup has both a left identity and a right identity, then it has a two-sided identity (which is therefore the unique one-sided identity).

#### Monoid

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
    
foldr :: (a -> b -> b) -> b -> [a] -> b 
# takes a binary operator (a -> b -> b)
# and init value b, list [a]
# reduces the list using the binary operator, from right to left:
# foldr f z [x1, x2, ..., xn] == x1 `f` (x2 `f` ... (xn `f` z)...)

foldl' :: forall a b. (b -> a -> b) -> b -> [a] -> b
# strict version of fold left
# applied to a binary operator, 
# a starting value (typically the left-identity of the operator), 
# and a list, reduces the list using the binary operator, from left to right:
# foldl f z [x1, x2, ..., xn] == (...((z `f` x1) `f` x2) `f`...) `f` xn
```

Clearly we can have a monoid should have following properties:

```haskell
mempty `mappend` x = x
x `mappend` mempty = x
(x `mappend` y) `mappend` z = x `mappend` (y `mappend` z)
```

Some laws:

```haskell
# -- associativity
(x <> y) <> z = x <> (y <> z) 

# -- left identity
mempty <> x = x

# -- right identity
x <> mempty = x               
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