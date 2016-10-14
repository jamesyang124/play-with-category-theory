# Free Monoids

#### Endofunctor

http://stackoverflow.com/questions/10342876/differences-between-functors-and-endofunctors

A functor may go from one category to a different one, an endofunctor is a functor for **which start and target category are the same**.

The key point why a monad has to be an endofunctor, is that `join`, as it is called in Haskell, or `µ`, as it is usually called in category theory, is part of the definition of a monad:

```haskell
:t join
join :: Monad m => m (m a) -> m a
```

Also, identity function support a functor to be an endofunctor.

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

which is **natural** for all variables `X` in `C` and `Y` in `D`.