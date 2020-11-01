---
layout: default
title: "Assignment 4: Basic code generation"
---

*This assignment description is somewhat preliminary, and will be updated*

**Due**: Friday, Nov 13th by 11pm

Points: This assignment is worth 200 points

# Overview

In this assignment, you will implement x86-64 assembly code generation for the source language you wrote a front-end for in [Assignment 3](assign03.html).

The purpose of this assignment is not to generate *good* code.  The goal is to generate *working* code, and create intermediate representations that can be used as the basis of optimizations to improve the code quality.

## Grading criteria

The grading criteria are as follows:

* Packaging: 5%
* Design and coding style: 10% (see expectations for [design, coding style, and efficiency](design.html))
* Functional requirements: 85%

For the part of the grade allocated to functional requirements, the grading will be substantially weighted towards correct compilation of programs involving only `INTEGER` variables and values (no arrays or records.)  You should get this working correctly, including control flow (`IF`, `ELSE`, `WHILE`, `REPEAT`) before tackling arrays and records.

# Getting started

Start with the code you wrote for [Assignment 3](assign03.html).  (I would recommend making a copy rather than directly modifying your original code, but it's up to you.)

The following source files are provided as a reference for a "code-like" intermediate representation:

* [cfg.h](assign04/cfg.h), [cfg.cpp](assign04/cfg.cpp) provide the `Operand`, `Instruction`, and `InstructionSequence` and classes, which implement a generic "code-like" intermediate representation
* [highlevel.h](assign04/highlevel.h), [highlevel.cpp](assign04/highlevel.cpp): these are an example of "high-level" opcodes that you might use as an initial translation target
* [x86\_64.h](assign04/x86_64.h), [x86\_64.cpp](assign04/x86_64.cpp): these implement x86-64 opcodes and machine registers

Note that the `PrintInstructionSequence` class in `cfg.h` and `cfg.cpp` is able to print x86-64 assembly code in the formated expected by the GNU assembler.  (Specifically, this is implemented by the `PrintX86_64InstructionSequence` class.)

You are not required to use any of the above code.

# Language semantics

This section clarifies some of the runtime semantics that weren't specified explicitly in [Assignment 3](assign03.html).  As with previous assignments, the most important specification of expected behavior is the [public test suite](#testing).

The `INTEGER` data type must be a signed integer type with at least 32 bits of precision.  For simplicity, we recommend that you make `INTEGER` a 64 bit type.

A `READ` statement should work as follows:

* For reading into an `INTEGER` variable (or array element, or field), the compiled program should make a call to the `scanf` function to read a single integer value
* *Coming soon, behavior of READ using other types*

A `WRITE` statement should work as follows:

* For writing an `INTEGER` value, the compiled program should print its decimal representation, followed by a single newline (`\n`) character; you should use `printf` to generate the output
* *Coming soon, behavior of WRITE using other types*

Array subscript references do not need to be bounds-checked.

# Recommendations and advice

This is a big task!  You will want to start early and make steady incremental progress.  Try to get compilation of simple programs working first, then move on to more complicated ones.

## Approach

We recommend that you follow the high-level compilation model suggested by the textbook, which is to generate "high-level" (machine independent) code from your source AST, and then translate this high-level code into machine-specific target code (in our case, x86-64 assembly language.)

The high-level instruction types in `highlevel.h` and `highlevel.cpp` are provided as an example, but you may want to define your own form of high-level code.

## Storage model

By far the most important thing you will need to think about carefully is how to allocate storage for variables, including arrays and records.

Since the source language has only a fixed set of program-level variables, you will be able to determine the storage requirements, including sizes and offsets of each variable, as part of semantic analysis.

For this assignment, we strongly recommend that you allocate storage for variables in memory.  All references to variable, array elements, and fields should be implemented as loads from memory.  All assignments should be stores to memory.

If your high level code representation uses "virtual registers" to name values (which is recommended!), then the question arises how and where you should allocate storage for the virtual registers.  We recommend that you allocate storage for virtual registers in memory, alongside the actual program variables.  In this sense, virtual registers become another form of local variable.

In your code generator, it is a good idea to annotate AST nodes with a representation of where their storage can be found.  One good way to do this is to add a field of type `Operand` to the `Node` data type.  If an AST node represents a value in a virtual register, then the node's `Operand` indicates which virtual register it is.  If an AST node represents an assignable location (variable, array element, or field ref — the grammar refers to these as "designators"), the `Operand` should be a memory reference.

## Emitting instructions

Your code generators (both high level and low level) should build an `InstructionSequence` by adding `Instruction` objects to it.

For example, here is a possible implementation of a `visit_int_literal` method in an AST visitor to do high-level code generation:

```cpp
void HighLevelCodeGen::visit_int_literal(struct Node *ast) {
  long vreg = next_vreg();
  Operand destreg(OPERAND_VREG, vreg);
  Operand immval(OPERAND_INT_LITERAL, ast->get_ival());
  Instruction *ins = new Instruction(HINS_LOAD_ICONST, destreg, immval);
  m_iseq->add_instruction(ins);
  ast->set_operand(destreg);
}
```

## Control flow

The `InstructionSequence` class allows labels to be defined.  `Operand` values can be be labels.  Control flow instructions, such as unconditional and conditional jumps, should have a single `Operand` with the target label.

As an example of how control flow can be implemented using labels, here is an example of high-level code generation for an `IF` statement (again, as part of an AST visitor class):

```cpp
void HighLevelCodeGen::visit_if(struct Node *ast) {
  std::string out_label = next_label();

  Node *cond = ast->get_kid(0);
  Node *iftrue = ast->get_kid(1);

  cond->set_inverted(true);
  cond->set_operand(Operand(out_label));

  visit(cond);
  visit(iftrue);
  m_iseq->define_label(out_label);
}
```

In the code above, the `next_label()` method generates a new control flow label.  The statement consists of a condition (`cond`) and an "if true" block (`iftrue`).  Only a single label is needed to mark the code that follows the `IF` statement.  Note that the condition is compiled using an "inverted" comparison, because we want a conditional jump to transfer control to the `out_label` if the condition evaluates as false.

In general, the `define_label` method defines a label that marks the next instruction that will be appended to the `InstructionSequence` (using the `add_instruction` method.)

## Emitting low-level instructions

Each high-level instruction should generate one or more low-level (x86-64) instructions.


Assume that `hlins` is a reference to an `Instruction` object in the high-level code. The translation of a `HINS_LOAD_ICONST` high-level instruction might look like this:

```cpp
emit(new Instruction(MINS_MOVQ, hlins[1], vreg_ref(hlins[0])));
```

The assumption is that the high-level instruction has two operands, `hlins[0]`, which is the destination virtual register, and `hlins[1]`, which is the literal (immediate) value being stored in the destination virtual register.

The `vreg_ref` method translates an `Operand` referring to a virtual register ("vreg") into a low-level (x86-64) memory reference operand.  The low-level operand would take into account where the storage for virtual registers is allocated in memory, as well as the offset of the specific virtual register in the block of memory allocated for all virtual registers.

`MINS_MOVQ` is a low-level opcode specifying the x86-64 `movq` instruction.

## Some example translations

For the [loop01](https://github.com/jhucompilers/fall2020-tests/blob/master/assign04/input/loop01.in) program in the test repo, here are possible high-level and low-level translations:

* [loop01.txt](assign04/loop01.txt): high-level code
* [loop01.S](assign04/loop01.S): generated x86-64 assembly code

Note that these translations are by no means the only possible translations, or even "good" translations.  In fact, the generated x86-64 code is pretty awful!  This is due to the simplistic storage model being used.  In [Assignment 5](assign/assign05.html) we'll use techniques such as register allocation and peephole optimization to generate better assembly code.

## Testing

Tests can be found in the following repository: <https://github.com/jhucompilers/fall2020-tests>, in the `assign04` subdirectory.

Set the `ASSIGN04_DIR` environment variable to the name of the directory containing your project.  For example, if your project is in the `git/assign04` subdirectory of your home directory, use the command

```bash
export ASSIGN04_DIR=$HOME/git/assign04
```

To simply compile and assemble a program, use the `build.rb` script:

<div class="highlighter-rouge"><pre>
./build.rb <i>testname</i>
</pre></div>

For example, if you used `loop01` as *testname*, and compilation and assembly were successful, the `out` directory will contain the files `out/loop01.S` and `out/loop01`.  The latter is an executable which you can test interactively, including running it in `gdb`.

To run a test:

<div class="highlighter-rouge"><pre>
./run&#95;test.rb <i>testname</i>
</pre></div>

where <i>testname</i> is one of the tests, e.g., <code>arith01.in</code>.

To run all of the tests:

<div class="highlighter-rouge"><pre>
./run&#95;all.rb
</pre></div>

As with previous assignments, we encourage you to contribute your own tests to the repository.  See the [Testing section of Assignment 2](assign02.html#testing) for details regarding how to contribute additional tests.

# Submitting

To submit, create a zipfile containing all of the files needed to compile your program.  Suggested commands:

```bash
make clean
zip -9r solution.zip Makefile *.h *.c *.y *.l *.cpp *.rb
```

The suggested command would create a zipfile called `solution.zip`.  Note that if your solution uses C exclusively, you can omit `*.cpp` from the filename patterns.  If you used the `scan_grammar_symbols.rb` script, them make sure you include the `*.rb` pattern as shown above.

Upload your submission to [Gradescope](https://www.gradescope.com).  If you are registered for 601.428, upload to **Assign04&#95;428**.  If you are registered for 601.628, upload to **Assign04&#95;628**.
