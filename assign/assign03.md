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

### Declarations and types

A *declaration* is a *const-declaration*, *var-declaration*, or *type-declaration*.

A *const-declaration* has the form

<div class="highlighter-rouge"><pre>
CONST <i>const-definitions</i>
</pre></div>

*const-definitions* is a sequence of one or more occurrences of *const-definition*.

A *const-definition* has the form

<div class="highlighter-rouge"><pre>
<i>identifier</i> = <i>expression</i> ;
</pre></div>

A *var-declaration* has the form

<div class="highlighter-rouge"><pre>
VAR <i>var-definitions</i>
</pre></div>

*var-definitions* is a sequence of one or more occurrences of *var-definition*.

A *var-definition* has the form

<div class="highlighter-rouge"><pre>
<i>identifier-list</i> : <i>type</i> ;
</pre></div>

An *identifier-list* is a sequence of 1 or more identifiers, separated by commas (`,`).

A *type-declaration* has the form

<div class="highlighter-rouge"><pre>
TYPE <i>type-definitions</i>
</pre></div>

*type-definitions* is a sequence of one or more occurrences of *type-definition*.

A *type-definition* has the form

<div class="highlighter-rouge"><pre>
<i>identifier</i> = <i>type</i> ;
</pre></div>

A *type* is a *named-type*, *array-type*, or *record-type*.

A *named-type* is simply an *identifier*.

An *array-type* has the form

<div class="highlighter-rouge"><pre>
ARRAY <i>expression</i> OF <i>type</i>
</pre></div>

A *record-type* has the form

<div class="highlighter-rouge"><pre>
RECORD <i>var-definitions</i> END
</pre></div>

### Expressions

A *primary-expression* is an integer literal, *designator*, or parenthesized expression.

A *unary-expression* is either a *primary-expression*, or a *unary-expression* preceded by either `-` or `+`.

The following infix operators may be used in expressions, with the operands being *unary-expression*s:

Operator          | Precedence | Associativity
----------------- | ---------- | -------------
`+`, `-`          | lower      | left
`*`, `DIV`, `MOD` | higher     | left

Note that relational operators may *not* appear in *expression*s.  (They are only used in *condition*s.)

### Instructions

An *instruction* is an *assignment-statement*, *if-statement*, *repeat-statement*, *while-statement*,
*read-statement*, or *write-statement*.  Each *instruction* must be followed by a semicolon (`;`).

An *assignment-statement* has the form

<div class="highlighter-rouge"><pre>
<i>designator</i> := <i>expression</i>
</pre></div>

An *if-statement* can have either of the following forms:

<div class="highlighter-rouge"><pre>
IF <i>condition</i> THEN <i>opt-instructions</i> END
</pre></div>

<div class="highlighter-rouge"><pre>
IF <i>condition</i> THEN <i>opt-instructions</i> ELSE <i>opt-instructions</i> END
</pre></div>

A *repeat-statement* has the form

<div class="highlighter-rouge"><pre>
REPEAT <i>opt-instructions</i> UNTIL <i>condition</i> END
</pre></div>

A *while-statement* has the form

<div class="highlighter-rouge"><pre>
WHILE <i>condition</i> DO <i>opt-instructions</i> END
</pre></div>

A *write-statement* has the form

<div class="highlighter-rouge"><pre>
WRITE <i>expression</i>
</pre></div>

A *read-statement* has the form

<div class="highlighter-rouge"><pre>
READ <i>designator</i>
</pre></div>

A *condition* has the form

<div class="highlighter-rouge"><pre>
<i>expression</i> <i>relational-op</i> <i>expression</i>
</pre></div>

where *relational-op* is one of `<`, `<=`, `>`, `>=`, `=`, or `#` (not equals).

A *designator* has one of the following forms:

<div class="highlighter-rouge"><pre>
<i>identifier</i>
</pre></div>

<div class="highlighter-rouge"><pre>
<i>identifier</i> [ <i>expression-list</i> ]
</pre></div>

<div class="highlighter-rouge"><pre>
<i>identifier</i> . <i>identifier</i>
</pre></div>

An <i>expression-list</i> is a sequence of one or more *expression*s, separated by commas (`,`).

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
