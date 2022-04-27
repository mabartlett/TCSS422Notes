---
title: Threads and Concurrency
layout: default
---

# Threads and Concurrency

## Threads

Communication between processes is expensive because the communication must go through the OS. This is where threads enter. Each tab in Google Chrome is a process. These tabs share the same code, data, privileges, and resources; however, they do not share hardware state, e.g., program counter, stack pointer, or register values. "Why don't we separate the concept of a process from its execution state?" We call the execution state the **thread of control**. In a multi-threaded process, there are multiple threads of control and hardware states, but code, data, and files are shared. In other words, they have *one address space* but unique execution states.

Thus, threads and processes are two different things in modern operating systems. "A thread is bound to a single process." Each process may have multiple threads. For example, you can have one thread for a GUI and another for I/O but these threads will still be running the same process.

C makes use of threads with the **`pthread`** API, which is defined in `pthread.h`. The function to create a thread is

{% highlight C %}
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void*), void *arg);
{% endhighlight %}

The argument `*attr` is usually `NULL`. You must join the thread later with the following function:

{% highlight C %}
int pthread_join(pthread_t thread, void **value_ptr)
{% endhighlight %}

Anything that comes after `pthread_join()` is going to be run *after* the end of the thread.

## Race Conditions

Be responsible when using multiple threads! If the order of execution is important, do not put events in separate threads. Beware of **race conditions**, in which multiple threads share a resource and the outcome is different depending on which thread accesses the resource first. Only global resources are susceptible to race conditions and we must protect them by using **synchronization**. Local variables are not shared but anything on the heap is. (Anything allocated with `malloc()` is on the heap.)

We can allow only one thread to complete the operation of a particular section of code by a process called **mutual exclusion** (mutex). Code under such exclusion is a **critical section** (CS), which requires:

1. Mutual Exclusion
2. Progress: No other threads are allowed inside. Any thread inside will eventually leave.
3. Bounded Waiting: Any threads waiting to enter will eventually enter.
4. Performance: The overhead required for entering the critical section is as small as possible.

In human terms, the requirements are safety, liveness (i.e., "something good happens"), and performance. Performance is the one property that is evaluated across all runs on average. Keep in mind that, while performance is important, safety is still priority number one!

## Locks

**Locks** are one way to implement critical sections. Metaphorically, a thread locks a door on the way in and then unlocks it on their way out. There are two operations associated with locks:

- `acquire()`: "Wait until the lock is free, then take it to enter a C.S."
- `release()`: "Release lock to leave a C.S., waking up anyone waiting for it."

These two functions must *always* go together. Once you use `acquire()`, you must later use `release()`. If one thread has acquired a lock, another cannot acquire it until the first has released it. When there are multiple threads needing to acquire a lock, acquisition is scheduled on a first-come, first-serve basis. Here is how you would acquire and release in C:

{% highlight c %}
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
// Critical section code goes here.
pthread-mutex_unlock(&lock);
{% endhighlight %}

The variable `lock` is either locked or unlocked. It is initialized to locked.

There are two design approaches for locks: coarse-grained locking, in which "[o]ne big lock is used any time any critical section is accessed," and fine-grained locking, in which different locks are used "to protect different data and data structures." This is a design choice that is up to the developer.
