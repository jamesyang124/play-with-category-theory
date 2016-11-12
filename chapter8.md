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