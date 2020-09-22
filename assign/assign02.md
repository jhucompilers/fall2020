---
layout: default
title: "Assignment 2: Interpreter"
---

*Preliminary assignment description, not official yet*

**Due**: TBD

# Overview

In this assignment you will implement an AST-based interpreter for a simple programming
language based on the calculator language from [Assignment 1](assign01.html).

## Grading criteria

TODO

# Getting started

Start by downloading the assignment skeleton: [assign02.zip](assign02.zip).

Starter code is provided, including:

* the `main` function
* a skeleton lexical analyzer (`lex.l`)
* a skeleton parser (`parse.y`)
* a complete implementation of the `struct Node` data type, which can be used for representation of lexical tokens and tree nodes (parse tree and/or AST)
* a `scan_grammar_symbols.rb` script for scanning the parser specification file to determine the names of all terminal and nonterminal grammar symbols, and create source files `grammar_symbols.h` and `grammar_symbols.c`
* the `treeprint` module for printing trees built from `struct Node` instances
* the `util` module, which includes functions for memory allocation, string duplication, and reporting of fatal errors
* the `cpputil` module, which includes a function (`cpputil::format`) for creating a C++ `std::string` value using printf-style formatting
* a skeleton interpreter implementation (`interp.h`, `interp.cpp`)

You may use the provided code in whatever way you see fit, including making changes, or not using it at all.  However, it is a requirement of this assignment that you use flex and bison to create the lexical analyzer and parser.

# Language specification

This section describes the lexical structure, syntax, and semantics of the interpreter language.

## Lexical structure

The tokens (lexical units) of the interpreter language are very similar to those of the [calculator language](assign01.html).

There are several *keywords*: `var`, `function`, `if`, `else`, and `while`.

An *identifier* is a letter followed by 0 or more letters or digits.  (Letters can be either upper or lower case. Case is significant, so `foo` and `Foo` are different identifiers.)

An *integer literal* is a sequence of 1 or more digits.

The operators of the language are `+`, `-`, `*`, `/`, `==`, `!=`, `<`, `>`, `<=`, `>=`, `&&`, `||`, and `=`.

Tokens used for grouping, sequences, and terminating statements are `(`, `)`, `{`, `}`, `,`, and `;`.

Whitespace characters are not significant, and can be used to separate tokens.  For example, the character sequence `abc123` is a single identifier token, but the sequence `abc 123` is two tokens, an identifier (`abc`) followed by an integer literal `123`.

C++-style comments are supported: a comment begins with `//` and continues until the end of the source line.  Comments should be ignored by the interpreter.

## Syntax

A *translation unit* consists of a sequence of one or more *definitions*.

A *definition* is a *statement* or *function*.

There are several kinds of *statement*s:

* An *expression-statement* is an *expression* followed by a semicolon (`;`)
* A *variable-declaration-statement* is the `var` keyword, followed by a comma-separated list of one or more identifiers, followed by a semicolon
* An *if-statement* has the form <code class="highlighter-rouge">if (<i>condition</i>) { <i>statement-list</i> }</code>
* An *if-else-statement* has the form <code class="highlighter-rouge">if (<i>condition</i>) { <i>statement-list</i> } else { <i>statement-list</i> }</code>
* A *while-statement* has the form <code class="highlighter-rouge">while (<i>condition</i>) { <i>statement-list</i> }</code>

A *function* has the form <code class="highlighter-rouge">function <i>identifier</i>(<i>opt-parameter-list</i>) { <i>statement-list</i> }</code>.  An *opt-parameter-list* is a comma-separated sequence of zero or more identifiers.

An *expression* is a sequence of one or more *unary-expression*s separated by infix operators.  The infix operators are as follows:

Operator(s)                      | Precedence  | Associativity
-------------------------------- | ----------- | -------------
`=`                              | 1 (lowest)  | right
`||`                             | 2           | left
`&&`                             | 3           | left
`==`, `!=`, `<`, `>`, `<=`, `>=` | 4           | left
`+`, `-`                         | 5           | left
`*`, `/`                         | 6 (highest) | left

A *unary* expression is either a single *primary-expression*, or the unary `-` operator followed by a *unary-expression*.

A *primary-expression* is an identifier, integer literal, parenthesized subexpression, or *function-call*.

A *function-call* has the form <code class="highlighter-rouge"><i>identifier</i> (<i>opt-argument-list</i>)</code>.  An *opt-argument-list* is a comma-separated sequence of zero or more expressions.

You will need to add grammar rules to `parse.y` according to these specifications.
