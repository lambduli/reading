# Visible Type Application

authors: Richard A. Eisenberg, Stephanie Weirich, Hamidhasan G. Ahmed

year of publishing: 2016


## Contents
This paper describes how Haskell supports the *visible type application* functionality.
It can be used as a primary resource for implementation.


### Section 4: HM with visible type application
The following things seem to be worth mentioning:

The way type schemes are defined allows us to have a type scheme and within it have a specified polytype. This means that in theory, we can have a type like this one:

`∀ { a, ..., c } . ∀ x ... z . τ`

The question is—how would we obtain such a type scheme and what would we use it for?
I am not sure about it, but I think that has something (or maybe everything) to do with a GHC extension that allows to write universally quantified types and specify that some of the variables will always be implicit—meaning they can't be specified by *visible type application*. It's done like this:

`∀ {a} {b} x y . τ`

This means, that `a` and `b` can not be instantiated by *visible type application*.
So types like the one above answer both questions from earlier.


#### The Lazy Instantiation
Arguably, the main contribution of section 5 is the notion of *lazy instantiation*.
The concept is at its core simple—because of the way the inference rules are split into two groups, we can infer (and propagate) a specified polytype for some expressions. This polytype can propagate through a few steps of the process until it gets instantiated when we use one of V_ABS, V_APP. The instantiation is done by V_INSTS.

> I think this is a very clever and simple way to handle the idea of *lazy instantiation* in the inference-only type system.


#### Figure 7
The figure shows System V, a syntax-directed version of HM with visible type applications.
The following are some thoughts about it.

```haskell
foo0 _ = ((\ x -> x) :: a -> a) 'a'

-- foo1 fn = (fn :: a -> a) 'a' -- this is REJECTED

{-# LANGUAGE ScopedTypeVariables #-}
-- foo2 _ = ((\ x -> x :: a) :: a -> a) 'a' -- this is REJECTED

{-# LANGUAGE ExplicitForAll #-}  

foo3 _ = ((\ x -> x :: a) :: forall a . a -> a) 'a' -- this is ACCEPTED
```
  

So it seems that in the at-most-first-rank system (Haskell 98) with *ScopedTypeVariables* and *ExplicitForAll* I can indeed use the type annotation (inside the lambda), say **a** and mean that one introduced by the annotation.

Now, since we are still in Haskell 98, my online type annotations with **forall**s  are not really a source of additional polymorphism.

They just state the obvious truth. BUT!

Consider the following example:

```haskell
foo4 _ = (\ x -> x) 'a'

{-# LANGUAGE ScopedTypeVariables #-}

{-# LANGUAGE ExplicitForAll #-}

foo3 _ = ((\ x -> x :: a) :: forall a . a -> a) 'a'
```

There is a difference in how those two functions are understood.

The first one (foo4) is type-checked like this:

1.  infer a concrete type for the left-hand-side of the application
2.  that type is `a? -> a?` for some concrete monotype `a?`
3.  infer a concrete type for the right-hand-side of the application
4.  that type is `Char`
5.  unify the argument type `a?` with `Char` and produce a return type (`Char`)

Nowhere in the process have we generalized the type of the lambda—there was no good reason for that.  

The second example (`foo3`), however, should lead to exactly that.

The typing rule for type annotation says that the expression has a polytype (assuming that the inline annotation is assigning the expression a polytype), to be specific—a specified polytype.

Now, that clearly means that for it to be legally applied to its argument (`‘a’`), that polytype needs to be instantiated.

That can be done with the use of the explicit type application.


But how is it done in the paper?

I think this is where the sort-of-non-deterministic use of V_INSTS comes to the picture.

1. We start with the rule V_APP, we want the left-hand-side to be a function type of two concrete types.
2. We then have to use the V_INSTS rule to instantiate the actual, polymorphic type of the expression (the type annotation).
3. Then we use V_ANNOT to produce that polytype.

So this should be it.


So this is how we deal with polymorphism introduced by inline typing annotations at places where we don’t want polymorphic types.

As I have mentioned in the scribbles near Figure 7—we could deal with it by using a type application to specify that type.

That certainly seems possible. I actually think it would be as reasonable.


So my intuition tells me that this sort of “rewriting” solution is how that term would, in fact, be elaborated. In other words—I think this is how the instantiation is implemented.

Simply because when we elaborate into System F all those type instantiations need to be introduced explicitly (as well as generalizations in the shape of explicit type lambdas).

So as I said—my intuition tells me that this is the right solution.

The question, is—how does one know the right monotype for the type argument?


I think this can be somewhat straightforwardly dealt with using bidirectional type analysis.

When I don’t know yet, I first infer the monotype and then use it.

To be more specific—I can apply that polymorphic expression (wrapped in type-lambda) to a flexible type variable (meta variable).

Unification will resolve it and that flexible placeholder will become that concrete monotype it should have been all along.


