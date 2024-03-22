# Type-checking multi-parameter type classes

authors: Dominic Duggan, John Ophel

year of publishing: 2002


## State of Reading
I have read section 1.


## Contents
This paper describes a different approach to support multi-parametric type classes. It is related to [Parametric Type Classes (Extended Abstract)](./parametric-type-classes.md).


### Section 1 — Introduction
The paper describes a so-called *domain-drive unifying* resolution*.
It talks about *ambiguities* and how the *domain-driven unifying resolution* can solve some of the issues.
At the same time, it states that the process is not decidable. That is - if the solution exists - it will find it, but if the solution does not exist, the algorithm might not finish.


It also defines a so-called *overlapping restriction* on the class declarations.
The restriction states that:

```
class C α_1 ... α_m β_1 ... β_n where ...
```
all the `β` types need to be uniquely determined by all the `α` types.

As the paper states - this directly subsumes the approach described in the [Parametric Type Classes (Extended Abstract)](./parametric-type-classes.md).

