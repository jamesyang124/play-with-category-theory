# Terms

#### Endofunctor

http://stackoverflow.com/questions/10342876/differences-between-functors-and-endofunctors

A functor may go from one category to a different one, an endofunctor is a functor for **which start and target category are the same**.

The key point why a monad has to be an endofunctor, is that `join`, as it is called in Haskell, or `µ`, as it is usually called in category theory, is part of the definition of a monad:

```haskell
:t join
join :: Monad m => m (m a) -> m a
```

Also, identity function support a functor to be an endofunctor.

#### Forgetful functor

A forgetful functor (also known as a **stripping functor**) **forgets** or drops some or all of the input's structure or properties **before** mapping to the output. 

Forgetful functors tend to have **left adjoints**, which are **free constructions**.

$$\operatorname {Hom}_{{{\mathbf  {Mod}}_{R}}}(\operatorname {Free}_{R}(X),M)=\operatorname {Hom}_{{{\mathbf  {Set}}}}(X,\operatorname {Forget}(M))$$

#### Free Object

https://en.wikipedia.org/wiki/Free_object#Free_functor

The most general setting for a `free` object is in category theory, where one defines a functor, the free functor, that is the left adjoint to the forgetful functor.

Informally, a free object over a set A can be thought of as being a "generic" algebraic structure over A

for any object B and any map between sets `f: X → F(B)`, there exists a unique morphism `g: A → B` such that `f = F(g) o i`. That is, the following diagram commutes:

![algebra](https://wikimedia.org/api/rest_v1/media/math/render/svg/e9dcb50f70871efebb0045c240dbf5567758dedc)


#### Natural transformation

Given categories `C`, `D`, two functors `F, G: C -> D` a natural transformation `$$α$$ : F -> G` and have the relationship as follows:

$$\array{ 
    F(x) 
    & 
    \stackrel{F(f)}{\to} 
    & 
    F(y) 
    \\ 
    \alpha_x\downarrow 
    && 
    \downarrow \alpha_y 
    \\ G(x) 
    & 
    \stackrel{G(f)}{\to} & G(y) 
  }
  \,.$$
  
  
#### Adjoint functor

This means that they are equipped with natural transformations $$η:1C→R∘L$$ (the unit) and $$ϵ:L∘R→1D$$ (the counit) satisfying the triangle identities.

[https://ncatlab.org/nlab/show/triangle+identities](https://ncatlab.org/nlab/show/triangle+identities)

An adjunction between categories `C` and `D` is a pair of functors,

$$F:{\mathcal {D}}\rightarrow {\mathcal {C}}$$ and $${\displaystyle G:{\mathcal {C}}\rightarrow {\mathcal {D}}}$$

and a family of bijections

$$\mathrm {hom} _{\mathcal {C}}(FY,X)\cong \mathrm {hom} _{\mathcal {D}}(Y,GX)$$

```sh
given `X` as input to `F` output Y  
given `y` as input to `G` output x
```

which is **natural** for all variables `X` in `C` and `Y` in `D`.

`F` is called a **left adjoint functor**, while `G` is called a **right adjoint functor**. The relationship **F is left adjoint to G (or equivalently, G is right adjoint to F**) is sometimes written:

$$F\dashv G$$

#### Home-set & Home-set functor

https://ncatlab.org/nlab/show/opposite+category

For a category `C`, the **opposite category** $$C^{op}$$ has the same objects as `C`, but a $$morphism$$ $$f:x→y$$ in $$C^{op}$$ is the same as a morphism $$f:y→x$$ in $$C $$, and a composite of morphisms `gf` in $$C^{op}$$ is defined to be the composite `fg` in $$C$$.

https://ncatlab.org/nlab/show/hom-functor

For $$C$$ a locally small category, its **hom-functor** is the functor:

> $$ hom:C^{op}×C→Set$$

for every pair $$x, y ∈ Ob(C)$$, a set $$Hom_C(x, y) ∈ Set$$. It is called the **hom-set** from `x` to `y`; its elements are called morphisms from `x` to `y`

A specified morphism denoted $$id_x ∈  Hom_x ∈ (x,x)$$, called the **identity morphism** on `x`.

we even used the current notation, referring to the set of functions `X -> Y` as $$Hom_{Set}(X, Y)$$.


## Type Basics II

#### Function has types

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

#### Type inference

If type signatures tell the interpreter (or compiler) about the function type, how did we write our earliest Haskell code **without type signatures**? 

Well, when you don't tell Haskell the types of your functions and variables it figures them out through a process called type inference:

```haskell
-- We're deliberately not providing a type signature for this function
isL c = c == 'l'
```

Without a type signature, the type of `c` and the type of the result are not specified. However, the compiler knows that `l` is a `Char`, Since `c` and `l` are being compared with equality with (`==`) and both arguments of (`==`) must have the same type, **it follows that `c` must be a `Char`**.  Finally, since `isL c` is the result of (`==`) it must be a `Bool`.

```hasekll
isL :: Char -> Bool
isL c = c == 'l'
```

#### Types prevent errors

The role of types in preventing errors is central to typed languages. When passing expressions around you have to make sure the types match up like they did here. If they don't, you'll get **type errors** when you try to compile; your program won't pass the **typecheck**. This helps reduce bugs in your programs.

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

#### Polymorphic types

Counting how many things in a `list` should be independent of the type of `list`. Fortunately, we have a single function `length`, which works on all lists. How can that possibly work? As usual, checking the type of `length` provides a good hint:

```haskell
Prelude>:t length
length :: [a] -> Int
```

The `a` in the square brackets is not a type – remember that **type names always start with uppercase letters**. Instead, it is a **type variable**.