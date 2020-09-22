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

## Semantics

Your interpreter will evaluate programs for a real programming language, so the semantics are considerably more interesting than in [Assignment 1](assign01.html).

This section is a reasonably precise run-time semantics of interpreter programs; however, the [test suite](#testing) is also an extremely *useful* specification of run-time semantics.

### Values

The interpreter language is dynamically-typed: variables can contain any kind of value.

Values are defined by the `ValueKind` enumeration and the `Value` struct data type (in `value.h`):

```c
enum ValueKind {
  VAL_VOID,
  VAL_ERROR,
  VAL_INT,
  VAL_FN,
  VAL_INTRINSIC,
  // could add additional kinds of values...
};

struct Value {
  enum ValueKind kind;
  long ival;
  struct Function *fn;
  IntrinsicFunction *intrinsic_fn;

  // You may add additional struct members for additional kinds
  // of values.
};
```

Instances of `struct Value` in the interpreter are meant to be accessed by value (rather than being dynamically-allocated).

The `kind` field specifies what kind of value a `struct Value` instance represents.

For `VAL_INT` values, the `ival` field stores a (64 bit signed) integer value.

For `VAL_FN` values, the `fn` field stores a pointer to a `struct Function`.

For `VAL_INTRINSIC` values, the `intrinsic_fn` field stores a pointer to an `IntrinsicFunction`.  The `IntrinsicFunction` type is defined as follows:

```c
typedef struct Value IntrinsicFunction(struct Value *args, int num_args,
                                       struct Interp *interp);
```

All of the infix operators, with the important exception of `==` and `!=`, operate exclusively on integer values.  It is a run-time error to apply an infix operator other than `==` and `!=` to a non-integer value.

The unary `-` operator is also only defined for integer values, and it is a run-time error to apply it to a non-integer value.

### Variables

Variables must be explicitly declared with a variable declaration statement.  Variable declarations define in functions are local to the function.  Other variables are global variables.

Local variables in functions have the entire function as their scope, even if they are defined in nested blocks of loops or if/else statements.  (This is similar to JavaScript.)  Variables must be declared before being referenced.

Uninitialized variables have the initial integer value 0.

A primary expression which is an identifier is a variable reference.

### Expressions

An expression computes a value.

Primary expressions should be computed as follows:

* A variable reference yields the value of the named variable
* An integer literal is a `VAL_INT` value converted from the lexeme of the integer literal token
* A function call evaluates each argument expression in order from left to right and passes them as parameters to the called function

The infix operators, when operating on two `VAL_INT` values, should behave the same way as the corresponding C/C++ operators.

As a special case, the `==` and `!=` operators may also be used with `VAL_NIL` values.  Two `VAL_NIL` values should compare as equal to each other.  If a `VAL_INT` value is compared to a `VAL_NIL` value, they are not equal.

The unary `-` operator, when applied to a `VAL_INT` value, should return the negation of that value.  I.e., the unary `-` operator applied to a variable reference `n` should do the same compuation as the expression `0 - n` would.

### Statements and statement lists

When evaluating a statement list, the value of the statement list is the value of the last statement.

Evaluating an expression statement yields the result of evaluating the expression.

An if or if/else statement should work as follows

* the condition is evaluated
* if the result of evaluating a condition is a "true" value, then the if clause is evaluated
* if the result of evaluating a condition is not a "true" value, and there is an else clause, then the else clause is evaluated
* the result of evaluating an if or if/else statement is a `VAL_VOID` value

A while loop is similar to an if statement (without an else clause), except that the loop body is evaluated repreatedly as long as the condition evaluates to a "true" value.  The result of evaluating a while statement is a `VAL_VOID` value.

### "true" values

A nonzero `VAL_INT` value is a true value.

A `VAL_FN`, `VAL_INTRINSIC`, or `VAL_CONS` value is a true value.

Any other value is *not* a true value.

### Functions and function calls

Note that variables and functions are in the same namespace.  A variable reference naming a function produces a `VAL_FN` value; i.e., the value is the function the name refers to.  Function calls should do a variable lookup to find the function being called.  This means that a function can be passed as an argument to another function.  For example:

```
function add1(n) {
  n+1;
}
function apply(f, x) {
  f(x);
}
apply(add1, 42);
```

The result of this program is 43.

# Examples, hints, advice, etc.

TODO

## Testing

Tests can be found in the following repository: <https://github.com/jhucompilers/fall2020-tests>

The tests are set up in a way that is very similar to [Assignment 1](assign01.html#testing).  Make sure you set the `ASSIGN02_DIR` environment variable to the directory containing your `interp` executable, e.g.

```bash
export ASSIGN02_DIR=$HOME/git/assign02
```

if the directory containing your code is in an `assign02` subdirectory of your `git` directory.

Run a test using the `run_test.rb` script, e.g.

```bash
./run_test.rb arith01
```

You can run all of the test cases using the `run_all.rb` script, e.g.

```bash
./run_all.rb
```
