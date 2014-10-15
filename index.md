---
layout: page
title: "MosquitOS - A tiny, annoying operating system"
excerpt: ""
category: articles
tags: [mosquitos, operating system]
comments: true
image:
  feature: sierras-1.jpg
---


<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
  <div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
  </div>
</section>



Welcome! This blog details my ongoing journey to write a modern toy operating system for the x86\_64 architecture. Throughout this process I noticed a lack of detailed information on how to interface with the hardware found in modern computers. There are obviously the multi-hundred page specification documents, but most of the tutorials I could find used older technologies (BIOS instead of EFI, 32-bit instead of 64-bit, etc.) The articles below will hopefully serve as a tutorial to those of you foolish enough to follow in my footsteps. In them, I try to detail my process, mistakes, and hopefully my successes.

 

I've tried to make the information as accurate as possible, but as this is a learning process for me as well, I guarantee there are tons of mistakes in my code and MosquitOS is a poor example of how to write an operating system. Please treat what follows not as "The One True and Correct Way to Write and Operating System," but instead as one guy’s "OS That Hasn’t Broken Anything Expensive Yet." If you run my code on your computer it could literally catch on fire. Always keep a bucket of water handy. Comments and corrections are appreciated!

 

Since many of the first articles were written much after the fact, they don’t follow the exact path I took through developing my OS. Instead, they follow what I think would have been a *good* order, and they take into account bug fixes/features I implemented long after the original functionality was somewhat complete. For a real history, feel free to checkout the [git repository](https://github.com/kmmoore/mosquitos).




Prerequisites
-------------

The articles assume you have a good working understanding of the C language, and some basic experience with x64 assembly. Additionally, the development is all done on the Linux command line, so experience with this is useful.


Articles
--------

1.  [Introduction]({% post_url 2014-10-05-introduction %})
2.  [UEFI and Bootloaders]({% post_url 2014-10-06-uefi-and-bootloaders %})
3.  [Setting Up a Development Environment]({% post_url 2014-10-07-development-environment %})
4.  [Build Systems]({% post_url 2014-10-08-build-systems %})
5.  [Writing a UEFI Application]({% post_url 2014-10-09-writing-a-uefi-application %})
6.  [Writing a Bootloader]({% post_url 2014-10-10-writing-a-bootloader %})
7.  A Basic Kernel
8.  Kernel Debugging
9.  Dynamic Memory (And Life Without libc)
10. Interrupts
11. Pre-emptive Multithreading
12. ACPICA
13. PCI
14. SATA
