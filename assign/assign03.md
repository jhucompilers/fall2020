---
layout: default
title: "Assignment 3: Compiler front end, semantic analysis"
---

*Preliminary assignment description, not official yet*

**Due**: TBD

Points: This assignment is worth 150 points

# Overview

In this assignment, you will build a front end (lexical analyzer and parser) for a
simple [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language))-like programming
language, and a *semantic analysis* phase that builds symbol tables representing the
data types and storage requirements for each program construct that requires them.

One of the main things you will create in this assignment is an AST-based intermediate
representation to represent the source program.

The programming language that your compiler will support, and this assignment,
are both heavily inspired by/stolen from
[Peter Fröhlich's spring 2018 compilers course](http://www.cs.jhu.edu/~phf/2018/spring/cs328/).

## Grading criteria

TODO

# Getting started

TODO

# Language specification

## Lexical structure

The language has the following kinds of tokens:

* Keywords: `PROGRAM` `BEGIN` `END` `CONST` `TYPE` `VAR` `ARRAY` `OF` `RECORD` `IF` `THEN` `ELSE` `REPEAT` `UNTIL` `WHILE` `DO` `READ` `WRITE`
* Operators: `:=` `=` `+` `-` `*` `DIV` `MOD` `#` `<` `>` `<=` `:=`
* Brackets/grouping: `(` `)` `[` `]`
* Misc/punctuation:  `:` `;` `.`
* Identifers: a letter or underscore (`_`), followed by 0 or more letters, underscores, and/or digits
* Integer literals: a sequence of one or more digit characters

Tokens may be separated by whitespace.

## Syntax

The overall input is a *program*, which has the form

<div class="highlighter-rouge"><pre>
PROGRAM <i>identifier</i> ; <i>opt-declarations</i> BEGIN <i>opt-instructions</i> END .
</pre></div>

*opt-declarations* is a sequence of 0 or more *declaration*s.

*opt-instructions* is a sequence of 0 or more *instruction*s.

A *declaration* is a *const-declaration*, *var-declaration*, or *type-declaration*.

A *const-declaration* has the form

<div class="highlighter-rouge"><pre>
CONST <i>const-definitions</i>
</pre></div>

*const-definitions* is a list of one or more *const-definition*.

A *const-definition* has the form

<div class="highlighter-rouge"><pre>
<i>identifier</i> = <i>expression</i> ;
</pre></div>

A *var-declaration* has the form

<div class="highlighter-rouge"><pre>
VAR <i>var-definitions</i>
</pre></div>

*var-definitions* is a list of one or more *var-definition*.

A *var-definition* has the form

<div class="highlighter-rouge"><pre>
<i>identifier-list</i> : <i>type</i> ;
</pre></div>

An *identifier-list* is a list of 1 or more identifiers, separated by commas (`,`).

## Semantics, type checking

TODO

# Hints, specifications, and advice

## Approach

Rough outline:

* Implement lexical analyzer and parser using flex and bison
* Add semantic actions in the parser to build an AST
* Create an object-based representation of types
* Implement a traversal of the AST to build symbol table entries
* Implement a traversal of the AST to do type checking

# Submitting

TODO
