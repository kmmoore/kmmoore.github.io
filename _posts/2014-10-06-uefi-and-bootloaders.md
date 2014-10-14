---
layout: post
title: "UEFI and Bootloaders"
excerpt: "An overview of the boot process and how to use the BIOS or EFI firmware to load your operating system."
category: articles
tags: [mosquitos, operating system]
comments: true
image:
  feature: sierras-5.jpg
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
 

This is the second installment in a series of articles detailing my experience writing a modern toy operating system from scratch. To start at the beginning (or see a list of articles), go to the [overview]({{ site.url }}).

 

Before we start writing code, we need to take a step back and talk about what happens when you turn on a computer. This is a topic that has been covered many times before, so I will keep this part brief and provide links to other articles for the bold and curious.


Overview of the Boot Process:
-----------------------------


When you turn on the power supply in your computer and the motherboard receives power (**I don't like how this sounds**), a couple of things happen:

1.  The motherboard executes what is called the [Power On Self-Test (POST)](http://en.wikipedia.org/wiki/Power-on_self-test). This performs a quick diagnostic test of hardware in the computer (such as the CPUs, firmware, and RAM) and stops the power-on process if it detects and hardware problems. The beep made by many computers shortly after they start up is the motherboard indicating that the POST has succeeded.
2.  The motherboard powers up the first CPU. At this point, the CPU is in what's called [Real Mode](http://en.wikipedia.org/wiki/Real_mode) and is emulating an Intel 8086 chip from 1976[^1].
3.  The motherboard tells the CPU to start executing firmware (which is stored on some sort of non-volatile storage on the motherboard, and is programmed when the motherboard is shipped (although it can usually be upgraded later)). For older computers, this firmware was usually the [Basic Input/Output System (BIOS)](http://en.wikipedia.org/wiki/BIOS), modern computers mostly use the [Unified Extensible Firmware Interface (UEFI)](http://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (which usually has BIOS backwards compatibility built in). More on the differences between BIOS and UEFI below.
4.  The motherboard firmware reads a piece of code called the "bootloader" executable from a hard drive[^2] and executes that code (more on how exactly it finds the firmware below). The bootloader is responsible for configuring whatever hardware is necessary, loading the kernel (and it's supporting files) into memory from disk, and then transferring control to the kernel to continue the boot process. More on this below.


BIOS vs UEFI
--------------

In the bad old days when BIOS was written, computers were simple, hard drives were small, and bootloaders were small. Because of this, and to keep things simple, BIOS required the bootloader to be stored in the very first sector of the boot medium (e.g., a hard drive, floppy, or CD). This first sector was called the [Master Boot Record (MBR)](http://en.wikipedia.org/wiki/Master_boot_record). Typical hard drive sectors are only 512 bytes, which is not a lot of space for the bootloader. Consequently, most moder bootloaders are multi-stage -- they have a simple bootloader in the MBR that loads a more complex one from a fixed location in a partition, which then loads the operating system.

 

When EFI (and then UEFI, which is a successor to EFI) was developed (primarily by Intel **VERIFY**), the designers decided to remove the 512 byte limit. UEFI based bootloaders are instead stored in a regular file (with a special name) on a FAT formatted partition[^3]. While this makes the UEFI firmware much more complicated than BIOS (since it now has to know about filesystems and partition maps and such), it makes writing a bootloader much easier.

 

While the missing MBR dependence is the first noticeable difference, UEFI is really a completely different beast than BIOS. UEFI provides what is effectively a mini-operating system for your bootloader. UEFI has the concept of "applications," which are simply executable binaries that are linked against the UEFI libraries. UEFI applications have the ability to use all of the services provided by UEFI, which include things like filesystem access, text input/output, basic graphics, and much more -- the [UEFI Specification](http://www.uefi.org/specifications), specifically the part on "Boot Services" provides a comprehensive list. We'll talk more about this when we write our first UEFI application.


The Bootloader
--------------

Because UEFI provides so many nice features, it doesn't involve an MBR, and it's clearly the direction the world is moving (almost all new motherboards support UEFI now, and BIOS is going the way of the dodos), I decided to use a UEFI bootloader for MosquitOS -- and not support BIOS. While there are a number of existing UEFI bootloaders (**ELIO**, **GRUB?**, and **MultiBoot?**), since one of my goals with MosquitOS is to write as much as possible from scratch, I decided to write my own bootloader.

 

More complicated bootloaders support things like multiple operating systems, and boot configuration menus -- mine does not (nor will it likely ever). The MosquitOS loader does the following things (which will be covered in more detail in following posts):

- Open the kernel file from the main partition (the same partition the bootloader is on). Currently the path of the kernel is hardcoded to be "/kernel".
- Parse the kernel file (which is compiled as an **ELF** executable file, the standard executable format used on Linux) and load all of the necessary sections into a fixed location in memory.
- Use the UEFI services to get the **XSDP** (eXtended System Description Pointer). This pointer later allows us to access the ACPI tables, which are a set of tables provided by the motherboard that describe various aspects of the system hardware.
- Use the UEFI services to get a handle for the UEFI Simple Graphics Output Protocol object. This allows the kernel to output graphics (using the UEFI services) once it's running.
- Use the UEFI services to get a copy of the system memory map. This map describes every byte of physical memory and whether it is free. UEFI, some hardware, etc. have already used some of physical memory at this point, and it's important that the kernel know which segments it can't overwrite.
- Exit the UEFI boot services. This tells UEFI to relinquish pieces of the system hardware in preparation for the kernel to take over. Once you exit boot services, you can no longer use many of the UEFI services.
- Call the `kernel_main()` function (which we loaded into memory earlier from the kernel binary), passing in the XDSP, graphics object, and memory map. Since `kernel_main()` never returns, this is the point where the bootloader officially ends, and the kernel begins.


To The Development Environment!
-------------------------------

Next up, we have a post about setting up a development environment (as this took me way more time than I'm comfortable admitting). Then we will write our very own bootloader. [Onward!]({% post_url 2014-10-07-development-environment %})

 

[^1]: One of the most interesting things I've found about computer hardware is how much backwards compatibility there is. Every x86 chip every produced can still faithfully reproduce the operation of the original 8086 — just in case. There are similar setups for almost every piece of hardware in modern computers, allowing many computers from 2014 to still run in modes where they are indistinguishable from a computer from the 80s.

[^2]: As a result of this, the motherboard firmware must have some knowledge of hard drive partition schemes and filesystem formats built into it. This is why for some computer/OS combinations (Linux does this frequently) you have a separate partition for the boot loader (which must be a filesystem type supported by the motherboard) and for the main operating system/user filesystem (which can be something much more modern and fancy).

[^3]: Other filesystems can be supported, but FAT-32, FAT-16, and FAT-12 are the only formats mandated by the specification. **VERIFY**