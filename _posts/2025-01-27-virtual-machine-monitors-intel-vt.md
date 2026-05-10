---
layout: post
title: "Virtual Machine Monitors and Intel VT: Foundations of Modern Virtualization"
date: 2025-01-27
author: Darsh Shah
tags: [virtualization, vmm, intel-vt, hypervisor, cpu-virtualization, memory-virtualization]
excerpt: "These two papers provide a comprehensive look at virtualization from both the software and hardware sides. Rosenblum and Garfinkel (VMware's co-founder among them) lay out the design goals and implementation challenges of Virtual Machine Monitors, while the Intel VT paper details how hardware extensions -- including new CPU operation modes and control structures -- eliminate the need for complex software workarounds like binary translation and paravirtualization."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Papers:** *"Virtual Machine Monitors: Current Technology and Future Trends"* by Mendel Rosenblum and Tal Garfinkel (IEEE Computer, 2005) and *"Intel Virtualization Technology: Hardware Support for Efficient Processor Virtualization"* by G. Neiger et al. (Intel Technology Journal, 2006)

## TL;DR

These two papers provide a comprehensive look at virtualization from both the software and hardware sides. Rosenblum and Garfinkel (VMware's co-founder among them) lay out the design goals and implementation challenges of Virtual Machine Monitors, while the Intel VT paper details how hardware extensions -- including new CPU operation modes and control structures -- eliminate the need for complex software workarounds like binary translation and paravirtualization.

## Summary

The first paper, jointly written by VMware's co-founder, discusses virtualization and specifically the Virtual Machine Monitor (VMM). The central design goals for a VMM are compatibility, performance, and simplicity. The paper presents VMM implementation challenges across CPU virtualization, memory virtualization, and I/O virtualization, describing the obstacles each area poses and how they can be addressed. It also explores the positive implications of VMMs in server design, security, and software distribution.

The central design goal of Intel VT is to eliminate the need for CPU paravirtualization and binary translation techniques, to simplify the implementation of robust VMMs that can support a broad range of unmodified guest operating systems, and to maintain high levels of performance. The paper provides details on the virtualization challenges posed by IA-32 and Itanium processors and explains how VT-x and VT-i architectures address them. These challenges include ring aliasing, address-space compression, non-faulting access to privileged state, interrupt virtualization, ring compression, access to hidden state, and frequent access to privileged resources. To tackle these challenges, Intel introduced new CPU operation modes (VMX root operation and VMX non-root operation) along with the VMCS (Virtual Machine Control Structure), which includes guest-state and host-state areas to facilitate virtualization.

## Strengths

- Virtualization makes it possible to capture old hardware architectures as virtual machine images and run them on current hardware. This is a compelling and practical use case for virtualization technology.
- The first paper has an effective writing style: it introduces each area of VMM implementation, explains the challenges it presents, and then walks through the solutions.
- In the Intel VT paper, the design where both VMX root operation and VMX non-root operation support all four privilege levels is clever. It allows guest software to run at its intended privilege level, so the guest OS does not need to be artificially restricted from using ring 0.

## Weaknesses

- For ring deprivileging in the Intel VT paper, the authors do not explain why certain privilege levels cannot be used directly. They simply divide the approach into the 0/1/3 and 0/3/3 models without sufficient justification.
- The Intel VT paper would benefit from an appendix listing all assembly instructions affected or supported by VT-x and VT-i. For example, the appendix could document instructions like LGDT, thash, and INVLPG, explaining the role each plays in the architecture.
- The Intel VT paper lacks any evaluation or benchmarks demonstrating that hardware-assisted virtualization outperforms paravirtualization or binary translation. Some empirical evidence would have strengthened the argument considerably.

## Discussion Questions

- What are the latency implications of maintaining shadow copies of page tables in software compared to using hardware-managed page tables?
- In VMware's hosted architecture, where the VMM is installed as an application on the host OS, could there be issues with memory usage or CPU scheduling starvation? If another application crashes the kernel, the VMM fails as well. Is it possible to achieve meaningful isolation in this model?
- Neither paper discusses power utilization. How do power consumption patterns change as more guest operating systems are installed on a single machine?
- What is the maximum number of guest operating systems a VMM can support? Is there a hard limit (e.g., 16), or does it depend entirely on the resource utilization of each guest?
- Do the specialized device drivers for I/O performance optimization provide an abstraction layer over underlying drivers, or do they access the hardware directly?
- It would be interesting to examine whether hardware-assisted virtualization has become widely adopted in practice, and if so, how much performance improvement it provides over earlier approaches. This is especially relevant given that VMware, VirtualBox, and similar technologies remain prevalent today.
