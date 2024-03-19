# Type Classes - Exploring the Design Space

authors: Simon Peyton Jones, Mark P. Jones, Erik Meier

year of publishing: 1997


## Contents
This paper is a discussion of many design choices related to implementing type classes in a programming language (like) Haskell.


### Section 2: Why multi-parameter type classes?
It mentions two papers - [Parametric Overloading in Polymorphic Programming Languages](./parametric-overloading-in-polymorphic-programming-languages.md) and [How to make ad-hoc polymorphism less ad hoc](./how-to-make-ad-hoc-polymorphism-less-adhoc.md) as two first sources on the subject of *type classes*.


#### Section 2.2: Overloading with constrained parameters
This section starts discussing parametric type classes.
It mentions the paper [[Parametric Type Classes (Extended Abstract)]].


### Section 3: Background
This section gives a necessary background on the concepts related to type classes.


### Section 4: Design Choices
This is the most important section of the paper. It presents the reader with a set of choices regarding the design of the type system with support for type classes.

There are 9 choices in total. They span from simple ones—those that really aren't choices but rather statements about a specific limitation of Haskell being superfluous—to more involved ones, those that influence or depend on other choices.

I won't go into much detail. The paper covers all the design decisions very well.


### Section 5: Other avenues
This section talks about additional extensions or mechanics of type classes. These, however, are considered more complicated or would require further investigation to ensure consistency with the whole system.

Things like *relaxed super-class contexts* and *controlling the scope of an instance*.


### Section 6: Conclusion
This section presents a set of choices that Haskell designers made themselves.
I have also denoted my preferred choices and compared them to theirs. With two notable exceptions, those were the same choices.


## Conclusion
I consider this paper to be an excellent read. While it does not give any implementation details it goes the exact opposite way. It covers the important design decisions related to all the important smaller parts of the system. It does so in an extraordinarily readable and captivating way.

After reading this paper a lot of things becomes significantly more clear.
The paper pays extra attention to those small details that make or break the whole system—those specific interactions and dependencies.
This is especially great since the reader does not have to go and spend a lot of time thinking about the design decisions and their consequences. It all is presented here. At least those big ones anyway.