This does not even require having type-holes in the language. Simply because the user would not be the one placing the flexible placeholder type there, the compiler would.

But adding type-holes into the surface language would then allow for even more flexibility; with, hopefully, not much overhead.




### Section 3: Higher-rank type system
This section describes a System SB—a higher-rank, bidirectional type system.
It is introduced in Figure 8 and Figure 9. The following text discusses how the System SB and the bidirectional higher-rank type system in PTIART interact and can be combined together.

#### Elaboration into System F
This section is going to explore how the translation in [Practical Type Inference for Arbitrary-rank Polymorphism](./practical-type-inference-for-arbitrary-rank-types.md) can be extended to cover visible type applications as described in *Visible Type Application*.

I think there are two main concerns:
-   elaboration of additional (and existing) syntactic forms, and
-   elaboration of subsumption.

> So I think the result of the discussion below is that elaboration of subsumption is OK, the operation is defined equivalently in both papers and it is also used in the same way.

I am going to deal with both of those here, exploring and exploiting all the details that I can think of.

##### Elaboration of Syntactic Forms
I need to elaborate the new form—type application.
I also need to deal with changes to existing forms. One of the forms that changes is inline type annotation.

To support lexically scoped type variables introduced by inline type annotations we need to wrap the annotated expression within a type-lambda. We also need to apply this type-lambda to its type arguments to instantiate it. This instantiation happens at the same place where the type analysis would do it.


##### Checking of Subsumption
I need to go over subsumption in both systems—the bidirectional system with higher-rank polymorphism from PTIART and the bidirectional system SB from VTA.

I need to see when are both of these used in their systems and compare them. If there are some differences in what one subsumption can handle (shouldn’t be)—this needs to be addressed.

What also needs to be addressed is any difference between how and when is either subsumption used in their respective systems. I need to have a complete understanding of what the subsumption does in both systems.

Then I can figure out what should a subsumption, within a system that includes both those systems, do.


##### Instantiation Checking
Let us start with the *instantiation checking*.

In PTIART there is a rule called ⊢inst. This rule is used when we need to confirm that a sigma-type on the left is a sub-type of the rho-type on the right.

In other words, the rho on the right is an instantiation of the sigma on the left.

##### Variable
This is used at various places—when we are analyzing an identifier (VAR) we use it to CHECK that the corresponding rho-type it _should_ have is an instantiation of a sigma-type that it has in the assumption set.

If we are INFERing the type of an identifier, we use the INST1. That one does instantiation on all generalized variables.

This makes the rule VAR work in both directions.

###### Application
Another place for ⊢inst is when analyzing application (APP).

If the application needs to be CHECKed against a known rho-type, we first infer the function type of the left-hand side. We then use the input part of it to check the right-hand side of the application. This way, we can handle the higher-rank types. What is left is figuring out if the type, that the result is supposed to have, is an instantiation of the output part of the function type from the application. That is what the INST is used for here.

If we were in an INFER mode, the INST would be used to instantiate the sigma type (the output part of a function type).

It is worth noting here, that the type of the left-hand side can indeed be (sigma --> sigma’).

We need to consider that the the type of the left-hand side can be obtained through VAR rule (for example). This would mean that if the type is not in weak-prenex-form and has some foralls on the right-hand side of the arrow type, those variables will not get instantiated by INST. The _deep instantiation_ happens in the CHECKing mode only.

This should not be a problem in any way. In CHECKing mode, the deep skolemisation takes care of it and in INFER mode, only the top-level variables get instantiated, leaving the rest as they are.

###### Annotation
The last one that uses INST is the rule for annotation (ANN).

If in INFERring mode, it takes care of instantiating the type from the annotation.

In CHECKing mode, it simply serves as a check that the type the expression ought to be and the one from the annotation are compatible. To be more specific, the type in an annotation can assert that the expression is **more** polymorphic than what the analysis needs. This is fine, as the POLY rule makes sure, that the type is not more polymorphic than the actual expression. And it is also fine with regards to the type from annotation being more polymorphic than what is “required”. We can see it like this—the “required” or “expected” type is what somebody is **asking** for. If we find out that what is given is actually more polymorphic than that, we are fine.

For example, if a function says that it expects a function argument that operates on values of _Show_ and we use that information to go check the actual argument, and at that point, we discover that some annotation states that the actual function argument is a totally polymorphic function, we are fine. The caller big-function intends to only use it at Show types, but the actual argument-function does not care. It does not have any requirements, not even for its arguments to be _Show_. So this is fine.

###### Instantiation (INST rule)
The rule INST itself has two directions.

In the INFER direction, we just to instantiation of the top-level type variables/parameters. There is no prenex conversion happening.

In the CHECK direction, we do the instantiation by delegating to _deep skolemisation_.  


###### Deep Skolemisation
Let's discuss **deep skolemisation checking**.

