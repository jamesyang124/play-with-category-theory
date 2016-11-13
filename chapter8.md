# Type Basics II

#### Typeclass

A type signature for `+` operator:

```haskell
(+) :: a -> a -> a
```

With that type signature, `+` would take two arguments of the same type `a`. But this type signature indicates any type at all, and we know that we can't use `+` with two `Bool` values, or two `Char` values.

So, the actual type signature of `+` uses a language feature that allows us **to express the semantic restriction** that a can be any type as long as it is a number type:

```haskell
(+) :: (Num a) => a -> a -> a
```

`Num` is a **typeclass** — **a group of types which includes all types which are regarded as numbers**.

The `(Num a) =>` part of the signature restricts `a` to number types – or, in Haskell terminology, `a` is restricted as a **instances of `Num`**.

<br>
#### Polymorphic guesswork

You know that we don't need to specify types always because **the compiler can infer types**. You also know that we cannot mix types when functions require matched types.

```haskell
Prelude> :t (-7)
(-7) :: (Num a) => a
```

**So, `-7` is neither `Int` nor `Integer`**! Rather, **it is a polymorphic constant**, which can ***morph*** into any number type.

```haskell
:t 5.12
5.12 :: (Fractional t) => t
```

`5.12` is also a **polymorphic constant**, but one of the `Fractional` class, which is a subset of `Num`.

The type inference accounts for the class specifications: `-7` can be any `Num`, but there are extra restrictions for `5.12`, so that's the **limiting factor**.

With no other restrictions, `5.12` will assume the default `Fractional` type of `Double`, so `-7` will become a `Double` as well. `+` then proceeds normally and returns a `Double`.

<br>
#### Monomorphic trouble

The common division operator `/`. It has the following type signature:

```haskell
(/) :: (Fractional a) => a -> a -> a
```

Then assume we want to divide the length of a list:

```haskell
Prelude> 4 / length [1,2,3]
```

This leads error because `length` output of type signature is `Int`. So we need some type coercion function:

```haskell
fromIntegral :: (Integral a, Num b) => a -> b

4 / fromIntegral (length [1,2,3])
1.3333333333333333
```

In some ways, this issue is annoying and tedious, **but it is an inevitable side-effect of having a rigorous approach to manipulating numbers**. 

In Haskell, if you define a function with an `Int` argument, it will never be converted to an `Integer` or `Double`, unless you explicitly use a function like `fromIntegral`.

Last Example `==`:

```haskell
(==) :: (Eq a) => a -> a -> Bool
```

Like `+` or `/`, `==` is a **polymorphic function**. `Eq` is simply the class for types of values which can be **compared for equality**, and **it includes all of the basic non-functional types**.

<br>
#### Type declarations

Haskell has three basic ways to declare a new type:
- The `data` declaration, which defines new data types.
- The `type` declaration for type synonyms, that is, alternative names for existing types.
- The `newtype` declaration, which defines new data types equivalent to existing ones.

<br>
####`data` and constructor functions

`data` is used to define new data types mostly using existing ones as building blocks:

```haskell
-- name, year, month, day
data Anniversary = Birthday String Int Int Int
 -- spouse name 1, spouse name 2, year, month, day
                 | Wedding String String Int Int Int
```

This declares a new data type `Anniversary`, which can be either a `Birthday` or a `Wedding`. A `Birthday` contains one string and three integers and a `Wedding` contains two strings and three integers. The definitions of the two possibilities **are separated by the vertical bar**. 

With the declaration we also get two **constructor functions** for `Anniversary`; they are called `Birthday` and `Wedding`. 

Types defined by `data` declarations are often referred to as ***algebraic data types***.

As usual with Haskell, the case of the first letter is important: **type names and constructor functions must start with capital letters**.

```haskell
*Main> :t Birthday
Birthday :: String -> Int -> Int -> Int -> Anniversary
```

Meaning it's just a function which takes one String and three Int as arguments and **evaluates** to an `Anniversary`.

```haskell
smithWedding :: Anniversary
smithWedding = Wedding "John Smith" "Jane Smith" 1987 3 4
```

<br>
####Deconstructing types

```haskell
showDate :: Int -> Int -> Int -> String
showDate y m d = show y ++ "-" ++ show m ++ "-" ++ show d

showAnniversary :: Anniversary -> String

-- deconstuct Anniversary type to specifc Birthday type
showAnniversary (Birthday name year month day) =
   name ++ " born " ++ showDate year month day

showAnniversary (Wedding name1 name2 year month day) =
   name1 ++ " married " ++ name2 ++ " on " ++ showDate year month day
```

We can deconstruct the values built in our data types. `showAnniversary` takes a single argument of type `Anniversary`. 

Instead of just providing a name for the argument on the left side of the definition, however, **we specify one of the constructor functions and give names to each argument of the constructor**.

A more formal way of describing this *giving names* process is to say we are **binding variables**. **Binding** is being used in the sense of assigning a variable to each of the values so that we can refer to them on the right side of the function definition.


> Note that the parentheses around the constructor name and the bound variables are mandatory; **otherwise the compiler or interpreter would not take them as a single argument**. 
> 
> Also, it is important to have it absolutely clear that **the expression inside the parentheses is not a call to the constructor function, even though it may look just like one**.


<br>
#### `type` for making type synonyms

Aliasing purpose. Type synonyms are mostly just a convenience.

```haskell
type String = [Char]
```

#### Parameterized Types

Parameterized types are similar to **generic** or **template** types in other languages. **A parameterized type takes one or more type parameters** in declaration.

```haskell
data Maybe a = Nothing | Just a
```

This says that the type **Maybe takes a type parameter `a`**. You can use this to **declare**, for example:

```haskell
lookupBirthday :: [Anniversary] -> String -> Maybe Anniversary
```

You can parameterize `type` and `newtype` declarations in exactly the same way. 