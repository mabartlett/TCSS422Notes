---
title: Concurrency Problems
layout: default
---

# Concurrency Problems

## Bugs

There are several types of concurrency bugs:

- Data Race
- Deadlock
- Order Violation
- Suspension
- Starvation
- Atomicity Violation
- Livelock

An **atomicity violation** is when there is "[s]erialized access to shared memory among separate threads is not enforced (e.g. non-atomic)." The fix is to add locks for all uses of a shared memory resource. We can tell if a variable is shared merely by looking at the code and seeing where it is used. We should be watchful of a variable's scope.

An **order violation** is when the desired order of access is inverted. An example is assigning a value to a variable before initializing the variable. The solution is to use condition variables.

**Deadlock** is when all threads are blocked. "If one [thread] tries to access a resource that a second [thread] holds, and vice-versa, they can never make progress." Deadlock arises from incorrect synchronization among a set of threads "if every [thread] is waiting for an event that can be caused only by another [thread] in the set." The solution is to lock the locks in the same order for each thread every time.
