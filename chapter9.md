# Classes and Types

#### Algebraic data type

https://en.wikipedia.org/wiki/Algebraic_data_type

**sum types** also called as **variant types**, ex: `Either`, `Boolean`. They both holding vary type constructors(function) ex: `True`, `False`, `Left`, `Right`.

> If we have sum types, then perhaps we also have product types, too. A product type is just a **tuple, or a constructor with more than one argument**:

```haskell
-- A product of an Integer and  String
(4, "Hello") :: (Integer, String)

-- A data type that is a product of a Char, an Integer, and Bool
-- M is type constructor function
data Multiple = M Char Integer Bool
```

A **sum type** is any type that has multiple possible representations, and we use | to separate each representation(type constructor). We can use these representations in our functions as if they were ordinary values.

https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/sum-types

>Notice that product types lack the ability to:

> - create new primitive data types that are not numbers, bools, or strings
> - represent a choice between two alternatives

https://wiki.haskell.org/Constructor#Type_constructor

```haskell
data Bool = True | False
data Tree a = Tip | Node a (Tree a) (Tree a)
```

`True`, `False` are nullary data constructor, `Node` take an argument as, data constructor.

Data constructors are first class values in Haskell and actually have a type. For instance, the type of the `Left` constructor of the `Either` data type is:

```
Left :: forall b a. a -> Either a b
```

**Data constructors as first class values**. As first class values, they may be passed to functions, held in a list, be data elements of other algebraic data types and so forth.

**Data constructors are not types.** As discussed above, they denote values. It is illegal to write `Node a (Node a) (Node a)` there, because the type is `Tree`, not `Node`.

http://stackoverflow.com/questions/5911267/what-are-sums-and-products-data-structures


#### Type vs Data constructor

https://en.wikipedia.org/wiki/Nullary_constructor

http://stackoverflow.com/questions/18204308/haskell-type-vs-data-constructor

In a data declaration, a type constructor is the thing on the **left hand side of the equals sign**. The data constructor(s) are the things on the **right hand side of the equals sign**. You use type constructors where a type is expected, and you use data constructors where a value is expected.

> Step back here a moment and take note of the similarities.
>
> - A data constructor is a "function" that takes 0 or more values and gives you back a new value.
> - A type constructor is a "function" that takes 0 or more types and gives you back a new type.

As the home stretch here, we can consider the Maybe a type. It's definition is

```haskell
data Maybe a = Nothing
             | Just a
```

Here, `Maybe` is a type constructor that returns a concrete type. `Just` is a data constructor that returns a value. `Nothing` is a data constructor that contains a value. If we look at the type of `Just`, we see that

```haskell
Just :: a -> Maybe a
```

In other words, `Just` takes a value of type a and returns a value of type Maybe a. If we look at the kind of `Maybe`, we see that

```haskell
Maybe :: * -> *
```
In other words, Maybe takes a concrete type and returns a concrete type.

Once again! The difference between a concrete type and a type constructor function. You can not create a list of `Maybes`. If you try to execute

```haskell
[] :: [Maybe]
```

you'll get an error. You can however create a list of `Maybe Int`, or `Maybe a`. That's because Maybe is a type constructor function, but a **list needs to contain values of a concrete type**. `Maybe Int` and `Maybe a` are concrete types (or if you want, calls to type constructor functions that return concrete types.)



<br>
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