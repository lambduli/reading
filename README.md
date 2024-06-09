# writing

All of my public writing.

## Paper and Book Reports
This is a collection of my (sometimes noticeably WIP) reports from reading papers and books.
The reports always share a common structure and sometimes contain my comments, thoughts, and questions.


### Semantics Engineering

#### [Semantics Engineering with Concrete Syntax](./reports/semantics-engineering-with-concrete-syntax.md)
This paper is about a tool (or rather a library) for semantics engineering—the same thing that the PLT REDEX is about, only this one uses a concrete syntax for the terms, context definitions and patterns.
The tool is called CREDEX.

______


### Mathematical Logic, Proof Theory, Type Theory, and Theorem Proving

#### [Propositions as Types](./reports/propositions-as-types.md) :star:
`#curry-howard correspondence` `#formulae as types` `#logic and computation`

This paper explains the correspondence between logic and computation known as the Curry-Howard correspondence.
It gives historical context for the discovery and its meaning.
It covers both the logic and the programming language sides of the correspondence.

　　

#### [Theorems for Free](./reports/theorems-for-free.md)
Reading now.

　　

#### [A Syntactic Approach to Type Soundness](./reports/syntactic-approach-to-type-soundness.md)
Reading now.

　　

<!-- #### [BASIC SIMPLE TYPE THEORY](./reports/basic-simple-type-theory.md)
TODO

　　 -->

#### [Type Theory and Formal Proof](./reports/type-theory-and-formal-proof.md)
A nice textbook covering the topics of type theory from the very basics.

　　

#### [Interactive Theorem Proving and Program Development — Coq'Art: The Calculus of Inductive Constructions](./reports/interactive-theorem-proving-and-program-development.md)
A textbook describing the Coq system and also the theory behind it.

　　

#### [Artificial Intelligence - A Modern Approach](./reports/artificial-intelligence-a-modern-approach.md) :star:
`#book` `#theory` `#automated reasoning` `#logical inference` `#resolution` `#automated theorem proving`

This is a great resource for studying many things. I have used it to learn about resolution theorem proving and all the smaller concepts involved with it.

　　

#### [Handbook of Practical Logic and Automated Reasoning](./reports/handbook-of-practical-logic-and-automated-reasoning.md) :star:
`#book` `#tutorial` `#automated reasoning` `#logical inference` `#resolution` `#automated theorem proving`

This book is a great resource for learning about mathematical logic, proving, and reasoning. It contains various chapters on different topics and goes quite deep. It also presents implementations of everything—which makes it extremely valuable.

　　

#### [Logical Frameworks — A Brief Introduction](./reports/logical-frameworks-a-brief-introduction.md) :star:
`#edinburgh logical framework` `#LF` `#λΠ`

This paper only focuses on the encoding of the first-order predicate logic. It cuts straight to the chase and skips the "syntactic only" encoding that seems to be going on in the [A Framework for Defining Logics](./reports/framework-for-defining-logics.md). This way, we get to the actual encoding that deals with the concept of logical validity right away.

　　

#### [A Framework for Defining Logics](./reports/framework-for-defining-logics.md)
`#edinburgh logical framework` `#LF` `#λΠ`

I think this is the foundational paper on LF. It defines the dependently typed lambda calculus and shows how one can encode first-order predicate logic and also second-order predicate logic into it.
There seems to be a big overlap with [An Overview of the Edinburgh Logical Framework](/reports/overview-of-the-edinburgh-logical-framework.md). This one is a bit larger, though. Another overlapping paper is [Logical Frameworks — A Brief Introduction](/reports/logical-frameworks-a-brief-introduction.md).

　　

#### [A Short Introduction to System F and Fω](./reports/short-introduction-to-system-f-fω.md)
`#λ-cube`

The paper builds the notion of *System Fω* starting from the Simply Typed Lambda Calculus (`λ->`).
In that sense, it serves as a very light introduction to all the related concepts.
It introduces and defines *types* and *kinds*, the *order* of the type, the concept of *parametric polymorphism* and a few more.
A notable nicety of the paper is that it gives operational semantics of the whole language and all the type-related extensions.
It also specifies the operational semantics of the type-language normalization and the reflexive, symmetric and transitive relation of type equality.

　　

#### [System Description: Twelf — A Meta-Logical Framework for Deductive Systems](./reports/a-meta-logical-framework-for-deductive-systems.md)
A lightweight description of the Twelf system.

　　

#### [SASyLF: An Educational Proof Assistant for Language Theory](./reports/an-educational-proof-assistant-for-language-theory.md)
TODO: reading, write soon

　　

#### [Lambda Cube Part 2](./reports/lambda-cube-pt-2.md)
`#λ-cube`

This is a short paper about two systems. The first part of the paper covers the system λ2 and the second one the **weak** system λω. This paper can be related to the [A Short Introduction to System F and Fω](/reports/short-introduction-to-system-f-fω.md). The current one might be even lighter.

　　

#### [An Overview of λ-Prolog](/reports/overview-of-lambda-prolog.md)
This paper is a nice, light exposure to the design and theory behind λ-Prolog.
It reads well and might serve as a nice introduction to the language and the topics behind it.

　　
______


### Haskell's Type System (and related to it)

#### [Typing Haskell in Haskell](./reports/typing-haskell-in-haskell.md) :star:
`#tutorial` `#inference` `#type classes` `#polymorphism`

