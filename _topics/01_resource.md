---
title: Resource Sharing
layout: default
---

# Resource Sharing

## Processes

"A **process** is a running program." Each process has numerous **components** such as stack, heap, static data, and code. Processes must "overlap" (i.e., one must run for a little while, then another, then back to the other, and so on) for a few reasons. Some accept user input and some are merely "running in the background," but the primary motivation for overlapping them is to reduce latency.

Two processes can be concurrently executing the same program. Remember that a program is merely static code while a process is something actually running that code. The Linux directory `/proc` contains all the information related to all the  currently running processes and the Linux program `top` will display all the currently running processes. The Windows task manager will provide similar information. Don't be fooled! These processes are all sharing the same CPU but if they are not running on different threads, they merely run one after another. We'll discuss that more in a moment.

In Firefox, every tab is a thread. In Google Chrome, every tab is a process, which means there is no communication between tabs. Juhua doesn't seem to like Firefox much.

To run a process in the background from the Linux terminal, use the `&` character at the end of the command. The number that the terminal echos is that process's process ID (PID).

## CPU Sharing

 Many programs *seem* to run at once. One CPU provides many **virtual CPUs**. Implementing virtual CPUs involves **time sharing**, which is when the OS runs different processes consecutively. At any time, *only one* process can be running on one core. This means four cores can have four simultaneously running processes. Thus, we must ask ourselves how the OS must go from one process to another while minimizing overhead.

## Memory Sharing

Each process has its own **virtual address space**. The OS maps *virtual* address spaces onto the *physical* memory. The same virtual address in different threads can actually point to different physical addresses. When your C program prints a memory address, it's printing a virtual address.
