# Artificial Intelligence — A Modern Approach

authors: Stuart Russel, Peter Norvig

year of publishing: 2010


## State of Reading
I plan to only read a few relevant chapters in part III of the book. Those are chapters 7, 8, and 9.
Those chapters describe formal logic and methods of formal reasoning.

Chapter 6 in the previous part of the book might also be interesting.

I have read those three chapters.


## Contents
This is a great resource for studying many things. I have used it to learn about resolution theorem proving and all the smaller concepts involved with it.

### Chapter 7: Logical Agents
The chapter contains some motivation for the use of logic as a way to direct agents. More specifically, it leads to the use of logical inference as a way of reasoning for the agent.
I am solely interested in the logic itself, the motivation—agents able to reason—is not of any interest to me.


#### 7.5 Propositional Theorem Proving
We are presented with an *inference*, a procedure, that can use so-called *inference rules* to derive a proof—a chain of conclusions that leads to the desired goal.

*Inference rules*, in this case, are the rules like *Modus Ponens* and *And-Elimination*.
They are written the same way as the rules we know from type theory—a sequence of premises and a conclusion.


#### 7.5.2 Proof by Resolution
This section introduces an inference rule called *resolution* that yields a complete (and sound) inference algorithm when coupled with any complete search algorithm.

It works like this: When there are two clauses (disjunctions of literals), for which it's true that one of them features some literal in a positive form and the other one features the same literal in a negative form, then we produce a new clause from these two where that literal is missing.

Example:
```
A ∨ B ∨ C ∨ D    K ∨ L ∨ M ∨ ¬A ∨ ¬C ∨ ¬D
------------------------------------------
B ∨ K ∨ L ∨ M
```

Now, what does the mention of the "complete search algorithm" mean?
I think the idea is as follows: The knowledge base (KB) is viewed as a state of the search problem. Those inference rules are transitions from one state to another. We can search the problem space to try and find whether some proposition `β` is entailed by the KB.

This section also introduces a specific aspect of the *resolution* called *factoring*. The *resolvent* should always contain only one instance of a specific literal. _Factoring_ is the removal of the redundant instances of the same literal from the resolvent.

Example: `(A ∨ B)` resolved with `(A ∨ ¬B)` produces `(A ∨ A)`, which is reduced to just `A` using *factoring*.

> Factoring is going to be more interesting and become non-obvious when we need to do it for First Order Logic resolution and unification is involved.

As it turns out there is a whole family of resolution-based inference procedures.

##### Conjunctive Normal Form
This section explains what it is. It also vaguely hints at how every valid sentence in *propositional logic* can be represented as a *logically equivalent* sentence in a CNF.

I think the algorithm should be somewhat straightforward. Starting with the most complicated connectives and rewriting the sentence until we reach one big conjunction of clauses.

##### A Resolution Algorithm
This section states that inference procedures based on resolution work by using the principle of proof by contradiction.

Simply put, to show that `KB ⊨ α`, we show that `KB ∧ ¬α` is unsatisfiable.

The procedure first transforms `KB ∧ ¬α` into CNF and then searches for **contradiction**.

It seems that we only use the single rule—*resolution*—to eliminate all the occurrences of complementary literals. Each step of *resolution* can lead to two possible outcomes:
- There are no two clauses that the *resolution* can be applied to. In this case, `KB` does not entail `α`.
- some two clauses resolve to yield the *empty clause* (⊥). In this case `KB ⊨ α`. 

We can now go back to the mention of the *complete search algorithm*.
Our goal when using *resolution* is to find a contradiction (or finish without proving anything). It should be obvious that a complete search strategy is necessary. Only a complete strategy can ensure that the contradiction (if present) will be discovered.

In our case—if we have a finite number of literals, it should not matter whether we use DFS or BFS. (As we can only make a finite amount of clauses and each resolution step takes two clauses and produces at most one clause.)

------

This should be the reason behind Prolog's strategy not being complete. It uses DFS with backtracking. The issue is—the number of literals (and clauses) in a Prolog program (or rather its execution) is not necessarily finite. A literal in this case is either an atom or a struct and a clause is an asserted sub-goal.

We could write a program that would lead to generating longer and longer lists. Technically, every such a list would still be finite but there is no *largest integer*, so we would just keep adding more and more elements to the list for eternity.

That program might look like this:
```prolog
is_list([H | T]) :- is_list(T).
is_list([]).
```

If we run it in `swipl`, we get an error—stack limit exceeded. That's because the program creates this ever-growing list in memory.

Because the search strategy of Prolog is DFS and the number of literals might potentially be infinite, that makes the search strategy not complete.

However, it should be true that a BFS even on infinite space would be "complete". Given an infinite amount of time, it would explore all the states. *At least I think it would.*

------


##### 7.5.3 Horn Clauses and Definite Clauses
This section states at the beginning that in many cases the full power of *resolution* is not needed. In many situations, we can express our knowledge base in a more restricted form which enables us to use a simpler algorithm.

The section mentions two restrictions—a *definite clause* and and *Horn clause*.

A *definite clause* is a disjunction of literals of which **exactly one is positive**.
A *Horn clause* is a disjunction of literals of which **at** most one is positive**.

Because of the *material implication* rule stating that: `P ⟹ Q  ≡  ¬P ∨ Q` we can rewrite those clauses as implications.

That one positive literal (if present) is called *head* and the (possibly empty) sequence of negative literals is called *body*.

