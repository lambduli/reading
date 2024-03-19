# A Solution to Haskell's MultiParameter Type Class Dillema

authors: Carlos Camarao, Rodrigo Ribeiro, Lucilia Figueiredo, Cristiano Vasconcellos

year of publishing: 2009


## State of Reading
I have finished reading the paper. I think I understood most of it.
There are some small details on which I am not clear—like what is the precise approach authors want to take when it comes to context reduction.
To be more specific they use their approach and sort of "collapse" some of the constraints using a "by inst" rule, why does it seem like those constraints are left in the context?
Is that sort of an oversight? Is it intentional in the sense that they did not want to deal with those small details? Or is it intentional in the sense that they really want those constraints to be present even though they technically narrowed them down?
Also-do they not deal with context reduction?
Or is that covered in that short paragraph that I didn't read very carefully because it seemed to be talking about context simplification?
I think it might, I need to go over it more carefully.
If it does say what I think it might that would mean eliminating those "narrowed down" constraints.
So even though it seems that the algorithm does not simplify the context, it might be covered in the paper.

One last interesting thing - what about constraints that contain both kinds of variables-unreachable and reachable?
Aren't they technically a source of ambiguity?
Why does it seem like the paper does not care about those at all?
So it seems that even according to GHC those are not ambiguous? (The paper cites a GHC user's guide.)
But as it turns out, that rule is no longer true for GHC, it now has a different notion of ambiguity, somewhat cryptic-so I will need to investigate some more.
https://downloads.haskell.org/ghc/latest/docs/users_guide/exts/ambiguous_types.html?highlight=ambiguity


## Contents

The paper proposes an alternative approach for resolving ambiguities in qualified types.
It does not require *Functional Dependencies*.
It requires a small change in the notion of ambiguity and a small change in the algorithm for resolving *constraints* **by instance**.
To be more specific, at some point, an ambiguous *constraint* needs to be resolved by *closing the world* and just picking a fitting instance for the constraint.

> TODO: Mention the very interesting and useful rule for checking that an instance is valid and also that a class is valid. That stuff is great!

### Section 1: Introduction
It mentions *Constraint Set Closure* - an operation that is already employed in GHC.
According to the paper it is already used to identify ambiguities in qualified types with multi-param type class constraints.


## Section 2: Haskell's MPTF Dilema
This section demonstrates the issue with multi-param type class constraints - they can lead to even more ambiguities. The issue is, that some of those can't be solved even using *functional dependencies*.


## Section 3: Proposal
This section introduces a few interesting facts.

### Well Formed Type Contexts
Figure 3 shows the well-formedness of an instance declaration.
I think some details are missing in the definitions.

> TODO: The PDF has my detailed comments, rewrite them here.

### Constraint Set Closure
Informally speaking, it is a binary operation on a constraint set and a set of variables.
The result is a constraint set containing all such constraints that are reachable from at least one of those variables. That means - constraints that directly contain those variables (as free variables) or all constraints that are sort of *reachable* through those original variables (using however many steps).

For example consider the following constraint set:
`{ One a b c, Two c d e, Three x y z }`
The first two constraints are in the *constraint set closure* for any of the following variables: `{a, b, c, d, e}`. They are, however, not reachable through any of `{x, y, z}`.

### Constraint Set Satisfiability
The most important part of this sub-section is the fact that they use **substitution** to "admit" instances. This is in contrast to what [Typing Haskell in Haskell](./reports/thih.md) does—it uses matching, a one-way unification.

I think it uses unification because it needs to deal with those "problematic" constraints.
Suppose we have a constraint like `Foo a b` where both `a` and `b` are unreachable variables. If we go with matching—we would never be allowed to instantiate any of those two. But that is exactly what the strategy is about.
I am currently unclear on whether this means that unification is supposed to be used for what is known as `byInst` rule in *Typing Haskell in Haskell*. It seems to me that picking instances by unification could be isolated only to those problematic constraints and the rest could be done by matching. I am not sure about it, though.

### Well Formed Constrained Type
We get to another difference - a constrained type is now well formed if all its constraints, that do not contain any type variables and those that do contain **only** unreachable (from the type part) type variables, are uniquely satisfiable by the typing context.

So I think the idea is that normally having a constraint without variables or with only unreachable variables would make those constraints ambiguous (actually, a constraint having even just one unreachable variable would be enough, but let's first focus on what the paper deals with). With our approach, we just make sure that those problematic constraints are solvable right at the moment when we check for the well-formedness of such a type (usually during the generalization). So if we can **unify** every such problematic constraint with at most one instance—all is good. I believe we would discharge such a constraint and use that unification substitution to figure out what instance dictionary should be used for the method invocation/reference that gave rise to that constraint. However, the paper does not mention that—it does not go into that kind of detail.

As for the constraint that contains at least one unreachable variable together with at least one "reachable" variable - I think that might still be considered ambiguity. I am not sure right now, though.


## Section 4: Type System
This section just presents the type system (defined in a syntax-directed way).


## Section 5: Type Inference


## Section 6: Conclusion


## Conclusion
I think this paper presents a couple of useful details.
First, it gives a formal notion of a rule that checks whether an instance is well formed. I believe that the rule as given in the paper is incomplete, but it is a start.
Second, it describes the *constraint set closure* operation.

I am currently not planning to implement what the paper describes, maybe I'll change my mind in the future though.
