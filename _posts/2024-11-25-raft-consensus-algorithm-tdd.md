---
layout: post
title: "Building Raft with Test-Driven Development in Go"
date: 2024-11-25
author: Darsh Shah
tags: [distributed-systems, consensus, raft, test-driven-development, go]
---

# Building Raft with Test-Driven Development in Go

> **Report:** *"Building Raft - An Exploration of Test-Driven Development in Go"* by Sean Klein and Advaya Krishna (Student Project Report, 2015)

## TL;DR

This student project report describes an implementation of the Raft consensus algorithm using test-driven development (TDD) in Go. The review evaluates the clarity of the report's Raft explanation, the usefulness of its debug server concept, and the quality of its API descriptions, while noting gaps in performance evaluation and some unclear architectural diagrams.

## Summary

The report describes the Raft consensus algorithm and details how the authors implemented it using test-driven development in the Go programming language. It covers the core Raft protocol, the system's API design, a debug server for runtime rule changes, and the development process including man-hours spent.

## Strengths

- **Useful debug server concept.** The idea of a debug server is very practical -- being able to change rules on the fly during development and testing is a handy tool for working with consensus protocols.

- **Clear Raft explanation.** The section explaining Raft effectively sets the stage for understanding the implementation work that follows, giving the reader sufficient context without being overly verbose.

- **Well-documented API design.** The description of the APIs is strong, providing a clear understanding of how the interaction between different components is designed and how they communicate.

- **Accessible writing style.** The language and writing style throughout the report are simple, clear, and understandable -- making the content approachable even for readers less familiar with consensus algorithms.

- **Transparent development metrics.** Including the graph of total man-hours was a bold and commendable move. It adds valuable transparency about the actual effort involved in building a distributed consensus implementation.

## Weaknesses

- **Unclear architectural diagram.** Figure 3 needs more explanation. It is not clear from the diagram whether all Raft nodes are connected to each other, whether each node connects directly to the client, or what role the server manager plays in the architecture.

- **Ambiguous term semantics.** Are terms globally unique, or is the term scoped per leader? The report mentions that "a term begins when a follower enters the candidate state and begins an election," but it does not explain what happens if the candidate fails to become the leader. Is the term reset, incremented, or retained?

- **No performance evaluation.** It would have been valuable to include performance numbers for the Raft implementation. Since the report draws comparisons to Paxos, the authors could have benchmarked against an open-source Paxos implementation for write and query throughput, or compared their implementation against an existing open-source Raft implementation. The report demonstrates that Raft was successfully implemented, but it does not show how efficient the implementation is.

- **Typographical error.** In Section 2.3.2, the "Illegal Read Log" paragraph contains the stray term "Non-majority Election Test" at the end, which appears to be a copy-paste artifact.

- **Color-dependent figure.** Figure 4 relies on color to distinguish columns, which makes them indistinguishable when printed in grayscale. Labeling each individual column would have resolved this accessibility issue.

---

*This review was written as part of CMU's 18-845: Internet Services course.*
