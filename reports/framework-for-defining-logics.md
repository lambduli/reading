# A Framework for Defining Logics


authors: Robert Harper, Furio Honsell, Gordon Plotkin

year of publishing: 1993


## Contents
I think this is the foundational paper on LF. It defines the dependently typed lambda calculus and shows how one can encode first-order predicate logic and also second-order predicate logic into it.

There seems to be a big overlap with [An Overview of the Edinburgh Logical Framework](/reports/overview-of-the-edinburgh-logical-framework.md). This one is a bit larger, though. Another overlapping paper is [Logical Frameworks — A Brief Introduction](/reports/logical-frameworks-a-brief-introduction.md).


### Section 2: The Type Theory of LF
In this section, the type theory of LF is described. Section 2.1 gives a grammar for the calculus and also typing rules for terms signatures and contexts.

Signatures are sort of a specialization of a context. The paper splits the lexical environment into two independent notions. There's a context `Γ` that is used for variables. There's also signature `Σ` that is only for constants. It seems that this is a convenience decision, it seems to make reasoning and definitions more straightforward, it is not clear to me whether this would bring something specific in a semi-practical implementation.

The LF type theory is also known as λΠ. It is a dependently typed lambda calculus. The most notable part is the fact that values/objects are now part of types. To be more specific—types can be parametrized by/indexed with values. We call this notion of types *type families*.

> The pure λΠ does not support types parametrized by types as in the *weak λω* or *strong λω*. This was a bit confusing to me after reading the [[A Tutorial Implementation of a Dependently Typed Lambda Calculus]] and getting the impression that λΠ can abstract over types, because type-level, kind-level, and value-level terms are now one. Which they are not actually.

We get the Π type. We also get the Π kind. I am not going to define it here, all three papers define it well.

Some of the figures in this section are a bit confusing. There seems to be a rule written that assigns an object-level constant `c` a *Kind*. Doing this would not make sense. Perhaps the `c` here was meant as a constant at the type level.

#### Sections 2.2 and 2.3
Those define and discuss interesting theorems and properties of the system. There are sub-sections of consistency and some other theorems.

> TODO: Consider expanding this part.

#### Section 2.4: Canonical Forms
This section defines the notion of a *canonical form* as a further restriction of the notion of a *normal form*.

> TODO: What is the notion of *canonical form* useful for?


### Section 3: Theory of Expressions
This section describes how the object language is represented in the LF.
It demonstrates this in two sections, one defining a first-order predicate logic and the other defining a higher-order predicate logic.

#### Section 3.1: First-Order Logic
I am going to focus only on the parts that offer some contrast between this and the following sub-section.

We define two types, `ι` and `ο` in LF. The first one is a type for *terms*, the second one is a type for *formulae*.
So we define constants like these: `0 : ι`,`suc : ι -> ι` , and `∀ : (ι -> ο) -> ο` and so on.

> There's an interesting observation to be made. They effectively encode a FOL in a `λ->`. This might seem strange at first, because we know that `λ->` is equivalent to first-order propositional logic. So how come we can encode a stronger system in a much weaker system.
> I think the key here is that this encoding is for the syntax of the language. The encoding does not encode a notion of logical validity. This is explicitly observed in the other paper [[Logical Frameworks — A Brief Introduction]]. There, to also consider the notion of logical validity for propositions we must step up to the more expressive system λΠ.


#### Section 3.2: Higher-Order Logic
This section goes one level up.
The main change is the introduction of a type `holtype : Type` and making the `ι` and the `ο` constants with that type `ι : holtype`, `ο : holtyp`. We also introduce another constant `=> : holtype -> holtype -> holtype` for implication.

The presentation of the "idea behind the encoding" at the beginning of the section again, sort of seems to be encoding the HOL in a system like `λ->`. As far as I can tell this could work if we would to modify the `λ->` a bit.

First, here's part of the presentation I am talking about:
```
0 : ι
succ : ι -> ι  
+ : ι -> ι -> ι

⊃ : o -> o -> o

=σ : σ -> σ -> o
∀σ : (σ -> ο) -> o 
∃σ : (σ -> o) -> o
```

The section also defines the grammar for types:
```
σ, τ ::= ι | o | σ -> τ
```

At first, I wasn't sure what to make of it. It looked very close to `λ->` but it was clear that there's a sort of polymorphism happening with the `σ` type.

Then I realized that the idea was quite simple. This is a `λ->` with one important modification, for every actual type (that is represented as `σ`) we have constants like the last three in the first part of the presentation. In other words, there's `∀ι : (ι -> ι) -> ι` and there's `∀ι->ι : ((ι -> ι) -> (ι -> ι)) -> (ι -> ι)` and there's `∀ο : (ο -> ο) -> ο` and so on. Each one of them differs in name, which is fine (the subscript is part of the name).

