# Functions and Currying

#### Anonymous Functions - lambdas

```haskell
sumStr = foldl (\ x str -> x + read str) 0.0
```

The expression in the parentheses is a lambda function. **The backslash is used as the nearest ASCII equivalent to the Greek letter lambda `λ`**. This lambda function takes two arguments, `x` and `str`.

<br>
#### Currying

In `Haskell`, functions are **curried** which means it only take a single input and return a partial function. The final result will be produced only when all input are curried.

```haskell
applyToIntegers :: (Integer -> Integer) -> [Integer] -> [Integer]
applyToIntegers _ [] = []

-- n:ns is a way of representing elements in List
applyToIntegers f (n:ns) = (f n) : applyToIntegers f ns
```

If all this abstraction confuses you, consider a concrete example: 

When we multiply `5 * 7` in Haskell, the `*` function doesn't just take two arguments at once, **it actually first takes the 5 and returns a new `5*` function**; and that new function then takes a second argument and multiplies that by 5.

So, **all functions in Haskell really take *only one* argument**. However, when we know how many intermediate functions we will generate to reach a final result, we can treat functions as if they take many arguments.

**The process of creating intermediate functions when feeding arguments into a complex function is called *currying***.

**Each step except the last of currying yields a partial applied function.**


<br>
#### Operators

In Haskell, **any function that takes two arguments and has a name consisting entirely of *non-alphanumeric* characters is considered an operator**. 

Unlike other functions, operators are normally used *infix* (written between the two arguments). All operators can also be surrounded with parentheses and then used prefix like other functions:

```haskell
-- these are the same:
2 + 4
(+) 2 4
```

<br>
####Sections

An operator within parentheses and flanked by one of its arguments:

```haskell
(2+) 4
(+4) 2
```

above is a new function in its own right. `(2+)`, for instance, has the type `(Num a) => a -> a`. We can pass sections to other functions, e.g. `map (+2) [1..4] == [3..6]`

**Sections are just syntactic sugar for lambda operations. I.e. `(+2)` is equivalent to `\x -> x + 2`**.

If you have a *normal prefix function* and want to use it as an operator, simply surround it with backticks:

```haskell
1 `elem` [1..4]
```

**This is called making the function *infix***. It's normally done for readability purposes: 

> `1 `elem` [1..4]` reads better than `elem 1 [1..4]`.

You can also define functions infix:

```haskell
elem :: (Eq a) => a -> [a] -> Bool
x `elem` xs = any (==x) xs
```

<br>
####Higher-Order Functions and Types

Example:

```haskell
-- simply currying
quickSort :: a -> a -> Ordering -> [a] -> [a]

-- revisit to higher-order function
quickSort' :: (a -> a -> Ordering) -> [a] -> [a]
```

Note that the parentheses surrounding `a -> a ->` Ordering are mandatory. They specify that `a -> a -> Ordering` forms a single argument that happens to **be a function**.

Without the parentheses, we would get `a -> a -> Ordering -> [a] -> [a]` which accepts **four arguments (none of which are themselves functions) instead of the desired two**, and that wouldn't work as desired.

Remember that the `->` operator is **right-associative**. Thus, our erroneous type signature `a -> a -> Ordering -> [a] -> [a]` means the same thing as `a -> (a -> (Ordering -> ([a] -> [a])))`.

<br>
#### Composition

The `.` composition operator is another higher-order function. It has the signature:

```haskell
(.) :: (b -> c) -> (a -> b) -> a -> c
```

<br>
#### Application

`$` is a curious higher-order operator. Its type is:

```haskell
($) :: (a -> b) -> a -> b
```

It takes a function as its first argument, and **all it does is to apply the function to the second argument**, so that, for instance:

```haskell
(head $ "abc") == (head "abc")
```
You might think that `$` is completely useless! However, there are two interesting points about it.

