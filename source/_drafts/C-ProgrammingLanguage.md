---
title: C-ProgrammingLanguage
tags:
---

> In the UNIX operating system, all input and output is done by reading or writing files,because all peripheral devices, even keyboard and screen, are files in the file system.

## The structue of the book _ C programming language

- Type,Operators and Expression
- Control Flow
- Functions and Program Structure
- Pointers and Array
- Structure
- Input and Output
- The Unix System Interface

In C, the semicolon is a statement terminator, rather than a separator as it is in languages like
Pascal

## File Descriptors

All information about an open file is maintained by the system; the user program refers to the file only by the file descriptor.

When the command interpreter (the ``shell'') runs a program,three files are open, with file descriptors 0, 1, and 2, called the standard input, the standard output, and the standard error.

If a program reads 0 and writes 1 and 2, it can do input and output without worrying about opening files.

the file assignments(operration) are changed by the shell, not by the program. The program does not know where its input comes from nor where its output goes, so long as it uses file 0 for input and 1 and 2 for output.

**If you want to learn more,buy this book and practice.**
