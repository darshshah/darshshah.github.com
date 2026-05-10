---
layout: post
title: "Dapper: Google's Large-Scale Distributed Tracing Infrastructure"
date: 2024-11-04
author: Darsh Shah
tags: [distributed-systems, google, tracing, monitoring, observability, performance]
excerpt: "Dapper is Google's production distributed tracing system that provides low-overhead, application-transparent instrumentation across Google's massive infrastructure. Originally conceived as a tracing tool, it evolved into a general-purpose monitoring platform -- enabling engineers to understand the behavior of complex distributed workloads, diagnose performance issues, and discover faults across systems where a single query may touch thousands of backend services."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"Dapper, a Large-Scale Distributed Systems Tracing Infrastructure"* by Benjamin H. Sigelman, Luiz Andre Barroso, Mike Burrows, et al. (Google Technical Report, 2010)

## TL;DR

Dapper is Google's production distributed tracing system that provides low-overhead, application-transparent instrumentation across Google's massive infrastructure. Originally conceived as a tracing tool, it evolved into a general-purpose monitoring platform -- enabling engineers to understand the behavior of complex distributed workloads, diagnose performance issues, and discover faults across systems where a single query may touch thousands of backend services.

## Summary

Dapper is Google's production distributed systems tracing infrastructure, designed to provide low overhead, application-level transparency, and ubiquitous deployment across a very large-scale system. Originally conceived as a tracing tool, Dapper evolved into a monitoring platform that enabled the creation of many other diagnostic tools. Dapper addresses a fundamental challenge: at Google's scale, it is extremely difficult to pinpoint the exact cause of a performance issue. For example, a single search query may interact with thousands of backend components, making it nearly impossible to determine which component caused a delay. Dapper solves this by attaching tracing instrumentation to the threading, control flow, and RPC library code used by almost all Google services. When a performance issue arises, the entire trace can be visualized through the Dapper UI.

In a Dapper trace tree, nodes represent basic units of work called spans, and edges indicate causal relationships between a span and its parent. The system also supports key-value annotations. Adaptive sampling makes Dapper lightweight by ensuring that tracing does not meaningfully affect application performance. At the time of the paper's publication, Dapper had been in use at Google for two years and had proven successful. While it has been used for fault discovery, Dapper has been generally more useful for uncovering performance issues and improving the overall understanding of large, complex workloads.

## Strengths

- The adaptive sampling technique and second-pass filtering are particularly clever, as they reduce disk overhead and minimize instrumentation cost without sacrificing trace quality.
- Dapper's trace data is language-independent, meaning it can be used across all systems regardless of whether they are implemented in C++ or Java. This demonstrates the extensibility and broad applicability of the system.
- By the time the paper was published, Dapper had already been in production use for two years and had become ubiquitous -- the go-to tool for understanding performance problems. The fact that the authors waited two years before publishing means the system was already proven, reducing the need for a traditional evaluation section.

## Weaknesses

- The paper should have provided concrete numbers to illustrate worst-case trace collection scenarios. For example, stating that a particular trace had 10,000 spans and took approximately three hours to propagate to the central repository would be more informative than simply saying that the remaining 25% of traces "can grow to be many hours" without providing an upper bound.
- The need for bulk access support in the Depot API deserved more explanation. A concrete use case would have made the functionality easier to understand.
- As a technical report rather than a research paper, the document focuses heavily on the existing system, its use cases, and lessons learned. A deeper dive into the intricate design details and the research challenges involved in building the system would have strengthened the contribution.

## Discussion Questions

- How are timestamps recorded in a span? Is the time sourced from NTP or from Google's own centralized time management system?
- It would be interesting to know whether Dapper extensions exist for systems built in the Go programming language, especially given Go's growing adoption at Google.
- Could the application-level opt-in mechanism for annotations introduce a security vulnerability? Since users can tag arbitrary annotations to a span and trace data is searchable by anyone within Google, could this pose a security or privacy risk?
- Can the level of logging be made more fine-grained? How difficult would it be to include data about memory usage, lock contention, process switching, and other system-level metrics? These factors can also play a significant role in performance degradation.
- Users of the Dapper system must maintain separate local logs, as shown in Figure 5. Would it be possible to write trace data directly into Bigtable instead of relying on local logging? Could a centralized log queue -- similar to an append-only log from RAMCloud -- hold data from all servers and push it to Bigtable, eliminating the need for local disk storage?
- What exactly is a "control path" in the context of Dapper (e.g., "when a thread handles a traced control path")? Is this a Google-specific concept, or does it simply refer to the execution path taken by a request?
