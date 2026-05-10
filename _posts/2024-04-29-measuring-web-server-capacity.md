---
layout: post
title: "Measuring Web Server Capacity Under Realistic Conditions"
date: 2024-04-29
author: Darsh Shah
tags: [web-servers, benchmarking, performance, networking]
excerpt: "This paper exposes critical shortcomings in how web server benchmarks were designed in the late 1990s -- they failed to push servers past their capacity and ignored the effects of WAN delays and packet losses. The authors propose a new benchmarking strategy using S-Clients and a WAN-modeling router, demonstrating that real-world server performance is significantly lower than what existing benchmarks suggested."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"Measuring the Capacity of a Web Server under Realistic Loads"* by G. Banga and P. Druschel (World Wide Web Journal, 1999)

## TL;DR

This paper exposes critical shortcomings in how web server benchmarks were designed in the late 1990s -- they failed to push servers past their capacity and ignored the effects of WAN delays and packet losses. The authors propose a new benchmarking strategy using S-Clients and a WAN-modeling router, demonstrating that real-world server performance is significantly lower than what existing benchmarks suggested.

## Summary

The paper discusses the limitations of existing approaches to measuring web server capacity and demonstrates that contemporary benchmarks neither drive a server beyond its capacity nor model the effects of delays and packet losses in the network.

The authors propose a new strategy that addresses these problems by using S-Clients and a WAN-modeling router. The router is used to measure the effects of packet losses and delays on server performance. The paper shows that actual server performance is significantly lower than what benchmarks indicate under overload conditions, bursty traffic, and in the presence of WAN packet losses and delays.

## Strengths

- The paper thoroughly explains the dynamics of an HTTP server, helping the reader understand the design limitations of existing load generators. This section is particularly valuable since the rest of the paper builds on improving these limitations.
- The authors draw upon a substantial body of prior work and benchmarking efforts to demonstrate that the existing setup is insufficient for testing and that a new benchmarking approach is needed.
- The extensive experimentation is the highlight of the paper. It provides an in-depth view of the proposed benchmarking methodology and its results.

## Weaknesses

- The authors fail to justify the need for two different test beds in their experiment.
- The paper does not adequately explain why high burstiness degrades throughput so substantially.
- The authors claim that most research papers do not account for WAN delay. However, many papers from that era did simulate WAN conditions to test their ideas. This claim feels somewhat exaggerated.

## Discussion Questions

- It would have been interesting to see S-Clients designed for the HTTP 1.1 protocol. Since HTTP 1.1 supports persistent connections, how many active sockets would be needed is a compelling research question.
- The delay router queues packets in a kernel data structure to be forwarded after a specific delay. What happens if the queue is full? How would that affect the benchmarking results?
- What happens when the client aborts an incomplete connection in the SYN-RCVD state at the server, but the server's SYN-ACK has not yet reached the client?
- It would have been interesting to observe the effect of WAN delays on a multi-process Zeus server -- whether it behaves similarly to Apache or not.
- How does this research from 1999 relate to modern web server testing? Are these conditions still relevant in today's web server environments?
