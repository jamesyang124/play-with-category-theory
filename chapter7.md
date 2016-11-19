# Type Basics I

#### Function has type

Values has its types, so did functions.

```haskell
:t not
Bool -> Bool
```

Then this `Bool -> Bool` is the **type signature** for `not` function.

For forming the type of a function that accepts more than one argument is simply to write down all the types of the arguments in a row, in order then link them all with `->`. 

Finally, add the type of the result to the end of the row and stick a final `->` in just before it.

The signature of `xor`:

```haskell
xor :: Bool -> Bool -> Bool
```

**Type signature** serve a dual role: they clarify the type of the functions both to human readers and to the compiler/interpreter:

```haskell
xor :: Bool -> Bool -> Bool
xor p q = (p || q) && not (p && q)
```

<br>
#### Type Signature

A type signature in `Haskell` represents the behavior of the morphism, ex: `readFile` function's type signature:

```haskell
readFile :: FilePath -> IO String
```

It means that it take a `FilePath` type then return a `IO String` type.

We can also find that fat arrow `=>` in another example:

```haskell
:t (==)  
(==) :: (Eq a) => a -> a -> Bool  
```

Everything before the `=>` symbol is called a **typeclass constraint**, We can read the previous type declaration like this:

> The equality function takes any two values that are of the same type and returns a `Bool`. The type of those two values must be a member of the `Eq` class (this was the class constraint).

<br>
#### Type inference

If type signatures tell the interpreter (or compiler) about the function type, how did we write our earliest Haskell code **without type signatures**? 

Well, when you don't tell Haskell the types of your functions and variables it figures them out through a process called type inference:

```haskell
-- We're deliberately not providing a type signature for this function
isL c = c == 'l'
```

Without a type signature, the type of `c` and the type of the result are not specified. However, the compiler knows that `l` is a `Char`, Since `c` and `l` are being compared with equality with `==` and both arguments of `==` must have the same type, **it follows that `c` must be a `Char`**.  Finally, since `isL c` is the result of `==` it must be a `Bool`.

```hasekll
isL :: Char -> Bool
isL c = c == 'l'
```

<br>
#### Types prevent errors

The role of types in preventing errors is central to typed languages. When passing expressions around you have to make sure the types match up like they did here. If they don't, you'll get **type errors** when you try to compile; your program won't pass the **typecheck**. This helps reduce bugs in your programs.

<br>
#### Constructor or Consing

You can build a list by `:` consing operator. The process of building up a list this way is often referred to as **consing**. This terminology comes from LISP programmers who invented the verb **to cons** (a mnemonic for **constructor**).

```haskell
Prelude> 1:0:numbers
[1,0,1,2,3,4]
Prelude> 2:1:0:numbers
[2,1,0,1,2,3,4]
Prelude> 5:4:3:2:1:0:numbers
[5,4,3,2,1,0,1,2,3,4]
```

In fact, Haskell builds all lists this way by consing all elements to the empty list, `[]`.

The commas-and-brackets notation are just syntactic sugar. So `[1,2,3,4,5]` is exactly equivalent to `1:2:3:4:5:[]`.

<br>
#### Polymorphic types

Counting how many things in a `list` should be independent of the type of `list`. Fortunately, we have a single function `length`, which works on all lists. How can that possibly work? As usual, checking the type of `length` provides a good hint:

```haskell
Prelude>:t length
length :: [a] -> Int
```

The `a` in the square brackets is not a type – remember that **type names always start with uppercase letters**. Instead, it is a **type variable**. When Haskell sees a type variable, it allows any type to take its place. In type theory (a branch of mathematics), this is called **polymorphism**.

Functions or values with only a single type are called **monomorphic**, and things that **use type variables to admit more than one type are polymorphic**.

Note that within a single type signature, **all cases of the same type variable must be of the same type**. For example:

```haskell
f :: a -> a
```

means that `f` takes an argument of any type and gives something of the **same type as the result**, as opposed to

```haskell
f :: a -> b
```

which means that `f` takes an argument of any type and gives a result of any type which **may or may not** match the type of whatever we have for `b`. 

**The different type variables do not specify that the types must be different, it only says that they can be different.** In this case, `a`, `b` may be the same type.

