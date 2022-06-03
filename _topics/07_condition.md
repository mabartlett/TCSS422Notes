---
title: Condition Variables
layout: default
---

# Condition Variables

## Condition Variables and Threads

A thread must often wait for some condition to be met in another thread before proceeding with execution. "A **condition variable** (CV) is associated with a condition needed for a thread to make progress." The thread is blocked and placed in a FIFO queue before the variable is satisfied. When the variable is satisfied, a signal is emitted, which frees threads from the queue.

C gives us a `pthread_cond_t` API for condition variables. They are initialized with `PTHREAD_COND_INITALIZER`. These are defined in `pthread.h`. There are three operations:

- Wait: `pthread_cond_wait(&c, &m)` -  Release the lock and wait for the CV to be signaled. The first argument is the CV itself and the second is a mutex lock. The mutex must be passed so that its lock can be released when the thread is dequeued.
- Signal: `pthread_cond_signal(&c)` - Wake up the first thread waiting in the queue. Its one argument is the CV.
- Broadcast: `pthread_cond_broadcast(&c)` - Wake up all the threads waiting in the queue. Its one argument is the CV.

## Bounded Buffer

A buffer is a portion of memory. A **producer** thread *puts* elements into the buffer and a **consumer** thread *gets* elements from it. If you've got resource buffers shared by consumer and producer threads that consume and produce at different rates, then fix the size of the queue, i.e., make it *bounded*. This prevents overflow. When you bound the buffer, you get a **bounded buffer**. Yeah. A bounded buffer stores a count of how many elements it has stored inside it. This count is a condition variable.

The producer/consumer must

1. Lock its critical section.
2. Check the buffer's count CV.
3. Put/Get.
4. Signal.
5. Unlock its critical section.

Checking for the count CV (step 2) must be within a while loop as opposed to an if statement. Why? When the thread is woken up within an if statement, it won't check the count CV again but will instead go right to the put or get operation, even if there is no space to put or nothing to get! A while loop ensures that the condition will be checked again when the thread is woken up.

Producer threads must signal consumers and vice-versa, so we should use two condition variables for the bounded buffer: one for *empty* and another for *full*. Producer threads will wait for empty and then signal on full while consumer threads wait for full and then signal on empty. ("Full" and "empty" are really misnomers here since "full" really means there is at least one item in the buffer and "empty" really means there is at least one empty slot in the buffer.)

When the buffer has a size greater than one, there are two indices: one for where elements can be inserted by the producer and another for where elements can be removed by the consumer. This is done in a ring-like fashion, which is to say that if the maximum size of the buffer is `MAX`, then the new index `idx` is `idx = (idx + 1) % MAX` where `%` is the modulo operator. A variable that contains a count of elements in the bounded buffer is also needed.
