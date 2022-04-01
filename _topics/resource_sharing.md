---
title: Resource Sharing
layout: default
---

# Resource Sharing

## CPU Sharing

Each running program gets its own "virtual" representation of the CPU. Many programs *seem* to run at once. The unix command `top` will give display all the currently running processes. The Windows task manager will provide similar information. Don't be fooled! These processes are all sharing the same CPU but if they are not running on different threads, they merely run one after another.

In C, the keyword **volatile** denotes a variable is shared by multiple threads. This name is fitting because shared threads are often desynchronized, which can cause problems like race conditions. The **pthread** C API is defined by the OS for multi-threading. First you must *create* threads and at the end, you must *join* them.

## Memory Sharing

Each process has its own **virtual address space**. The OS maps *virtual* address spaces onto the *physical* memory. The same virtual address in different threads can actually point to different physical addresses. When your C program prints a memory address, it's printing a virtual address.
