---
layout: post
title: "Introduction"
excerpt: "Why I started the MosquitOS project, and what my goals for it (and this blog) are."
category: articles
tags: [mosquitos, operating system]
comments: true
image:
  feature: sierras-4.jpg
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
 

This is the ​first installment in a series of articles detailing my experience writing a modern toy operating system from scratch. To see a list of articles, go to the [overview]({{ site.url }}).


History
-------

When I was in college, I took an upper-level class on operating system design in which my partner and I had the opportunity to actually design and implement much of the functionality in a toy operating system called `pintos`. During the 10-week class, we implemented features such as thread scheduling, virtual memory, user-land program loaders, and a filesystem. While this certainly felt like we were programming right on the hardware, in reality the course instructors had provided us with a huge amount of code that glossed over many of the ugly details of writing an operating system from scratch. At the end of that class, we had a tiny OS that could boot, run compiled programs, print to the screen, and save to a hard drive, and, while I had learned a lot about the higher level algorithms and decisions you have to make when designing an OS, I realized that there were still huge and fundamental gaps in my knowledge of how computers actually work. For example, when we booted our OS in a virtual machine it started running code in a function called `kernel_main()` in our compiled kernel, how did it know to start running that code? Do CPUs have some sort of logic embedded in their transistors that tells them to look for a file called `kernel` and call a function called `kernel_main()`? Surely they do not, but they must have some logic that tells them to start running code somewhere, so how do we use this? Additionally, in `pintos` we could write `printf(“Hello, world!”)` and `Hello, world!` would show up on the screen. I know `printf()` is typically implemented (on \*nix) using the `write` system call, but what does `write` do? It can’t be turtles all the way down!

 

Being the empiricist I am, I decided the only way to really answer these questions was to write my own operating system — as much from scratch as possible. And thus, in July of 2014, MosquitOS was born. There was very little fanfare (and at that point, even less actual code), but I was excited.


This Blog
---------

Now, about four months later, MosquitOS is nowhere close to usable for anything. Nothing is special about right now except, we’ve reached a point where I’ve decided to start documenting my journey over a series of blog posts that will begin now, and continue for as long as I work on MosquitOS. Primarily, I am writing these to augment my imperfect memory, but hopefully it will be useful to others as well. In my experience with MosquitOS, I found that most of the existing reference/tutorials for people writing their own toy operating systems is about 20 years outdated.​ I wanted MosquitOS to work with 64-bit CPUs and use modern hardware, but unfortunately, while there is copious, great writing on OS design for old hardware, many of the technologies I wanted to use lacked thorough tutorials. These blog posts aim to be that missing tutorial - for all of you fools out there who wish to write a UEFI-based, modern OS for x86\_64. Good luck — it’s a jungle out there.

 

Goals For MosquitOS
-------------------

From the start, I had some specific goals for my toy OS, these have been refined and added to over time (and will likely change in the future), but some rough goals are (in no real order):

-   64-bit kernel
-   UEFI bootloader
-   Pre-emptive multitasking
-   Multiprocessor support
-   PCI support
-   USB support
-   Basic filesystem
-   Virtual memory
-   Bootable on a real computer

At one point I dreamed of hardware-accelerated graphics (by writing a driver for the computer’s GPU), but I’ve since come to decide that this sheer folly. I will console myself with the possibility of integrating some Linux GPU drivers at some point and exposing and OpenGL API or something. Or maybe I’ll chicken out and stick with the basic graphics support provided by the motherboard firmware. We’ll see.

 

To The Bootloader!
-------------------------------

I will begin the series with a post about the initial boot process, and explain the differences between UEFI and BIOS. [Onward!]({% post_url 2014-10-06-uefi-and-bootloaders %})
