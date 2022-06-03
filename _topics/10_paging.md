---
title: Paging
layout: default
---

# Paging

## What Is Paging?

Modern operating systems utilize a memory virtualization technique called **paging**, in which memory is divided into "pages" of fixed size. This cuts down on wasted space. For each process, there is a **page table** that maps "virtual pages to physical **page frames**." Each process takes up multiple pages in memory. Virtual pages and page frames are the exact same size in memory. Page frames need not be contiguous in physical memory.

The typical page size is 4KB. This has been determined to be the best size only by empirical study.

A virtual address is composed of a virtual page number (VPN) as well as an offset. The page table maps a VPN to a page frame number (PFN) or physical page number (PPN). The table is just an array and the VPN is actually just the index of the array. A page table entry (PTE) is an element in the page table. The offset value must be checked to ensure it does not go out of bounds. If it does, this is a segmentation fault.

Paging allows for the easy allocation of memory. There is also "no need to track the direction" of the heap or stack growth. However, paging can cause **internal fragmentation**, i.e., the "process may not use memory in multiples of a page." Another problem is that the page table is itself stored in memory, so there is an amount of "memory reference overhead" involved with retrieving it. The page table can take up quite a bit of memory: in a 32-bit system, each page table can be up to 4MB!

## Multi-Level Page Tables

So the page tables are too big. What should we do? We can make the pages larger, which reduces the number of pages; however, this means internal fragmentation becomes an even bigger problem. One thing we can do is "only map the portion memory that is actually being used." We can use a **two-level page table** but modern operating systems can use up to four or five levels. Rather than pointing to a page frame, the first pointer of a virtual address points to a master page table, which points to *a secondary page table*, which provides a page frame. Each level of a multi-level page table is itself divided into pages. The master page table itself ends up being only a single page.

## TLB

Multi-level page tables trade space for time because the address translation across multiple levels takes more time. The solution to this is to use a **translation lookaside buffer** (TLB) which is managed by the MMU and is a part of the hardware itself. It is on the CPU. It caches translations and this speeds things up *significantly*. The TLB translates virtual page numbers into PTEs rather than physical addresses. Even though only 32-128 PTEs are stored on the TLB, these PTEs can make up *99%* of translations. If there is a miss, the PTE will be stored directly on the TLB *and then the TLB will be queried for that entry again*. This means a PTE that's on the TLB has to be "evicted." Selecting an unlucky PTE depends on the replacement policy.

## Page Replacement

The OS must "evict" a page at times, which is to say it must replace a page in memory with another. It sets the valid bit in the page table to "invalid" and then "stores the location of the page in the **swap file** in the PTE." The swap file is information about the page stored on the hard disk. A **page fault** occurs when a process tries to access the page when the PTE is invalid. The OS's page fault handler then fetches the page from the swap file on the hard disk, puts it into memory, "updates [the] PTE to point to it" and then "restarts the process."

Which page should be replaced in memory? There are several algorithms to determine this.

### FIFO

In first-in, first-out (FIFO), the oldest-fetched page is evicted. However, this leads to Belady's anomaly, in which adding more page frames does not guarantee fewer faults.

### Optimal Page Replacement

In an optimal page replacement algorithm, the element that will not be used for the longest period of time is the one that should be evicted. Since it's not possible to know the future, there is no one "optimal" algorithm, but this optimum can make a good benchmark. This theoretical optimal solution is also called "**Belady's algorithm**."

### LRU

The **least recently used** (LRU) algorithm approximates the optimal solution by evicting the page that has not been used in the longest period of time. (This is based on the principle that the past is like the future.) This can cause problems when there are loops because this means each consecutive memory access is a miss after the first loop. In this case, you would want an eviction policy of *most* recently used (MRU), but MRU is its own can of worms. LRU is the most commonly used algorithm in modern operating systems.

Let's talk about implementations. For each PTE, you could store a timestamp, but this clearly has large space and time costs. We could also store pages in a doubly-linked list. When we access a page, we put it at the tail of the list. The head is the oldest page. This algorithm is expensive, too.

Actually implementing LRU is expensive no matter what, so we merely *approximate* it with the **clock algorithm**. It uses an **A bit**, which is in the hardware. At the beginning, each page's A bit is set to 0. When a page is accessed, it is set to 1. When scanning for a page to replace, if the A bit is 1, it is then set to 0. If the next page's A bit is 0, that page is then evicted. This algorithm is also called "second-chance replacement." If there is a lot of memory, the clock can take a long time to find a page to replace, so we can use a second clock hand. One hand is the head and the other is the tail. The head sets A bits to 0 and the tail "evicts pages with A = 0." The head starts pointing to the first page allocated.

## The End

You have reached the end of this website. Good luck on your final exam!