------
Examples:
Exactly one positive—a rule in Prolog.
`¬A ∨ ¬B ∨ C  ≡  ¬(¬A ∨ ¬B) ⟹ C  ≡  (A ∧ B) ⟹ C`
Here, `C` is the *head* and `¬A ∨ ¬B` is the body. We could write *almost* `C ⟸ A ∧ B.`
In Prolog, this would look like `C :- A, B.`

No positive—a query in Prolog.
`¬A ∨ ¬B  ≡  ¬A ∨ ¬B ∨ ⊥  ≡  ¬(¬A ∨ ¬B) ⟹ ⊥  ≡  (A ∧ B) ⟹ ⊥`
This is because we prove the goal using a proof by contradiction. We assume the negation of the goal (as written by the user) and try to get to a contradiction.
This is consistent with what we see above. To prove the entailment, we must arrive at a contradiction. The reason is, Prolog is driven by an _SLD__ resolution_. A kind of resolution and as such, we prove the entailment by getting to a contradiction.

Single positive—a fact in Prolog.
`K  ≡  ⊥ ∨ K  ≡  ¬⊥ ⟹ K  ≡  ⊤ ⟹ K`
Here `⊤` is the head. In Prolog, we would write `K.`
______

> Inference with *Horn clauses* can be done through the **forward-chaining** and **backward-chaining** algorithms. (Sometimes also called **forward reasoning** and **backward reasoning**.)
> Apparently, it can also be done using a modified *resolution* rule.

> Another important aspect of *Horn clauses* is that deciding entailment with them can be done in time that is *linear* in the size of the knowledge base.


###### SLD Resolution
We need the *SLD inference rule* and a complete search strategy.
The algorithm is, once again, based on the *proof by contradiction*. We take the query that is a conjunction of positive literals and negate it. We get a disjunction of negative literals.

We then pick (some) one of those negative literals such that there is some definite clause with positive literals complementary to that one we picked. Those two literals must unify together—providing an unifying substitution.

This produces a new goal clause. It is the original goal clause with the selected literal replaced by all the negative literals making up a body of the definite clause mentioned earlier. The unifying substitution is applied to that whole goal clause.

Our goal is to arrive at a contradiction. That means producing an empty clause (disjunction with no disjuncts). That happens when we can not apply the inference rule anymore and the current goal clause is empty (⊥). On the other hand, if we can not select some negative literals from the goal clause and unify it with a complementary (positive) literal in some definite clause but the goal clause is not empty—then we have failed to obtain a proof (at least in this branch).

The idea behind it should be intuitively visible. If the goal clause still contains some negated literals but we can not get to the contradiction it means that the assumed contradiction of our goal is not unsatisfiable. In other words, there is an assignment for which the entailment relation does not hold. The assignment causes the *KB* to hold but it causes the query to not hold.

> Since our goal was to show that `KB ∧ ¬α` is unsatisfiable our failure to do so means *a not success*.

The algorithm also needs a *complete* search strategy. We could go with a BFS as it is a complete search strategy.
There is an interesting observation waiting for us to make. The DFS is not a complete search strategy because if one branch is infinite it keeps expanding it forever. There might be some other path that would lead to a contradiction (and hence a success) but we never explore that one. If we use something like BFS, we are able to cover the whole space. Now, if there are infinite branches in the search space those never lead to a contradiction. This is equivalent to stating that those branches will never lead to an assignment where to *original query* holds.

> IMPORTANT NOTE: The queries are existentially quantified. So when we make a contradiction out of the query, we produce a statement saying something like: "There are NO assignments that satisfy the original query." Every contradiction means a counter-example to that negative statement has been found—a solution to the original existentially quantified query.

______


##### 7.5.4 Forward and backward chaining

###### Forward Reasoning Algorithm
TODO: Finish later?

###### Backward Reasoning Algorithm
TODO: Finish later?


______


### Chapter 8: Propositional Logic

> TODO: Write this part as well. I am just skipping ahead to note something.

#### 8.2 Syntax and Semantics of First-order Logic

##### 8.2.2 Symbols and Interpretations
The section states that each model must provide an interpretation. The interpretation is an assignment of the constant symbols, predicate symbols, and function symbols to specific objects in the model.

Every interpretation is possible. Even those that do not make any good sense. Like assigning a constant symbol `Prague` to a small rock on the side of the road.

Later, it is said that the notion of validity, entailment, satisfiability and so on is defined just as for the propositional logic. This means that for a formula to be valid it must be satisfied in every model or its negation must be unsatisfiable in all models. That includes models with nonsensical interpretations.

> That is my interpretation anyway.

This leads me to believe that the notion of validity or entailment means that the formula/sentence must hold independent of the interpretation and the entailment likewise must hold with no regard to the interpretation.

It seems that the actual interpretation is not that important, however. Or rather if I chose a model with interpretation assigning a constant symbol `Prague` to a small rock on the side of the road and there is a relation `Capital of` and I also assign the constant symbol `Czechia` to the nearest body of water it should be my fault that the inferred information might not make sense.
I feel like it should not be the concern of the logical framework (inference system/theorem prover) to consider that my interpretation is convoluted.

> That is my feeling now, anyway.

I think I have realized an important fact regarding this. The entailment means that we are only concerned with the validity of the "query" within models for which the `KB` holds.
In that sense, the interpretations are already fixed.

And it should not really bother us, that the sentence `Capital of(Czechia, Prague)` is not **valid** because it might not hold in some contrived model. Such models are simply of no use to us.

