# Classes and Types

#### Classes and instances

The point of **type classes** is to ensure that certain operations will be available for values of chosen types. 

For example, if we know a type belongs to (or, to use the jargon, instantiates) the class `Fractional`, then we are guaranteed to, among other things, be able to perform real division with its values.

Recall that we can define **algebraic data type** by `data`, which is not the same as define a type by typeclass `class`.

Typeclass define the operations for the instance of that typeclass.

```haskell
class  Eq a  where
   (==), (/=) :: a -> a -> Bool

       -- Minimal complete definition:
       --      (==) or (/=)
   x /= y     =  not (x == y)
   x == y     =  not (x /= y)
```

The definition states that if a type `a` is to be made **an instance of the class `Eq`** it must support the functions `==` and `/=` - the class methods - both of them having type `a -> a -> Bool`. With a class defined, we proceed to **make existing types instances of it**. 

An arbitrary example of **an algebraic data type made into an instance of `Eq` by an instance declaration**:

```haskell
data Foo = Foo {x :: Integer, str :: String}
 
instance Eq Foo where
   (Foo x1 str1) == (Foo x2 str2) = (x1 == x2) && (str1 == str2)
```

And now we can apply `==` and `/=` to Foo values in the usual way.

<br>
#### Deriving

To avoid large amounts of boilerplate for every new type, Haskell has a convenient way to **declare the *obvious* instance definitions using the keyword `deriving`** for built-in classes:

```haskell
-- instance declaration is formed by deriving keyword
data Foo = Foo {x :: Integer, str :: String}
    deriving (Eq, Ord, Show)
```

You can only use deriving with a limited set of built-in classes:

- `Eq`
- `Ord`
- `Enum`
- `Bounded`
- `Show` - defines the function show, which converts a value into a string, and other related functions.
- `Read` 

The precise rules for deriving the relevant functions are given in the language report.

<br>
#### Class inheritance

Classes can inherit from other classes. For example, here is the main part of the definition of `Ord` in Prelude:

```haskell
class  (Eq a) => Ord a  where
    compare              :: a -> a -> Ordering
    (<), (<=), (>=), (>) :: a -> a -> Bool
    max, min             :: a -> a -> a
```

A class can inherit from several other classes: **just put all of its *superclasses* in the parentheses before the `=>`**.

```haskell
class  (Num a, Ord a) => Real a  where
    -- | the rational equivalent of its real argument with full precision
    toRational          ::  a -> Rational
```

<br>
#### Type constraints

```haskell
(+) :: (Num a) => a -> a -> a
```

`(Num a) =>` is a type constraint, which **restricts the type `a` to instances of the class `Num`**.

You can put several limits into a type signature like this:

```haskell
foo :: (Num a, Show a, Show b) => a -> a -> b -> String
foo x y t = 
   show x ++ " plus " ++ show y ++ " is " ++ show (x+y) ++ ".  " 
```

Beyond simple type signatures, type constraints can be introduced in a number of other places:

- `instance` declarations: typical with parametrized types

- `class` declarations: constraints can be introduced in the method signatures in the usual way for any type variable other than the one defining the `class`

- `data` declarations: where they act as constraints for the **constructor signatures**.

Type constraints in data declarations are less useful than it might seem at first. Consider:

```haskell
data (Num a) => Foo a = F1 a | F2 a String
```

Here, `Foo` is a type with two constructors, both taking an argument of a type `a` which must be in `Num`. However, **the `(Num a) =>` constraint is only effective for the `F1` and `F2` constructors, and not for other functions involving `Foo`**.

```haskell
-- duplicating type constraint (Num a)
fooSquared :: (Num a) => Foo a -> Foo a
fooSquared (F1 x)   = F1 (x * x)
fooSquared (F2 x s) = F2 (x * x) s
```

Even though the constructors ensure a will be some type in `Num` **we can't avoid duplicating the constraint in the signature of `fooSquared`**.