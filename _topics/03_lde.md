---
title: Limited Direct Execution
layout: default
---

# Limited Direct Execution

## User and Kernel

There are many operations that are not safe for the OS to allow a program to execute on its own (or at all) such as division by zero, accepting user input, or accessing memory. You are already aware that such a thing as malware exists. The mechanism for giving program access to resources is **limited direct execution** (LDE). Giving software direct access to resources means OS would be little more than a library. That's not safe! The "limited" in "limited direct execution" means only particular processes are allowed direct access to the CPU. Running on the CPU directly is called running in "trusted mode."

Think of LDE like a bank teller and the operating system is the bank vault. A user process is like a bank customer asking access to the bank vault to make a deposit or withdrawal. The customer cannot make a deposit directly but rather only through the teller. The CPU can tell the difference between a user and a kernel process and the implementation of doing so is an open design choice, but it is most often the case that a single bit, the **hardware mode bit**, is used to differentiate the two kinds of processes. In x86, it's actually two bits where 0 is for a kernel process, 1 VM kernel, 2 unused, and 3 for a user process. The lower numbers have more privileges than the higher. These bits are set by the parent (creator) process.

In **user mode**, the application has no access to I/O applications directly. Some instructions are entirely disabled by the CPU, e.g., the aforementioned division by zero or the `HALT` instruction. How does a user program perform privileged operations then? It can perform **system calls**, which are provided to the user via the OS's API. Examples include:

- `malloc()`
- `fork()`
- `getpid()`
- `open()`
- `read()`

Be careful because system calls will differ across operating systems. Every system call has a corresponding **trap handler**, which looks up the appropriate kernel routine and this kernel routine has a return value, which the trap handler brings back to the user process that made the system call. Traps can be enabled by exceptions or interrupts, too. All this can remain within the same process and there is not necessarily any context switching.

The kernel stack and the user stack are kept separate.

## Multitasking

If there is only one core and one process is running on that core, then the OS is not running. If the OS isn't running, then how can anything get done on that machine? Naturally, we would say the OS should regain control of the CPU, but how? One solution has been *cooperative multitasking*, where the OS would trust certain processes, but this can cause problems because some user processes are evil and/or stupid such as an infinite loop. This led to the development of the **timer interrupt**, where the current process is periodically halted. This is fixed by the hardware. Each core will have its own timer interrupt.
