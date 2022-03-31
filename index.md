---
title: TCSS 422 - Operating Systems
layout: default
---

# Welcome

## Introduction

Welcome, fellow student! This page is where you'll find the notes I've typed for TCSS 422 in the Spring 2022 quarter. (Please refer to the footer for copyright information.) I hope you'll find this page as helpful as I do. I'll be updating this site regularly throughout the quarter, so check in regularly! Please do not substitute reading this page for coming to class.

I built this page by using [Jekyll](https://jekyllrb.com/), a popular static-site generator. This means I'm typing up my notes in [Markdown](https://daringfireball.net/projects/markdown/), which Jekyll compiles into HTML.

## What Is an Operating System?

The **operating system** (OS) handles the communication between input/output devices and CPU/memory. It is a resource manager that handles the hardware for one's programs. An operating system is *software*. Many operating systems are written in **C**, so that's the language we'll be using in this class. It's more predictable than a higher-level language like Java or Python. [The GNU C Reference Manual](https://www.gnu.org/software/gnu-c-manual/gnu-c-manual.html) is a great resource. Here's what a simple program in C looks like:

{% highlight c %}
#include <stdio.h>

public int main(void) {
    printf("Hello, world!\n;
    return 0;
}
{% endhighlight %}
