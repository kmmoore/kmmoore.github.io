---
layout: post
title: "(U)EFI and Bootloaders"
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

3.  The motherboard tells the CPU to start executing firmware (which is stored on some sort of non-volatile storage on the motherboard, and is programmed when the motherboard is shipped (although it can usually be upgraded later)). For older computers, this firmware was usually [BIOS](http://en.wikipedia.org/wiki/BIOS), modern computers mostly use [UEFI](http://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (which usually has BIOS backwards compatibility built in). More on the differences between BIOS and UEFI below.

4.  The motherboard firmware reads a piece of code called the "bootloader" executable from a hard drive[^2] and executes that code (more on how exactly it finds the firmware below). The bootloader is responsible for configuring whatever hardware is necessary, loading the kernel (and it's supporting files) into memory from disk, and then transferring control to the kernel to continue the boot process. More on this below.

 

BIOS vs UEFI
--------------

In the bad old days when BIOS was written, computers were simple, hard drives were small, and bootloaders were small. Because of this, and to keep things simple, BIOS required the bootloader to be stored in the very first sector of the boot medium (e.g., a hard drive, floppy, or CD). This first sector was called the [Master Boot Record (MBR)](http://en.wikipedia.org/wiki/Master_boot_record). Typical hard drive sectors are only 512 bytes, which is not a lot of space for the bootloader. Consequently, most moder bootloaders are multi-stage -- they have a simple bootloader in the MBR that loads a more complex one from a fixed location in a partition, which then loads the operating system.

 

When UEFI was developed, the designers decided to remove the 512 byte limit. UEFI based bootloaders are instead stored in a regular file (with a special name) on a FAT-16 formatted partition. While this makes the UEFI firmware much more complicated than BIOS (since it now has to know about filesystems and partion maps and such), it makes writing a bootloader much easier.
 

 

 

The Bootloader
--------------

 

To The Development Environment!
-------------------------------

Next up, we have a post about setting up a development environment (as this took me way more time than I’m comfortable admitting). Then we will write our very own bootloader. [Onward!]({% post_url 2014-10-07-development-environment %})

 

[^1]: One of the most interesting things I’ve found about computer hardware is how much backwards compatibility there is. Every x86 chip every produced can still faithfully reproduce the operation of the original 8086 — just in case. There are similar setups for almost every piece of hardware in modern computers, allowing many computers from 2014 to still run in modes where they are indistinguishable from a computer from the 80s.

[^2]: As a result of this, the motherboard firmware must have some knowledge of hard drive partition schemes and filesystem formats built into it. This is why for some computer/OS combinations (Linux does this frequently) you have a separate partition for the boot loader (which must be a filesystem type supported by the motherboard) and for the main operating system/user filesystem (which can be something much more modern and fancy).
