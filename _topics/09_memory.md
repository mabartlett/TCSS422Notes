---
title: Memory Virtualization
layout: default
---

# Memory Virtualization

## Memory and Processes

We want to have many processes in memory at the same time. How do we allocate space for each process in an efficient manner?

One requirement of memory management is safety. We don't want a bug in one program to cause problems in another. A **segmentation fault** can occur when a program tries to access memory outside its own boundaries. Transparency is another requirement of memory management. Processes "shouldn't require particular physical memory bits." The third requirement is that resources can be exhausted and we need a sort of "safety valve" for when the memory requirements exceed the amount of physical memory.

## Virtual Addressing

The **memory-management unit** (MMU) translates the virtual address to the physical address so that only the OS knows the physical address. (A "core dumped" error message means there is no corresponding physical address.) Even the CPU doesn't know the physical address. Remember that each process gets its own virtual address space with its own space for code, data, heap and stack. 
