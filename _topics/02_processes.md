---
title: More About Processes
layout: default
---

# More About Processes

## Kernel's View of Processes

### Process Control Block

Each process has its own data structure called a **process control block** (PCB), which contains all the information about a process, namely:

- Process State
- Process ID (PID)
- User ID, etc.
- Program Counter
- Registers
- Address Space
- Open Files

### Process State

The state of a process could be running, ready, or waiting.

- A running process is one that is, well, running. The number of simultaneously running process is equal to the number of cores on the machine.
- A ready process is one that is ready and willing for the OS to assign to the CPU. A process spends most of the time in this state.
- A waiting (or blocked) process is one waiting for some event like a keyboard press or a network signal.

Clearly, the state of a process is fluid. Regardless of the corresponding process's state, the OS maintains the PCB. A process cannot go from waiting to running. It must go from waiting to ready to running.

### PCB and Hardware

A process's hardware state (e.g., current register value) is stored in its PCB. When it starts it back up, it reloads that state back into the hardware. Switching from one CPU hardware state to another is a **context switch**. With this switch, there is necessary idle time. OS designers must come up with designs that minimize this. Doing so is left as an exercise for the reader.

## Programmer's View of Processes

Parent processes create child processes. In Unix, `init` creates the first process and has a PID of 0 or 1. Processes are made by using the C function **`fork()`** in Unix. The process returned has its own PCB and address space. The `fork()` function "[i]nitializes the address space with a copy of the entire contents of the address space of the parent." This is very important. Also important to note is that *this function returns twice*, hence the name "fork." "It returns the child's PID to the parent and '0' to the child."
