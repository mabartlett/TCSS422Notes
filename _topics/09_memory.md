---
title: Memory Virtualization
layout: default
---

# Memory Virtualization

## Memory and Processes

We want to have many processes in memory at the same time. How do we allocate space for each process in an efficient manner?

One requirement of memory management is safety. We don't want a bug in one program to cause problems in another. A **segmentation fault** can occur when a program tries to access memory outside its own boundaries. Transparency is another requirement of memory management. Processes "shouldn't require particular physical memory bits." The third requirement is that resources can be exhausted and we need a sort of "safety valve" for when the memory requirements exceed the amount of physical memory.

## Virtual Addressing

The **memory-management unit** (MMU) translates the virtual address to the physical address so that only the OS knows the physical address. (A "core dumped" error message means there is no corresponding physical address.) Even the CPU doesn't know the physical address. Remember that each process gets its own virtual address space with its own space for code, data, heap and stack. Each process has the illusion of having the whole memory, but these are shadows on the cave wall.

If each process has 16KB of memory, then the minimum virtual address is 0, and the maximum address is (16 × 1024) - 1. The code starts at 0 and the heap starts just after and grows to the larger addresses. The stack starts at the highest address and "grows" negatively to the lower addresses.

## The Algorithms

### Base and Bounds

In **base and bounds**, there are two registers on the CPU: one is "base" and the other is "bounds". The "base" is where the memory segment begins and the "bounds" is the allowed size of the memory. They are on the PCB for the process. The algorithm is simple:

    Let physical address = virtual address + base.
    If virtual address < 0 or virtual address ≥ bounds:
        Trap to kernel.

We can change the base register to move processes in memory.

The operating system maintains a **free list** of available memory spaces. When a process starts, the OS searches for an appropriate spot and assigns the process. When a process is terminated, the free list is updated. If the OS detects that the items in the free list is contiguous, it can be compacted into a single entry in the list. The alternate name for this algorithm is "dynamic relocation" because the "OS can move process data when not running" that process.

Base and bounds needs only two registers, so the hardware requirements are low, and the algorithm is computationally simple for sure. Now what are the disadvantages? One problem is that the fixed amount of memory allocated may leave too much unused free space. Another is that "growing a process is expensive or impossible" because of the requirements of moving it. There is also "[n]o way to share code or data."

### Segmentation

**Segmentation** gives each address space three portions: code, stack, and heap. Each will have its own base and bounds for six total registers. Each portion need not be contiguous in physical memory. They can be separate, but virtual memory is still addressed as if the portions were contiguous. All this makes address translation a bit more complicated.

Here, we address (no pun intended) the disadvantage of base and bounds in that this algorithm allows for better sharing of memory. However, it "requires translation hardware, which could limit performance." Another problem is **external fragmentation**, which is when portions of free memory are not of the size required by the allocation request.

### Paging

The algorithm used in modern operating systems is paging, which has its very own page on this site.
