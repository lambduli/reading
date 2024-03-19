# Lightweight Extensible Records for Haskell

authors: Mark P. Jones, Simon Peyton Jones

year of publishing: 1999


## Contents
The paper talks about a *record system* for Haskell. It is discussed as a more expressive alternative to Haskell 98's very light syntactic sugar over positional constructors.

It mentions being strongly inspired by the work of Benedict R. Gaster and Mark P. Jones (Trex extension to Hugs compiler).

They propose a record system that would allow record extension, label selection, record-label update (both value and type level).

The syntax they use is similar to what can be seen in PureScript or Elm languages.

The notion of a *Row Type* is introduced, together with the notion of a *Row Kind*.

The paper is best read after [A Polymorphic Type System for Extensible Records and Variants](/reports/plymorphic-type-system-for-extensible-records-and-variants.md).


### Record Update
In the paper, they use `:=` for updating a field's value in the record.

### Record Extension
At the value level, the extension is done like this:

`{ r | b = True, c = "Hello World!" }

Where `r` is a *record* value. So this follows directly from above:

`{ { a = 23, z = 42 } | b = True, c = "Hello Again." }`

### Row Type
A *Row* is a type-level concept. It is a sequence of typing assumptions separated by comma and wrapped in parentheses.

`( a :: A, b :: B, c :: C )`

The above is just a syntactic sugar for the explicit construction of the term:

`( () | a :: A, b :: B, c :: C )`

which really, is just a syntax sugar for the completely desugared version:

`( ((() | a :: A) | b :: B) | c :: C )`

where the extension is done one step at a time.

### Record Type
A record type is constructed by applying a *Record* type constructor `{}` to a *Row*. Like so:

`{ ( a :: A, b :: B, c :: C ) }`

> As a side note, the term above has a shorthand variant `{ a :: A, b :: B, c :: C }`.
> Simply put, when a *Row* is wrapped in `{}` the `()` can be ommited.


### Record Type Extension and Predicates
When it comes to extending an existing *Record type* with a new label, we need to make sure that the specific label is not already present in the original *Record type*.

> There is a restriction on uniqueness of labels. (For good reasons.)

This is handled by a new predicate. It is called **lacks** and it asserts that some *Row* lacks a specific label.

Here is an example:

```
move :: (r \ x, r \ y) => Int -> Int -> {r | x :: Int, y :: Int} -> {r | x :: Int, y :: Int}
```

To move a "point" from one destination to another we need to make sure that the "point" will have those two fields `x` and `y`. At the same time, the type asserts that neither `x` nor `y` will exist within `r`.
Here it might look redundant or useless, but there are cases where this concept makes sense.

TODO: Write the example where it demonstrates clearly how it is useful.

The technical details regarding a type inference/checking of those predicates should be explained in [A Polymorphic Type System for Extensible Records and Variants](/reports/plymorphic-type-system-for-extensible-records-and-variants.md).

### Punning
Nothing new. Just a syntax sugar for pattern matching (and possibly for construction too).

### Kind Signatures for Types
They raise a very good point about allowing types to be annotated with kind signatures.
Example from the paper:

```
Point :: row -> row
type Point r = ( r | x :: Int, y :: Int )
```

or

```
type Point (r :: row) :: row = ( r | x :: Int, y :: Int )
```

honestly I wouldn't probably be opposed to the following either:

```
type Point (r :: row) = ( r | x :: Int, y :: Int ) :: row
```


I think I quite like the idea of kind annotations on types.
It seems to me that there is no reason to make a choice. All of them could be allowed at the same time. The constraint solver would then find any inconsistencies if they should arise. (From a programmer mistake.)


## Special Mentions
It mentions an "issue" (?) when it comes to typing record concatenation and a related paper *Typing Record Concatenation for Free* from D. Rémy. There might be something interesting there as well.


## Conclusion
I think it's quite a useful paper. It deals with most of the topics in the field of record systems. It compares the proposed system to what Haskell currently does with records.
