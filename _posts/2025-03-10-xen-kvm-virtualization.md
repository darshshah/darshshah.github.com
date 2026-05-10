---
layout: post
title: "Xen and KVM: Two Approaches to Virtualization"
date: 2025-03-10
author: Darsh Shah
tags: [virtualization, xen, kvm, paravirtualization, hypervisor, linux, cloud-computing]
excerpt: "Xen pioneered paravirtualization -- modifying guest OSes slightly to achieve near-native performance on a bare-metal hypervisor -- while KVM took a radically simpler approach by turning the Linux kernel itself into a hypervisor through a loadable kernel module that leverages hardware virtualization extensions. Together, these two systems represent the dominant open-source virtualization paradigms that underpin modern cloud infrastructure."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Papers:** *"Xen and the Art of Virtualization"* by P. Barham et al. (SOSP, 2003) and *"kvm: the Linux Virtual Machine Monitor"* by A. Kivity et al. (Linux Symposium, 2007)

## TL;DR

Xen pioneered paravirtualization -- modifying guest OSes slightly to achieve near-native performance on a bare-metal hypervisor -- while KVM took a radically simpler approach by turning the Linux kernel itself into a hypervisor through a loadable kernel module that leverages hardware virtualization extensions. Together, these two systems represent the dominant open-source virtualization paradigms that underpin modern cloud infrastructure.

## Summary

The Kernel-based Virtual Machine (KVM) is a Linux subsystem that leverages hardware virtualization extensions on x86 processors to add hypervisor capability to Linux. Using KVM, one can create and run multiple virtual machines that appear as normal Linux processes, integrating seamlessly with the rest of the system. Its simplicity makes extending it straightforward, while its integration into Linux allows it to leverage the kernel's large feature set and rapid pace of development.

Xen is an x86 virtual machine monitor that allows multiple commodity operating systems to share conventional hardware in a safe and resource-managed fashion without sacrificing performance or functionality. This is achieved by providing an idealized virtual machine abstraction to which operating systems such as Linux, BSD, and Windows XP can be ported with minimal effort -- a technique called paravirtualization. Paravirtualization improves performance over pure software virtualization techniques, but the trade-off is that guest operating systems must be modified to take advantage of the Xen hypervisor.

## Strengths

- The design of Xen's asynchronous I/O rings for data transfer between the hypervisor and guest OSes provides a clean and visual abstraction of the communication process. The mechanism is also generic enough to support a variety of device paradigms.
- KVM's simplicity is appealing: it is as easy to deploy as loading a kernel module. Since it uses hardware virtualization, it should deliver better performance than binary translation. Its tight integration with Linux and its open-source nature are additional advantages.
- Xen, as a bare-metal hypervisor, cannot run without domain software (Domain 0). From a commercial standpoint, the hypervisor itself can be free, while the domain management software becomes the revenue source -- since it is required to install guest operating systems. This is a smart business strategy.

## Weaknesses

- The KVM paper claims support for up to 100 concurrent VMs but provides no experiments to substantiate this number or to demonstrate worst-case behavior. The authors should have run experiments and presented results showing system behavior under that level of concurrency.
- It is unclear whether the Xen team completed the Windows XP porting effort. To demonstrate that Xen works with a variety of operating systems beyond Linux, they should have included results for Windows and NetBSD before publication. Additionally, since Windows XP is closed-source, the paravirtualization approach may not be practical for it.
- The KVM paper lacks an evaluation section entirely. Since KVM was published after Xen, it would have been valuable to include comparative benchmarks between the two systems to justify KVM's existence and highlight its strengths.

## Discussion Questions

- With hardware virtualization now widely supported, is Xen's paravirtualization approach still relevant? Which is better suited for production use -- Xen or KVM? Xen, as a Type 1 hypervisor, can support multiple OSes directly, while KVM integrates tightly into Linux. Which one sees more adoption today?
- Does KVM provide VM isolation? If one VM crashes the kernel, will it affect other VMs or threads? Given that VMs reside at `/dev/kvm` as character devices, is meaningful isolation achievable?
- The KVM paper does not discuss live migration in detail. Can a VM on x86-64 be migrated to an i386 system? Can memory be increased or decreased after migration? How long does migration and convergence take? If a process tries to read a page that has already been transferred to another machine, how does KVM ensure this is not treated as a page fault?
- Is it possible to share memory pages across Xen guests for consolidation? Can Xen overcommit memory? For instance, if the host has 8 GB of RAM, can it present 8 GB to each guest OS, or must the total memory allocated across all guests equal 8 GB?
- Xen performs well even under uncooperative user applications because of isolation. But what about an uncooperative guest OS? Does Xen provide isolation at the guest OS level as well? Could a malicious guest OS compromise the Xen hypervisor itself?
