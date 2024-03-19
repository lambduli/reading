# A Short Introduction to System F and Fω

authors: Pablo Nogueira

year of publishing: 2006


## Contents
The paper builds the notion of *System Fω* starting from the Simply Typed Lambda Calculus (`λ->`).
In that sense, it serves as a very light introduction to all the related concepts.

It introduces and defines *types* and *kinds*, the *order* of the type, the concept of *parametric polymorphism* and a few more.

A notable nicety of the paper is that it gives operational semantics of the whole language and all the type-related extensions.
It also specifies the operational semantics of the type-language normalization and the reflexive, symmetric and transitive relation of type equality.


### Section 4: Adding primitive types and values
It defines a small and simple language with some primitive types and values.
It extends the STLC with some simple constructs like `if` expression and with types like `Bool` and *product* and *sum* types.


### Section 5: Adding parametric polymorphism: System F
This section introduces the notion of polymorphism through the universal abstraction (`Λ`) and gives the formal description of the extensions-grammar, type derivation rules and operational semantics.


### Section 6: Adding type operators: System Fω
This section introduces the notion of a *type operator*.

> NOTE: The paper uses a small lambda for type-level binders.

There is a short section on the meaning of *kinds*-the paper argues that they are not *types of types* in the presence of features like type classes and such.
It features a snippet showing this definition in Haskell:
```haskell
data Ord a => List a = Nil | Cons a (List a)
```

*To my best knowledge, that is currently not a valid Haskell; a point made, nonetheless.*

Figure 5 gives the **structural type equivalence** relation.

*I have doubts that the relation holds in the presence of the `Fix` combinator at the type level that is introduced in the next chapter.*


### Chapter 7: Adding general recursion
This section retrieves recursion for the language.
Two language primitives (combinators) are defined.
The first one exists at the value level (`fix`), and the second one at the type level (`Fix`).

We will go over the value-level recursion matters only briefly.
We will only note that the type of the `fix` as given by the paper should probably require the `τ` to have kind `*`.
The paper defines:
```haskell
fix :: ∀ (τ :: κ) . (τ -> τ) -> τ
```

Here is a short demonstration:
```haskell
-- this is what we want
len' :: List Int -> Int
-- so our function needs to be given itself
len :: (List Int -> Int) -> List Int -> Int
```

So the application `fix len` then means that the `τ` parameter is instantiated at `List Int -> Int`.
So we get: `(τ -> τ) -> τ` and then `((List Int -> Int) -> List Int -> Int) -> (List Int -> Int)`.


The paper then defines type-derivation rules for both fixpoint operators as well as operational semantics.

> It does not extend the notion of type equality, however.


## Conclusion
The paper is quite a lightweight read about the core principles behind *System F* and *System Fω*.
The missing comment on how the type checker deals with general recursion at the type level makes it less useful in the context of implementing a type checker for ones own language.

It seems worth noting that instead of the general recursion at the type level, we could go for the notion of `μ`-types described in part IV of *Types and Programming Languages*.
There, the notion of recursivity at the type level is explored in much more detail and both theory and intuition are given about dealing with recursive types.
