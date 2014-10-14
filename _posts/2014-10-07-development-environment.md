---
layout: post
title: "Setting Up a Development Environment"
excerpt: "In this article, I go over the development environment I use to develop MosquitOS."
category: articles
tags: [mosquitos, operating system]
comments: true
image:
    feature: sierras-6.jpg
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
 

This is the third installment in a series of articles detailing my experience writing a modern toy operating system from scratch. To start at the beginning (or see a list of articles), go to the [overview]({{ site.baseurl }}).

 

Getting a proper development environment setup is often one of the most
difficult parts of starting a new project, and my case was no exception. I do my normal development on a Macbook Pro running (at the time) OS X 10.9, but for this project I found that the cross-compiler and libraries that I needed installed were very difficult (I struggled for about 3 days before giving up) to get set up on Mac OS X. Consequently, all of the development I do for this project is on an Ubuntu virtual machine.

 

**Aside:** The main stumbling block to developing on Mac OS X was installing the appropriate libraries/headers/tools to compile EFI code. I tried both the `gnu-efi` and `TianoCore` libraries, but I could not get either to work properly. However, so far, developing on Linux has been great. Beside `gnu-efi` installing properly with one command, I've enjoyed the availability of development tools, out-of-the-box `gcc` support for the executable formats I use, and some other niceties -- so I think using Linux has definitely been the right choice.

 

If you go the Linux VM route, you can either do your development inside the VM directly, or use some tool that allows you to run commands on your VM from your main OS (e.g., an SSH client) and having some way to access the VM’s filesystem (e.g., SFTP, shared folders). I’ve gone the second route, and find it much more comfortable

 

Here’s the setup I use (as of October, 2014):

-   A minimal installation of [Ubuntu 14](https://help.ubuntu.com/community/Installation/MinimalCD) running in VirtualBox. This only includes a command line interface (as I only interact with it over SSH), and enough packages to do development. As a result, the whole VM requires only 2.85GB of disk space.
-   [Sublime Text 3](http://sublimetext.com/). I try not to take sides in the editor wars, but I personally like my editors to have a GUI and the other trappings of the 21st century. To this end, Sublime Text is best editor I've tried so far. I run Sublime on my Mac, and use [VirtualBox shared folders](https://forums.virtualbox.org/viewtopic.php?t=15868) to share my project folder between the host and the VM.

On my Ubuntu VM, I’ve installed the following packages (which, on Ubuntu can be
done with `sudo apt-get install <package-name>`):

-   `openssh-server` — Allows me to SSH into the VM.
-   `binutils` — Contains tools for linking, creating, and examining binaries.
-   `gcc` — The compiler I use to compile both the bootloader and the kernel/drivers, you could probably do the same things with `clang` if you wanted to.
-   `gnu-efi` — Headers and libraries used to to compile UEFI applications (for example, the bootloader).
-   `pkg-config` and `libfuse-dev` — Requirements for `tup`.
-   `tup` - A build system (similar to `Make`) that I use to build both the kernel and boot loader (more on this next).

 

To The Build System!
--------------------

Next up, we'll set up `tup` and get everything ready to compile our first UEFI application. [Onward!]({% post_url 2014-10-08-build-systems %})

