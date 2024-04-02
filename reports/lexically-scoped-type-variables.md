# Lexicaly-scoped Type Variables

authors: Simon Peyton Jones, Mark Shields

year of publishing: 2004


## Contents
The paper describes two possible approaches to support lexically-scoped type variables.

Those two approaches are quite different. The first one is described as a *type-lambda approach* and the second one as a *type-sharing approach*. It is stated that the first approach is the one SML takes, whereas the second one is taken by GHC.
However, that is now not true—current GHC does not take this approach. Instead, it implements what seems to be the first one, the *type-lambda* one.

As a consequence—it might be confusing when the paper refers to the second approach as GHC's approach. On the other hand, the *type-sharing approach* seems to be quite interesting. I think I would use a different syntax for those *meta-variables* that can stand for arbitrary monotypes. Here is an example:
```haskell
fn (x :: _a) y = let f :: _ -> _a
                     f _ = x
```
Although contrived, this example shows `_a` as a *meta variable*, that is a variable that can represent any mono-type. It also shows another, related feature—the dash in the type. It represents any mono type at all.
In that sense, the `_a` does the same, except it has a name by which we can refer to that type again. Basically saying that whatever type it is, we don't care, but we are going to call it `_a`.


### Section 3: The type-lambda approach: SML



### Section 4: The type-sharing approach: GHC
The second approach treats scoped type variables as names for actual types (or type variables).
This means that we can use scoped type variables to denote a concrete type like *Bool* or *Int*. That would not be allowed within the first approach—a type variable is always related to generalization/polymorphism.

The following is legal in the *type-sharing approach*:
```haskell
foo :: Int -> Bool -> Int
foo (i :: x) (b :: y) = let a :: (x, y)
							a = (i, b)
						in  i
```
Here, `x` and `y` are just names for `Int` and `Bool` respectively.


## Conclusion
An interesting read. It gives some insight into previous approaches and maybe even motivates other features and extensions.
