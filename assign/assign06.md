---
layout: default
title: "Assignment 6: Procedures"
---

**Due**: Monday, Dec 14th by 11pm

Points: This assignment is worth 50 points

Note that this is an **optional** assignment, and will be graded as extra credit.

# Overview

Your task is to add support for procedures to your compiler.  The syntax you use,
as well as the semantics of procedure calls and returns, is up to you.

Include a `README.txt` file in your submission explaining your design and
implementation.  Also, include at least one test program to demonstrate how
procedures work.

## Submitting

To submit, create a zipfile containing

* all of the files needed to compile your program, and
* your `README.txt` file

Suggested commands:

```bash
make clean
zip -9r solution.zip Makefile *.h *.c *.y *.l *.cpp *.rb README.txt
```

The suggested command would create a zipfile called `solution.zip`.  Note that if your solution uses C exclusively, you can omit `*.cpp` from the filename patterns.  If you used the `scan_grammar_symbols.rb` script, then make sure you include the `*.rb` pattern as shown above.

Upload your submission to [Gradescope](https://www.gradescope.com).  If you are registered for 601.428, upload to **Assign06&#95;428**.  If you are registered for 601.628, upload to **Assign06&#95;628**.
