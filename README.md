# writing

All of my public, non-academic writing.

## Paper and Book Reports
This is a collection of my (sometimes noticeably WIP) reports from reading papers and books.
The reports always share a common structure and sometimes contain my comments, thoughts, and questions.


### Semantics Engineering

#### [Semantics Engineering with Concrete Syntax](./reports/semantics-engineering-with-concrete-syntax.md)
This paper is about a tool (or rather a library) for semantics engineering—the same thing that the PLT REDEX is about, only this one uses a concrete syntax for the terms, context definitions and patterns.
The tool is called CREDEX.


### Haskell's Type System (and related to it)

#### [A Polymorphic Type System for Extensible Records and Variants](./reports/plymorphic-type-system-for-extensible-records-and-variants.md)
The paper introduces and defines a type system with row polymorphism using the `lacks` constraints.
The constraint is built on top of the *qualified types* and its constraint resolution strategy.
The paper generalizes the concept of rows both for extensible and polymorphic records as well as for variant types.


#### [A Short Introduction to System F and Fω](./reports/short-introduction-to-system-f-fω.md)
The paper builds the notion of *System Fω* starting from the Simply Typed Lambda Calculus (`λ->`).
In that sense, it serves as a very light introduction to all the related concepts.
It introduces and defines *types* and *kinds*, the *order* of the type, the concept of *parametric polymorphism* and a few more.
A notable nicety of the paper is that it gives operational semantics of the whole language and all the type-related extensions.
It also specifies the operational semantics of the type-language normalization and the reflexive, symmetric and transitive relation of type equality.


#### [A Solution to Haskell's MultiParameter Type Class Dillema](./reports/solution-to-haskells-multiparameter-type-class-dillema.md)
The paper proposes an alternative approach for resolving ambiguities in qualified types.
It does not require *Functional Dependencies*.
It requires a small change in the notion of ambiguity and a small change in the algorithm for resolving *constraints* **by instance**.
To be more specific, at some point, an ambiguous *constraint* needs to be resolved by *closing the world* and just picking a fitting instance for the constraint.


#### [How to make ad-hoc polymorphism less ad hoc](./reports/how-to-make-ad-hoc-polymorphism-less-adhoc.md)
The paper defines the concept of type classes as a way of ad-hoc overloading in Haskell.
The main portion of the paper talks about features of *type classes* as they are introduced in Haskell.
But when it comes to the language in the appendix, that is, the language on which the mechanics are defined and demonstrated, that has very little to do with Haskell.
It uses a similar form as [Parametric Overloading in Polymorphic Programming Languages](./reports/TODO.md).
That is, a form similar to `let` expressions for declaring a to-be overloaded operator and another one for overloading it.


#### [Implementing Type Classes](/reports/implementing-type-classes.md)
This paper describes the part of the type checker that deals with class-based overloading.
It does not go into much detail. It does not feature any code or a code-like description of an algorithm.
It describes the idea of using placeholders for the resolution of simple, single-parameter *type classes* during the type-checking process.


#### [Lexicaly-scoped Type Variables](./reports/lexically-scoped-type-variables.md)
The paper describes two possible approaches to support lexically-scoped type variables.
Those two approaches are quite different. The first one is described as a _type-lambda__ approach_ and the second one as a *type-sharing approach*. It is stated that the first approach is the one SML takes, whereas the second one is taken by GHC.
However, that is now not true—current GHC does not take this approach. Instead, it implements what seems to be the first one, the *type-lambda* one.

#### [Lightweight Extensible Records for Haskell](./reports/lightweight-extensible-records-for-haskell.md)
The paper talks about a *record system* for Haskell. It is discussed as a more expressive alternative to Haskell 98's very light syntactic sugar over positional constructors.
It mentions being strongly inspired by the work of Benedict R. Gaster and Mark P. Jones (Trex extension to Hugs compiler).
They propose a record system that would allow record extension, label selection, record-label update (both value and type level).
The syntax they use is similar to what can be seen in PureScript or Elm languages.
The notion of a *Row Type* is introduced, together with the notion of a *Row Kind*.
The paper is best read after [A Polymorphic Type System for Extensible Records and Variants](/reports/plymorphic-type-system-for-extensible-records-and-variants.md).

#### [Parametric Overloading in Polymorphic Programming Languages](./reports/parametric-overloading-in-polymorphic-programming-languages.md)
The paper defines a concept of parametric overloading similar to Haskell's type classes.
It seems equivalent to single-parameter type classes.
With a major exception of it being defined in a way that is very similar to what I have seen in [How to make ad-hoc polymorphism less ad hoc](/reports/how-to-make-ad-hoc-polymorphism-less-adhoc.md).
That is, there is a syntactic form like `let` expression for declaring an overloaded operator and another one for overloading it.
This, of course, does not allow for *recursive declarations*.
It also offers a simple unification-based inference algorithm.
The author explains that the inference differs from the *Algorightm W* by additional support for resolving the overloading.

## Series

TODO: the *Write You a Theorem Prover* series
