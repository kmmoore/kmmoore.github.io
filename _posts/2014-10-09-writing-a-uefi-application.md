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

 

Running a UEFI Application
--------------------------

To run a UEFI application, we are going to set up a UEFI-compatible virtual machine. I typically use [VirtualBox](https://www.virtualbox.org/) because it is free and it seems to have good support for the hardware features I've used so far. The instructions that follow will reference VirtualBox, but if you want, you can use another virtual machine. I've had success with [qemu](http://www.qemu.org/) and [VMWare Fusion](http://www.vmware.com/products/fusion).



