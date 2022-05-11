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

One way to implement locks on the OS is the "spin lock," where a thread waits to enter a locked critical section by "spinning," which is when it merely burns cycles in a while loop. Lock implementation on the OS should be **atomic**. "An atomic operation is one which executes as though it could not be interrupted." Something is required from the hardware to make an operation atomic. An example of an atomic operation is **test-and-set**, where an old value in memory is fetched, the new value is written instead, and the old value is returned. When executing a test-and-set instruction on a lock "flag," the new value should be 1, i.e., locked. The new value is always 1, too. The returned value could be either 0 or 1. *Test-and-set has to be something supported on the hardware itself for all of this to work.*

Spin locks ensure correctness, but it does not guarantee fairness. Once a lock is in a critical section, there is nothing to force it to give up control. The starvation problem shows up here, too. Performance is clearly a problem because of the "spinning." Thus, spin locks are too wasteful to be considered an optimal implementation.

So what's the solution? Yield! Instead of burning cycles in a while loop, the thread should simply yield control of the CPU when it detects a critical section is locked. This will change the thread's state from "running" to "ready." This context switch does come with some overhead and starvation still isn't prevented, so you don't get better performance for free.

**Fetch-and-add** ensures fairness. It is like test-and-set except instead of setting a new value, you add 1 to the old value. We can use this operation improve our lock implementation. Instead of merely being a flag, the lock has a "ticket" and a "turn." If the lock's turn is equal to a thread's ticket, then the thread is given entry; otherwise, the thread yields. Every time a thread releases a lock, the lock's "turn" value is fetched-and-added. (We have to assume that a thread cannot be terminated by something outside in this implementation.)

## Lock-Based Data Structures

"Adding locks to shared data structures make them thread safe." This means we do not have to implement a critical section every time access is needed to them.

### Sloppy Counter

If you've got a shared counter, you can lock the counter when you want to increment it. Synchronized counters have poor performance however. We say they "scale" poorly. The solution is a **sloppy counter**, which is where each core has its own counter that updates the global counter periodically. The sloppy counters each have a sloppiness threshold. Once the counter reaches that threshold, this value is added to the global counter and the sloppy counter is reset. The rationale behind sloppy counters is the independence of CPU cores.

### Concurrent Linked List

A **concurrent linked list** has a lock for the head of the linked list. Insertions are protected. Here is the procedure:

1. Lock.
2. Allocate space in the heap for the new node.
3. If the allocation failed, throw an error and unlock. Do not forget to unlock!
4. Create the new node.
5. Update the head as the new node.
6. Unlock.

If it fails, return -1. If it is successful, return 0. The lookup operation works similarly: each element is locked and then is unlocked as the list is traversed. Alternatively, you can lock and unlock only the head; however, locking an entire list means users have to wait for a thread to finish with a list before performing their own operation on it. The solution is **hand-over-hand locking** in which "[t]raversal involves handing over previous node's lock, acquiring the next node's lock, and so on." This does unfortunately come at a performance cost.

### Michael & Scott Concurrent Queues

In a **Michael and Scott concurrent queue**, there are two locks: one for the head and the other for the tail. This allows items to "be added and removed by separate threads at the same time." When we have only one item in such a queue, rather than having the head and tail point to the same node, the tail points to a dummy node. The operations enqueue and dequeue are synchronized. Here's the procedure for enqueue:

1. Determine if space can be allocated for the new node.
2. If so, create a temporary node.
3. Lock the tail.
4. Add this new node to the queue.
5. Unlock the tail.

To dequeue:

1. Lock the head.
2. Make the temporary node the head.
3. The new head is the one pointed to by the temporary node.
4. If the new head is not `NULL`, unlock the head and free the temporary node.
