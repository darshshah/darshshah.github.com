---
layout: post
title: "Chubby: Google's Distributed Lock Service for Loosely-Coupled Systems"
date: 2024-08-12
author: Darsh Shah
tags: [distributed-systems, locking, google, consensus, paxos, fault-tolerance]
---

# Chubby: Google's Distributed Lock Service for Loosely-Coupled Systems

> **Paper:** *"The Chubby Lock Service for Loosely-Coupled Distributed Systems"* by Mike Burrows (OSDI, 2006)

## TL;DR

Chubby is Google's distributed lock service that provides coarse-grained locking and reliable low-volume storage for loosely-coupled distributed systems. Rather than exposing a raw Paxos library to developers, Chubby offers a familiar file-system-like interface with advisory locks, prioritizing availability and reliability over raw performance -- and it has become a foundational building block for many Google infrastructure services.

## Summary

The paper describes Google's Chubby lock service, which provides coarse-grained locking as well as reliable (though low-volume) storage for loosely-coupled distributed systems. Chubby exposes an interface similar to a distributed file system with advisory locks, but the design emphasis is on availability and reliability rather than high performance. Many instances of the service have been deployed for over a year, with several of them each handling tens of thousands of concurrent clients. The paper describes the initial design and expected use, compares it with actual use, and explains how the design had to be modified to accommodate the differences.

## Strengths

- The design decisions made before building Chubby are among the highlights of the paper. Key examples include choosing to build a lock service rather than a Paxos library, and providing the option to store small files and metadata directly in Chubby.
- The Chubby service enables developers to implement correct distributed locking without having to build the machinery from scratch.
- The paper addresses the drawback of Paxos relating to multiple active masters by implementing master leases and only allowing a new master election when the current master's lease has expired.

## Weaknesses

- Cache invalidation should not render other nodes unreachable. The coupling between cache consistency and node availability seems problematic.
- Requiring a manual review of clients' code before they can use shared Chubby cells is not a scalable solution.
- One design aspect worth reconsidering is allowing reads to be served from replicas. This would reduce the burden on the master, and since a majority quorum is already required for writes, occasional stale reads may be an acceptable trade-off.

## Discussion Questions

- Why is coarse-grained locking advisory rather than mandatory? Could advisory locking lead to issues in scenarios where mandatory locking is required but not enforced?
- Why does Chubby use exactly five replicas? Could it work with three or seven? Notably, an odd number of replicas is always chosen -- likely to ensure a clear majority for consensus.
- With so many clients connected to a single Chubby cell, how does the system ensure that a single misbehaving client cannot bring the entire system down?
- Is Chubby used more as a name service than for distributed locking? Can it scale effectively in that use case?
- What are the key differences between Apache ZooKeeper and Chubby? Could ZooKeeper serve as a drop-in replacement for Chubby?

---

*This review was written as part of CMU's 18-845: Internet Services course.*
