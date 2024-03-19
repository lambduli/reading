# Logical Frameworks — A Brief Introduction

author: Frank Pfenning

year of publishing: 2002


## Contents
This paper only focuses on the encoding of the first-order predicate logic. It cuts straight to the chase and skips the "syntactic only" encoding that seems to be going on in the [A Framework for Defining Logics](./reports/framework-for-defining-logics.md). This way, we get to the actual encoding that deals with the concept of logical validity right away.


### Chapter 3: Judgments and Deductions

#### Section 3.2: Natural Deduction
This section mentions a couple of interesting-sounding concepts. I'll list them here.

*The principle _of orthogonality_ is mentioned in the context of restricting the way each connective should be defined.

*The local soundness and _local completeness_ are mentioned and explained.

> Local soundness expresses that we should not be able to gain information by introducing a connective and immediately eliminating it.

> Local completeness expresses that we should not lose information by introducing a connective. That is, given a judgment there is some way to eliminate its principal connective and then re-introduce it to arrive at the original judgment.


At the end of this section, the paper also makes an interesting claim that I'd like to emphasize:
> [..] natural deduction is at heart an intuitionistic calculus. The symmetries of classical logic are better exhibited in sequent calculi.


#### Section 3.3: Deductions as Objects
This chapter talks about the encoding. Right at the beginning it is established that we will choose to encode the actual derivations/deductions as objects in our meta-language instead of just representing the derivable judgments.

We get a type constructor `nd` (for *natural deduction*). This gives us a family of types indexed by the values (propositions) that we are representing.

The `nd`'s kind is `ο -> type` where the `ο` is the type of formulae (while the `ι` is the type of objects in the FOL).

Here's an example of how the encoding works:

```
imp-elim : Π (A : ο) . Π (B : ο) . nd (imp A B) -> nd A -> nd B
```

So here's how to understand this. The constant `imp-elim` is used to eliminate the (proof of) implication with the proof of its premise. This is just a modus ponens.

So, for any two formulae `A` and `B` if we have a derivation proving *A ⟹ B* and we also have a derivation proving *A* then we can obtain a derivation proving *B*.

We can think of `imp` as a value-level constructor with the type `ο -> ο`.


**Universal Quantification**
This one is really interesting looking. The constructor/constant takes two lambdas. This is because one of those lambdas really is a proof of the universally quantified proposition whereas the other one is a function "coincidentally"—we represent a conditional (parametrized) proof using functions too.
In other words, the lambda corresponding to the parametrized proof is a representation of the part of the *ND* proof that assumes some "universal variable" while the other lambda is for when we need to eliminate this universally quantified statement. During elimination, we apply that lambda to an object (typed `ι`) getting a way to instantiate the universal and type it correctly too.

```
forall-i : Π (A : i -> ο) . (Π (a:i) . nd (A a)) -> nd (forall (λ x . A x))
```


At the end of this section, we get a summary and the principle *judgments as types* is mentioned.


#### Section 3.4: Higher-level Judgments
This section explains how the LF can also encode judgments of higher-level (relations on derivations). The LF supports this too since derivations are objects so they can be readily used as indexes to type families.


### Chapter 4: A Dependently Typed λ-calculus
This chapter gives a definition of the λΠ. It differs from the definition in [[A Framework for Defining Logics]] in that only admits canonical forms. I am not sure why its syntax does not feature type-level lambdas, however.


### Chapter 5: Conclusion
This chapter contains quite a few interesting references to related works. Here are some that I'd like to read soon:

- Natural Deduction as Higher-order Resolution (1989) (Paulson)
- Logic Programming in the LF Logical Framework (1991) (Pfenning)
- The Practice of Logical Frameworks (1996) (Pfenning)
- Logical Frameworks (2001) (Pfenning)


## Conclusion
This is a great read and makes reading the other two papers on LF a much better experience.
