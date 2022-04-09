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

### Fork

Parent processes create child processes. In Unix, `init` creates the first process and has a PID of 0 or 1. Processes are made by using the C function **`fork()`** in Unix. The process returned has its own PCB and address space. The `fork()` function "[i]nitializes the address space with a copy of the entire contents of the address space of the parent." This is very important! Also important to note is that *this function returns twice*, hence the name "fork." "It returns the child's PID to the parent and '0' to the child." Quite often, the terminal is the parent process. The order of execution of processes is typically non-deterministic (and we assume it is unless stated otherwise).

One C function defined by the Linux API is `getpid()`, which returns the pid of the calling process. It is defined in `unistd.h`.

Let's say we have a program that has a process created with `fork()`. If the child process is run, a file is closed. If a parent process is run, the file is read. However, these processes are separate and running in their own address space, so the attempt to read the file in the parent process will *not* fail even if the child process is run first! Each process has their own copies of everything.

### Wait

Another important function is **`wait(int *wstatus)`**, which will wait for all child processes to finish before continuing execution. The argument `wstatus` is almost always `NULL`. Similarly, `waitpid()` will wait for a specific child process. Do not be confused by the word "wait." This is not a `sleep()` function. The `wait()` function is defined by `sys/wait.h`. Its return value is the pid of the last finished child. A **zombie process** is a child that was created by a `fork()` and has exited but its parent is still waiting.

### Process Creation

What if a child process wants to start its own program? You can use the following, which is also defined in `unistd.h`:

{% highlight c %}

int execvp(const char *filename, char *const argv[])

{% endhighlight %}

- `filename` is the command.
- `argv[]` is the list of arguments, the first of which should be whatever `filename` was. You better remember to put `NULL` at the end!

This function stops the current process and loads the specified program into the current one's address space. *This all remains within the same process.* It returns nothing if everything went well and -1 otherwise. There is a similar--perhaps even more popular function--called `exec()` that you might see referenced more often, but that one doesn't have `const` in front of its parameters and I don't like that. By the way, `strdup()` is going to be your friend.

This isn't related to anything, but the `wc` program will give you the newline, word, and byte count for a file.