> This makes more sense now.

It seems that the notion of *interpretation* is strongly connected to the notion of quantification. The sentence `∀ x P(x)` is true in a given model if `P(x)` is true for all possible **extended interpretations** constructed from the *interpretation* given in the model.

Does that mean something like the following?
There is a model. It has an interpretation. The **extended interpretation** is an interpretation that adds the assignment associating the variable (`x` in the case above) to all the objects in the model (one at a time).

> Maybe. It would make sense because the *interpretation* under which the `∀` holds must also subsume the whole original *interpretation* so in that sense, it is extended.


______


### Chapter 9: Inference In First-order Logic
This chapter covers techniques for inference in FOL.
It begins with the rules for quantifiers and unification.
It then covers the idea of propositionalization—converting sentences in FOL to *Propositional Logic*. It also covers the idea of lifting inference rules from propositional logic to FOL.

It then describes three kinds of ways to implement the inference for FOL.
They are forward-chaining, backward-chaining and resolution.
It also discusses the upsides and downsides of each technique together with some ideas to make the implementation more efficient and usable in the real world.


#### 9.1 Propositional vs. First-order Inference
This section covers the technique for the elimination of quantifiers from the sentences, effectively transforming our FOL *KB* into a *KN* in *Propositional Logic*.

##### 9.1.1 Inference Rules for Quantifiers
###### Universal Instantiation
The rule of **Universal Instantiation** says that we can infer any sentence obtained by substituting a **ground term** (a term without variables) for the variable.

This means that when we have a FOL sentence like
`∀ x King(x) ∧ Greedy(x) ⟹ Evil(x)`
we can substitute for `x` any and all of the *ground terms* that we have available.
That includes all the named objects in the model (constants) but also terms consisting of a *function application*. Functions actually make the inference a bit more complicated, we will come back to them later.

Suppose that we have objects `John`, `Richard` and `Father(John)` (Father is a function symbol). Then we obtain these three sentences:
`King(John) ∧ Greedy(John) ⟹ Evil(John)`
`King(Richard) ∧ Greedy(Richard) ⟹ Evil(Richard)`
`King(Father(John)) ∧ Greedy(Father(John)) ⟹ Evil(Father(John))`

###### Existential Instantiation and Skolem Constants
In the rule for  **Existential Instantiation**, the variable is replaced by a single new constant symbol.

Here is an example of that:
`∃ x Crown(x) ∧ OnHead(x, John)` becomes `Crown(C) ∧ OnHead(C, John)`.

Intuitively, we just give a name to that *one* object that satisfies the sentence.

That new name is called **Skolem constant**.


The rule for *Universal Instantiation* can be applied many times.
The rule for _Existential Instantiation_, however, can be applied only once per a FOL sentence and then the original sentence is discarded.

> The new *knowledge base* is not logically equivalent to the old one, but it can be shown to be **inferentially equivalent** in the sense that it is satisfiable exactly when the original *KN* is satisfiable.


##### 9.1.2 Reduction to Propositional Inference
###### Propositionalization
We eliminate all the quantified sentences using the rule for *Universal Instantiation* and *Existential Instantiation*. The key is to use the *UI* rule so that all the sentences that can be obtained (and could be useful—we will come back to function symbols later) are obtained.

We then proceed with an inference for *Propositional Logic* as normal.

That should give us a *complete decision procedure for entailment*.

> There is a problem, however! When the *KB* contains a *function symbol* then the set of *ground terms* is infinite.

Fortunately, there is a famous theorem due to Jacques Herbrand (1930) to the effect that if a sentence is entailed by the original, first-order knowledge base, then there is a proof involving just a *finite* subset of the propositionalized knowledge base.

This means two things.
First, we can use an iterative approach—we first only consider ground terms that are objects when doing the instantiation. If that is not enough to prove an entailment, we then use all the function symbols and generate all the *ground terms* that "come to be" only from the objects. If that is still not enough, we use functions to go one "level deeper". And so on.
At each step, we use as inputs to the function all the ground terms that existed in the previous step. This is quite an inductive procedure.

Second, we don't know whether a sentence is entailed by a *KB* until we are able to prove it. This is because the question of entailment for first-order logic is **semi-decidable**—that is, algorithms exist that say yes to every entailed sentence, but no algorithm exists that also says no to every non-entailed sentence.
This clearly means that our iterative/inductive approach might never terminate for sentences that are not entailed by the *KB*.


#### 9.2 Unification and Lifting
This section somewhat follows the structure of the similar section in the 7th chapter. It explains how we can lift the inference rules from *Propositional Logic* to *FOL*.

The section starts with the inference rule *Modus Ponens*. We see the **Generalized Modus Ponens**.

> There are some details that are not explained by the book. For instance, how does the *Modus Ponens-based Inference* deal with existential quantifiers? What are the specific rules for instantiation as we can see that the definition of the *Generalized Modus Ponens* does not mention any quantifiers?

> Additionally, this inference rule is sound and complete but only for the *knowledge bases* in a specific form—in the form of positive implications. In other words, it is only complete for *Horn Clauses*.

Maybe that clears up the question of dealing with existentials—it doesn't (with the exception of queries). It would also explain what is the process for the instantiation—we do **standardizing apart** and remove the quantifiers, use the unification and that's pretty much it.

##### 9.2.2 Unification
The section gives a pretty standard-looking definition of unification.
*Skolem constants* are a kind of constants so they unify only with itself or with a variable.

