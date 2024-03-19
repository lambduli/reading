# A Polymorphic Type System for Extensible Records and Variants

authors: Benedict R. Gaster, Mark P. Jones

year of publishing: 1996


## Contents
The paper introduces and defines a type system with row polymorphism using the `lacks` constraints.
The constraint is built on top of the *qualified types* and its constraint resolution strategy.

The paper generalizes the concept of rows both for extensible and polymorphic records as well as for variant types.


### Type System
Section 3 gives non-syntax-directed typing rules for the type system.

It also gives a useful definition of unification and *insertion*.
The *insertion* is something like unification but for row types.
Simply—it says that a row must have a member `(label :: type)`.
Both seem to be directly applicable to the usual constraint-solving algorithm.

In section 4.2 it gives a syntax-directed algorithm for inference as an extension of an algorithm W.

In section 5 it talks about compilation.
It demonstrates the idea of the *evidence* of some row having or not having a label *l*.
It is based on the idea of offsets of that particular label in that particular row.

Then it gives just two relevant rules for type-directed translation of expressions with qualified types.
That is the one for the application and the one for the abstraction.


### The "lacks" Constraint and the Evidence of Absence
The idea of the "lacks" constraint and how it compiles represents how the access in polymorphic records works.
Whenever I have a function that requests that some label is present in an otherwise unknown (with polymorphic row variable) record, it introduces a "lacks" constraint on that row variable.
This in practice should mean, that when some function provides a record value to another one (the one with this requirement) both functions obtain the "lacks" constraint into their type.
The constraint propagates just as type class constraints do.

### Offsets
The idea of offsets is a brilliant one! When I have a function with a type like this:

```haskell
forall r . r \ foo => { foo :: Int } -> Int
```

The "lacks" constraint tells me the offset at which the label "foo" (or rather its value) would be stored in the record made from the row `r`.
This in turn means that a record created by extending `{| r |}` (to use the syntax from the paper) with a label `foo` would have that label (or rather its value) stored at that exact same offset.

This works the same for removing fields from the record.


### Multiple "lacks" Constraints

#### The Problem
The situation becomes a bit more interesting when we require the presence of multiple labels and therefore introduce multiple "lacks" constraints at once.
Consider the type below:

```haskell
forall r . (r \ foo, r \ bar) => { foo :: Int, bar :: Char } -> Int -> Char -> Bool
```

The reason for it being more interesting is the fact that the constraints say that `r \ foo` and `r \ bar`.
Those translate to the evidence of `foo` not being in `r` and `bar` not being in `r`.
To be even more specific, they translate into a pair of offsets, one for `foo` and one for `bar`.
Both offsets are, however, meant in relation to `r`.
This means that further calculation is necessary.
The reason is if I intend to insert `foo` first (and let's suppose alphabetical ordering of labels) then the offset I get should point me to `foo`'s value in the record `{| foo :: Int | r |}`.
Obviously, every label that is after `foo` now needed to be shifted one position to the right, to make room for `foo`.
This in turn means that when the `bar` is inserted, `foo` will get shifted too.

The issue now is, that we are given a value that is constructed as an extension of `{| r |}` with `foo` and `bar` both.
But the evidences reflect only an extension of the `{| r |}` with a specific label.

#### The Solution
I think the solution to this might be the following:
When there are multiple "lacks" constraints we need to first sort them by the same ordering operation.
This gives us the simplest order of insertion so that when we start inserting the lowest-offsetted label first and move on to the highest-offsetted one we never invalidate our previous offsets.
Then the offset given for each specific label needs to be adjusted (+) by the number of labels going "before" that particular one.
This should give us the precise position in the actual record value as opposed to just knowing where all of those labels would be if they only extended `{| r |}`.

## Special Mentions
The paper also introduces the concept of **first-class labels**.
