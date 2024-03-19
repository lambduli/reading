# Semantics Engineering with Concrete Syntax

author: Tijs van der Storm

year of publishing: 2023


## Contents
This paper is about a tool (or rather a library) for semantics engineering—the same thing that the PLT REDEX is about, only this one uses a concrete syntax for the terms, context definitions and patterns.
The tool is called CREDEX.


### Introduction
This section introduces the idea and motivation. It mentions a few similar tools like PLT REDEX, K, Rascal, and Spoofax.
It also mentions ASF+SDF—research "ancestor" of tools like Rascal and Spoofax (or so it seems).

It starts by introducing the tool, the subject of this paper—CREDEX—a library for meta-language Rascal.
It makes an interesting proposition—CREDEX is unique for its *use of parsing and (ambiguous) parse forests to drive the reduction process*—a topic that will be explained in the later sections of the paper.


### Overview
This section establishes that CREDEX (same as REDEX) uses the notion of (evaluation) contexts to avoid having to litter the description of the rewrite/reduction rules with the details of the tree traversal—for the purposes of getting to the reducible expression.

It establishes the difference between tools like REDEX and the approach they take—they operate on *abstract syntax* whereas CREDEX operates on a concrete representation.
This difference means that CREDEX can use the concrete representation whereas REDEX uses abstract ones, one instance would be debugging/stepping visualisation.

At the end of this section there is an interesting note about the *link between context-redex decomposition and context-free grammars*. The mention belongs to the paper [[From syntactic theories to interpreters: Automating the proof of unique decomposition]] from Yong Xiao, Amr Sabry, and Zena M. Ariola.


### CREDEX by Example
This section explains how the splitting-through-parsing works. The only issue with this approach is that once the term is unparsed (after it was first parsed by the original grammar) it might become ambiguous. The section then goes on to explain how to solve the problem of these ambiguities.

The solution is to insert some sort of special parentheses around sub-terms. The paper seems to say that we can automatically generate a grammar of contexts featuring those special parentheses using already established theory.


### Example CREDEX Definitions
This section gives examples and demonstrates the flexibility and expressiveness of the tool. Those examples are lambda calculus, Imp language (a Simple Imperative Language), and QL (a Language for Questionnaires).


### Instead of Conclusion
This short conclusion-like section mentions that CREDEX comes with browser-based tools for visualisation.

It also mentions previous research and projects similar to CREDEX and PLT REDEX.
