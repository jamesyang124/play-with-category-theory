# Cats Data Types

## semigroup

A semigroup is a set $$S$$ together with a binary operation "$${\displaystyle \cdot}$$" (that is, a function $${\displaystyle \cdot :S\times S\rightarrow S}$$) that satisfies the associative property:

For all $${\displaystyle a,b,c\in S}$$, the equation $${\displaystyle (a\cdot b)\cdot c=a\cdot (b\cdot c)}$$ holds.

#### semigroup law

1. associativity as above described.

    ```scala
    (x |+| y) |+| z = x |+| (y |+| z)
    ```

## 