This is very much in line with the idea of the function `obj : holtype -> Type` that can be used to encode any `σ` type into an LF type. In a sense, we use the λΠ to encode the syntax of the HOL into `λ->`.

The definition of `obj` is not given but I imagine it might be pretty trivial, pretty much translating `ι` into some type like `Term` and `ο` into something like `Formula`. The `=> : holtype -> holtype -> holtype` would then be translated into a function.

> It seems that this might work.

The figure on page 15 shows exactly how all that happens.

> TODO: The rest of the section talks about something I am unsure about.


_______
###### Discussion
Here, I want to think about the representation/encoding of the *Higher-order Predicate Logic* in the LF.
It is my understanding that we encode the language of HOL into that of LF and that should mean that if I can write a well-typed λΠ/LF expression it should constitute a proof of the corresponding HOL statement (term or a proposition).

I am going to discuss (and build some intuition) on that.

The paper mentions a function `obj : holtype -> Type`. It does not give its definition. I can only assume that it might work something like this:
```
obj ι = Term
obj ο = Formula
```

Those two types `Term` and `Formula` are imagined/made up by me. The paper does not seem to say what the actual LF types would be. It introduces "types" `ι` and `ο` and also `σ` and its alias `τ`. The paper defines:
```
σ, τ ::= ι | ο | σ -> τ
```

But those seem like "thought" types not real types. That is, there is something going on that would not work. For one, it seems strange that the following paragraph defines:
```
∀σ : (σ -> ο) -> ο
```

Ok, so I know it was strange when I first saw it, because this looked weirdly polymorphic, but it isn't. I think what this is is a *schema*—it says that for every *σ type* there is this constant with this higher-order function type. So, for `ι` there's `∀ι : (ι -> ο) -> ο` and for `ο` there is
`∀ο : (ο -> ο) -> ο` and for `ι -> ι` there's `∀ι->ι : ((ι -> ι) -> ο) -> ο`.
At least I think that's what this is about.

It seems to me that this encoding has a specific downside. To make it work, the system must be made so that it "knows" about this infinity of constants.

_____
So now I am a bit confused by the details. The paper talks about complicated-looking mappings and I didn't spend enough time trying to read them. Instead, I am going to try and "think off" some intuition.

Suppose there's a type `Term` and a type `Formula`. Let's start with the Term first. Suppose that there's a constant `0 : Term` and a constant `succ : Term -> Term`. This is what the paper does on page 15.

In a sense, to construct a "proof of 1" we would have a `succ 0`. The type of that checks out.

What about a proposition `1 = 1`? Now I am thinking, how does the `=` make sure that we are not trying to construct a bad equality?

Its type in the paper is: `= : Π s : holtype . obj(s => s => ο)`. What this tells me is just that `=` can accept two arguments of the same type of pretty much any type that is legit in the system. So I can get equality on two terms, on two predicates over terms (first-order predicates), on two predicates over predicates (second-order predicates) and so on.

So I guess it is ok that I can represent a proposition that is not logically valid. That I can do in logic two. But I would expect that the type system would prohibit me from constructing a proof of that proposition.

> So how come that the type system does not seem to do that?

I think the answer is—there is a difference between an encoding that encodes the syntax and one that also encodes the notion of logical validity of the propositions.

______

### Section 4: Theory of Rules and Proofs
This section is a strange one.

Its first subsection talks about various logics, which I don't really know enough about.

The beginning of the next section uses some unfamiliar-looking notation. However, it gets better when it is clear that this paper uses the same idea as the [[Logical Frameworks — A Brief Introduction]]. We can see that from types of the *introduction* and *elimination* constants.


There are some small differences. We have a type-level constant `true : ο -> Type`. I think this is basically the `nd : ο -> type` from the other paper.

Just for the argument, this is the type for the ∀-introduction rule:

```
ALL-I : Π (F : ι -> ο) . (Π (x:ι) . true (F x)) -> true (∀ (λ (x:ι) . F x))
```

Aside from the fact that we have `true` instead of `nd` and we have `∀` instead of `forall` (and the last lambda's parameter is explicitly annotated in this paper) the type is identical to the one in the other paper.

For this reason, I have decided to mostly just skim through the rest of the section and rely on the contents of the other paper.


### Section 5: Related Work
This short section mentions some interesting-sounding related research. I am mostly interested in texts about higher-order unification and likes—things that would help with the implementation of such a system.


## Conclusion
This paper is quite extensive and detailed. I like that. It also features an exhaustive-looking appendix. I am sure that reading this paper together with the [Logical Frameworks — A Brief Introduction](/reports/logical-frameworks-a-brief-introduction.md) was a great idea. That one is quite a bit shorter and goes right into the topic. The way the other one defines and demonstrates the encoding makes it easier to read. The current one starts with a couple of not-so-clear-to-me paragraphs of notation that could obstruct me from seeing the encoding itself have I had not finished the other paper first.
