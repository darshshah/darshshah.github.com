---
layout: post
title: "Nested Virtualization and the Turtles Project: Hypervisors All the Way Down"
date: 2025-03-31
author: Darsh Shah
tags: [virtualization, nested-virtualization, kvm, hypervisor, cloud-computing, performance]
---

# Nested Virtualization and the Turtles Project: Hypervisors All the Way Down

> **Paper:** *"The Turtles Project: Design and Implementation of Nested Virtualization"* by M. Ben-Yehuda et al. (OSDI, 2010)

## TL;DR

The Turtles Project demonstrates that hypervisors can efficiently run inside other hypervisors -- even without architectural support for nesting on x86. By implementing multi-dimensional paging for MMU virtualization and multi-level device assignment for I/O virtualization within Linux/KVM, the project achieves nested virtualization performance within 6-8% of single-level virtualization for common workloads, opening the door to live hypervisor migration and new security paradigms.

## Summary

Nested virtualization is a technique where a hypervisor runs multiple other hypervisors along with their associated virtual machines. This paper describes the Turtles Project at IBM, which is built as part of the Linux/KVM hypervisor and runs multiple unmodified hypervisors (including KVM and VMware) and operating systems (Windows and Linux) despite the lack of architectural support for nested virtualization in the x86 architecture. Using techniques for multi-dimensional paging (MMU virtualization) and multi-level device assignment (I/O virtualization), the project achieved performance within 6-8% of single-level (non-nested) virtualization for common workloads.

Nested VMs are important because they enable live migration of an entire hypervisor and its guest operating systems as a single entity -- useful for load balancing or disaster recovery. They also enable new approaches to computer security, such as honeypots capable of running hypervisor-level rootkits. Additionally, nested virtualization facilitates testing, benchmarking, and debugging of hypervisors and virtualization setups. Nested virtualization is likely to become an increasingly important component of cloud computing.

## Strengths

- The techniques of multi-dimensional paging and multi-level device assignment are well-designed and effective at achieving strong performance for nested virtualization.
- The evaluation is thorough, testing with both KVM and VMware as the L1 (guest) hypervisor, which demonstrates the system's generality.
- Placing the related work section at the beginning of the paper is unusual but effective. It helps the authors immediately establish that their proposed solution is distinct from prior approaches.

## Weaknesses

- The paper would have benefited from more detail on how nested virtualization enables hypervisor-level rootkit protection and hypervisor-level intrusion detection. These security use cases are mentioned but not explored.
- The authors should have provided more explanation of how the IOMMU works, as it plays a critical role in the I/O virtualization story.
- The paper does not discuss whether the nested VM technique generalizes to different ISAs, nor does it mention whether Xen can be used as an L1 hypervisor.

## Discussion Questions

- In the single-level architectural support model for nested virtualization, traps at any nesting level are handled by the L0 hypervisor. To forward a trap to the appropriate guest hypervisor, how does the data travel? If L0 receives a fault from a guest OS on L2, does it forward the trap through L1? If so, L1 would need to handle an event that does not concern it, which seems inefficient.
- Could a malicious rootkit installed on an L1 hypervisor adversely affect the L0 (root) hypervisor? Is there a security concern with nesting where an upper level can compromise a lower level (e.g., a Blue Pill-style attack)?
- Since x86 does not support nested virtualization in hardware, would it be feasible to use binary translation for the L1 hypervisor while keeping the L0 hypervisor hardware-assisted?
- With each additional nesting level, the exit multiplication increases. What is the practical maximum depth of nesting before performance degrades unacceptably? Performance likely saturates after just a few levels.
- How does the root hypervisor schedule guest hypervisors? Consider a scenario with 5 guest hypervisors running on a host hypervisor alongside 4 guest OSes, where each guest hypervisor runs 2 guest OSes of its own -- that is 14 guest OSes in total. How is CPU time distributed? The resulting latency could lead to significant performance degradation.

---

*This review was written as part of CMU's 18-845: Internet Services course.*
