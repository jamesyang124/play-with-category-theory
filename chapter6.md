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