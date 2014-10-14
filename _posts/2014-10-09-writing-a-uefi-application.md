---
layout: post
title: "Writing a UEFI Application"
excerpt: "In this article, I describe how I wrote my first UEFI application."
category: articles
tags: [mosquitos, operating system, uefi]
comments: true
image:
  feature: sierras-8.jpg
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
 
Now that we have a development environment and build system set up, we can use it to build an executable that can be used to boot a virtual machine (theoretically it should boot a real computer as well, but I've been unable to convince my MacBook Pro to boot off of anything except a real operating system, and I have no other computers on which to try).

 

A Minimal UEFI Application
--------------------------

To jump right in, here's a minimal UEFI application. After we get it compiled and running, I'll explain line-by-line what's going on.

**hello.c**
{% highlight c %}
#include <efi.h>
#include <efilib.h>

EFI_STATUS
EFIAPI
efi_main (EFI_HANDLE ImageHandle, EFI_SYSTEM_TABLE *SystemTable) {
  // Allows UEFI to set up some resources for you
  InitializeLib(ImageHandle, SystemTable);

  // Print() can take a format string, much like printf()
  Print("Hello, world!\n");

  return EFI_SUCCESS;
}
{% endhighlight %}

[Here's]({{ site.url }}/assets/downloads/first-uefi-application.zip) the whole project, with Tupfiles and everything. Put this into your development environment with all the prerequisites installed (see [Setting Up a Development Environment]({% post_url 2014-10-07-development-environment %}) for more information), `cd` to the project root, run `tup`, and you should be the proud new owner of a `hello` executable in the `project_root/build` directory. This file is in the [PE](http://en.wikipedia.org/wiki/Portable_Executable) format, and is all ready to run in a UEFI environment.

 

Line-by-Line Explanation
------------------------

This is covered elsewhere (like [this tutorial](http://www.rodsbooks.com/efi-programming/index.html)), but I will briefly go over what's happening in the simple program above.

 

- Lines 1-2: We include the EFI headers (from the gnu-efi project, in this case).
- Line 4: `EFI_STATUS` is a common return code for UEFI functions. Look in <efi.h> (or the UEFI specification) to see the possible values.
- Line 5: UEFI uses the **SOMETHING CALLING CONVENTION -- LOOK UP**
- Line 6: `efi_main` is the entry point for UEFI applications (like `main` in a typical C program). `ImageHandle` is a handle to your application (you'll rarely need this directly, but some UEFI functions take it as a parameter), and `SystemTable` is a struct provided by the firmware that allows you to access various UEFI services (more on this later).
- Line 8: This line does some early initialization for you and sets up some global variables to make it easier for you to access the UEFI services.
- Line 11: `Print()` is very similar to `printf()` in `libc`, except all strings in UEFI are UTF-16. This means all string literals need to be prefixed with the `L` character and your `char *`s should be `wchar *`s **VERIFY**.

 

Running a UEFI Application
--------------------------

To run a UEFI application, we are going to set up a UEFI-compatible virtual machine. I typically use [VirtualBox](https://www.virtualbox.org/) because it is free and it seems to have good support for the hardware features I've used so far. The instructions that follow will reference VirtualBox, but if you want, you can use another virtual machine. I've had success with [qemu](http://www.qemu.org/) and [VMWare Fusion](http://www.vmware.com/products/fusion).

 

Setting Up the CD Image
-----------------------

We need a way for the virtual machine to load your UEFI application, and the easiest way to do this is to attach a CD image to a virtual CD drive in the VM.

 

This image should have no partition map, and have the whole volume formatted with a FAT filesystem. I've only ever done this using Disk Utility on Mac OS X, but I'm sure it's possible on Windows and Linux as well.

 

To make things easy, I've set up a blank 100MB image with the right format, you can download it [here]({{ site.url }}/assets/downloads/uefi-cdimage.cdr.zip).

 

Setting Up Virtual Box
----------------------

1. Install [VirtualBox](https://www.virtualbox.org/).
2. Create a new virtual machine of type "Other" and version "Other/Unknown (64-bit)". You can leave the RAM at the default settings, and you don't need a hard drive.
3. Open the virtual machine's settings, and under "System" > "Motherboard", set the chipset to "ICH9" and enable both "I/O APIC" and "EFI" (the I/O APIC isn't strictly necessary for this example, but it's necessary once we start working on the kernel).
4. Go to "Storage" and delete the default IDE controller. Add a new "SATA Controller" and make sure the type is "AHCI".
5. Still in "Storage," add a new CD/DVD drive to the SATA controller. Select the CD image from the previous step.
6. Save the virtual machine settings.

 

Running Your UEFI Application
-----------------------------

1. Copy the `hello` binary we compiled in the first step into the root of your CD image.
2. Start up your virtual machine, after a few seconds, you should find your self in a UEFI shell. This shell is much like the \*nix command line, but the path separator is `\` and the commands are somewhat different. You can type "help" to get a list of possible commands, but as there is no scrolling, you'll only be able to see the last few lines. For a more useful reference, check **HERE**. Here are some useful commands:
  - `fs0:` -- Mount the first filesystem (in our case it will be the CD drive) and set the current directory to the root of that filesystem.
  - `dir` -- List all of the files in the current directory.
  - `cd PATH` -- Change the current directory to `PATH` (remember that the path separator is `\` for UEFI). This only works if you're in a mounted filesystem.
  - `.\BINARY` -- Executes `BINARY` in the current directory. Alternatively you can just type the name of the binary, without the leading `.\`.
3. Run the `hello` binary by running `fs0:` followed by `hello`. You should see `Hello, world!` printed to the screen and then return to the shell.
4. Feel self-righteous, because you just compiled and ran a program on a computer that didn't even have a (real) operating system!

 

Running Your UEFI Application at Boot
-------------------------------------

While it's cool that we can run an application from the UEFI shell, it would be decidedly cooler if the application would run automatically on boot (like, just for example, a bootloader does). This is actually quite easy, but before we make our `hello.c` program run at boot, we need to make a quick modification.

 

Currently, our program prints `Hello, world!` and then exits (returns from `efi_main`). This works great in the UEFI shell, but when we're booting from this application, we need it to stick around for long enough for us to see what we've done. As it is, the program would execute and then dump us into the UEFI boot manager -- not what we want.

 

The easiest way to accomplish this is to throw an infinite loop at the end. To this end, add `while(1);` to `hello.c`, right before the `return` statement. Then recompile the project.

 

During boot, UEFI (by default) looks for a file named `fs0:\EFI\BOOOT\BOOT<architecture>.EFI` (where `<architecture>` is the architecture of the machine being booted -- in our case `X64`). Therefore, all we have to do to boot off of our modified `hello` executable is create the folders `EFI` and `BOOT` in our CD image, and then move `hello` to `BOOT`, and rename it to `BOOTX64.EFI`.

 

Once you do this, boot up the virtual machine (make sure the state wasn't saved) and watch the magic!

 

To The Bootloader!
------------------

Next, we will go over how I wrote the MosquitOS bootloader and see a basic bootloader implementation. [Onward!]({% post_url 2014-10-10-writing-a-bootloader %})

