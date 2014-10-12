---
layout: post
title: "Writing a Bootloader"
excerpt: "In this article, I describe how I wrote my first UEFI application, and, eventually a simple bootloader."
category: articles
tags: [mosquitos, operating system]
comments: true
image:
    feature: pasayten-1.jpg
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
 
Now that we have a development environment setup, we can use it to build an executable that can be used to boot a virtual machine (theoretically it should boot a real computer as well, but I've been unable to convince my MacBook Pro to boot off of anything except a real operating system, and I have no other computers on which to try).