1. `$` has very low precedence, **unlike regular function application which has the highest precedence**. That means we can avoid confusing **nesting of parentheses** by breaking precedence with `$`

  For a tiny sample, first consider the `inits` function, defined in the module `Data.List`. Quoting the documentation, it "returns all initial segments of the argument, shortest first", so that:

  ```haskell
  Prelude Data.List> inits [1,2,3]
  [[],[1],[1,2],[1,2,3]]
  
  myInits :: [a] -> [[a]]
  myInits = map reverse . scanl (flip (:)) []
  ```

  We write a non-point-free version of `myInits` **without adding new parentheses**:
  
  ```haskell
  myInits :: [a] -> [[a]]
  -- $ xs always execute in low precedence
  myInits xs = map reverse . scanl (flip (:)) [] $ xs
  ```

  Lets break these into little pieces:
  
  ```haskell
  :t (:)
  (:) :: a -> [a] -> [a]
  
  1 : []
  [1]
  
  [2] : []
  [[2]]
  
  [3, 4] : [[]]
  [[], [3, 4]]
  
  -- so a -> [a] -> [a], a is some type: Num or List. 
  -- if a is List type, then [a] can be [] which is a empty list in any type a
  -- That's why [3, 4] : [] == [[3, 4]]
  
  [3, 4] : [5]
  -- Constraint to Num [t] in right hand side => [[], [1,2]] or [], [t] allowed
  
  [] : [1,2,3]
  -- [1,2,3] could not type infer to each Num element a as empty list []
  -- Constraint error
  
  [] : []
  [[]]
  ```
  
  After explore `(:)`, we can check `flip`:
  
  ```haskell
  :t flip
  flip :: (a -> b -> c) -> b -> a -> c
  -- it flips first two arguments in function f's input
  
  flip fmap [1,2,3] (*2)
  (2,4,6,6)
  -- for readability and precedence check, better to surround it with parenthesis
  (flip map) [1,2,3] (*2)
  ```
  
  Now we examine the `scanl`:
  
  ```haskell
  :t scanl
  scanl :: (b -> a -> b) -> b -> [a] -> [b]
  -- it gets a init type b, and function f, and list a,
  -- put init type b to list [b] first, then scan each a from left to right, 
  -- prepend it by (:), finally append to list [b]
  
  1 (:) [5, 6]
  -- prepended to [1, 5, 6]
  
  scanl (flip (:)) [] [1,2,3]
  [[], [1], [2,1], [3,2,1]]
  
  -- because we could not directly use (:)
  -- since its type signature is: a -> [a] -> [a]
  -- so we flip to make it match scanl's type signature
  
  scanl (flip (:)) [] [1,2,3] == scanl (\x y -> y : x) [] [1, 2, 3]
  
  scanl (\init elem -> init + elem) 0 [1,2,3,4]
  [0, 1, 3, 6, 10]
  ```
  
  We can combine above computations with `(.)` composition. Here we must use `$` for lower precedence. The evaluation will consume inputs as soon as possible, which can not keep its type signature: 
  
  ```haskell
  :t (.)
  (.) :: (b -> c) -> (a -> b) -> a -> c
  
  reverse . scanl (flip (:)) [] [1,2,3]
  -- could not match expected type "a -> [a1]"
  -- possible cause: "scanl" is applied to too many arguments
  
  :t scanl (flip (:)) []
  scanl (flip (:)) [] :: [a] -> [[a]]
  
  reverse . scanl (flip (:)) [] $ [1,2,3] ==   (reverse . scanl (flip (:)) []) [1,2,3]
  -- by above type signature, we need [a] for last input
  -- we can use $ application operator as laziest evaluated input to reduce paremthesis wrapping
  
  map reverse . scanl (flip (:)) [] $ [1,2,3] == (map reverse . scanl (flip (:)) []) [1,2,3]
  
  :t map reverse . scanl (flip (:)) []
  (map reverse . scanl (flip (:)) []) :: [a] -> [[a]]
  
  -- another example
  (scanl (flip (:)) []) $ [1,2]
  [[],[1],[2,1]]
  
  map reverse [[],[1],[2,1]]
  [[],[1],[1,2]]
  
  fmap reverse . (\x -> [x]) $ [1,2,3,4]
  [[4,3,2,1]]

  Prelude> (fmap reverse . fmap (\x -> [x])) [1,2,3,4]
  [[1],[2],[3],[4]]

  Prelude> fmap (reverse . (\x -> [x])) [1,2,3,4]
  [[1],[2],[3],[4]]

  Prelude> (fmap reverse) . (fmap (\x -> [x])) $ [1,2,3,4]
  [[1],[2],[3],[4]]
  
  Prelude> fmap reverse . (\x -> [x]) $ [1,2,3,4]
  [[4,3,2,1]]
  
  fmap reverse . (\x -> [x]) $ [1,2,3,4] == fmap reverse [[1,2,3,4]]
  fmap reverse . (\x -> [x]) $ [1,2,3,4] == (fmap reverse . (\x -> [x])) [1,2,3,4]
  
  Prelude> fmap (reverse . (\x -> [x])) $ [1,2,3,4]
  [[1],[2],[3],[4]]
  
  :t fmap (reverse . (\x -> [x]))
  fmap (reverse . (\x -> [x])) :: Functor f => f a -> f [a]

  Prelude> :t fmap reverse . (\x -> [x])
  fmap reverse . (\x -> [x]) :: [a] -> [[a]]
  ```

2. `$` is just a function which happens to apply functions, and functions are just values, we can write **intriguing expressions** such as:

  ```haskell
  map ($ 2) [(2*), (4*), (8*)]
  [4, 8, 16]
  ```
  
So the application function `$` is pretty much like a lazy evaluation version of function application.

<br>
#### `id` and `const`

Finally, we should mention two functions which, **while not higher-order functions themselves**. `id` stands for identity function, it return the input itself anyway:

```haskell
id :: a -> a
```

Similar to `id`, `const` takes two arguments, **discards the second and returns the first**:

```haskell
-- a -> b -> a == a -> (b -> a) by currying
const :: a -> b -> a

Prelude> const "Hello" "world"
"Hello"
```

Seen as a function of one argument, `a -> (b -> a)` by currying, it returns a constant function, which **always returns the same value no matter what argument it is given**.

`id` and `const` might appear worthless at first. However, when dealing with higher-order functions **it is sometimes necessary to pass a dummy function**, be it one that does nothing with its argument or one that always returns the same value. `id` and `const` give us convenient dummy functions for such cases.

<br>
#### `uncurry` and `curry`

As the name suggests, `uncurry` is a function that undoes currying; **that is, it converts a function of two arguments into a function that takes a pair as its only argument**.

```haskell
uncurry :: (a -> b -> c) -> (a, b) -> c

Prelude> let addPair = uncurry (+)
Prelude> addPair (2, 3)
5
```

