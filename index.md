---
layout: page
title: Home
---

MosquitOS - A tiny, annoying operating system
=============================================

 

Welcome! This blog details my ongoing journey to write a modern toy operating
system for the x86\_64 architecture. Throughout this process I noticed a lack of
detailed information on how to interface with the hardware found in modern
computers. There are obviously the multi-hundred page specification documents,
but most of the tutorials I could find used older technologies (BIOS instead of
EFI, 32-bit instead of 64-bit, etc.) The articles below will hopefully serve as
a tutorial to those of you foolish enough to follow in my footsteps. In them, I
try to detail my process, mistakes, and ultimately (hopefully) my successes.

 

I’ve tried to make the information as accurate as possible, but as this is a
learning process for me as well, I guarantee there are tons of mistakes in my
code and much of what I write is actually no way to write an operating system.
Please treat what follows not as “The One True and Correct Way to Write and
Operating System,” but instead as one guy’s “Attempt That Hasn’t Broken Anything
(too) Expensive Yet.” If you run my code on your computer it could literally
catch on fire. Always keep a bucket of water handy. Comments and corrections are
appreciated!

 

Articles
--------

 

Since many of the first articles were written much after the fact, they don’t
follow the exact path I took through developing my OS. Instead, they follow what
I think a *good* order would have been and they take into account bug
fixes/features I implemented long after the original functionality was somewhat
complete.

 

The articles assume you have a good working understanding of the C language, and
some basic experience with x64 assembly. Additionally, the development is all
done on the Linux command line, so experience with this is useful. For overviews
of all of these topics, see the **Resources** page.

 

1.  Introduction and Motivation

2.  Setting Up a Build Environment

3.  (U)EFI and Bootloaders

4.  Writing a Real Bootloader

5.  Build Systems

6.  A Basic Kernel

7.  Kernel Debugging

8.  Dynamic Memory (And Life Without `libc`)

9.  Interrupts

10. Pre-emptive Multithreading

11. ACPICA

12. PCI

13. SATA
