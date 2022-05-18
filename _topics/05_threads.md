---
title: Threads and Concurrency
layout: default
---

# Threads and Concurrency

## Threads

Communication between processes is expensive because the communication must go through the OS. This is where threads enter. Each tab in Google Chrome is a process. These tabs share the same code, data, privileges, and resources; however, they do not share hardware state, e.g., program counter, stack pointer, or register values. "Why don't we separate the concept of a process from its execution state?" We call the execution state the **thread of control**. In a multi-threaded process, there are multiple threads of control and hardware states, but code, data, and files are shared. In other words, they have one address space but unique execution states.

Thus, threads and processes are two different things in modern operating systems. "A thread is bound to a single process." Each process may have multiple threads. For example, you can have one thread for a GUI and another for I/O, but these threads will still be running the same process.

C makes use of threads with the **`pthread`** API, which is defined in `pthread.h`. The function to create a thread is

{% highlight C %}
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine)(void*), void *arg);
{% endhighlight %}

- `pthread_t *thread` is the thread being initialized. It should have been declared before calling `pthread_create`. Notice that this is a *pointer*, so we need the thread's address, which means you'll want to use the `&` operator.
- `pthread_attr_t *attr` is usually `NULL`.
- `void *(*start_routine)(void*)` is a pointer to the function the thread should start running. Don't get confused here. All that's needed from you is simply the name of the function.
- `void *arg` is the argument--another pointer--to pass to the above function. If you want to pass more than one argument, simply put them all in a `struct`.

If successful, the function returns 0; otherwise, it returns an error code number and `*thread` is undefined. You must join the thread later with the following function:

{% highlight C %}
int pthread_join(pthread_t thread, void **value_ptr)
{% endhighlight %}

This also returns 0 upon success and an error code upon failure. The argument `**value_ptr` is usually `NULL`. If you really want to know what it's for, just read the man pages, alright!

In C, the keyword **volatile** denotes that a variable is shared by multiple threads. This name is fitting because shared threads are often desynchronized, which can cause problems like race conditions, so be careful.

## Race Conditions

Be responsible when using multiple threads! If the order of execution is important, do not put events in separate threads. Beware of **race conditions**, in which multiple threads share a resource and the outcome is different depending on which thread accesses the resource first. Only global resources are susceptible to race conditions and we must protect them by using **synchronization**. Local variables are not shared but anything on the heap is. (Anything allocated with `malloc()` is on the heap.)

We can allow only one thread to complete the operation of a particular section of code by a process called **mutual exclusion** (mutex). Code under such exclusion is a **critical section** (CS), which requires:

1. Mutual Exclusion
2. Progress: No other threads are allowed inside. Any thread inside will eventually leave.
3. Bounded Waiting: Any threads waiting to enter will eventually enter.
4. Performance: The overhead required for entering the critical section is as small as possible.

In human terms, the requirements are safety, liveness (i.e., "something good happens"), and performance. Performance is the one property that is evaluated across all runs on average. Keep in mind that, while performance is important, safety is still priority number one.
