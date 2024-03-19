# Implementing Type Classes

authors: John Peterson, Mark P. Jones

year of publishing: 1995


## Contents
This paper describes the part of the type checker that deals with class-based overloading.

It does not go into much detail. It does not feature any code or a code-like description of an algorithm.

It describes the idea of using placeholders for the resolution of simple, single-parameter *type classes* during the type-checking process.

### The Approach

#### The Issue with More Polymorphic Methods
The only issue is the inherent limitation of the approach.
Because we are resolving the overloading into a language with no overloading, we need a way to represent the _dictionary_ for the particular instance.
The paper chooses to do that with the use of tuples.

What is missing, however, is a mention of a serious limitation of the approach when our type system does not support higher-rank types. More precisely, the paper never mentions that we can't have methods with types like:
```haskell
class Foo a where
  m1 :: a -> b -> (a, b)
  m2 :: a -> a -- just so that it is clear, that the dictionary must be a tuple
```

In precise terms, the additional polymorphic type variable `b` poses a serious problem here. The rank-1 type system is not able to express a type of dictionary for such a type class.
Here is why.

The type of such a dictionary would be:
```haskell
type Dict'Foo a b = (a -> b -> (a, b), a -> a)
```
Notice the additional type parameter `b`. It has to be present, otherwise the type alias is not valid.

From this, it follows that even though the `m1` should be polymorphic at its second argument type, it is not.
Even though we can get around the issue of having to supply the second type at which `Dict'Foo` should be instantiated. Consider the construction of the following dictionary:
```haskell
dict'foo'char :: Dict'Foo Char b
dict'foo'char = (\ a b -> (a, b), \ ch -> ch)
```

We also obtain two accessors for the insides of the dictionary:
```haskell
get'm1 :: Dict'Foo a b -> (a -> b -> (a, b))
get'm1 (f, _) = f

get'm2 :: Dict'Foo a b -> (a -> a)
get'm2 (_, f) = f
```

However, the issue manifests itself when we have a function taking a dictionary for `Foo` for polymorphic type. A function like the one below:
```haskell
func :: Dict'Foo a b -> a -> ()
func dict'foo'a a = let m1 = get'm1 dict'foo'a
                        _  = m1 a True
                        in  ()
```
The definition above fails to type check. The issue is the type annotation. GHC complains about `b` being a rigid type variable (a type variable expressing polymorphism, in other words—the intent of `func` to not care about the actual type that `b` stands for) and the attempt to unify it with `Bool`.

We can remove the type annotation in hopes of a better result. However, that only hides the symptom - the type of `func` is now `forall a . (a -> Bool -> (a, Bool), a -> a) -> a -> ()` or in a more readable form `forall a . Dict'Foo a Bool -> ()`.

This happens because even though `m1`'s type is generalized (because of the `let`), the key part (`b`) can not be generalized. The reason is that `b` is present in the typing context when the type of `m1` is generalized. The `b` comes from the argument `dict'foo'a` and as such it will stand in the type of `m1`. This in turn means that it can only be unified with one proper type at most.
This prevents us from using the `m1` as a function polymorphic at the type of the second argument.

----

The issue with the paper is, that it never mentions that having methods with additional polymorphism is not possible. There is a section 8.5 titled **Overloaded Methods** but the framing is that we can't have methods with additional type context. Reasoning being those dictionaries, requested by such methods, would have to be supplied during the construction of the dictionary and not when the actual method gets called.
That is absolutely correct, but since the paper explains why that would be a problem and even goes into the details of how the resolved code would look and break, it seems that it would be worth noting that not even more polymorphic methods are allowed.

----

#### The Solution
Fortunately, in the language with higher-rank types, we can represent dictionaries as custom data types with arguments typed exactly as they should be. We can't use tuples, since the polymorphic type variables in a tuple tuple can not be unified with a poly type. For that reason defining a custom data type with arguments typed exactly as requested by the type class is a fitting solution.

----
----
#### The Issue with Different Placeholders
Another issue is with the distinction between a placeholder for a method and an "ordinary" *overloaded variable*.
The paper's approach is to distinguish between those and generate two different kinds of placeholders. It seems to make sense for the very limited language described in the paper, but it does not make sense for a bit more expressive language like Glask.

The main point is this - overloaded variables are translated into an application to a sequence of placeholders representing instance-dictionaries, whereas methods are translated into a single placeholder that holds the information about the method (name) and the type at play.

This only makes sense if methods can not introduce additional polymorphism above the type class variable. But as we've discussed above, they should be allowed to.

---
#### The Solution
Those two kinds of placeholders should be just one.
When an identifier with an overloaded type is instantiated, all the predicates from its type that have been introduced into the context should be transformed into a placeholder (instance-dictionary one) and the identifier should be applied to all of them in the right order.
When it comes to methods - we can rely on the type-directed translation to generate a selector function for each method within each class. This way all the identifiers with method names can be used as if they were ordinary overloaded variables.


## Conclusion
Useful paper. It gives enough information to implement the compile-time resolution of class-based overloading.
Together with [Typing Haskell in Haskell](./reports/thih.md) should be enough to cover the implementation of type classes in a small language.
