# Functions and Currying

#### Anonymous Functions - lambdas

```haskell
sumStr = foldl (\ x str -> x + read str) 0.0
```

The expression in the parentheses is a lambda function. **The backslash is used as the nearest ASCII equivalent to the Greek letter lambda `λ`**. This lambda function takes two arguments, `x` and `str`.


