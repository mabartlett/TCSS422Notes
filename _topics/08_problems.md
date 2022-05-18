---
title: Concurrency Problems
layout: default
---

# Concurrency Problems

## Deadlock

**Deadlock** is when all threads are blocked. "If one [thread] tries to access a resource that a second [thread] holds, and vice-versa, they can never make progress." Deadlock arises from incorrect synchronization among a set of threads "if every [thread] is waiting for an event that can be caused only by another [thread] in the set." Four conditions must be satisfied in order to cause deadlock:

- Mutual exclusion: The thread uses locks.
- Hold-and-wait: The thread holds resources while waiting for other resources.
- No preemption: Threads hold resources that cannot be removed from them.
- Circular wait: Thread A has lock 1 and wants lock 2 but thread B has lock 2 and wants lock 1.

If we prevent one condition, we prevent deadlock altogether. We don't really want to prevent mutual exclusion if we want a multi-threaded program; however, we can "build wait-free data structures" by "using atomic CPU instruction[s]," but this isn't necessarily safe. Hold-and-wait can prevented with locks for locks, but this is silly and doesn't prevent race conditions. Let's try to see if we can prevent the other two conditions instead.

No preemption can be prevented by threads not blocking when trying to acquire locks. The function `pthread_mutex_trylock()` allows a thread to try to acquire a lock only once and `pthread_mutex_timedlock()` puts lock acquisition on a sort of timer. This is our first good solution to prevent deadlock! The solution to circular wait is to lock the locks in the same order for each thread every time. Intelligent scheduling can also help us. It will schedule the execution of threads according to what locks it acquires. This isn't a very good solution because such a scheduler has to be too careful and this causes such a loss in performance that it's not worth the cost.

In **detect and recover**, deadlock is allowed to occur occasionally and then some action is taken. Databases use such techniques. In a database, each user is a thread. A resource allocation graph can show where cycles appear. (I'm on my laptop, so I'm not drawing you any pictures.) If a cycle is found, the thread is forced to release the resource it's holding. The performance cost for this algorithm is quite large, so this procedure is run only sparingly. 

Another solution to deadlock is to detect it during runtime.

## Other Bugs

There are several other types of concurrency bugs:

- Data Race
- Order Violation
- Suspension
- Starvation
- Atomicity Violation
- Livelock

An **atomicity violation** is when there is "[s]erialized access to shared memory among separate threads is not enforced (e.g. non-atomic)." The fix is to add locks for all uses of a shared memory resource. We can tell if a variable is shared merely by looking at the code and seeing where it is used. We should be watchful of a variable's scope.

An **order violation** is when the desired order of access is inverted. An example is assigning a value to a variable before initializing the variable. The solution is to use condition variables.
