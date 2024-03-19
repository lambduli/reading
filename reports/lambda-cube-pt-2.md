# Lambda Cube Part 2

authors: Lambda Cube Crew

year of publishing: 2023


## Contents
This is a short paper about two systems. The first part of the paper covers the system λ2 and the second one the **weak** system λω. This paper can be related to the [A Short Introduction to System F and Fω](/reports/short-introduction-to-system-f-fω.md). The current one might be even lighter.


### Section 1: Polymorphic Types, λ2
I will mostly skip this part of the paper as I am well familiar with λ2 from other resources.

There are, however, parts of the section that are worth emphasizing.
For one, I don't really understand the remark about being able to type self-application now. The type given in the paper doesn't look well.

**Theorem**: λ2 types exactly all primitive recursive functions.
This is an interesting claim and I would like to read more about it.


### Section 2: weak λω
This section defines the weak system λω. It introduces the notion of *kinds* as types-of-types, it also mentions *sorts* but does not exactly define them or explain them. Perhaps the authors expect the reader to have some pre-existing knowledge.

The paper concludes with a list of a few properties of the weak λω:
- Uniqueness of types
- Church-Roser property holds
- Subject reduction
- Strong normalization

It also states that the weak λω has the same expressive power as the λ⟼ and that it types exactly all extended polynomials.

I am not sure about the meaning of the second claim.


## Discussion
The fact that the weak system λω has lambda on the type level made me think whether this could lead to an issue with non-termination. (I also recall a short remark in [Typing Haskell in Haskell](/reports/typing-haskell-in-haskell.md) regarding Haskell's synonyms needing to always be fully applied to not take the role of some restricted type-level lambdas and cause non-termination.)

I want to give a bit of attention to it here.

The simplest diverging λ-expression that I can think of is the following:

```
(λ x . x x)  (λ x . x x)
```

To have this in our language, we would have to add explicit type annotations:

```
(λ (x : ?) . x x)  (λ (x : ?) . x x)
```

Now, the `x` binds a type. This means that `x` will be assigned a *kind* by the annotation.
We only need to think of arities.
The `x`, whatever it is, needs to have a function kind:

```
(λ (x : ? -> ?) . x x)  (λ (x : ? -> ?) . x x)
```

For the final step, `x` needs to take itself. This immediately leads to an infinite kind.

My conclusion is, that we don't need to worry about divergence at the type level because we basically have something like a restricted λ⟼ at the kind level. This should prevent us from writing type-level lambdas with infinite kinds.


## Conclusion
The paper is short, probably way too short. It doesn't give me any new information at this point (as I have already read [A Short Introduction to System F and Fω](/reports/short-introduction-to-system-f-fω.md)). Either way, it was a nice read and didn't take long.