##### 9.2.3 Storage and Retrieval
This section discusses different aspects of representing the *KB*, the impact of those on the performance and such.


#### 9.3 Forward Chaining

##### 9.3.1 First-order Definite Clauses

> Unlike propositional literals, first-order literals can include variables, in which case those variables are assumed to be universally quantified. (Quantifiers are typically omitted.)

> Not all *knowledge bases* can be converted into a set of definite clauses because of the single-positive-literal restriction, but many can.


The algorithm is fairly simple. It starts with the set of known facts and at each step, it uses the *generalized modus ponens* to generate more facts from the facts it has available.
It concludes when the *goal* is obtained.

> We don't add new facts that are just a **renaming** of a fact already present.

An example of *renaming* would be `Likes(x, IceCream)` and `Likes(y, IceCream)`.

______
###### Class of Datalog Knowledge Bases

> A knowledge base containing no function symbols is said to be an instance of the class of **Datalog Knowledge Bases**.

This class of knowledge bases makes the inference much easier.
______

###### Non-termination of Forward Chaining
For general *definite clauses* with _function symbols_, the inference based on forward chaining might not terminate.
An example of that might be a knowledge base containing the *Peano axioms*. The forward chaining will keep generating natural numbers indefinitely.

```
NatNum(0)
∀ n NatNum(n) ⟹ NatNum(S(n))
```

This, however, does not necessarily make the *Forward Chaining* incomplete.
We can observe that at each step, the algorithm would only generate one new natural number. There is more to the proof—the book mentions *Herbrand's theorem*—but this should suffice.

The *Forward Chaining* is both **sound** and **complete** for definite clauses (even with function symbols).

The sub-chapter then discusses various ways to make the implementation of Forward Chaining more efficient and practical for real-world use.


#### 9.4 Backward Chaining

##### 9.4.1 A Backward-chaining Algorithm

> A Backward chaining is a kind of **AND/OR** search. The **OR** part because the goal query can be proved by *any* rule in the knowledge base, and the **AND** part because all the conjuncts in the *lhs* of a clause must be proved.

The section formulates the backward chaining as a kind of *depth-first search algorithm*. That, however, makes it incomplete. Fortunately, it is possible to change the search strategy and make the algorithm complete.

The algorithm is, again, pretty simple. It uses the *Generalized Modus Ponens* in a backward direction.

##### 9.4.2 Logic Programming
This section gives an overview of the Logic Programming paradigm. It chooses Prolog as the fundamental example of logic programming.

> Prolog uses the **database semantics**. This means three things.
> From the *unique-names assumption*—all *constant symbols* and *ground terms* refer to a different object.
> From the *domain closure*—all objects that exist in the model are given names (names using functions like `s(X)` are also legal).
> From the *closed-world assumption*—all atomic sentences that are unprovable (not known to be true) using the corresponding knowledge base are considered false.

##### 9.4.3 Efficient Implementation of Logic Programs
This section mentions techniques for interpretation and compilation into *WAM*.

##### 9.4.5 Database Semantics of Prolog

> There is no way to assert that a sentence is false in Prolog.

The negation in Prolog works like this—Prolog tries to prove the sentence being negated, if it succeeds, the negation fails, if it fails, the negation succeeds (because it holds). However, because of the DFS strategy, the search for proof of the sentence might never terminate.

##### 9.4.6 Constraint Logic Programming
This is a very short section on a quite interesting aspect of logic programming. It seems worth it to look into CLP more, however, it is a bit outside the scope of my current plan.


#### Resolution

##### 9.5.1 Conjunctive Normal Form for First-order Logic

> As in the propositional case, first-order resolution requires that sentences be in **conjunctive normal form** (CNF)—that is, a conjunction of clauses, where each clause is a disjunction of literals. Literals can contain variables which are assumed to be universally quantified.

The above restriction causes no issue—every sentence in FOL can be expressed as a sentence in CNF that is inferentially equivalent.

The section showcases how the transformation from a FOL sentence to a CNF works. The one step that is worth looking into is the **skolemisation**.

The *skolemisation* is used to remove existential quantifiers. In the simple case, it works just like the *Existential Instantiation*—it introduces a fresh new constant and removes the quantifier. However, that can only be done if the whole sentence is in the form of `∃ x P(x)`. That is, the top quantifier is the `∃` and it binds the variable that we want to skolemize.

In a general case, we use the following rule:

`∀ x ∃ y R(x, y) ⟺ ∃ ƒ ∀ x R(x, ƒ(x))`

The idea is that since the original sentence says something like "for every `x` there exists some `y` such that ..." we rewrite it so that it says "there exists a function `ƒ` for every `x` which when given `x` returns that `y` ...".

Now we can use the simple rule and rewrite the sentence for the last time:

`∀ x ∃ y R(x, y)  ⟺  ∃ ƒ ∀ x R(x, ƒ(x))  ⟺  ∀ x R(x, F(x))`

where `ƒ` and `F` are so-called **Skolem functions**. They are fresh new constants/function symbols. The usual rules of unification apply to it.

> The *skolem function* is only applied to universally quantified variables that are in the scope. In other words, the function really only maps those universally quantified variables to that one object in the model that was originally existentially quantified.


______

##### Intermezzo—Conversion to CNF
The book does not cover the conversion to CNF in full detail. We are going to give it the full treatment in this section.

