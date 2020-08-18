---
layout: default
title: "Assignment 1: Mini Calculator"
---

Due: TBD

# Overview

In this assignment you will implement a mini calculator program which can evaluate infix
expressions using 64 bit signed integer values.  The main purpose of the assignment is
provide an opportunity implement a lexer, parser, and runtime support for a simple but
nontrivial language.

# Language specification

This section describes the lexical structure, syntax, and semantics of the calculator language.

## Lexical structure

The tokens (lexical units) of the calculator language are as follows.

An *identifier* is a letter followed by 0 or more letters or digits.  (Letters can be either upper or lower case. Case is significant, so `foo` and `Foo` are different identifiers.)

An *integer literal* is a sequence of 1 or more digits.

The operators of the language are `+`, `-`, `*`, `/`, `^`, and `=`.

The other single-character tokens are `(`, `)`, and `;`.

Whitespace characters are not significant, and can be used to separate tokens.  For example, the character sequence `abc123` is a single identifier token, but the sequence `abc 123` is two tokens, an identifier (`abc`) followed by an integer literal `123`.

## Syntax

A *unit* consists of a series of one or more *expressions*, with a single semicolon (`;`) token terminating each expression.

There are several kinds of expressions.  A single identifier or integer literal is an expression.  The `+`, `-`, `*`, `^`, and `=` tokens are binary infix operators that combine left and right subexpressions.  The binary operators have the following precedence and associativity:

Operator(s) | Precedence  | Associativity
----------- | ----------- | -------------
`=`         | 1 (lowest)  | right
`+`, `-`    | 2           | left
`*`, `/`    | 3           | left
`^`         | 4 (highest) | right

Expressions can be parenthesized to force the order of evaluation.  For example, in the expression

> `a + b * 3`

the multiplication (`*`) will happen before the addition (`+`) because it is a higher-precedence operator.  However, in the expression

> `(a + b) * 3`

the addition will happen before the multiplication.

Note that we are deliberately *not* giving you an exact grammar for this language.  Part of your job is to formalize this language's grammatical structure: for example by defining it as a context-free grammar.  You will need to think about what parsing techniques are appropriate for this language.

## Semantics

Each expression computes a 64 bit signed integer value.  You should represent values using the `long` data type in the C or C++ program implementing the calculator.

An integer literal is, as the name suggests, a literal integer value, specified in base 10.

The `+`, `-`, `*`, and `/` operators perform addition, subtraction, multiplication, and division, respectively.  They should do the same computation as the corresponding operator in C and C++.

The `^` operator performs exponentiation.  For example, the expression

> `2 ^ 3`

should evaluate to the result value 8.

The `=` operator performs variable assignment.  Note that the left-hand operand of the `=` operator *must* be a identifier naming the variable being assigned.  For example, even though

> `2 = 3`

is a syntactically valid expression according to the [Syntax](#syntax) section above, it is not semantically valid because the left hand side is not an identifier.  The result of evaluating an assignment expression is the same as the result of evaluating the right-hand subexpression.  So, the result of evaluating `a = 3` is 3.  Note that because assignments are right-associative, they may be "chained".  For example, the expression `a = b = 3` will assign the value 3 to both `b` and `a`.

Your calculator program should evaluate each expression in order.  If a variable is assigned in an earlier expression, its value can be used in a later expression.  The result of evaluating the last expression is the overall result of the entire unit.

# Requirements

This section details the functional requirements.

## Invocation

Your program should support two ways of being invoked.

If invoked without any command line arguments, it should read input from `stdin` (or `cin`).

If invoked with one (non-option) command line argument, it should read input from the file named by the command line argument.

Your program may, optionally, support "option" arguments.  When the program is invoked with a command line argument beginning with "-", it should treat the argument as an option.  There is no requirement to support any particular options, but one option you might want to support is a `-p` option to allow the program to print a parse tree of the input.  This should be useful when implementing the parser.

## Reporting the evaluation result

After evaluating all of the expressions in the input, the program should print a single line of output to `stdout` or `cout` of the form

<div class="highlighter-rouge">
<pre>
Result: <i>N</i>
</pre>
</div>

where <i>N</i> is the integer value resulting from evaluating the last expression.

## Error reporting

Lexical, syntax, and semantic errors should be reported by printing a single line of output to `stderr` or `cerr` of the form

<div class="highlighter-rouge">
<pre>
<i>filename</i>:<i>line</i>:<i>column</i>: Error: <i>explanation</i>
</pre>
</div>

In an error message:

* <i>filename</i> should be the name of the input file, with the special name `<stdin>` if the program is reading from standard input
* <i>line</i> is the line number associated with the error
* <i>column</i> is the column number associated with the error
* <i>explanation</i> is a message explaining the error

Line and column values should each start at 1.  Each newline character should increase the current by 1 and reset the column to 1.  All non-newline characters should increase the column by 1.

<i>TODO: examples of what kinds of errors to report</i>

## Additional requirements for 628 students

TODO: explanation of additional requirements for students taking 601.628

# Examples, hints, advice, etc.

## Example invocations and expected results

TODO

## Testing

Tests can be found in the following repository: <https://github.com/jhucompilers/fall2020-tests>

You can clone this repository using the following command:

```bash
git clone https://github.com/jhucompilers/fall2020-tests.git
```

The tests for this assignment are in the `assign01` subdirectory.  Each test case consists of an input file, and either an expected output file or an expected error file.  The `run_test.rb` script executes a single test case.  Before running the script, you will need to set the `ASSIGN01_DIR` environment variable to the full path of the directory containing your `minicalc` executable.  For example, if your work for this assignment is in a directory called `git/minicalc` within your Unix home directory, you could use the command

```bash
export ASSIGN01_DIR=$HOME/git/minicalc
```

to set this environment variable.  You will also need to change directory into the `assign01` subdirectory: from the `fall2020-tests` directory, run the command

```bash
cd assign01
```

The `run_test.rb` script expects a single command line argument, which is the name of the test case.  For example, to run the `arith01` test case, invoke the `run_test.rb` script as follows:

```bash
./run_test.rb arith01
```

You can run all of the test cases using the `run_all.rb` script:

```bash
./run_all.rb
```

We highly encourage you to write your own tests.  To do so, add an input file to the `input` subdirectory.  The input file should have a filename ending in `.in`.  You can use the `genout.sh` script to generate an expected output or expected error file.  For example, if your input file is called `input/contrib17.in`, the command

```bash
./genout.sh input/contrib17.in
```

will generate an output file in either the `expected_output` or `expected_error` directory.

We also encourage you to contribute your tests to the repository.  To do so, fork the [fall2020-tests](https://github.com/jhucompilers/fall2020-tests) repository on Github, commit and push new tests, and then create a pull request.  Note that when your changes are incorporated into the repository, your Github identity will become part of the commit history.  Please name your tests using the `contrib` prefix so that they are distinguishable from the "official" tests.

## Approach

TODO: how to approach implementation
