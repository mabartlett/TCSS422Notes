---
title: Scheduling
layout: default
---

# Scheduling

## The Problem

We have *k* **jobs** ready to run but only *n* CPUs. ("Jobs" refers to processes, threads, etc.) The **policy** determines which jobs get assigned to which CPUs. The naive solution would be to assign jobs randomly, and this makes a good baseline, but it's far from optimal. Scheduling is a wide open design choice and there are three major criteria for "good service":

- **Throughput** is the "number of processes that complete per unit time"
- **Turnaround time** (TT) is the "time for each process to complete." It is calculated as the time of completion minus the time of arrival.
- **Response time** (RT) is the "time from request to first response." It is the first run time minus the arrival time.

Execution time is not turnaround time because turnaround time includes the time a process spends in states other than running. We also want a fair scheduler, but such a thing is impossible to measure reliably. There exists no single algorithm to maximize all criteria. *Batch systems* prioritize throughput and turnaround time while *interactive systems* prioritize response time. An example of the former would be supercomputers while the latter would be PCs.

The operating system calls the scheduler when processes change state or exit.

We can improve throughput if jobs require I/O *and* computation. We can run the job needing I/O and another job needing computation at the same time.

## The Algorithms

### First-In First-Out

**First-in first-out** (FIFO) scheduling is self-explanatory. I will literally not explain it. This algorithm can also be called *first-come first-serve* (FCFS) The problem with this algorithm is if a job in front of the queue has a long turnaround time, then the faster jobs in the back of the queue have to wait. This causes a loss in average turnaround time because the faster jobs could have been scheduled sooner.

### Shortest Job First

**Shortest Job First** (SJF) schedules the shortest job first. This algorithm has been proven to give optimal average turnaround time *if the jobs arrive simultaneously*. A drawback is a "starvation problem," where a long job is perpetually pushed to the back and is never run. A version of SJF is "non-preemptive," in which the OS gives the CPU to a job and does not relinquish control of the CPU until the job is complete.

### Shortest Time-to-Completion First

In the other version of SJF, called "preemptive" or "**shortest time-to-completion first**" (STCF), a new job with a shorter burst time than the burst time remaining on the current job will be given control of the CPU. [This video](https://www.youtube.com/watch?v=lnE7Pr99dfo) provides a helpful explanation.

A shortcoming of the SJF and STCF algorithms is that the OS might not know how long each job will take to complete. It usually doesn't. Minimizing response time in *all* cases is also impossible.

### Round Robin

**Round Robin** (RR) ensures fairness and prevents starvation by giving each job a fixed length of time called a **quantum**, which is the Latin word for "How much?" After this quantum expires, the current job is preempted and then moved to the back of the FIFO queue. One possible drawback is that context switching time could dominate the system. Higher average turnaround times could also be a result of RR. Nevertheless, round robin provides a great average response time.

How does one pick a good quantum length? It should certainly be greater than context switching time length. The length of the quantum is decided empirically, i.e., values are picked based on how well they perform in actual OS settings. The typical value is between one and 100 milliseconds. It is fixed in the system.

### Multi-Level Feedback Queues

**Multi-level feedback queues** (MLFQ) is the algorithm most used in modern operating systems. It improves turnaround time by running shorter jobs first and minimizes response time. This algorithm was invented by Spanish-American computer scientist Fernando José Corbató, who was the recipient of the Turing Award.

In MLFQ, there exist multiple job queues. (The typical design right now is three or four queues.) Rule 1 is to schedule jobs with higher priority first. Rule 2 is to schedule jobs with the same priority according to round robin scheduling. The motto of MLFQ is to "[a]djust job priority based on observed behavior." Interactive jobs have high priority while batch jobs have lower priority. Rule 3 is to give a new job the highest priority. Rule 4a states a job's priority should be reduced if it uses an entire time slice (because this indicates the job is likely a batch job). Its counterpart rule 4b states to keep a job's priority level if it forfeits control of the CPU before the end of its time slice. Keep in mind that such a job may not be done because it may simply be *yielding*. Notice that this algorithm approximates shortest job first.

MLFQ isn't free of problems. Starvation is still an issue here. Jobs might also take advantage of the scheduler by fraudulently taking priority. We call this "gaming" the scheduler. There's no way to prevent this. It's also possible a job might sink to the lowest priority queue and never rise back up.

We can solve the starvation problem by using **priority boosting**, in which a job's priority is increased artificially. Rule 5 states to move a job--no matter its current priority--to the topmost queue after some fixed time period. We can prevent "gaming" by improving time accounting, i.e., by "track[ing] total job execution time in the queue" and allotting each job a fixed time. When this time expires, the job's priority is lowered.

The rules restated are:

1. Schedule jobs with higher priority first.
2. Schedule jobs with the same priority according to round robin.
3. Give a new job the highest priority.
4. This rule has two parts:
    a) Reduce a job's priority if it uses an entire time slice.
    b) If a job forfeits control of the CPU before the end of its time slice, its priority level should remain the same.
5. Move a job to the topmost queue after some fixed time period.
