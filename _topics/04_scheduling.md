---
title: Scheduling
layout: default
---

# Scheduling

## The Problem

We have *k* **jobs** ready to run but only *n* CPUs. ("Jobs" refers to processes, threads, etc.) The **policy** determines which jobs get assigned to which CPUs. Let's put our thinking caps on because we'll have to work with algorithms here. The naive solution would be to assign jobs randomly, and this makes a good baseline, but it's far from optimal. Scheduling is a wide open design choice and the criteria for "good service" include:

- Throughput - "number of processes that complete per unit time"
- Turnaround time - "time for each process to complete"
- Response time - "time from request to first response"

Execution time is not turnaround time because turnaround time includes the time a process spends in states other than running. We also want a fair scheduler, but such a thing is impossible to measure reliably. 

The operating system calls the scheduler when processes change state or exits.
