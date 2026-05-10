---
layout: post
title: "MapReduce: Simplified Data Processing on Large Clusters"
date: 2024-07-01
author: Darsh Shah
tags: [distributed-systems, mapreduce, google, parallel-computing, big-data]
excerpt: "This landmark paper introduces MapReduce, a programming model and runtime framework for processing massive datasets across thousands of machines. By abstracting away the complexity of parallelization, fault tolerance, and load balancing behind two simple functions -- Map and Reduce -- the framework enabled Google engineers to write distributed computations with minimal effort while the system handled all the hard parts."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"MapReduce: Simplified Data Processing on Large Clusters"* by Jeffrey Dean and Sanjay Ghemawat (OSDI, 2004)

## TL;DR

This landmark paper introduces MapReduce, a programming model and runtime framework for processing massive datasets across thousands of machines. By abstracting away the complexity of parallelization, fault tolerance, and load balancing behind two simple functions -- Map and Reduce -- the framework enabled Google engineers to write distributed computations with minimal effort while the system handled all the hard parts.

## Summary

The paper describes the architecture, implementation, and performance of the MapReduce framework, which was used for many of Google's internal computations. MapReduce is highly scalable -- it processes many terabytes of data across thousands of machines. It hides the details of parallelization, fault tolerance, locality optimization, and load balancing. Some applications of the framework include building inverted indexes, distributed sorting, distributed grep, and word frequency analysis.

The computation takes a set of input key/value pairs and produces a set of output key/value pairs. Two user-defined functions drive the process: the Map function processes input key/value pairs to generate a set of intermediate key/value pairs, and the Reduce function merges all intermediate values associated with the same intermediate key. The framework takes care of data partitioning, job scheduling, machine failure handling, and inter-machine communication.

## Strengths

- The authors provide many motivating examples -- distributed grep, reverse web-link graphs, and others -- that clearly demonstrate the need for the MapReduce framework. These examples help the reader understand why such a solution was necessary.
- MapReduce uses GFS for storage, which means it does not have to deal with reliability, availability, or durability concerns. These are handled by GFS, keeping the MapReduce design simple and focused.
- The authors include the complete Word Frequency code to demonstrate the simplicity of writing a MapReduce application. The brevity of the code powerfully illustrates the framework's expressive power.

## Weaknesses

- The grouping of intermediate values produced by the Map function is not clearly explained. Is hashing used for grouping? The paper mentions that "the intermediate values are supplied to the user's reduce function via an iterator," but provides no details about how this iterator works.
- The paper lacks information about the CPU and I/O utilization of individual worker machines during the MapReduce process. Are the functions CPU-intensive? Can other tasks run concurrently while MapReduce is executing? This information would have been valuable.
- The sequencing between map and reduce phases is unclear. After a worker finishes a map task, does it immediately send data to the reducer? Will a reducer read the files from the worker before that worker is reassigned to another map task? The paper does not clearly describe when the mapping phase ends and the reducing phase begins.
- There is no information on how the framework provides ordering guarantees.

## Discussion Questions

- The framework uses a single master, and the entire MapReduce computation is aborted if the master fails. It would be interesting to see master replication similar to GFS. While the single-master approach provides simplicity, a master failure after a long computation would be painful and require significant rework.
- How is input data assigned to map functions? Can locality be taken into account when distributing the 64 MB input splits to mapper machines? How does the master choose a worker for a particular input split?
- Could two Map functions, or a Map and a Reduce function, run concurrently on the same machine? Since each is a separate process, is this feasible?
- What happens if the intermediate data produced by the Map function is so large that it overwhelms the local disk? For example, if there are R reducers, the Map function will produce R files on the local disk. If R is very large, could the machine run out of disk space or exceed the maximum number of allowable files?
- The authors mention backup execution of remaining in-progress tasks near the end of a computation. What would happen if the threshold were set much more aggressively -- say 20% -- so that no worker is ever idle? Would instantly assigning backup executions to idle workers be more efficient?
