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

We can allow only one thread to complete the operation of a code by a process called **mutual exclusion**. Code under such exclusion is a *critical section*.
