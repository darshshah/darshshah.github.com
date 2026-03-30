---
layout: post
title: "Percolator: Large-Scale Incremental Processing at Google"
date: 2024-09-23
author: Darsh Shah
tags: [distributed-systems, google, incremental-processing, transactions, bigtable, search-indexing]
---

# Percolator: Large-Scale Incremental Processing at Google

> **Paper:** *"Large-scale Incremental Processing Using Distributed Transactions and Notifications"* by Daniel Peng and Frank Dabek (OSDI, 2010)

## TL;DR

Percolator is Google's system for incrementally processing updates to massive datasets, built on top of Bigtable to replace batch-oriented MapReduce pipelines for web search indexing. By providing ACID snapshot-isolation transactions and an observer-based notification mechanism, Percolator reduced the median document indexing latency by over 100x compared to the previous MapReduce-based system.

## Summary

Percolator is a system for incrementally processing updates to a large dataset, deployed to build the Google web search index. Built on top of the Bigtable distributed storage system, Percolator provides two main abstractions for performing incremental processing at large scale: ACID transactions over a random-access repository, and observers -- a mechanism for organizing incremental computations. Percolator supports cross-row, cross-table transactions with ACID snapshot-isolation semantics.

Percolator is significantly faster than MapReduce for this workload. Under the previous MapReduce-based pipeline, each document spent two to three days being indexed before it could appear in search results. With the Percolator-based indexing system, each document is fed through the pipeline as it is crawled. The immediate advantage is a dramatic reduction in latency: the median document moves through Caffeine over 100x faster than in the previous system, and the average document age is reduced by 50%.

## Strengths

- Percolator does not reinvent the wheel. It leverages Bigtable and GFS for the majority of its needs, building a focused layer of transaction and notification logic on top of proven infrastructure.
- The money transfer example and accompanying code snippet are extremely effective at explaining how Percolator works. This was a particularly useful section for building intuition.
- The evaluation is thorough. The authors compare Percolator against both MapReduce and a synthetic workload, providing a clear picture of the system's performance characteristics.

## Weaknesses

- The paper should have provided more justification for why Bigtable alone is not sufficient -- and what specifically Percolator adds on top. At a high level, Percolator can appear to be primarily a wrapper around Bigtable, and the paper could do more to clarify the distinction.
- The description of the prefetching mechanism is incomplete. The factors that drive the prefetching algorithm and how it is evaluated are not clearly explained.
- The failure mode of the timestamp oracle is underspecified. What happens if the oracle fails, and how is recovery handled? More clarity on this would be valuable.

## Discussion Questions

- Could Percolator use Chubby for its lock service? The paper states that the lock service must be replicated, distributed, and balanced -- requirements that Chubby appears to satisfy.
- In MapReduce, stragglers are a well-known source of latency. The paper claims that Percolator is immune to stragglers, but can slow-running processes still affect overall system performance?
- Timestamps are assigned in strictly increasing order. Is it possible to exhaust the timestamp space under very high request rates? What is the timestamp format and its practical range?
- Is the additional computational overhead required by Percolator justified by the reduction in average document age?
- Since there is no global deadlock detector, how frequently do deadlocks occur in practice? How are deadlocks resolved -- are the involved processes simply killed?

---

*This review was written as part of CMU's 18-845: Internet Services course.*
