---
layout: default
title: "Schedule"
category: "schedule"
---

This page lists lecture topics, readings, and exam dates.  It also lists assignment due dates.

The links to slides are provided for reference.  In general, there is no guarantee that they will be posted before class, or that their content will not change.

Readings:

* EaC is Cooper and Torczon, [Engineering a Compiler (2nd Ed.)](https://www.elsevier.com/books/engineering-a-compiler/cooper/978-0-12-088478-0)
* F&amp;B is Levine, [Flex and Bison](https://www.oreilly.com/library/view/flex-bison/9780596805418/)

*Note*: The schedule will become more concrete as the semester progresses. Expect it to be updated frequently.  Tentative topics are marked <span class="tentative">in a lighter italic font</span>: expect these to change.

Date               | Topic/Slides | Reading      | Assignment
:----------------: | ------------ | ------------ | ----------
Mon, Aug 31 | Course intro, lexical analysis: [slides](lectures/lecture01-public.pdf)
Wed, Sep 2 | Context free grammars, parse trees, ambiguity, recursive descent parsing: [slides](lectures/lecture02-public.pdf) | EaC 3.1–3.2
Mon, Sep 7 | *Holiday, no class*
Wed, Sep 9 | Limitations of recursive descent, precedence climbing: [slides](lectures/lecture03-public.pdf) | EaC 3.3
Mon, Sep 14 | Lexical analyzer generators, lex/flex: [slides](lectures/lecture04-public.pdf), [lexdemo.zip](lectures/lexdemo.zip) | EaC 2.1–2.5,<br>F&amp;B Chapters 1–2
Wed, Sep 16 | LL(1) parsing: [slides](lectures/lecture05-public.pdf) | EaC 3.3 | [A1](assign/assign01.html) due 9/18
Mon, Sep 21 | Parser generators, yacc/bison: [slides](lectures/lecture06-public.pdf) | F&amp;B Chapter 3
Wed, Sep 23 | Bottom-up parsing: [slides](lectures/lecture07-public.pdf) | EaC 3.4
Mon, Sep 28 | <b>Exam 1 out</b>, ASTs, Interpreters: [slides](lectures/lecture08-public.pdf) | 
Wed, Sep 30 | Interpreter runtime structures: [slides](lectures/lecture09-public.pdf) | 
Mon, Oct 5 | Context-sensitive analysis, attribute grammars: [slides](lectures/Context_sensitive_Analysis_I.pdf) | EaC 4.1–4.3
Wed, Oct 7 | Context-sensitive analysis, ad-hoc techniques: [slides](lectures/Context_sensitive_Analysis_II.pdf) | EaC 4.4
Mon, Oct 12 | Compiler project intro, ASTs and symbol tables: [slides](lectures/lecture12-public.pdf) | EaC 5.5
Wed, Oct 14 | Intermediate representations: [slides](lectures/Intermediate_Representations.pdf) | EaC 5.1–5.4 | [A2](assign/assign02.html) due 10/16
Mon, Oct 19 | AST visitors, symbol tables, the procedure abstraction: [slides](lectures/The_Procedure_Abstraction_I.pdf) | EaC Chapter 6
Wed, Oct 21 | Code shape for expressions: [slides](/lectures/Code_Shape_I_Quick_Intro_to_Code_Generation_+_Code_Shape_for_Expressions.pdf) | EaC 7.1–7.4
Mon, Oct 26 | <b>Exam 2 out</b>, x86-64 assembly language, code generation: [slides](lectures/lecture16-public.pdf) | 
Wed, Oct 28 | Arrays and strings: [slides](lectures/Code_Shape_II_Arrays_Aggregates_&_Strings.pdf) | EaC 7.5–7.7 | [A3](assign/assign03.html) due 10/30
Mon, Nov 2 | Boolean and relational expressions, decisions and loops: [slides](lectures/Code_Shape_III_Boolean_and_Relational_Expressions_+_Control_Flow.pdf)  | EaC 7.8 |
Wed, Nov 4 | Intro to Code optimization: [slides](lectures/Introduction_to_Optimization_terminology_&_local_value_numbering.pdf), [slides](lectures/Regional_Optimization_Superlocal_Value_Numbering_and_Loop_Unrolling.pdf) | EaC 8.1–8.5 |
Mon, Nov 9 | Intro to Global optimization, Instruction selection: [slides](lectures/Global_Optimization_Live_Analysis.pdf), [slides](lectures/Introduction_to_Instruction_Selection_and_Peephole_based_Selection.pdf) | EaC 8.6, 11.5
Wed, Nov 11 | Instruction selection, continued | | [A4](assign/assign04.html) due 11/13
Mon, Nov 16 | <b>Exam 3 out</b>, Local register allocation: [slides](lectures/Local_Register_Allocation_and_Lab_1.pdf) | EaC 13.1–13.3
Wed, Nov 18 | Discussion and [advice](assign/assign05-advice.html) on implementing code optimization 
Mon, Nov 23 | *Thanksgiving break*
Wed, Nov 25 | *Thanksgiving break*
Mon, Nov 30 | Dataflow analysis: [slides](lectures/foster-dataflow.pdf) | EaC 9.1–9.2, [Killdall-POPL73](lectures/killdall-popl73.pdf)
Wed, Dec 2 | Dataflow analysis, continued; [more code optimization advice](lectures/dec02-outline.txt)  | | [A5](assign/assign05.html) due 12/4
Mon, Dec 7 | Last day of class: [slides](lectures/lecture26-public.pdf) | [Gosling-IR95](https://dl.acm.org/doi/pdf/10.1145/202530.202541) | 
—          | <b>Exam 4</b> will be due during the final exam period
