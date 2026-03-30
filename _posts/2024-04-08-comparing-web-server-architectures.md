---
layout: post
title: "Comparing Web Server Architectures: Events, Threads, and Pipelines"
date: 2024-04-08
author: Darsh Shah
tags: [web-servers, performance, event-driven, threads, pipeline, systems-architecture]
---

# Comparing Web Server Architectures: Events, Threads, and Pipelines

> **Paper:** *"Comparing the Performance of Web Server Architectures"* by David Pariag, Tim Brecht, Ashif Harji, Peter Buhr, and Amol Shukla (EuroSys 2007)

## TL;DR

This paper provides a rigorous performance comparison of event-driven (userver), thread-per-connection (Knot), and hybrid pipeline (WatPipe) web server architectures. After carefully tuning each server and eliminating confounding factors, the authors conclude that event-based and pipeline-based architectures outperform thread-per-connection designs -- refuting earlier claims that threading performs equally well.

## Summary

The paper presents a performance-oriented comparison of three web server architectures: event-driven, thread-per-connection, and hybrid pipeline. The userver represents the event-driven design, the Knot server represents the thread-per-connection model, and WatPipe represents the hybrid pipeline-based approach. The paper introduces two new approaches for supporting the zero-copy `sendfile` system call within the Capriccio threading library. It also introduces three new server architecture variants: Symmetric Multi-Process Event-Driven (SYMPED), shared-SYMPED, and the hybrid pipeline architecture (WatPipe).

The paper then describes in detail the three main server architectures -- userver, Knot, and WatPipe -- and explains how to tune each of them for maximum performance. A thorough comparison among these three servers follows, with detailed analysis of the results. The authors conclude that the event-based userver and pipeline-based WatPipe outperform the threading-based Knot server, refuting the claim of an earlier paper that thread-based servers perform equally well as event-based servers.

## Strengths

- The authors invest significant effort in tuning and testing to ensure a fair comparison of the different architectures. Table 1 is particularly valuable, providing insights into each server's performance with respect to CPU time and caching behavior.
- The authors acknowledge the drawbacks of the SPECweb99 benchmark suite and take concrete steps to overcome them, including using httperf and accounting for user think times, browser delays, and network delays in their testing.
- The authors modify the Knot server to implement the `sendfile` function, ensuring all servers share the same baseline capabilities. They also take care to eliminate confounding factors such as differing caching strategies and hashing mechanisms.

## Weaknesses

- The authors mention that user-level thread libraries like Capriccio are built on an event-driven foundation but do not discuss this point in depth or provide evidence to back up the statement.
- The description of WatPipe lacks implementation details. While it is mentioned that WatPipe's code is based on the userver codebase, there is no discussion of garbage collection, memory management, or other implementation considerations.
- The authors should have tested the servers on additional workloads. Evaluating across a broader range of workloads would provide more perspective on the generalizability of the results.

## Discussion Questions

- WatPipe is based on the userver codebase but uses `select()` while the other servers use `poll()`. The authors do not explain this choice. It would be interesting to see how server performance compares if WatPipe also used `poll()`.
- It would be interesting to explore the effects of having more than one thread in stages 1-3 of the WatPipe server. The authors use a single thread for simplicity to avoid synchronization and locking issues, but would adding more threads in the initial stages make the server behave more like Haboob, or would it degrade performance?
- The authors mention that under some conditions, the non-blocking `sendfile` implementation in the Knot server is susceptible to high polling overheads, but they do not specify what those conditions are. What causes these high overheads, and could using `select()` mitigate the issue? The second implementation also uses `poll()`, so why does it not exhibit the same overhead? The authors use a one-second timeout for thread unblocking -- would changing this parameter affect performance? Testing across different timeout values would have been valuable.
- The authors assume that Capriccio provides a scalable, cooperatively scheduled, user-level threading package for use with high-concurrency servers. A worthwhile research question would be to verify this claim by comparing Capriccio against other threading libraries.
- The authors refute the claim of a previous paper by arguing that Haboob is not as effective as originally claimed, since WatPipe outperforms the threaded model. However, I believe this comparison is not entirely fair -- WatPipe is implemented in C++ and lacks the dynamic controller that adjusts thread counts and performs load shedding at each stage in Haboob. Therefore, I do not think the authors have sufficient grounds to refute the previous paper's claims.
- It would be interesting to see how to fine-tune the shared-SYMPED server under real memory constraints and how performance is affected by lock overhead and contention issues.

---

*This review was written as part of CMU's 18-845: Internet Services course.*
