---
layout: post
title: "The End-to-End Design Principle: Placing Functions in the Right Layer"
date: 2024-01-15
author: Darsh Shah
tags: [networking, systems-design, end-to-end-argument, design-principles, distributed-systems]
excerpt: "This foundational paper argues that functions placed at low levels of a system may be redundant or of little value when compared to the cost of providing them at that level. Instead, many functions -- such as reliable delivery, encryption, and duplicate suppression -- are best implemented at the application endpoints, making the end-to-end argument one of the most influential design principles in networked systems."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"End-to-End Arguments in System Design"* by J.H. Saltzer, D.P. Reed, and D.D. Clark (ACM Transactions on Computer Systems, 1984)

## TL;DR

This foundational paper argues that functions placed at low levels of a system may be redundant or of little value when compared to the cost of providing them at that level. Instead, many functions -- such as reliable delivery, encryption, and duplicate suppression -- are best implemented at the application endpoints, making the end-to-end argument one of the most influential design principles in networked systems.

## Summary

The paper presents arguments about the end-to-end design principle in system design, focusing particularly on data communication networks in computer systems. The authors argue that it is critical to place functions in the correct layers and, when appropriate, to push them upward in a layered system. They illustrate why the end-to-end principle is necessary through the example of a careful file transfer application and discuss the tradeoffs between performance and correctness in system design.

The authors then provide several examples to support the end-to-end argument, including delivery guarantees, secure data transmission, duplicate message suppression, guaranteed FIFO delivery, and transaction management. Finally, the paper discusses how to identify layer endpoints and how the end-to-end argument applies to other areas of system design. The overall paper quality is strong -- it is clearly written and accessible, and the multiple examples effectively drive the central point home.

## Strengths

- The paper presents the end-to-end principle at a time when the systems design field was still in its infancy, yet its insights remain remarkably applicable to today's system designers.
- The authors use numerous examples to support the end-to-end argument. Each example is thorough, providing detailed reasoning for how the principle applies.
- The authors discuss tradeoffs in detail and explicitly note that the end-to-end argument is not an absolute rule but rather a guideline -- an important nuance that prevents dogmatic application of the principle.

## Weaknesses

- The paper does not clearly delineate or discuss other layers in the system. It focuses almost exclusively on data communication as an illustrative example.
- The authors should have explained the two-phase commit protocol in greater depth and discussed how it interacts with the end-to-end argument.
- More information on how to identify the correct endpoints would have strengthened the reader's understanding of the principle.

## Discussion Questions

- Where do UDP and TCP fit within the end-to-end design argument?
- Was the design of IP motivated by the end-to-end argument, or did the end-to-end argument emerge from observing IP's design?
- Other examples of this argument include serial communication using checksums, page faults during memory transfers, and subway ticketing systems (you pay when entering the station rather than on each train, allowing multiple connections without repaying -- the collection burden is pushed to the upper layer).
- What mechanism could be used for encryption that simultaneously supports application-level authentication and protection?
- Does the end-to-end argument still hold after more than 30 years, or have faster processors and higher-speed networks invalidated its core claims?