These are the steps that need to happen:
1) Convert to **Negation Normal Form**
	- eliminate implications and equivalences
	- push `¬` inwards
2) **Standardize Apart** variables
3) Convert to **Skolem Normal Form**
	- push the quantifiers outwards (*Prenex Normal Form*)
	- eliminate `∃`s using *Skolem functions*
4) eliminate `∀`
5) Distribute `∨` over `∧`

Most of the steps should be pretty clear and straightforward. The skolemisation procedure is the only one that demands more explanation.

There are two parts to it, the sources I've found are not clear on whether the order of those matters or if one or the other should be done first. We will get to the bottom of this together (I hope).

The step to *eliminating `∃`s* is done using the rule described above—we've already called it *skolemisation*. The step to push out the quantifiers is called conversion to *Prenex Normal Form*.

In the rest of this section we will focus precisely on these two operations and what could the right order for them be.

> The skolemisation (the elimination of `∃`s) is the only step in the conversion to CNF that does not preserve the logical equivalence. It only preserves satisfiability.
> As I understand it—that's because the new formula features a new *function constant* not present in the original models. However, whenever the new formula holds the original formula holds in the same model. It seems to me that the only difference between those models is the existence of the *skolem function*. If that's the case then the fact that skolemisation only preserves satisfiability should not be an issue.


###### Conversion to *Prenex Normal Form*
The goal is to obtain a logically equivalent formula that has all the quantifiers in a sequence at the top level. Alternatively, all the quantifiers are at the left. The sequence of the quantifiers is called the *prenex* and the rest of the formula that contains no quantifiers is called the *matrix*.

The conversion rules:
**Conjunction and Disjunction**

`(∀ x φ) ∧ ψ`    ≡    `∀ x (φ ∧ ψ)`  under (mild) additional condition that `∃ x ⊤`

`(∀ x φ) ∨ ψ`    ≡    `∀ x (φ ∨ ψ)`

`(∃ x φ) ∧ ψ`    ≡    `∃ x (φ ∧ ψ)`

`(∃ x φ) ∨ ψ`    ≡    `∃ x (φ ∨ ψ)`  under additional condition that `∃ x ⊤`

> The two instances of an additional condition are an interesting thing to consider. The last one should trivially make sense—because the original sentence is a disjunction the left-hand-side is not required to hold, it could be the `ψ` what makes the whole sentence hold. Its equivalent sentence, however, asserts that there exists some `x` for which the disjunction holds.
> Put differently, the original sentence holds in a model where no `x` exists if `ψ` holds in that model. The new sentence, however, would not hold in such a model.
> An example would be `ψ ≡ ⊤`.
> 
> The earlier instance of the (mild) additional condition is not entirely clear to me.
> What if `ψ ≡ ⊥` and we are considering an empty model? The original sentence wouldn't hold. But assuming that any universal proposition trivially holds in an empty mode, the new sentence would hold. If we require at least one element in the model then the new sentence can not happen to hold only vacuously.
> Assuming that all universal propositions hold trivially for an empty mode—this explanation makes sense to me.


**Negation**

`¬(∃ x φ)`    ≡    `∀ x ¬φ`

`¬(∀ x φ)`    ≡    `∃ x ¬φ`


**Implication**

`(∀ x φ) ⟹ ψ`    ≡    `∃ x (φ ⟹ ψ)`  under additional condition that `∃ x ⊤`

`(∃ x φ) ⟹ ψ`    ≡    `∀ x (φ ⟹ ψ)`

`φ ⟹ (∃ x ψ)`    ≡    `∃ x (φ ⟹ ψ)`  under additional condition that `∃ x ⊤`

`φ ⟹ (∀ x ψ)`    ≡    `∀ x (φ ⟹ ψ)`

> The two additional conditions, again, should make sense if we accept the explanation above.
> Here, they are only introduced when we rewrite the sentence so that it is only true in a model where there is at least one `x` to consider. Let's take a look at the second instance of it.
> 
> `φ ⟹ (∃ x ψ)`    ≡    `∃ x (φ ⟹ ψ)`
> 
> First, we will assume `φ ≡ ⊤`. So `⊤ ⟹ (∃ x ψ)`    ≡    `∃ x (⊤ ⟹ ψ)`.
> If the implication is supposed to hold, then there must be some `x` such that `ψ`.
> If the implication is supposed to not hold, then there is no `x` such that `ψ` (or no `x` at all).
> 
> Second, we will assume `φ ≡ ⊥`. So `⊥ ⟹ (∃ x ψ)`    ≡    `∃ x (⊥ ⟹ ψ)`.
> The implication will hold if there is no `x` so that `ψ` (but there are still some `x`s) if there is no `x` at all, and if there is `x` such that `ψ`.
> 
> Now, let's take a look at the second part of the equivalence. We are assuming `∃ x (⊤ ⟹ ψ)`.
> If there is some `x` such that `ψ` then the whole sentence holds.
> If there is no such `x` that `ψ` (or no `x` at all) then the whole sentence does not hold.
> 
> We are now assuming `∃ x (⊥ ⟹ ψ)`.
> If there are some `x`s but none of them is such that `ψ` then the whole sentence holds.
> **If there are no `x`s at all then the whole sentence will not hold.**
> If there is some `x` so that `ψ` then the whole sentence holds.
> 
> All, except the bold one correspond to one of the sentences (or parts of a sentence) for the first half of the equivalence. All, but the bold one, preserve the equivalence—they hold when (for a model) the corresponding original sentence holds.
> 
> The bold one does not hold because the new sentence requires there to be at least some `x`. The contradiction in the *lhs* of the implication would allow any `x`—it wouldn't need to make the `ψ` to hold—but there are none in the model, the whole sentence must fail to hold.
> 
> To me, this is a complete explanation of the additional condition.


