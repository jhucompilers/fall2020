---
layout: default
title: "Assignment 5: Better code generation"
---

**Due**: Friday, Dec 4th by 11pm

Points: This assignment is worth 150 points

# Overview

In this assignment, you will implement optimizations to improve the target code quality
compared to the code generator you implemented in [Assignment 4](assign04.html).

## Grading criteria

* Optimizations implemented: 40%
* Report:
    * General discussion of optimizations implemented: 25%
    * Analysis and experimental evaluation: 25%
* Design and coding style: 10%

# Getting started

Start with the code you wrote for [Assignment 4](assign04.html).  (I would recommend making a copy rather than directly modifying your original code, but it's up to you.)

The following source files are provided to implement a control flow graph representation for high level and target code:

* [cfg.h](assign05/cfg.h), [cfg.cpp](assign05/cfg.cpp): expanded to provide `BasicBlock`, `Edge`, `ControlFlowGraph`, `ControlFlowGraphBuilder`, and `ControlFlowGraphPrinter` types
* [highlevel.h](assign05/highlevel.h), [highlevel.cpp](assign05/highlevel.cpp): expanded to provide concrete `HighLevelControlFlowGraphBuilder` and `HighLevelControlFlowGraphPrinter` types
* [x86\_64.h](assign05/x86_64.h), [x86\_64.cpp](assign05/x86_64.cpp): expanded to provide `X86_64ControlFlowGraphBuilder` and `X86_64ControlFlowGraphPrinter` types

# Your task

## Implementing optimizations

The primary metric you should be optimizing for is the run-time efficiency of the generated assembly code.

Optimizing for (smaller) code size is also valid, but should be considered a secondary concern.

Two benchmark programs are provided (in the [assign05/input](https://github.com/jhucompilers/fall2020-tests/tree/master/assign05/input) subdirectory of the [test repository](https://github.com/jhucompilers/fall2020-tests)):

* [array20.in](https://github.com/jhucompilers/fall2020-tests/blob/master/assign05/input/array20.in) multiplies 500x500 matrices represented as 250,000 element 1-D arrays
* [multidim20.in](https://github.com/jhucompilers/fall2020-tests/blob/master/assign05/input/multidim20.in) multiplies 500x500 matrices represented as 500x500 2-D arrays (not required for 428 students)

**Note**: additional benchmark programs may be added.  The assignment description will be updated if this happens.

You can benchmark your compiler as follows (user input in bold):

<div class="highlighter-rouge"><pre>
$ <b>./build.rb array20</b>
Generated code successfully assembled, output exe is out/array20
$ <b>time ./out/array20 &lt; data/array20.in &gt; actual&#95;output/array20.out </b>

real	0m3.076s
user	0m3.060s
sys	0m0.016s
$ <b>diff expected_output/array20.out actual_output/array20.out</b>
</pre></div>

Your can substitute other test program names in place of `array20`.

If the `diff` command does not produce any output, then the program's output matched the expected output.

The `build.rb` puts the generated assembly language code in the `out` directory, so in the above test case, the assembly code would be in `out/array20.S`.

The **user** time in the output of the `time` command represents the amount of time the test program spent using the CPU to execute code, and so is a good measure of how long it took the compiled program to do its computation.  Your code optimizations should aim to reduce user time for the benchmark programs.

## Analysis and experiments

As you work on your optimizations, do experiments to see how effective they are at improving the efficiency of the generated code.

You can use any test programs as a basis for these experiments.  One approach is to start with some very simple test programs, and then work your way up to the benchmark programs (which are relatively complex.)

## Report

One of the most important deliverables of this assignment is a written report that describes what optimizations you implemented and how effective they were.

For each optimization technique you implemented, your report should document

* how the quality of the generated code was improved (include representative snippets of code before and after the optimization, for relevant test programs)
* how the efficiency of the generated code improved (i.e., how much did performance of the benchmark programs improve)

Your report should also discuss what inefficiencies remain in the generated code, and what optimization techniques might be helpful for addressing them.

# Hints and suggestions

## Ideas for optmizations to implement

Some ideas for code optimizations:

* Use machine registers for (at least some) local variables, especially loop variables
* Allocate machine registers for virtual registers
* Peephole optimization of the high-level code to eliminate redundancies and reduce the number of virtual registers needed
* Peephole optimization of the generated x86-64 assembly code to eliminate redundancies

The recommended way to approach the implementation of optimizations is to look at the high-level and low-level code generated by your compiler, and look for specific inefficiencies that you can eliminate.

We *highly* recommend that you keep notes about your work, so that you have a written record you can refer to in preparing your report.

## Intermediate representations

You can implement your optimization passes as transformations of `InstructionSequence` (linear IR) or `ControlFlowGraph` (control flow graph IR).  We recommend that you implement transformations constructively: for example, if transforming a `ControlFlowGraph`, the result of the transformation should be a different `ControlFlowGraph`, rather than an in-place modification of the original `ControlFlowGraph`.

If you do transformations of an `InstructionSequence`, you will need to take control flow into account.  Any instruction that either

* is a branch, or
* has an immediate successor that is a labeled control-flow target

should be considered the end of a basic block.

If you decide to use the `ControlFlowGraph` intermediate representation, you can create it from an `InstructionSequence` as follows.  For high-level code:

```cpp
InstructionSequence iseq = /* InstructionSequence containing high-level code... */
HighLevelControlFlowGraphBuilder cfg_builder(iseq);
ControlFlowGraph *cfg = cfg_builder.build();    
```

For low-level (x86-64) code:

```cpp
InstructionSequence *ll_iseq = /* InstructionSequence containing low-level code... */
X86_64ControlFlowGraphBuilder xcfg_builder(ll_iseq);
ControlFlowGraph *xcfg = xcfg_builder.build();
```

To convert a `ControlFlowGraph` back to an `InstructionSequence`:

```cpp
ControlFlowGraph *cfg = /* a ControlFlowGraph */
InstructionSequence *result_iseq = cfg->create_instruction_sequence();
```

Note that the `create_instruction_sequence()` method is not guaranteed to work if the structure of the `ControlFlowGraph` was modified.  I.e., we do not recommend implementing optimizations which change control flow.  Local optimizations (within single basic blocks) are recommended.

# Submitting

To submit, create a zipfile containing

* all of the files needed to compile your program, and
* a PDF of your report in a file called `report.pdf`

Suggested commands:

```bash
make clean
zip -9r solution.zip Makefile *.h *.c *.y *.l *.cpp *.rb report.pdf
```

The suggested command would create a zipfile called `solution.zip`.  Note that if your solution uses C exclusively, you can omit `*.cpp` from the filename patterns.  If you used the `scan_grammar_symbols.rb` script, then make sure you include the `*.rb` pattern as shown above.

Make sure that your report is a PDF file called `report.pdf`.

Upload your submission to [Gradescope](https://www.gradescope.com).  If you are registered for 601.428, upload to **Assign05&#95;428**.  If you are registered for 601.628, upload to **Assign05&#95;628**.