A very detailed description of the implementation of a type checker for a simplified Haskell 98. Can be used as a primary resource for implementing HM inference and type classes.

　　

#### [Practical type inference for arbitrary-rank types](./reports/practical-type-inference-for-arbitrary-rank-types.md) :star:
`#tutorial` `#inference` `#bidirectional type-checking` `#higher-rank polymorphism`

This is one of the most important papers in this category. It gives a very detailed explanation of the extended type-checking algorithm found in (past) Haskell. It also gives a complete implementation in the appendix. This paper can serve as a basis for the implementation of a similar language with bi-directional type-checking.

　　

#### [A Polymorphic Type System for Extensible Records and Variants](./reports/plymorphic-type-system-for-extensible-records-and-variants.md) :star:
`#records` `#row polymorphism`

The paper introduces and defines a type system with row polymorphism using the `lacks` constraints.
The constraint is built on top of the *qualified types* and its constraint resolution strategy.
The paper generalizes the concept of rows both for extensible and polymorphic records as well as for variant types.

　　

#### [Lightweight Extensible Records for Haskell](./reports/lightweight-extensible-records-for-haskell.md)
The paper talks about a *record system* for Haskell. It is discussed as a more expressive alternative to Haskell 98's very light syntactic sugar over positional constructors.
It mentions being strongly inspired by the work of Benedict R. Gaster and Mark P. Jones (Trex extension to Hugs compiler).
They propose a record system that would allow record extension, label selection, and record-label update (both value and type level).
The syntax they use is similar to what can be seen in PureScript or Elm languages.
The notion of a *Row Type* is introduced, together with the notion of a *Row Kind*.
The paper is best read after [A Polymorphic Type System for Extensible Records and Variants](/reports/plymorphic-type-system-for-extensible-records-and-variants.md).

　　

#### [Implementing Type Classes](/reports/implementing-type-classes.md)
`#type classes`

This paper describes the part of the type checker that deals with class-based overloading.
It does not go into much detail. It does not feature any code or a code-like description of an algorithm.
It describes the idea of using placeholders for the resolution of simple, single-parameter *type classes* during the type-checking process.

　　

#### [How to make ad-hoc polymorphism less ad hoc](./reports/how-to-make-ad-hoc-polymorphism-less-adhoc.md)
`#type classes`

The paper defines the concept of type classes as a way of ad-hoc overloading in Haskell.
The main portion of the paper talks about features of *type classes* as they are introduced in Haskell.
But when it comes to the language in the appendix, that is, the language on which the mechanics are defined and demonstrated, that has very little to do with Haskell.
It uses a similar form as [Parametric Overloading in Polymorphic Programming Languages](./reports/TODO.md).
That is, a form similar to `let` expressions for declaring a to-be overloaded operator and another one for overloading it.

　　

#### [Parametric Overloading in Polymorphic Programming Languages](./reports/parametric-overloading-in-polymorphic-programming-languages.md)
`#ad-hoc overloading`

The paper defines a concept of parametric overloading similar to Haskell's type classes.
It seems equivalent to single-parameter type classes.
With a major exception of it being defined in a way that is very similar to what I have seen in [How to make ad-hoc polymorphism less ad hoc](/reports/how-to-make-ad-hoc-polymorphism-less-adhoc.md).
That is, there is a syntactic form like `let` expression for declaring an overloaded operator and another one for overloading it.
This, of course, does not allow for *recursive declarations*.
It also offers a simple unification-based inference algorithm.
The author explains that the inference differs from the *Algorithm W* by additional support for resolving the overloading.

　　

#### [A Solution to Haskell's MultiParameter Type Class Dillema](./reports/solution-to-haskells-multiparameter-type-class-dillema.md)
The paper proposes an alternative approach for resolving ambiguities in qualified types.
It does not require *Functional Dependencies*.
It requires a small change in the notion of ambiguity and a small change in the algorithm for resolving *constraints* **by instance**.
To be more specific, at some point, an ambiguous *constraint* needs to be resolved by *closing the world* and just picking a fitting instance for the constraint.

　　

#### [Parametric Type Classes (Extended Abstract)](./reports/parametric-type-classes.md)
The paper proposes a generalization to the type class system in Haskell, enabling Type Classes with multiple parameters (on top of the "placeholder variable") and still preserving principal type property.
They present a unification and type reconstruction algorithm.

　　

#### [Type Classes - Exploring the Design Space](./reports/type-classes-exploring-the-design-space.md)
This paper is a discussion of many design choices related to implementing type classes in a programming language (like) Haskell.

　　

#### [Type-checking multi-parameter type classes](./reports/type-checking-multi-parameter-type-classes.md)
This paper describes a different approach to support multi-parametric type classes. It is related to [Parametric Type Classes (Extended Abstract)](./parametric-type-classes.md).

　　

#### [Lexicaly-scoped Type Variables](./reports/lexically-scoped-type-variables.md)
The paper describes two possible approaches to support lexically-scoped type variables.
Those two approaches are quite different. The first one is described as a *type-lambda approach* and the second one as a *type-sharing approach*. It is stated that the first approach is the one SML takes, whereas the second one is taken by GHC.
However, that is now not true—current GHC does not take this approach. Instead, it implements what seems to be the first one, the *type-lambda* one.

　　

#### [Visible Type Application](./reports/visible-type-application.md) :star:
`#tutorial` `#polymorphism` `#visible type application`

This paper describes how Haskell supports the *visible type application* functionality.
It can be used as a primary resource for implementation.