###### Preserving the Logical Equivalence
All the *prenex conversions* are said to preserve the logical equivalence, in other words, the original formula is true in all of the models where the new formula is true and the other way around too. Therefore, in all the models where one does not hold, the other one doesn't hold either.

> With the slight exception of the exclusion of empty models in those specific cases, of course.

Here's an interesting example to make an intuition:
*"If every person is mortal then the sky is blue."*

`(∀ p Mortal(p)) ⟹ Blue(Sky)` that's because we only consider models of people—there is nothing in the universe besides people.

> This is a slight simplification. There is a *sky* beside people but we want to be able to say that *a model is empty* in cases where there are no people. It's only a slight simplification because we could replace the sentence `Blue(Sky)` with `⊤` and `⊥` splitting the example into two parts and observing the same thing. However, we prefer the simpler approach.
> We could also rewrite the sentence like this:
> `(∀ o Person(o) ⟹ Mortal(o)) ⟹ ∀ o (Sky(o) ⟹ Blue(o))`
> 
> As an aside, think of why we decided to say `∀ o (Sky(o) ⟹ Blue(o))`
> instead of `∃ o (Sky(o) ⟹ Blue(o))`.
> Hint: The original sentence was supposed to hold in a world where there are no people and the sky is blue. Does the version of the sentence with the `∃` hold in that world?

This sentence is nonsensical, obviously. Modern science tells us that the mortality of people has no effect on the color of the sky. Additionally, it is not a *valid* sentence—there are models where this sentence does not hold. This makes such a sentence a great instance for an illustrative example.

We consider these models:
1) All people are mortal, the sky is blue.
2) All people are mortal, and the sky is red.
3) Some people are mortal, some are immortal, and the sky is blue.
4) Some people are mortal, some are immortal, and the sky is pink.
5) All people are immortal, the sky is blue.
6) All people are immortal, the sky is pink.
7) There are no people, the sky is blue.
8) There are no people, the sky is purple.

The relevant equivalence for this example is the following:

`(∀ x φ) ⟹ ψ`    ≡    `∃ x (φ ⟹ ψ)`  under additional condition that `∃ x ⊤`

We can immediately see that those last two models will cause issues—the additional condition requires models to be non-empty. We will rule them out later, for now, we consider them and see what happens.

We rewrite our sentence according to the equivalence. First in a "natural" way:
`"There exists a person whose mortality implies that the sky is blue."`

Now in a formal way: `∃ p (Mortal(p) ⟹ Blue(Sky))`.

This really is something to appreciate! The new sentence seems to be making even less sense than the original one. No matter, we will check whether they are logically equivalent to each other.


The first sentence holds in models: 1, 3, 4, 5, 6, 7, 8.
The second sentence holds in models: 1, 3, 4, 5, 6.

A quick way to see if the new sentence holds in a specific model is the observation that the sentence basically says: `"There is a person who makes the implication hold"`. In other words, if the sky is blue, just about any person will do—mortal or immortal—because `φ ⟹ ⊤` is always true. On the other hand, if the sky is **not blue** we need to find someone who is immortal so that the implication looks like this `⊥ ⟹ ⊥`. Knowing this, we can quickly evaluate the sentence in a model by first looking at the sky color and then seeing if there is a person whose mortality or immortality will match the implication's requirement.

The only two models where the first one is satisfied but the second one isn't are those two empty models. So applying that additional condition we rule out those and observe that the new sentence is indeed equivalent to the old one.

This doesn't constitute proof, however, it is only a nice example to help with intuition.

We will hint at the sketch of a proof here:

`(∀ x φ) ⟹ ψ`    ≡    `∃ x (φ ⟹ ψ)`  under additional condition that `∃ x ⊤`

The sentence on the left is an implication. That means we need to consider these four cases:

- `⊤ ⟹ ⊤`
- `⊤ ⟹ ⊥`
- `⊥ ⟹ ⊤`
- `⊥ ⟹ ⊥`

