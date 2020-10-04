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

TODO

## Syntax

TODO

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
