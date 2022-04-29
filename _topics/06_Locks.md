---
title: Locks
layout: default
---

# Locks

## Locks and Critical Sections

**Locks** are one way to implement critical sections. Metaphorically, a thread locks a door on the way in and then unlocks it on their way out. There are two operations associated with locks:

- Acquire: "Wait until the lock is free, then take it to enter a C.S."
- Release: "Release lock to leave a C.S., waking up anyone waiting for it."

These two functions must *always* go together. Once you use acquire, you must later use release. If one thread has acquired a lock, another cannot acquire it until the first has released it. When there are multiple threads needing to acquire a lock, acquisition is scheduled on a first-come, first-serve basis. Here is how you would acquire and release in C:

{% highlight c %}
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
// Critical section code goes here.
pthread-mutex_unlock(&lock);
{% endhighlight %}

The variable `lock` is either locked or unlocked. It is initialized to locked.

There are two design approaches for locks: coarse-grained locking, in which "[o]ne big lock is used any time any critical section is accessed," and fine-grained locking, in which different locks are used "to protect different data and data structures." This is a design choice that is up to the developer.

## Lock Implementations

One way to implement locks on the OS is the "spin lock," where a thread waits to enter a locked critical section by "spinning," which is when it merely burns cycles in a while loop. Lock implementation on the OS should be **atomic**. "An atomic operation is on which executes as though it could not be interrupted." Something is required from the hardware to make an operation atomic. An example of an atomic operation is **test-and-set**, where an old value in memory is fetched, the new value is written instead, and the old value is returned. When executing a test-and-set instruction on a lock "flag," the new value should be 1, i.e., locked. The new value is always 1, too. The returned value could be either 0 or 1. *Test-and-set has to be something supported on the hardware itself for all of this to work.*

Spin locks ensure correctness, but it does not guarantee fairness. Once a lock is in a critical section, there is nothing to force it to give up control of it. The starvation problem shows up here, too. Performance is clearly a problem because of the "spinning." Thus, spinlocks are too wasteful to be considered an optimal implementation.

So what's the solution? Yield! Instead of burning cycles in a while loop, the thread should simply yield control of the CPU when it detects a critical section is locked. This will change the thread's state from "running" to "ready." This context switch does come with some overhead and starvation still isn't prevented, so you don't get better performance for free.

**Fetch-and-add** ensures fairness. It is like test-and-set except instead of setting a new value, you add 1 to the old value. In this implementation, instead of merely being a flag, the lock has a "ticket" and a "turn." If the lock's turn is equal to a thread's ticket, then the thread is given entry. Every time a thread releases a lock, the lock's "turn" value is fetched-and-added. (We have to assume that a thread cannot be terminated by something outside in this implementation.)

## Lock-Based Data Structures

"Adding locks to shared data structures make them thread safe." This means we do not have to implement a critical section every time access is needed to them. 
