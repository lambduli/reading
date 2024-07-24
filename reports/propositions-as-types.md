# Propositions as Types

author: Phil Wadler

year of publishing: 2014


## Contents
This paper explains the correspondence between logic and computation known as the Curry-Howard correspondence.
It gives historical context for the discovery and its meaning.
It covers both the logic and the programming language sides of the correspondence.


### Church, and the theory of computation
The first chapter gives a brief introduction to formal logic, a field that emerged in the 19th century. It briefly mentions Whitehead's and Russels' *Principia Mathematica* and its role in demonstrating the importance of formal logic (not only) to mathematics.

It then moves on to the historical developments of formal logic and the theory of computation.
It follows the development of Hilbert's *Entscheidungsproblem*. Talks about Gödel's proof of the incompleteness of arithmetics of natural numbers and the undecidability of the decision problem.

It emphasizes the necessity of the definition of "effectively calculable" or "algorithm" to show that there can not be a way to effectively calculate an answer to any logical proposition. It gives more historical context—putting on a timeline the invention of λ-calculus, Gödel's skepticism about it being a good definition of *effectively calculable*, Gödel's own *general recursive functions*, and Turing's *Turing Machines*.

One interesting thing about the origin of λ-calculus is that it was originally intended as a way to encode logic by Church. However, its discovered (Kleene and Rosser) inconsistency meant that it could not be used for this purpose.

On the other hand, Turing's machines are presented as a result of an analysis of the capabilities of the computer. Back then, those were people doing calculations with a pen and paper, but that is beside the point. Turing's machines were shown capable of doing any calculation that a person would be able to do.

There is some more interesting historical context on the interpersonal developments and relationships between the people involved mentioned in the paper.

The section also mentions the issue with λ-calculus and its self-application ability relating it to *Russel's paradox*. The solution to that, similar to Russel's theory of sets, was to restrict what can be done with some terms, introducing types.

There's an interesting connection being subtly mentioned in one paragraph between *Russel's paradox* and the non-terminating computation in λ-calculus. The non-termination of computation is understood to be the inconsistency manifesting.

Church introduced *Simply Typed λ-calculus*, a system that had a trivial *Halting Problem*. Every term in the system had a *Normal Form*—every computation terminated.
This certainty, however, costs some expressive power in the system. Not every effectively computable function is definable within the *Simply Typed λ-calculus*.


### Gentzen, and the theory of proof
Around the same time, Gerhard Gentzen introduced two new formulations of formal logic—*Natural Deduction* and *Sequent Calculus*.

He also introduced the universal quantifier *∀* to pair it with the *∃* introduced by Peano.

Gentzen's Natural Deduction demonstrates his new insight—proof rules forming pairs known as *introduction* and *elimination* rules.

His Sequent Calculus was introduced so that he could prove the *Subformula Principle*, however, it has its own use.

It is said that Natural Deduction has interesting symmetries with *intuitionistic logic* while Sequent Calculus has symmetries with *classical logic*.


### Propositions as Types
Around the same time still, Haskell Brooks Curry observed a certain connection between the theory of functions and the theory of implications.

> Every type of a function (A -> B) could be read as a proposition (A ==> B), and under this reading the type of any given function would always correspond to a provable proposition.

> Conversely, for every provable proposition there was a function with the corresponding type.

Curry, together with Feys extended the correspondence to include also terms and proofs and hinted at the relation between evaluation of terms and simplification of proofs.

Later, in 1969, William Alvin Howard circulated his manuscript which was later published in 1980. Howard pointed at the correspondence between Natural Deduction and Simply Typed λ-calculus making the third correspondence explicit and obvious.

The section then gives a very brief introduction to the intuitionistic reading of proofs mentioning, again, the historical context—naming Brouwer, Heyting, and Kolmogorov as founders of intuitionistic logic.

The second half of Howard's paper foretold the concepts that we now know as *dependent types*.

> With the introduction of dependent types, every proof in predicate logic can be presented by a term of a suitable typed lambda calculus.

This fact gave rise to numerous (software) systems for automated reasoning.


### Intuitionistic logic
This short section briefly introduces the concepts of intuitionism and intuitionistic logic.
It explains the idea that intuitionistic logic is constructive and the consequent rejection of the *law of excluded middle*.

The classical and intuitionistic logic are, however, closely related. There is a so-called *double-negation translation* such that a formula is provable in classical logic if and only if its translation is provable in intuitionistic logic.

Propositions as Types was first formulated for intuitionistic logic.


### Other logics, other computation
This section discusses the interesting fact that this principle also applies to other logics and computations.

The section gives some examples. It mentions parametric polymorphism and second-order λ-calculus being related to second-order propositional logic. It also mentions monads, temporal logic and its importance in formalization of various computing systems.
It also mentions linear logic and its known relation to linear and other sub-structural type systems.

It also mentions the study of category theory.


### Natural deduction
This section presents the fragment of the system of Natural Deduction. The goal is to present it in a way that makes the connection between it and the typed λ-calculi obvious.

It gives the usual rules for the small fragment of propositional logic and also gives a few rewrite rules for proof simplification.


### Lambda calculus
In this section, the Simply Typed λ-calculus of Church is given the same treatment.
Here, the main proposition is of the shape *M : A* where M is some arbitrary term and A is some type. The statement holds if it can be shown that A is the type of M.

Same as with Natural Deduction, derivations are represented as trees.

The section also discusses the evaluation through rewrites and its relation to proof simplification.


### Conclusion
The author concludes with a short thesis on the transcendental universality of systems like λ-calculus. Perhaps systems like these are so inherent to the nature of reasoning that they would work not only across our own universe but in other universes too.


### Appendix A: Howard on Curry-Howard
Correspondence between Philip Lee Wadler and William Alvin Howard on the topic of Formulae as Types.


## Conclusion
This paper is a great read. It is quite light on the technical side and gives enough historical context. I appreciate the mentions of all the dates and also the ages of some of the involved researchers, it makes it far more captivating to be able to put everything in the relevant perspective.
