# Thinking Category Theory

This book is about the category theory. Most of the words and explanations are from `Haskell` related resources. We also leverage the concepts from `Haskell` to help us understand functional programming in advance.

<br>
> Category theory was invented in the early 1940s by Samuel Eilenberg and Saunders Mac Lane. It was specifically designed to bridge two seemingly disparate fields: **topology and algebra**. Topology is the study of abstract shapes such as 7-dimensional spheres;  algebra is the study of abstract equations such as $$y^{2z} = x^3 - x^{z2}$$.
>
> In 1980 Joachim Lambek showed that the types and programs used in computer science form a specific kind of category.
>  
> Category theory is incredibly efficient as a language for experimental design patterns, introducing formality while remaining flexible.
> 
> from David I. Spivak, ***Category Theory for Scientists***, 2013, 7-9.

<br>
Category theory, in general speaking, is about describing a collection of **objects** and **arrows**. It defines a category system which is useful for the type system in functional programming.

A category is an **algebraic data structure** which is comprised of **objects** and **arrows**. 

Two categories are the same if they have:

1. Same collection of objects. 
2. Same collection of arrows.
3. Same associative method of composing any pair of arrows.

Throughout this book, we prefer to use the term **morphism or function** instead of **arrow** to describe the relationship between mapping domain to co-domain.

With above introduction, let's get started to its definition in next chapter.


