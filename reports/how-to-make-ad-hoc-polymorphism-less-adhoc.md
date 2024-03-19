# How to make ad-hoc polymorphism less ad hoc

authors: Philip Wadler, Stephen Blott

year of publishing: 1989


## Contents
The paper defines the concept of type classes as a way of ad-hoc overloading in Haskell.

The main portion of the paper talks about features of *type classes* as they are introduced in Haskell.

But when it comes to the language in the appendix, that is, the language on which the mechanics are defined and demonstrated, that has very little to do with Haskell.

It uses a similar form as [Parametric Overloading in Polymorphic Programming Languages](./reports/TODO.md).
That is, a form similar to `let` expressions for declaring a to-be overloaded operator and another one for overloading it.


## Appendix

### On Multi-parametric Overloading
It seems that the reason why the system in this paper supports "multi-param type classes" is because there is no concern for ambiguity.
It seems that the language simply deals with "methods".

It also seems that those parts would require extra attention:
- ambiguity and defaulting
- function `split`

The issue seems to be the situation when knowing *some* of the *instance types* is not enough to be sure what instance needs to be picked. Or something to that matter.
This somewhat seems to relate to both of those points above.

The function `split` needs to know how to deal with predicates containing a mix of type variables—at least one that is supposed to be generalized and at least one that occurs freely in the typing context.

> One solution that comes to mind is to always make those *retained*. The defaulting then needs to take care of it, or it is an error.


### The Formalization of the System
Another major challenge of the paper is that the system is formalized **not** in a syntax-directed way, making it somewhat challenging to implement.
It doesn't seem impossible, but I think the issues mentioned above would arise.


## Conclusion
I consider it an interesting paper for getting the basics and mostly the history of the matter.

I read the appendix somewhat carefully. It seems that this paper is not going to help with the implementation of one's own language. It does not deal with too many details and specifics of Haskell's type class implementation.

From the implementation perspective, I think it should be read together with the [Implementing Type Classes](./reports/TODO.md).