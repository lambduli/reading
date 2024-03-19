# Handbook of Practical Logic and Automated Reasoning


author: John Harrison

year of publishing: 2009


## State of Reading

I have read a few chapters. There is still much to read.


## Contents
This book is a great resource for learning about mathematical logic, proving, and reasoning. It contains various chapters on different topics and goes quite deep. It also presents implementations of everything—which makes it extremely valuable.


### Chapter 2: Propositional Logic
This chapter introduces *Propositional Logic*. There are sections on syntax, semantics, important terminology, and so on.

It defines *validity*, *satisfiability*, *tautology*.

It also defines *Negation Normal Form* and *Disjunctive and Conjunctive Normal Forms*.


#### 2.8: Definitional CNF

> TODO: write!
#### 2.9: The Davis-Putnam Procedure

> TODO: write!


### Chapter 3: First-Order Logic
This chapter treats *First Order Logic*. It gives its semantics, explains *interpretation* and *valuation*.

It defines *Prenex Normal Form* and *Skolemisation*.

#### 3.7: Canonical Models

> TODO: write!


#### 3.9: Unification

> TODO: write!


#### 3.10: Tableaux

> TODO: write!


#### 3.11: Resolution
The section starts with asserting an interesting proposition:

> In fact, given a set of propositional clauses, if we form all resolvents on any literal p and then discard all formulas involving p or −p, the result- ing set is equisatisfiable with the original [..]

It is not entirely clear to me what to take from it. Assuming that the sentence should have said `[..] and then discard all **clauses** involving p or -p [..]` I could take from it the following.

We pick any literal *P* and find all the pairs of clauses that would allow to use the *resolution rule*. We apply the *resolution* to those pairs. Because we are using unification, sometimes there will be multiple ways to do so. Here is an example of two such clauses:

`P(x, y) ∨ P(a, a)` and `¬P(t, t) ∨ ¬P(k, l)`

We have to pick the literal `P(_, _)` and the only two clauses present. Now, we can decide to unify `P(x, y)` with either `¬P(t, t)` or `¬P(k, l)`. We can also unify `P(a, a)` with either `¬P(t, t)` or `¬P(k, l)`.

- If we go for the first one, we get this resolvent: `P(a, a) ∨ ¬P(k, l)`.

- If we go for the second one, we get this resolvent: `P(a, a)` ∨ `¬P(t, t)`.

- If we go for the third one, we get this resolvent: `P(x, y) ∨ ¬P(k, l)`.

- If we go for the fourth one, we get this resolvent: `P(x, y) ∨ ¬P(t, t)`.

The assertion from the book seems to be saying that once we do this (obtaining all), we get to discard the original clauses.

Looking at this demonstration, it seems to make sense. It seems to me that what we've done is some sort of "distributing out" all the clauses with all the other clauses (assuming they qualified for the resolution).

The example formula seems to be contradictory. Can we derive the *empty clause* from it?

This example is actually exactly the one discussed on page 181 of the book. The book claims that there is no way to derive the empty clause using the naive resolution with the *most general unifier*—the *mgu* is way too general for it to work.

It turns out, it's not as much about the generality of the *mgu* (even though that's what one of the sentences at the beginning of the section seems to be implying) but more about the fact that this notion of *Binary Resolution* does not yield a complete inference procedure.

The book does a good work defining exactly what is necessary to extend the resolution to make it yield a complete inference procedure. There's the Lemma 3.28 and a commentary about its proof. I will elaborate a little bit more here, making my explanation as detailed as possible. I believe it to be a good idea as the book does not give many examples and it also does not always connect the knowledge or arguments it gives/makes.

The idea about the solution to the issue is as follows (read the lemma along with the explanation):
`A'` and `B'` can be thought of as `subst σ A` and `subst σ B` respectively. The `σ` is a substitution making the resolution possible—it makes some literal in `A'` and some literal in `B'` unifiable.

The first part of the next sentence about the non-empty subsets `A₁` and `B₁` follows directly (and trivially) from the above. Both `A₁` and `B₁` must contain at least one literal, otherwise `A'` and `B'` would not be resolvable and there would be no resolvent `C'`.

I am not sure about the "and for any `σ` that is an MGU of `S`"—I would expect there to be just one MGU of `S` as I would expect it to be unique. In any case, it does not seem like this part is critical—all of it makes sense even without a complete understanding of what this means.

The point is that `C'` features neither `subst σ` A₁` nor `subst σ A₂`. In other words, we drop all those literals that are resolvable, not just one from each clause. There is another key aspect of completeness in this step—we must make sure to find all such sets `S`.

##### Implementation
The details of this lie in the Implementation section of this sub-chapter.
Suppose we have those two clauses to work with, we also have the selected literal `P` (from the first clause). Now, we have to find the set `S` (or actually, all possible sets). It would make sense to start by considering all the literals from the second clause that unify with the literal `P` (obviously, while being of opposite polarity) and also all the other literals from the first clause that also unify with `P` (obviously, of the same polarity).
However, not all those might make it into `S`. Consider, for instance, the following situation: There are two literals `P₁` and `P₂` in the first clause that are unifiable with their "neighboring" literal `P`. As we unify `P` and `¬P` (from the second clause) we obtain a unifying substitution `σ`. We apply `σ` to `P₁` and attempt to unify `P₁` with `subst σ P`, we succeed and obtain a substitution`σ'`.
At this point, we would want to unify `subst σ' P₂` and `subst σ' P`. We might, however, find out that that is impossible. The previous unification involving `P₁` made it so that `P₂` can no longer belong to the `S` as well.

To make the inference procedure maintain its completeness property, we must consider all the possible sets `S`—all the possible combinations of literals from both clauses if such a combination would be witnessed by some unifying substitution. That is exactly what this section makes clear and handles.

Our goal is to use the statement from the beginning of this sub-chapter—taking two clauses, picking a literal and producing all the resolvents, moving on to the next possible literal, doing the same and when we are done, discarding those two original clauses.

That is exactly what the implementation in this section does. It uses a strategy known as the *given clause algorithm* to make sure that each clause participates in an attempt at resolution with any other (and each) clause only once.





### Chapter 6: Interactive Theorem Proving

> TODO