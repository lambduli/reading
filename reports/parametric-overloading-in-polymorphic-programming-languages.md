# Parametric Overloading in Polymorphic Programming Languages

author: Stefan Kaes

year of publishing: 1988


## Contents
The paper defines a concept of parametric overloading similar to Haskell's type classes.

It seems equivalent to single-parameter type classes.

With a major exception of it being defined in a way that is very similar to what I have seen in [How to make ad-hoc polymorphism less ad hoc](/reports/how-to-make-ad-hoc-polymorphism-less-adhoc.md).
That is, there is a syntactic form like `let` expression for declaring an overloaded operator and another one for overloading it.

This, of course, does not allow for *recursive declarations*.

It also offers a simple unification-based inference algorithm.
The author explains that the inference differs from the *Algorithm W* by additional support for resolving the overloading.


## Special Mentions
The paper talks about the property of *principal type* and asserts that the proposed system has that property.

Does not use a notion of *qualified types*, instead it uses so-called *overloading schemes*.

The algorithm presented here is called *Algorithm Z*.


## Conclusion
Both the algorithm and the shown language are very simple. They seem to be way simpler than what would be necessary for a language like Haskell 98. Therefore this paper seems to be a nice introduction to the concept of overloading, but implementation-wise it is not a very contentfull resource.