The rule is marked DSK. It is used in two places in the main typing rule.

The first place is a CHECK rule for abstraction with annotated parameters. We don’t use this rule in Glask, because we have patterns and not simple binders.

In any case, we can understand the use of DSK like this: So we have a lambda with an annotated parameter and we also have the function type for that lambda.

We now need to check that the type from the parameter annotation is an instantiation of the argument type from the function type. This should make intuitive sense—the parameter annotation can not assert that the argument’s type is more polymorphic than the type it is supposed to have.

The other place where DSK happens is the already mentioned INST2 rule.

Now for the DSK rule itself.
It expects two sigma-types. The second one is supposed to be an instantiation of the first one.

It converts the second one (the instance) to the weak-prenex form. This transforms the second sigma into a type like `∀ a …` z . ρ`

Here the rho-type does not feature any foralls on the right-hand side of the arrow.

There is some obvious renaming, to make sure that all the names are unique enough and do not get mixed up.

Then an auxiliary judgment DISK* is used to check that the rho-type is an instantiation of the original left-hand side sigma-type.

The DSK is also used within the DSK* when we deal with function types. Because function types can have sigmas (foralls) on the left-hand side of an arrow, we need to call back to DSK. It needs to invoke prenex transformation on one of the sigmas as part of the process.

Let us move on to the DSK* judgment.

> TODO: Finish this.


###### Subsumption, Instantiation, and Deep Skolemisation in VTA
The difference between a deep skolemisation in both papers seems to be the following: The PTIART’s DSK does not decompose/deal with the left-hand side sigma-type, it just passes it as it is into the DSK*.

The DSK from VTA does, on the other hand, decompose the sigma-type, it separates generalized and specified type variables and only passes phi-type and rho-type into the DSK*. Instantiating the type parameters in the left one, skolemising the parameters in the right one.

Besides that, it seems that the notion of subsumption under deep skolemisation is completely equivalent in both papers.

I think the biggest difference is in instantiation. In the System SB there is no standalone judgement for instantiation. The steps necessary for it to happen are in-place within the rules that would call instantiation.

Take, for example, a rule VAR in PTIART—it solely relies on INST to deal with both directions. That means that in CHECKing mode, we do deep skolemisation and in INFER mode we do instantiation.

Since the System SB in VAT does not have an instantiation rule, it effectively splits the rules in two directions—INFER and CHECK. The rule VAR or rather SB_VAR works only in INFER mode—this means that when we need to check that a variable has a certain type, we need to use the SB_INFER rule. This amounts to switching to INFER mode, inferring the type of the identifier and then using _deep skolemisation_ for checking that the inferred type is compatible with the checked-against type. To be more specific, that the checked-against type is an instantiation of the inferred one.

This “switching into INFER mode” raises a question—since the big advantage of the bidirectional type system is the ability to push types inwards, what do we lose by switching to INFER and checking the subsumption relation afterward?

Are there some syntactic forms that must be checked and can not be inferred and subsumption-checked?

###### Switching to INFER mode using the rule SB_INFER
After a closer inspection, we can see that System SB is equivalent to the bidirectional type system described in PTIART with respect to the INFER/CHECK mode.

The System SB switches into INFER for VAR, APP, and ANN; this means that types of those are inferred and only then checked using _deep skolemisation_. This is precisely the behavior of the bidirectional type system in PTIART.

In that sense, combining both systems will not pose any problems.




### Appendix B: Integrating Visible Type Application with GHC

#### B1: Case Expressions
This sub-section discusses the design decision of GHC in regard to typing case expressions and ifs. The usual rule says that all the branches must have the same type. Additionally, we specify that case expressions and ifs synthesize mono-types. It has to do with the fact that the system now forks with higher-rank types so this decision might not be obvious and is certainly not the only option. This is also addressed in PTIART so it's nothing new.
In checking mode, the type of those forms can, of course, be a (specified) poly-type.

#### B2: Type Classes
This sub-section just states that type variables from a type class definition have a priority in the order of type variables of method types. It demonstrates an important feature, however.
The following is legal according to the paper and GHC:
```haskell
foo :: a -> b -> (a, b)
foo a b = (a, b)

test _ = foo @Int @Char
```

This example does not even feature type classes, but it would be the same for methods.
It means that type in annotation does not have to feature an explicit `forall` and give the order of type variables. The type checker can do it for the user. It seems that the order is according to the order in which the type variables appear in the type annotation—with the special case for methods that additionally need to obey the priority of type class variables.

I personally would go for a more strict requirement—for a type to be considered *specified* and its type parameters to be considered as well, it needs to feature an explicit `forall` and as the *all-or-nothing* rule dictates—give an explicit ordering to all bound type variables/parameters.

#### B3:


## Conclusion
This, quite a substantial paper, is an excellent read. It can be used as a resource for implementation and gives a lot of details.
