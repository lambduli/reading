# Parametric Type Classes (Extended Abstract)

authors: Kung Chen, Paul Hudak, Martin Odersky

year of publishing: 1992


## Contents
The paper proposes a generalization to the type class system in Haskell, enabling Type Classes with multiple parameters (on top of the "placeholder variable") and still preserving principal type property.

> It seems to me that there will be some connection to the concept of *functional dependencies*.

They present a unification and type reconstruction algorithm.


### Section 2 - Parametric Type Classes
It introduces the distinction between the actual type-class parameter (through a placeholder variable) and possibly none or many additional parameters. They are said to be determined from the placeholder one.
This reminds me of *functional dependencies*.

It also talks about consistency. A property of *sets of predicates*. That would include type contexts.
The consistency says that if we have `(T :: Sequence a, T :: Sequence b)` then it implies `a = b`.
It seems to me that this is in line with the idea of the type parameters being uniquely determined by the placeholder type.


### Section 3 - The Type System of Parametric Classes
It introduces a few restrictions on types standing at type-parameter positions.
Some of them seem to be too restrictive compared to Haskell 98's restrictions for those.

There are also a bunch of definitions regarding the set operations happening on contexts.
Contexts are something like restricted sets. Those restrictions, however, must be preserved when a substitution is applied to some context. That is discussed and covered here.

One of the most critical parts of the whole paper is the paragraph where the instance declarations are limited.
It is defined, in *(b)*, that all free type variables occurring at the placeholder positions must also occur freely at the argument position.
This has a critical consequence on the rest of the system. It means that the *instantiation* and *generalization*, as described in the next section, will work. Were this restriction not put in place, we could have type schemes like `∀ α . { α :: Cl (x, y) } . κ (x, y)`. That is—there could be at least one type-variable freely occuring at the class-parameters position but not at the class-argument position. This would lead to such variable(s) not being generalized. This would lead to a critical sort of monomorphisation error during the unification.
To be precise—when such a type would be instantiated, those un-generalized type variables would be shared among all types (that are instantiations of such a faulty type scheme).

It also presents a non-deterministic type system for the small language.


### Section 4 - A Deterministic Type Inference System
Aside from a sort of cryptic rule `let'`, all seems to be clear.
The lack of clarity comes from the presence of three instance predicate contexts. As far as I can tell, the idea is the following:

`C'` is the result of deriving a type for `e'`. It can be thought of as an output of that "sub-call".
The `C'` is then also used in the _generalization_ of the inferred type. This leads to some of the predicates being released from the context. The resulting context is denoted `C''`.
It is stated, that `C''` must be *contained* within `C`.
This should make sense.
The `C''` contains predicates that could not be generalized - they are relevant on the outer level.
Those predicates should be in the resulting context - `C`. They should also, for the purpose of unification, be part of the typing derivation of the *body* of `let` expression.

> The way the type system is formalized is sort of direction-agnostic. This means that to sort of visualize the order, we must do a little bit of "mental processing".


Another interesting point is the `gen` function.
It demonstrates how a concrete type `τ` is generalized into a typing scheme `σ`.

There are also some more lemmas and theorems used to compare both systems.
I didn't inspect them closely.


### Section 5 - Unification and Type Reconstruction

#### Context Preserving Unification
This subsection presents an algorithm for unification that handles predicates too.

> I will refrain from more detailed desccription for now.

It seems to make good sense.

The part that I want to talk about is the function `mgn'`, more specifically its second part.
The part that handles a type application of `κ τ'` seems to be treating the `κ` in the *type application* and in the instance declaration (at the placeholder position) as identical. That means that this algorithm does not account for predicates like `m Int :: Foo ()` (in the syntax of the described language).
This omission has a good reason—the syntactical rules of Haskell for instances disallow forms like this one: `instance Foo (m Int)` or `instance Foo (m Int)`.
This means that for the set of predicates to be considered a context—those must not occur. In other words, all the predicates in the context have the `κ` known.

A question that arises—what if we are more liberal with instance declarations, like the `FlexibleContexts` extension?

A detailed description of the figure 6:
> TODO: Write! (There are quite a few notes in the PDF.)

Detailed description of the figure 7:
> TODO: Write! (There are quite a few notes in the PDF.)


### Section 6 - Ambiguity Revisited
This section compares the ambiguity in a hypothetical multi-argument-type-class system with the situation in the system introduced by the paper.

It also introduces a super-super-class `TC` to be used as a sort of "trick" to make some types more expressive in a fairly elegant way.

It defines a notion of *weak ambiguity* and *strong ambiguity* for a type variable.


### Section 7 - From Monads to Lists
I haven't read this section very carefully, just skimmed through it.
I don't think it is relevant to my work.


### Section 8 - Conclusion
This section mentions [How to make ad-hoc polymorphism less ad hoc](./how-to-make-ad-hoc-polymorphism-less-adhoc.md). It talks about how the system described in the paper can be translated/compiled. That matter is fairly well covered in the mentioned paper as well as in [Implementing Type Classes](./implementing-type-classes.md).


## Conclusion
I consider the paper to be fairly relevant in terms of the implementation.
There is quite a bit of theory. Those parts are, however, still fairly readable.

I especially appreciate the description of the predicate resolution as part of the unification. It seems to me that the algorithm could be simplified and used for single-parametric type classes as well.

In such a case, it seems important to sort out the issue with a possibly cyclic context.
