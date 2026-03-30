---
layout: post
title: "Bigtable: Google's Distributed Storage System for Structured Data"
date: 2024-09-02
author: Darsh Shah
tags: [distributed-systems, storage, google, databases, nosql, bigtable]
---

# Bigtable: Google's Distributed Storage System for Structured Data

> **Paper:** *"Bigtable: A Distributed Storage System for Structured Data"* by Fay Chang, Jeffrey Dean, Sanjay Ghemawat, et al. (OSDI, 2006)

## TL;DR

Bigtable is Google's sparse, distributed, multi-dimensional sorted map designed to scale to petabytes of data across thousands of machines. Rather than building a traditional RDBMS, Google created a system organized around rows, column families, and timestamps -- built atop GFS and Chubby -- that powers critical services like Google Analytics, Google Earth, and web crawling infrastructure.

## Summary

Bigtable is a sparse, distributed, persistent, multi-dimensional sorted map designed to manage structured data at very large scale. It is not a traditional RDBMS. Bigtable organizes data around rows, column families, and timestamps. Rows are ordered lexicographically, and access to data within a row is atomic. A Bigtable instance is partitioned into many tablets based on row keys, with one tablet server responsible for serving each tablet. Bigtable relies on the services of GFS and Chubby to operate.

Many internal clients at Google, including Google Analytics, Google Earth, and the web crawling infrastructure, use Bigtable.

## Strengths

- The Bigtable master and the tablet servers are mostly stateless. State information is stored in GFS and Chubby, which makes the system significantly more scalable.
- A notable aspect of the implementation is that the authors did not try to reinvent the wheel. They built on top of existing proven systems like GFS and Chubby -- both of which were already battle-tested in production.
- The paper provides concrete examples of Google systems using Bigtable -- Google Earth, Google Analytics, and others -- which demonstrates the real power of the system, especially since Bigtable had already been in production use for over a year.

## Weaknesses

- The paper should have provided more detail regarding the ACID properties (or lack thereof) in Bigtable, particularly around transaction semantics and isolation guarantees.
- SSTables allow only appends and no in-place updates. The paper should have explained the rationale behind this design choice more thoroughly.
- More information regarding bloom filters and their role in the system would have been helpful, given their importance in reducing unnecessary disk reads.

## Discussion Questions

- Is killing the tablet server the only option when it loses its exclusive lock? Are there more graceful recovery mechanisms?
- Bigtable has a hard dependency on Chubby. Is there a way to reduce this coupling? If Chubby fails, the entire system becomes unavailable.
- HBase, the open-source counterpart to Bigtable, is a columnar store where all columns are stored together in memory, enabling fast aggregation and counting queries. Is it possible to achieve similar performance for aggregation workloads in Bigtable?
- What happens when a master goes down? Is there a standby replica of the master, or does the entire Bigtable instance go out of service during failover?
- In the evaluation section, the authors should have addressed latency and end-to-end performance for systems using Bigtable. Rather than focusing on the number of tablets required, actual read/write throughput and latency statistics would have been more informative.

---

*This review was written as part of CMU's 18-845: Internet Services course.*
