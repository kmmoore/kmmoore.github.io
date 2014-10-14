---
layout: post
title: "Build Systems"
excerpt: "In this article, I talk about the different build systems I tried and the setup I eventually settled on."
category: articles
tags: [mosquitos, operating system]
comments: true
image:
  feature: sierras-7.jpg
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

 

For simple projects, it's easy enough to type `gcc -o kernel kernel.c` at the command line, but when you're compiling dozens of files with gobs of compiler and linker options, it helps to have some automation. Probably the most widely used tool for automating C project builds is [Make](http://www.gnu.org/software/make/). `Make` is decently old, and has a bit of an odd syntax, but it works, it's used all over the place, and the syntax really isn't too bad once you get used to it.

 

Life With Make
--------------

I started out building my project using `Make`, and stuck with it for about 4 months. When my project was simple (i.e., just the bootloader), `Make` worked great. I downloaded a short Makefile from [this guy's](http://www.rodsbooks.com/efi-programming/index.html) excellent UEFI tutorial (this tutorial essentially single-handedly resulted in me compiling my first UEFI application) that specified the compiler and linker flags, and ran a few commands on the source files. Everything was peachy.

 


Fast-forward a few weeks -- I'd written a bootloader and was starting on the kernel. The kernel is really a separate project from the bootloader -- it needed different compiler/linker flags, required different linked in libraries and included headers, and ends up as a separate binary. However, I wanted the two projects to be in the same Git repository, and I wanted a single invocation of `make` from the project root to build both projects. After some Googling, I patched together a set of recursive Makefiles that fulfilled my needs, and things were good again. Then I noticed that when I made a change to a `.h` file, it wasn't recompiling the source files that depended on that header. This resulted in a lot of `make clean && make`, which felt like overkill. More Googling. More tweaking of Makefiles. Fixed this as well. Perfect. For the moment.

 

The final straw for me was when I was working on including the ACPICA library (an external library, written by Intel, used to interpret the ACPI tables. There will be at least one article on the mess that is ACPI later.) I put the code for this library in a subdirectory in my project, and attempted to compile it in with the rest of my code. Because it needed different compiler flags than the rest of my kernel (it wouldn't compile with `-Werror`, for example) I had a Makefile specifically to build ACPICA. I struggled, however to get the dependencies all correct -- I would frequently have to `make clean && make` to make a change to the ACPICA code visible to the rest of the project. While I have no doubts that all of my issues were caused by my own ignorance and that I could have solved my problems with a bit more Googling, I decided that it was time for a change.

 

Life After Make
---------------

After struggling with a few other systems (that appeared to offer few benefits over `make`), I stumbled upon [tup](gittup.org/tup/). For me `tup` has a few distinct advantages:

- It works recursively by default. When you run `tup` from any directory in your project, it automatically scans every subdirectory in the project and executes in every directory with a `Tupfile`. This means you have to have one `Tupfile` per code directory, but I've found that this is hardly difficult, and it gives you the ability to easily change the compilation options for every submodule in your project.
- `tup` keeps track of all the files opened by every command executed in your `Tupfile` to automatically determine dependencies. For example, a line like `hello.c |> gcc -o %o %f |> hello` means take the file `hello.c`, and invoke the command `gcc -o hello hello.c` to produce the file `hello`. If `hello.c` includes `hello.h`, `gcc` will open `hello.h` during compilation, `tup` will "see" this, and it will implicitly determine that `hello.c` depends on `hello.h`. If you subsequently make a change to `hello.h`, `tup` will automatically recompile all files that depend on it.
- `tup` builds up a full graph of all file dependencies and uses this graph to determine which files to recompile during a build. From my experience, `tup` has done a very good job of only recompiling the necessary files when I make a change.

Overall, I have been very happy with `tup`. It only took me an hour or so to set up, seems to understand my project dependencies perfectly with minimal configuration, and adds very little overhead to each build. When I was using `Make`, I was doing clean builds almost every time (which took somewhere around 25s). Now, with `tup`, I never do clean builds, and the builds are usually essentially instantaneous. While I admit that my issues with `Make` were almost certainly my fault, `tup` has been incredibly simple to use and produced the final product I was looking for. It was definitely worth making the switch and I plan to use it for all my future projects.

 

My Tupfiles
-----------

I won't try to explain the entire syntax of `tup`. The [examples](gittup.org/tup/examples.html) and [manual](gittup.org/tup/manual.html) do a pretty good job (and the syntax should look kind of familiar for those of you who have used `Make`). Take this time to go read over the examples quickly and familiarize yourself with `tup` -- it doesn't take very long. I will, however, provide the `Tupfiles` I use to build the MosquitOS kernel and bootloader (with some explanation). 


### Root Tuprules.tup

This file sets defines some common variables and macros for the whole project.

{% gist kmmoore/cc0b1d828c80ab716cf0 Tuprules.tup%23Root %}


### Root Tupfile


{% gist kmmoore/cc0b1d828c80ab716cf0 Tupfile%23Root %}


### EFI Application Tuprules.tup

**CHECK `$(CC) -print-libgcc-file-name`**

{% gist kmmoore/cc0b1d828c80ab716cf0 Tuprules.tup%23EFI %}


### EFI Application Tupfile

{% gist kmmoore/cc0b1d828c80ab716cf0 Tupfile%23EFI %}


### Submodule Tupfile

This is a `Tupfile` that I place in any subdirectory that contains code I want to compile into the main project. If you want, you can add/override any of the CFLAGS at the top of the file and it will just apply to the files in that directory. Or you can create another `Tuprules.tup` file in that subdirectory and it will apply to all files in that subdirectory and any nested subdirectories.

{% gist kmmoore/cc0b1d828c80ab716cf0 Tupfile%23Submodule %}

 

Empty Project
-------------

[Here]({{ site.baseurl }}/assets/downloads/empty-uefi-project.zip) are those files packaged into the appropriate file structure. If you `cd` into the root directory and run `tup`, nothing should happen, but lots of cool things are poised to happen just as soon as we add some source code.

 

To The Code!
------------

Next up, we will write our first UEFI application and run it on an operating-system-less virtual machine. [Onward!]({% post_url 2014-10-09-writing-a-uefi-application %})