- Since the `ψ` holds, it's enough to find some `x` so that `φ` holds or does not hold, both are fine. And since the `φ` holds for every `x` then finding some `x` so that the implication looks like `⊤ ⟹ ⊤` will not be an issue. The only question is—will the `ψ` behave the same way even if it's now in the scope of the `∃ x`? We will assume it's proven that it will.
- Since the `ψ` does not hold and the `φ` holds for every `x` (making the whole implication not hold) we need to show that the new sentence will also be false in this case. It could be true only if there were some `x`` for which `φ` would not hold producing `⊥ ⟹ ⊥`. However, this will not happen as we know that `φ` holds for all `x`s. That means that any `x` that the `∃` might pick will cause `φ` to be true giving us `⊤ ⟹ ⊥`. However, that is a contradiction as was our goal. In other words, we wanted to verify that any `x` in such a model will cause the new sentence to be a contradiction.
- Since `ψ` holds but there are no `x`s such that `φ` holds we must find at least one `x` so that `φ` doesn't hold. Here comes the additional condition—we know that the model is not empty and we know that it's not true that `(∀ x φ)` therefore there must be at least one `x` such that `φ` holds giving us `⊤ ⟹ ⊤` inside the `∃`.
- Since `ψ` does not hold and neither does `(∀ x φ)`, we will once again get at least one `x` such that `φ` will not hold for it getting `⊥ ⟹ ⊥` inside the `∃`.

The idea is to show that from knowing the four possible options for the implication on the left we can see that the sentence on the right will be true when the original sentence is truthful and false when it is false.


We only focused on the equivalence of one specific rule but it is our belief that all the other rules are correct as well.


###### Order of Pushing-out the Quantifiers
The conversion to *Prenex Normal Form* is done to make the rule for skolemisation applicable. To be more specific, to push out all the existential quantifiers, one at a time, as to-the-top as possible. Once we reach a form where the formula starts with a sequence of universal quantifiers followed by an existential we apply the skolemisation rule and introduce a new function constant. Here is the rule again.

`∀ x ∃ y R(x, y) ⟺ ∃ ƒ ∀ x R(x, ƒ(x))`

All the universally bound variables that are in scope for the function `ƒ` will become `ƒ`'s arguments. This raises a question—when pushing the quantifiers out to the top, does the order matter? Could it happen that when doing it in a specific order we could add some additional arguments (universally quantified variables) to the future function `ƒ` even though in a different order that wouldn't need to happen?

Here's an example of what I mean:

`∀ x [(∀ y φ) ∧ (∃ z ψ)]`

On one hand, we could use the rule to push out the `∃` from the right-hand side of the conjunction and obtain:

`∀ x ∃ z [(∀ y φ) ∧ ψ]` and after the skolemisation:

`∀ x [(∀ y φ) ∧ ψ]` where all the occurrences of `z` are replaced with `ƒ(x)`.
We only apply the function `ƒ` to the `x` as that is the only variable bound "to the left" of the original `∃ z`.

On the other hand, we could use a different sequence of rules. We could first push out the inner `∀`:

`∀ x ∀ y (φ ∧ ∃ z ψ)` (assuming the additional condition) then we would use the rule to push out the `∃`:

`∀ x ∀ y ∃ z (φ ∧ ψ)` and we finally apply the skolemisation rule to obtain:

`∀ x ∀ y (φ ∧ ψ)` where all the occurrences of `z` are replaced with `ƒ(x, y)`. That is because in this case, not only `x` but also `y` is bound before the `∃ z`.

> I guess we could say something along the lines of: "The function `ƒ` can just ignore the additional `y` argument as it clearly does not need it—since it was able to do fine without it in the first case."
> 
> And I don't see a reason why it would not be the case—those conversions are equality preserving (with the exception of skolemisation but I am not talking about that). Those resulting formulae should be equivalent to each other with the slight difference in all the models where it holds—the function `ƒ` is unary in one case and binary supposedly-ignoring-its-first-argument in the other case.

Maybe we could even state that, assuming that the function `ƒ` just ignores its second argument in our example, we don't need to care about the order—we just reach the *Prenex Normal Form* using any order and we don't mind our *skolem functions* taking an excessive amount of arguments to be ignored after we do the skolemisation. Maybe.

But if that were the case, why would we even need to push out all the `∃`s in the first place? Why not just replace the existentially bound variables with Skolem functions straight away (assuming that we make sure to go from the top to the bottom) and just keep track of all the universally quantified variables in the scope?

It seems like we could do that. That was actually what caused some serious confusion for me when reading the book and looking up skolemisation, or rather *Skolem Normal Form*, on Wikipedia.
The book seems to go for skolemisation without first pushing out the quantifiers.

However, all the other materials that I've found on the internet mention both pushing out the quantifiers and skolemising. The order in which those two steps are done, however, seems to be inconsistent, sometimes even within a single source.

I want to emphasize—I am well aware that implication could be problematic. Two of its rules change the quantifier. This could cause some problems if not taken care of. However, if we've done all the previous steps of the conversion to the CNF, there can be no implication in the sentence. All of them were rewritten using negation and disjunction.

What I am trying to point out is that by looking at the rules of *prenex conversion* for conjunction and disjunction we can see that the quantifier does not change, the only thing that changes is the position of the quantifier (and of course, some additional condition might be introduced). This means that assuming that the order does not really matter and any possible redundant arguments can be ignored in the function `ƒ`, we can just start at the top, keep track of the universally quantified variables and each time we arrive at an existential quantifier we assign its variable a unique function `ƒ` applied to all those universally quantified variables in the scope. This way we go one existential at a time and never eliminate an existential that is not "above" all the other existentials.

Additionally, as the order of universal does not matter and since the only pushing-out would happen on conjunctions and disjunctions, we do not bother with the prenex conversion for universals either. Those universal quantifiers would get stripped anyway, so we just strip them and move on to the next step of CNF conversion.

> This seems to be exactly what the book describes.


______


##### 9.5.2 The Resolution Inference Rule
The resolution works almost the same way as for the Proposition Logic. The difference is the presence of variables and the use of unification and the resulting substitution during it.

There are, however, noteworthy consequences of using unification for making two literals equal (while preserving the opposite polarities).

The paragraph on **BINARY RESOLUTION** of this chapter mentions, but does not go into a necessary depth, the issue with first-order resolution with unification. Namely, the completeness of it.

Consider two first-order clauses. They are in CNF and their variable names have been standardized apart. Suppose two literals `P₁` and `¬P₂` each occurring in one of those two clauses—`P₁` in the first one and `¬P₂` in the second one.

We can apply resolution to those two clauses and obtain a resolvent clause. This is what we would call *binary resolution* and it does not yield a *complete inference procedure*.

The issue here is the fact that there could be more literals `Pᵢ` in the first clause and `¬Pᵢ` in the second one that could be an instance of the same literal—considering the unifying substitution from the above (for any and also multiple `i > 1`). As was said about propositional resolution—the resolvent must feature only one instance of each literal (except for different polarities, of course).
This does not necessarily mean that, if at least one of those two clauses contains multiple `Pᵢ` literals that would unify with `P₂`, we need to get rid of all those `Pᵢ`s from the resolvent. 

We need to get rid of all those instances that could "stand for the same ground term". As the book states: *The full resolution rule resolves subsets of literals in each clause that are unifiable.*
I won't go into more details here instead I will cover this topic in the [[Handbook of Practical Logic and Automated Reasoning]] where this strategy is described and formalized in much more depth than in the current book.

The section mentions one other option—extending the *factoring* to the first-order case. The book does not go into detail—just gives the idea in about two sentences—but it seems like the approach would be somewhat similar to the former one. I will have to try to find something on it later.


##### 9.5.3 Example Proofs
The section features some examples, they should be pretty straightforward.

The last paragraph of this section contains an interesting mention. The resolution can produce **nonconstructive proofs** for existential goals.

> Further inspection of this strangeness led me to discover that I misunderstood how the *Resolution* works at the beginning. What follows is an explanation of it as I understand it now.
> There are still some pieces that I am not sure I got, but I will note those.

I originally thought (for whatever reason) that when the resolution rule is applied to two clauses, those two clauses are discharged (eliminated) from the formula (KB). That is not the case, however.

Instead, it is the case that a single clause can be used multiple times during resolution with many other clauses to produce many new resolvents.

This should make sense as the unifying substitution is (are—one per each parent of a resolvent) applied only to the resolvent.

Additionally, it is the case that most sources I've found state something like: "All the pairs (made out of clauses) that can be resolved together should be resolved together".

It seems to me that what this means is that I do *Resolution* by pairing up all the clauses in the formula that can be resolved together and do so. This gives me a lot of new clauses that are now part of the formula. In the next step, I do this again—each clause in the new formula is paired up with every other clause in the new formula. I keep repeating until I produce an empty clause (⊥) or there are no clauses that could be resolved together.

> In this sense, it seems like the *Resolution* differs from my DFS + Backtracking + Unification-based implementation of Prolog-like language (my [Minilog](https://github.com/lambduli/minilog)) in that that it takes all the paths at the same time.

It also seems that this would explain the multiple mentions of the need for a complete search strategy—to make this efficient, we need some strategy to pick the right pair of clauses to resolve with each other. Ideally, we need to pick in such a way that we will obtain ⊥ as fast as possible.

###### Constructive and Nonconstructive Proofs
This brings us back to the topic of the last paragraph in the mentioned chapter—nonconstructive proofs.

The example in the book now makes sense—we can use the same clause multiple times even if it means that the originally existentially quantified variable, gets unified with a different constant each time.

This, however, leads to a nonconstructive proof. The text mentions multiple possible solutions like backtracking and only limiting one substitution and such.

I got to like the last option—for each originally existentially quantified variable we add a new literal `Answer(x)` (where `x` is the existentially quantified variable) and now instead of searching for an empty clause, we are searching for a clause that contains only those `Answer` literals.
If we want only constructive proofs, we only search for clauses with a single `Answer` clause per unique variable. I find this approach interesting because if we choose to admit a nonconstructive proof, we get to see what are the possible bindings for that one variable quite simply.

> Resolution on Horn Clauses does not have the problem of generating nonconstructive proofs.


## Links
In no particular order:

- [Introduction to Logic](http://intrologic.stanford.edu/extras/resolution.html)
- [Resolution Principle](https://mathworld.wolfram.com/ResolutionPrinciple.html)
- [Automated theorem proving](https://en.wikipedia.org/wiki/Automated_theorem_proving)
- [Natural deduction](https://en.wikipedia.org/wiki/Natural_deduction)
- [Rule of inference](https://en.wikipedia.org/wiki/Rule_of_inference)
- [SLD resolution](https://en.m.wikipedia.org/wiki/SLD_resolution)
- [Horn clause](https://en.wikipedia.org/wiki/Horn_clause)
- [Constructive proof](https://en.m.wikipedia.org/wiki/Constructive_proof)
- [Proof by contradiction](https://en.m.wikipedia.org/wiki/Proof_by_contradiction)
- [Unit propagation](https://en.wikipedia.org/wiki/Unit_propagation)
- [Davis–Putnam algorithm](https://en.wikipedia.org/wiki/Davis%E2%80%93Putnam_algorithm)
- [Universal quantification](https://en.m.wikipedia.org/wiki/Universal_quantification)
- [Resolution (logic)](https://en.m.wikipedia.org/wiki/Resolution_(logic)#Implementations)
- [Conjunctive normal form](https://en.m.wikipedia.org/wiki/Conjunctive_normal_form#Converting_from_first-order_logic)
- [Skolem normal form](https://en.m.wikipedia.org/wiki/Skolem_normal_form#How_Skolemization_works)
- [Prenex normal form](https://en.m.wikipedia.org/wiki/Prenex_normal_form#Conversion_to_prenex_form)
