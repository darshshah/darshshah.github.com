---
layout: post
title: "Hardware vs. Software Virtualization: A Deep Dive into x86 Techniques"
date: 2025-02-17
author: Darsh Shah
tags: [virtualization, x86, binary-translation, hardware-virtualization, software-virtualization, performance]
excerpt: "This paper provides a rigorous head-to-head comparison of software-based and hardware-based x86 virtualization. The surprising finding is that software VMMs outperform first-generation hardware VMMs for I/O-heavy and context-switch-heavy workloads, while hardware VMMs win on system-call-intensive workloads. The authors argue that the best path forward is a hybrid approach where hardware extensions complement existing software techniques."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"A Comparison of Software and Hardware Techniques for x86 Virtualization"* by K. Adams and O. Agesen (ASPLOS, 2006)

## TL;DR

This paper provides a rigorous head-to-head comparison of software-based and hardware-based x86 virtualization. The surprising finding is that software VMMs outperform first-generation hardware VMMs for I/O-heavy and context-switch-heavy workloads, while hardware VMMs win on system-call-intensive workloads. The authors argue that the best path forward is a hybrid approach where hardware extensions complement existing software techniques.

## Summary

The paper examines virtualization by comparing classical, software-based, and hardware-based approaches. It begins by explaining classical virtualization and how it was achieved using the trap-and-emulate method. It then discusses software virtualization, focusing specifically on x86 virtualization challenges and how binary translation (BT) can overcome them. The authors provide a concrete code-based example of the binary translation technique, demonstrating how adaptive translation addresses some of BT's drawbacks. The paper then gives an overview of hardware virtualization and presents an in-depth comparison and analysis of hardware versus software virtualization techniques.

The key findings show that for compute-intensive benchmarks, both hardware and software VMMs run at near-native speed. However, for workloads that perform I/O, create processes, or switch contexts rapidly, the software VMM outperforms the hardware VMM. For workloads rich in system calls, the hardware VMM wins. The authors conclude by expressing hope that future hardware virtualization support will be designed to blend seamlessly with and complement existing software techniques, leading to better overall virtualization performance.

## Strengths

- The binary translation code example is very helpful in illustrating how BT works and how code is translated into a Translation Unit (TU) and Compiled Code Fragment (CCF).
- The section on hardware MMU support is well-written, effectively explaining how hardware-based nested page tables function.
- The evaluation is particularly strong, especially the nanobenchmarks section. It provides clear insight into the advantages and drawbacks of software VMMs versus hardware VMMs versus native execution. The raw data on the last page also gives a more complete picture of the measured values.

## Weaknesses

- The paper does not adequately cover I/O virtualization for either the hardware or software approach.
- In Figure 5, the overhead of cr8wr is negative. This test is performed on FrobOS, which never takes interrupts, meaning no %cr8 write in the test ever causes an exit. It is unclear whether this test result is accurate, and the authors should have provided more information to explain the anomaly.
- The authors briefly mention hybrid VMMs in Section 7.3 but do not explore the concept in depth. A more thorough introduction to hybrid VMMs and their potential use cases would have strengthened the paper.

## Discussion Questions

- For the binary translation technique in software virtualization, what is the granularity of translation? "On-the-fly" or "on-demand" implies translation occurs just before execution -- does this happen at the decode stage of the instruction pipeline, or does it rely on tracking the program counter to predict what will execute next?
- If a guest OS allocates a page table but never uses it, will the shadow copy be created at allocation time or on first use? When exactly does the trap occur in software virtualization?
- For hardware virtualization, must the guest OS use the same Instruction Set Architecture as the host? AMD, ARM, and PowerPC ISAs differ from x86 -- can a guest OS compiled for a particular ISA run on a host with a different ISA under hardware virtualization? Is on-the-fly ISA translation feasible in that scenario?
- Is it possible to share I/O devices among multiple guest operating systems in hardware virtualization? Since the VMM traps I/O accesses, does hardware support enable simultaneous usage -- effectively multiplexing I/O peripherals across guests?
- The VMCB includes a hardware-maintained shadow of the guest %eflags register. Does this mean each guest OS has a dedicated hardware register holding %eflags information? If so, how many such registers can the hardware support simultaneously?
