---
title: Scheduling
layout: default
---

# Scheduling

## The Problem

We have *k* **jobs** ready to run but only *n* CPUs. ("Jobs" refers to processes, threads, etc.) The **policy** determines which jobs get assigned to which CPUs. Let's put our thinking caps on because we'll have to work with algorithms here. The naive solution would be to assign jobs randomly, and this makes a good baseline, but it's far from optimal. Scheduling is a wide open design choice and the criteria for "good service" include:

- **Throughput** is the "number of processes that complete per unit time"
- **Turnaround time** (TT) is the "time for each process to complete." It is calculated as the time of completion minus the time of *arrival*.
- **Response time** is the "time from request to first response." It is the first run time minus the arrival time.

Execution time is not turnaround time because turnaround time includes the time a process spends in states other than running. We also want a fair scheduler, but such a thing is impossible to measure reliably. There exists no one algorithm to maximize all criteria. *Batch systems* prioritize throughput and turnaround time while *interactive systems* prioritize response time. An example of the former would be supercomputers while the latter would be PCs.

The operating system calls the scheduler when processes change state or exits.

## The Algorithms

### FIFO

**First-in first-out** (FIFO) scheduling is self-explanatory. I will literally not explain it. The problem with this algorithm is if a job in front of the queue has a long turnaround time, then the faster jobs in the back of the queue have to wait. This causes a loss in average turnaround time because the faster jobs could have been scheduled sooner.

### SJF and STCF

**Shortest Job First** (SJF) schedules the shortest job first. This algorithm has been proven to give optimal average turnaround time *if the the jobs arrive simultaneously*. A drawback is a "starvation problem," where a long job is perpetually pushed to the back and is never run. A version of SJF is "non-preemptive," in which the OS gives the CPU to a job and does not relinquish control of the CPU until the job is complete. In the other version of SJF, called "preemptive" or "**shortest time-to-completion first**" (STCF), a new job with a length shorter than the length remaining on the current job will be given control of the CPU. [This video](https://www.youtube.com/watch?v=lnE7Pr99dfo) provides a helpful explanation.

A shortcoming of the SJF and STCF algorithms is that the OS might not know how long each job will take to complete. It usually doesn't. Minimizing response time in *all* cases is also impossible.

### RR

**Round Robin** (RR) ensures fairness and prevents starvation by giving each job a fixed length of time called a **quantum**, which is the Latin word for "How much?" After this quantum expires, the current job is preempted and then moved to the back of the FIFO queue. One possible drawback is that context switching time could dominate the system. Higher average turnaround times could also be a result of RR. Nevertheless, round robin provides a great average response time.

How does one pick a good quantum length? It should certainly be greater than context switching time length. The length of the quantum is decided empirically, i.e., values are picked based on how well they perform in actual OS settings. The typical value is one to 100 milliseconds. It is fixed in the system.

### Improving Throughput

We can improve throughput if jobs require I/O *and* computation. We can run the job needing I/O and another job needing computation at the same time.
