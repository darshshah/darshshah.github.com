---
layout: post
title: "Mesa: Google's Geo-Replicated, Near Real-Time Data Warehousing System"
date: 2024-10-14
author: Darsh Shah
tags: [distributed-systems, google, data-warehousing, geo-replication, advertising, analytics]
---

# Mesa: Google's Geo-Replicated, Near Real-Time Data Warehousing System

> **Paper:** *"Mesa: Geo-Replicated, Near Real-Time, Scalable Data Warehousing"* by Ashish Gupta, Fan Yang, Jason Govig, et al. (VLDB, 2014)

## TL;DR

Mesa is Google's distributed data warehousing system designed for storing and querying critical advertising metrics. It achieves a unique combination of geo-replication across multiple datacenters, near real-time update ingestion, strong consistency, and high availability -- occupying a design space between traditional RDBMS and NoSQL systems by restricting updates to batched, controlled operations.

## Summary

Mesa is a distributed, replicated, and highly available data processing, storage, and query system for structured data. It is a cloud-native data warehouse that is geo-replicated across multiple datacenters and provides strongly consistent, ordered versioning of data. A primary use case for Mesa is storing critical measurement data related to Google's Internet advertising business.

Mesa builds upon Google infrastructure components including Colossus, Bigtable, and MapReduce. It is geo-replicated across multiple datacenters and provides consistent, repeatable query answers at low latency, even when an entire datacenter fails. Key features of Mesa include atomic updates, consistency and correctness guarantees, high availability, near real-time update throughput, low-latency query performance, scalability, and online data and metadata transformation. Mesa occupies a hybrid design space between traditional RDBMS and NoSQL systems: it achieves high scalability, strong consistency, and transactional guarantees by restricting the system to batched, controlled updates that are processed in near real-time.

## Strengths

- Mesa is built upon Colossus and Bigtable, reusing already proven components rather than building everything from scratch.
- The approach of updating data asynchronously across datacenters is a well-considered design choice, as it minimizes synchronization overhead while still maintaining consistency guarantees.
- The experiences and lessons learned section is particularly valuable, as it provides candid insights into the challenges of building a large-scale distributed system in production.

## Weaknesses

- The paper should have discussed compression ratios and read/decompression times, which are important for understanding the practical performance characteristics of Mesa.
- In Figure 7, the X-axis data points are not labeled, and the oscillating pattern in the graph is not adequately explained.
- It would have been informative to see the size and types of metadata stored in Bigtable for the production Mesa deployment.

## Discussion Questions

- Is there a foreign key concept in Mesa? In the provided example, how would Mesa update Table C when Table A and Table B are updated? Is there a callback mechanism? If not, are join operations supported?
- Can users query for a specific version? How do users discover current and previous version numbers? At the scale Mesa operates, could the system exhaust the integer range for version numbers?
- How does the query server decide which tables to cache at startup? Does it use a hashing or modulo function for this selection?
- What is the latency overhead introduced by per-instance verification, as well as the online and offline data verification processes?
- Where is the versions database stored? Is it in Bigtable? The paper states that it is replicated and uses Paxos, but how does the versioning mechanism itself work?

---

*This review was written as part of CMU's 18-845: Internet Services course.*
